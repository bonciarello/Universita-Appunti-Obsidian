In questo laboratorio implementeremo diversi tipi di Autoencoder utilizzando il dataset **Fashion MNIST**, che contiene 60.000 immagini in scala di grigi (28x28 pixel) di 10 categorie di abbigliamento (t-shirt, pantaloni, scarpe, ecc.).

## 1. Preparazione dei Dati
A differenza della classificazione supervisionata, qui non ci servono le etichette (`y_train`, `y_test`). L'obiettivo dell'autoencoder è ricostruire l'input, quindi il target ($y$) è uguale all'input ($x$).

```python
import numpy as np
import matplotlib.pyplot as plt
import tensorflow as tf
from tensorflow.keras.datasets import fashion_mnist
from sklearn.model_selection import train_test_split

# Caricamento dati (ignoriamo le etichette con _)
(x_train, _), (x_test, _) = fashion_mnist.load_data()

# Normalizzazione: scaliamo i pixel da [0, 255] a [0, 1]
x_train = x_train.astype('float32') / 255.
x_test = x_test.astype('float32') / 255.

# Per l'Autoencoder DENSO (non convoluzionale), dobbiamo appiattire le immagini
# Da matrici 28x28 a vettori di lunghezza 784
x_train_flat = x_train.reshape((len(x_train), 784))
x_test_flat = x_test.reshape((len(x_test), 784))

# Creiamo un set di validazione
x_train_flat, x_val_flat = train_test_split(x_train_flat, test_size=0.2, random_state=42)
```

## 2. Autocodificatore Semplice (Dense)
Costruiamo la versione più basilare: un encoder che comprime l'input in un vettore latente di dimensione 32 (fattore di compressione ~24.5) e un decoder che lo ricostruisce.

```python
from tensorflow.keras.layers import Input, Dense
from tensorflow.keras.models import Model

# Dimensione dello spazio latente (Bottleneck)
encoding_dim = 32

# Input Placeholder
input_img = Input(shape=(784,))

# Encoder: 784 -> 32
encoded = Dense(encoding_dim, activation='relu')(input_img)

# Decoder: 32 -> 784
# Usiamo 'sigmoid' perché i dati sono normalizzati tra 0 e 1
decoded = Dense(784, activation='sigmoid')(encoded)

# Modello completo: Input -> Ricostruzione
autoencoder = Model(input_img, decoded)

# Compilazione: usiamo Binary Crossentropy come loss "pixel-wise"
# (funziona bene quando i valori dei pixel sono interpretabili come probabilità [0,1])
autoencoder.compile(optimizer='adam', loss='binary_crossentropy')

# Addestramento: nota che x_train_flat è sia input che target!
autoencoder.fit(x_train_flat, x_train_flat,
                epochs=20,
                batch_size=256,
                validation_data=(x_val_flat, x_val_flat))
```

## 3. Autocodificatore Convoluzionale (Denoising)
Le reti dense perdono l'informazione spaziale (struttura 2D). Per le immagini, le **Convolutional Neural Networks (CNN)** sono molto più potenti.
Implementeremo un **Denoising Autoencoder**:
1.  Corrompiamo le immagini con rumore.
2.  Chiediamo alla rete di ricostruire l'immagine *pulita*.
Questo costringe la rete a imparare caratteristiche robuste (es. "le linee dei pantaloni sono continue") per rimuovere il rumore.

### Aggiunta di Rumore
```python
noise_factor = 0.2
# Aggiungiamo rumore gaussiano
x_train_noisy = x_train + noise_factor * tf.random.normal(shape=x_train.shape) 
x_test_noisy = x_test + noise_factor * tf.random.normal(shape=x_test.shape) 

# Clamping: assicuriamoci che i valori restino nel range [0, 1]
x_train_noisy = tf.clip_by_value(x_train_noisy, clip_value_min=0., clip_value_max=1.)
x_test_noisy = tf.clip_by_value(x_test_noisy, clip_value_min=0., clip_value_max=1.)
```

### Architettura Convoluzionale
Per il Decoder, dobbiamo aumentare le dimensioni spaziali (Upsampling). Usiamo `UpSampling2D` (ripete righe/colonne) o `Conv2DTranspose` (convoluzione inversa apprendibile).

```python
from tensorflow.keras.layers import Conv2D, MaxPooling2D, UpSampling2D

input_img = Input(shape=(28, 28, 1)) # Immagini 28x28, 1 canale

# --- ENCODER ---
# Conv1: 28x28x32
x = Conv2D(32, (3, 3), activation='relu', padding='same')(input_img)
# Pool1: 14x14x32
x = MaxPooling2D((2, 2), padding='same')(x)
# Conv2: 14x14x32
x = Conv2D(32, (3, 3), activation='relu', padding='same')(x)
# Pool2: 7x7x32 (Spazio Latente)
encoded = MaxPooling2D((2, 2), padding='same')(x)

# --- DECODER ---
# Conv3: 7x7x32
x = Conv2D(32, (3, 3), activation='relu', padding='same')(encoded)
# Up1: 14x14x32
x = UpSampling2D((2, 2))(x)
# Conv4: 14x14x32
x = Conv2D(32, (3, 3), activation='relu', padding='same')(x)
# Up2: 28x28x32
x = UpSampling2D((2, 2))(x)
# Output: 28x28x1 (Sigmoid per tornare a [0, 1])
decoded = Conv2D(1, (3, 3), activation='sigmoid', padding='same')(x)

autoencoder = Model(input_img, decoded)
autoencoder.compile(optimizer='adam', loss='binary_crossentropy')

# Addestramento: Input = RUMOROSO, Target = PULITO
autoencoder.fit(x_train_noisy, x_train,
                epochs=50,
                batch_size=128,
                validation_split=0.2)
```

### Visualizzazione
Confrontiamo l'immagine rumorosa di input con la ricostruzione pulita.

```python
def plot_results(models, data_noisy, data_clean, n=10):
    decoded_imgs = models.predict(data_noisy)
    plt.figure(figsize=(20, 4))
    for i in range(n):
        # Input Rumoroso
        ax = plt.subplot(2, n, i + 1)
        plt.imshow(data_noisy[i].reshape(28, 28))
        plt.gray()
        ax.get_xaxis().set_visible(False)
        ax.get_yaxis().set_visible(False)
        if i == 0: ax.set_title("Input Rumoroso")

        # Ricostruzione
        ax = plt.subplot(2, n, i + 1 + n)
        plt.imshow(decoded_imgs[i].reshape(28, 28))
        plt.gray()
        ax.get_xaxis().set_visible(False)
        ax.get_yaxis().set_visible(False)
        if i == 0: ax.set_title("Ricostruzione Denoised")
    plt.show()

plot_results(autoencoder, x_test_noisy[:10], x_test[:10])
```
