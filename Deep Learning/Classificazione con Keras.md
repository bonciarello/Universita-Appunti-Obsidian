## Framework Keras
**Keras** è un framework di deep learning per Python che consente una sperimentazione rapida.
*   Esegue codice su CPU o GPU.
*   API intuitiva per prototipazione rapida.
*   Supporto per reti convoluzionali (CNN) e ricorrenti (RNN).
*   Backend modulare: può girare su **TensorFlow**, **Theano** o **CNTK**.

Il flusso di lavoro tipico in Keras è:
1.  Definire i dati di training (input e target).
2.  Definire il modello (spesso `Sequential`).
3.  Configurare il processo di apprendimento (`compile` con loss, optimizer, metrics).
4.  Iterare sui dati di training (`fit`).

---

## Esempio 1: Classificazione Binaria (Dataset IMDb)

Il dataset IMDb contiene 50.000 recensioni di film (25k training, 25k test), classificate come positive o negative.

### Caricamento e Preprocessing
```python
import numpy as np
from keras.datasets import imdb

# Caricamento dati (num_words=10000 mantiene solo le 10k parole più frequenti)
(train_data, train_labels), (test_data, test_labels) = imdb.load_data(num_words=10000)

# One-hot encoding delle sequenze
def vectorize_sequences(sequences, dimension=10000):
    results = np.zeros((len(sequences), dimension))
    for i, sequence in enumerate(sequences):
        results[i, sequence] = 1.
    return results

x_train = vectorize_sequences(train_data)
x_test = vectorize_sequences(test_data)

# Conversione etichette in float
y_train = np.asarray(train_labels).astype('float32')
y_test = np.asarray(test_labels).astype('float32')
```

### Definizione del Modello
```python
from keras import models
from keras import layers

model = models.Sequential()
model.add(layers.Dense(16, activation='relu', input_shape=(10000,)))
model.add(layers.Dense(16, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid')) # Output binario (0 o 1)

model.compile(optimizer='rmsprop',
              loss='binary_crossentropy',
              metrics=['accuracy'])
```

### Validazione e Training
Creiamo un set di validazione per monitorare l'addestramento ed evitare overfitting.
```python
x_val = x_train[:10000]
partial_x_train = x_train[10000:]
y_val = y_train[:10000]
partial_y_train = y_train[10000:]

history = model.fit(partial_x_train,
                    partial_y_train,
                    epochs=20,
                    batch_size=512,
                    validation_data=(x_val, y_val))
```

### Visualizzazione dei Risultati
```python
import matplotlib.pyplot as plt

history_dict = history.history
loss_values = history_dict['loss']
val_loss_values = history_dict['val_loss']
epochs = range(1, len(loss_values) + 1)

# Plot Loss
plt.plot(epochs, loss_values, 'bo', label='Training loss')
plt.plot(epochs, val_loss_values, 'b', label='Validation loss')
plt.title('Training and validation loss')
plt.xlabel('Epochs')
plt.ylabel('Loss')
plt.legend()
plt.show()

# Plot Accuracy
plt.clf()
acc = history_dict['binary_accuracy']
val_acc = history_dict['val_binary_accuracy']
plt.plot(epochs, acc, 'bo', label='Training acc')
plt.plot(epochs, val_acc, 'b', label='Validation acc')
plt.legend()
plt.show()
```

---

## Esempio 2: Classificazione Multiclasse (Dataset Reuters)

Classificazione di notiziari in 46 argomenti diversi.

### Preprocessing e Modello
```python
from keras.datasets import reuters
from keras.utils.np_utils import to_categorical

(train_data, train_labels), (test_data, test_labels) = reuters.load_data(num_words=10000)

# Vettorizzazione (stessa funzione di prima)
x_train = vectorize_sequences(train_data)
x_test = vectorize_sequences(test_data)

# One-hot encoding delle etichette
one_hot_train_labels = to_categorical(train_labels)
one_hot_test_labels = to_categorical(test_labels)

model = models.Sequential()
model.add(layers.Dense(64, activation='relu', input_shape=(10000,)))
model.add(layers.Dense(64, activation='relu'))
model.add(layers.Dense(46, activation='softmax')) # 46 classi, softmax per probabilità

model.compile(optimizer='rmsprop',
              loss='categorical_crossentropy',
              metrics=['accuracy'])

# Training
history = model.fit(x_train,
                    one_hot_train_labels,
                    epochs=20,
                    batch_size=512,
                    validation_split=0.2) # Usa il 20% per validazione automaticamente
```

**Nota:** Se si usano etichette intere (non one-hot), si deve usare la loss `sparse_categorical_crossentropy`.