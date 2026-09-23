---
aliases: [Regressiona]
tags: [deep-learning]
---
Un problema di regressione consiste nel prevedere un valore continuo (es. temperatura, prezzo) anziché un'etichetta discreta.

## Esempio: Predizione Prezzo Case (Boston Housing)

Vogliamo prevedere il prezzo medio delle case a Boston negli anni '70 basandoci su 13 caratteristiche (tasso criminalità, tasse, ecc.).
Il dataset è piccolo: 506 campioni (404 train, 102 test). Le caratteristiche hanno scale diverse.

### Caricamento e Normalizzazione
```python
from keras.datasets import boston_housing

(train_data, train_targets), (test_data, test_targets) = boston_housing.load_data()

# Normalizzazione (Standardizzazione)
# Sottraiamo la media e dividiamo per la deviazione standard
mean = train_data.mean(axis=0)
train_data -= mean
std = train_data.std(axis=0)
train_data /= std

# Usiamo le statistiche del training set per normalizzare il test set
test_data -= mean
test_data /= std
```

### Definizione del Modello
Per dataset piccoli, usiamo una rete piccola per evitare overfitting.
```python
from keras import models, layers

def build_model():
    model = models.Sequential()
    model.add(layers.Dense(64, activation='relu', input_shape=(train_data.shape[1],)))
    model.add(layers.Dense(64, activation='relu'))
    # Nessuna attivazione nell'ultimo strato (output lineare per regressione)
    model.add(layers.Dense(1)) 
    
    model.compile(optimizer='rmsprop', loss='mse', metrics=['mae'])
    return model
```
*   **Loss:** MSE (Mean Squared Error).
*   **Metrica:** MAE (Mean Absolute Error).

### K-Fold Cross-Validation
Dato che i dati sono pochi, il validation set sarebbe troppo piccolo e instabile. Usiamo la K-fold Cross-Validation: dividiamo i dati in $K$ partizioni, addestriamo $K$ modelli identici su $K-1$ partizioni e valutiamo sulla rimanente.

```python
import numpy as np

k = 4
num_val_samples = len(train_data) // k
num_epochs = 100
all_scores = []

for i in range(k):
    print(f'Processing fold #{i}')
    
    # Dati di validazione: partizione k-esima
    val_data = train_data[i * num_val_samples: (i + 1) * num_val_samples]
    val_targets = train_targets[i * num_val_samples: (i + 1) * num_val_samples]
    
    # Dati di training: tutte le altre partizioni
    partial_train_data = np.concatenate(
        [train_data[:i * num_val_samples],
         train_data[(i + 1) * num_val_samples:]], axis=0)
    partial_train_targets = np.concatenate(
        [train_targets[:i * num_val_samples],
         train_targets[(i + 1) * num_val_samples:]], axis=0)
    
    model = build_model()
    model.fit(partial_train_data, partial_train_targets,
              epochs=num_epochs, batch_size=1, verbose=0)
    
    val_mse, val_mae = model.evaluate(val_data, val_targets, verbose=0)
    all_scores.append(val_mae)

print("Average MAE:", np.mean(all_scores))
```

### Analisi Storico (Training Lungo)
Per visualizzare l'andamento su più epoche (es. 500) e salvare la storia:

```python
# ... (all'interno del loop k-fold) ...
history = model.fit(partial_train_data, partial_train_targets,
                    validation_data=(val_data, val_targets),
                    epochs=500, batch_size=1, verbose=0)
mae_history = history.history['val_mae'] # o 'val_mean_absolute_error'
all_mae_histories.append(mae_history)

# Media per epoca
average_mae_history = [
    np.mean([x[i] for x in all_mae_histories]) for i in range(500)
]

# Plot (escludendo i primi 10 punti per scala)
import matplotlib.pyplot as plt
plt.plot(range(11, len(average_mae_history) + 1), average_mae_history[10:])
plt.xlabel('Epochs')
plt.ylabel('Validation MAE')
plt.show()
```