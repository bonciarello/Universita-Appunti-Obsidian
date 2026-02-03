In questo laboratorio useremo il dataset **IMDb Reviews** (25.000 recensioni di film) per costruire un classificatore di sentiment (Positivo/Negativo). Useremo tecniche moderne di Keras come il layer `TextVectorization` integrato.

## 1. Caricamento e Ottimizzazione Dati
Usiamo `tensorflow_datasets` per scaricare i dati.

```python
import numpy as np
import tensorflow as tf
import tensorflow_datasets as tfds
import matplotlib.pyplot as plt

# Caricamento dati
dataset, info = tfds.load('imdb_reviews', with_info=True, as_supervised=True)
train_dataset, test_dataset = dataset['train'], dataset['test']

# Ottimizzazione della pipeline di input
BUFFER_SIZE = 10000
BATCH_SIZE = 64

# Shuffle: mescola i dati per evitare bias
# Batch: raggruppa le recensioni
# Prefetch: carica il prossimo batch mentre la GPU lavora
train_dataset = train_dataset.shuffle(BUFFER_SIZE).batch(BATCH_SIZE).prefetch(tf.data.AUTOTUNE)
test_dataset = test_dataset.batch(BATCH_SIZE).prefetch(tf.data.AUTOTUNE)
```

## 2. Text Vectorization Layer
Invece di pre-processare il testo "offline" con strumenti Python, usiamo un layer Keras che diventa parte del modello. Questo rende il modello portabile (accetta stringhe raw in input).

```python
VOCAB_SIZE = 1000 # Numero massimo di parole nel vocabolario
encoder = tf.keras.layers.TextVectorization(max_tokens=VOCAB_SIZE)

# .adapt() legge il dataset di training e costruisce il vocabolario
print("Adattamento del layer di vettorizzazione...")
encoder.adapt(train_dataset.map(lambda text, label: text))

# Esempio di funzionamento
vocab = np.array(encoder.get_vocabulary())
print(f"Prime 20 parole: {vocab[:20]}")
# Esempio di codifica: "Hello world" -> [indice_hello, indice_world]
```

## 3. Modello con LSTM Bidirezionale
Una LSTM standard legge il testo solo da sinistra a destra. Ma il contesto può dipendere anche da parole future ("Non mi è piaciuto, *tuttavia*...").
Una **Bidirectional LSTM** consiste in due LSTM: una legge l'input in avanti, l'altra all'indietro. I loro output vengono concatenati.

```python
model = tf.keras.Sequential([
    # Layer 1: Encoder (String -> Indici interi)
    encoder,
    
    # Layer 2: Embedding (Indici -> Vettori densi)
    # mask_zero=True dice ai layer successivi di ignorare gli zeri di padding
    tf.keras.layers.Embedding(
        input_dim=len(encoder.get_vocabulary()),
        output_dim=64,
        mask_zero=True),
    
    # Layer 3: Bidirectional LSTM
    # 64 unità per direzione -> Output totale 128
    tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(64)),
    
    # Layer 4: Classificatore denso
    tf.keras.layers.Dense(64, activation='relu'),
    tf.keras.layers.Dropout(0.5), # Previene l'overfitting
    
    # Output: 1 neurone (logit). Se > 0 positivo, < 0 negativo.
    tf.keras.layers.Dense(1)
])

# Compilazione
# from_logits=True è numericamente più stabile
model.compile(loss=tf.keras.losses.BinaryCrossentropy(from_logits=True),
              optimizer=tf.keras.optimizers.Adam(1e-4),
              metrics=['accuracy'])
```

## 4. Addestramento
```python
history = model.fit(train_dataset,
                    epochs=10,
                    validation_data=test_dataset,
                    validation_steps=30)
```

## 5. Inferenza su Testo Grezzo
Grazie a `TextVectorization` dentro il modello, possiamo fare predizioni direttamente su stringhe.

```python
sample_text = ('The movie was cool. The animation and the graphics '
               'were out of this world. I would recommend this movie.')
predictions = model.predict(np.array([sample_text]))
print(f"Score (logit): {predictions[0][0]}") 
# Se score > 0 -> Recensione Positiva
```

## Stack di RNN (Deep RNN)
Se volessimo una rete più profonda, potremmo impilare più livelli ricorrenti.
Importante: i layer intermedi devono restituire l'intera sequenza (`return_sequences=True`).

```python
model_deep = tf.keras.Sequential([
    encoder,
    tf.keras.layers.Embedding(len(encoder.get_vocabulary()), 64, mask_zero=True),
    
    # Primo livello: restituisce sequenza 3D (batch, time, features)
    tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(64, return_sequences=True)),
    
    # Secondo livello: restituisce vettore 2D (batch, features)
    tf.keras.layers.Bidirectional(tf.keras.layers.LSTM(32)),
    
    tf.keras.layers.Dense(64, activation='relu'),
    tf.keras.layers.Dropout(0.5),
    tf.keras.layers.Dense(1)
])
```
