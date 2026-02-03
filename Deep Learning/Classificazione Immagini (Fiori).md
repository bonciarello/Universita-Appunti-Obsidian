In questo laboratorio metteremo in pratica la teoria delle ConvNets costruendo un classificatore per riconoscere specie di fiori. Utilizzeremo un dataset contenente 3670 foto a colori divise in 5 classi: *daisy* (margherita), *dandelion* (dente di leone), *roses* (rose), *sunflowers* (girasoli), *tulips* (tulipani).

Affronteremo i problemi tipici della Computer Vision reale: caricamento dati da disco, pre-processing, e overfitting su dataset piccoli.

## 1. Caricamento e Preparazione del Dataset

Le immagini non sono tensori NumPy pre-caricati (come in MNIST), ma file JPEG su disco. Dobbiamo creare una pipeline che le legga, le decodifichi e le ridimensioni.

```python
import tensorflow as tf
import pathlib
import matplotlib.pyplot as plt
import numpy as np

# Scarichiamo il dataset (archivio .tgz)
dataset_url = "https://storage.googleapis.com/download.tensorflow.org/example_images/flower_photos.tgz"
data_dir = tf.keras.utils.get_file('flower_photos', origin=dataset_url, untar=True)
data_dir = pathlib.Path(data_dir)

# Parametri globali
batch_size = 32
img_height = 180
img_width = 180

# Usiamo `image_dataset_from_directory` per creare un tf.data.Dataset
# Questa utility inferisce le etichette (classi) dalla struttura delle cartelle.
train_ds = tf.keras.preprocessing.image_dataset_from_directory(
    data_dir,
    validation_split=0.2, # Usiamo il 20% per la validazione
    subset='training',
    seed=123,
    image_size=(img_height, img_width),
    batch_size=batch_size)

val_ds = tf.keras.preprocessing.image_dataset_from_directory(
    data_dir,
    validation_split=0.2,
    subset='validation',
    seed=123,
    image_size=(img_height, img_width),
    batch_size=batch_size)

class_names = train_ds.class_names
print(f"Classi trovate: {class_names}")

# Ottimizzazione della performance (caching e prefetching)
# Manteniamo i dati in memoria (cache) e pre-carichiamo il prossimo batch mentre la GPU lavora (prefetch)
AUTOTUNE = tf.data.AUTOTUNE
train_ds = train_ds.cache().shuffle(1000).prefetch(buffer_size=AUTOTUNE)
val_ds = val_ds.cache().prefetch(buffer_size=AUTOTUNE)
```

## 2. Normalizzazione dei Dati
Le immagini RGB hanno valori dei pixel nel range [0, 255]. Le reti neurali convergono molto più velocemente se gli input sono piccoli e normalizzati, tipicamente nel range [0, 1].
Possiamo includere questo passaggio direttamente nel modello usando un livello di `Rescaling`.

```python
from tensorflow.keras import layers

normalization_layer = layers.Rescaling(1./255)
```

## 3. Data Augmentation (Aumento dei Dati)
Con solo ~3000 immagini di training, l'overfitting è il nemico numero uno. Il modello imparerà a riconoscere *quelle specifiche foto* invece dei fiori in generale.
La **Data Augmentation** genera nuovi campioni di training a partire da quelli esistenti applicando trasformazioni casuali (che però mantengono la semantica dell'immagine): rotazioni, ribaltamenti, zoom.
Questo "confonde" il modello in modo costruttivo, costringendolo a imparare caratteristiche invarianti.

```python
data_augmentation = tf.keras.Sequential([
    layers.RandomFlip("horizontal", input_shape=(img_height, img_width, 3)),
    layers.RandomRotation(0.1), # Ruota casualmente fino al 10%
    layers.RandomZoom(0.1),     # Zoom casuale fino al 10%
])
```
Visualizziamo l'effetto su una singola immagine:
```python
plt.figure(figsize=(10, 10))
for images, _ in train_ds.take(1):
    for i in range(9):
        augmented_images = data_augmentation(images)
        ax = plt.subplot(3, 3, i + 1)
        plt.imshow(augmented_images[0].numpy().astype("uint8"))
        plt.axis("off")
```

## 4. Costruzione del Modello
Costruiamo una ConvNet completa. Useremo anche il **Dropout** come ulteriore tecnica di regolarizzazione.

```python
from tensorflow.keras.models import Sequential

num_classes = len(class_names)

model = Sequential([
    # I primi layer applicano augmentation e normalizzazione (attivi solo in training)
    data_augmentation,
    layers.Rescaling(1./255),
    
    # Primo blocco: estrae bordi e texture semplici
    layers.Conv2D(16, 3, padding='same', activation='relu'),
    layers.MaxPooling2D(),
    
    # Secondo blocco: combina i bordi in forme
    layers.Conv2D(32, 3, padding='same', activation='relu'),
    layers.MaxPooling2D(),
    
    # Terzo blocco: forme complesse
    layers.Conv2D(64, 3, padding='same', activation='relu'),
    layers.MaxPooling2D(),
    
    # Dropout: spegne casualmente il 20% dei neuroni per evitare co-adattamento
    layers.Dropout(0.2),
    
    # Classificatore finale
    layers.Flatten(),
    layers.Dense(128, activation='relu'),
    layers.Dense(num_classes) # Output lineare (logit)
])

# Compilazione
# Usiamo from_logits=True perché l'ultimo layer non ha softmax (più stabile numericamente)
model.compile(optimizer='adam',
              loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True),
              metrics=['accuracy'])

model.summary()
```

## 5. Addestramento
Addestriamo per 15 epoche. Grazie alla Data Augmentation e al Dropout, dovremmo vedere che la *validation accuracy* segue da vicino la *training accuracy*, indicando che l'overfitting è sotto controllo.

```python
epochs = 15
history = model.fit(
  train_ds,
  validation_data=val_ds,
  epochs=epochs
)
```

## 6. Predizione su Nuovi Dati
Come usare il modello addestrato su un'immagine scaricata da internet?

```python
# Scarichiamo un'immagine di girasole
sunflower_url = "https://storage.googleapis.com/download.tensorflow.org/example_images/592px-Red_sunflower.jpg"
sunflower_path = tf.keras.utils.get_file('Red_sunflower', origin=sunflower_url)

# Carichiamo e ridimensioniamo l'immagine alle dimensioni attese dal modello (180x180)
img = tf.keras.utils.load_img(
    sunflower_path, target_size=(img_height, img_width)
)

# Convertiamo in array e aggiungiamo la dimensione del batch (1, 180, 180, 3)
img_array = tf.keras.utils.img_to_array(img)
img_array = tf.expand_dims(img_array, 0)

# Predizione
predictions = model.predict(img_array)
# Applichiamo softmax per ottenere probabilità
score = tf.nn.softmax(predictions[0])

print(
    "Questa immagine appartiene alla classe {} con una confidenza del {:.2f}%."
    .format(class_names[np.argmax(score)], 100 * np.max(score))
)
```
