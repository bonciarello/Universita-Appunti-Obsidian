# Capitolo 7: Laboratorio - Convalida K-Fold e TensorFlow

## Convalida K-Fold (Approfondimento)

Nei capitoli precedenti, per valutare i nostri modelli, abbiamo diviso i dati in un set di addestramento e un set di validazione.
Tuttavia, quando si dispone di pochi dati (es. poche centinaia o migliaia di campioni), il set di validazione rischia di essere troppo piccolo. Di conseguenza, le metriche di validazione (accuratezza, perdita) potrebbero avere una **varianza molto elevata**: dipenderebbero pesantemente da *quali* specifici campioni sono finiti casualmente nel set di validazione e quali nel training.
Se cambiando il seed casuale (random state) le prestazioni cambiano drasticamente, non possiamo fidarci della valutazione del modello.

La soluzione standard in questi casi è la **Convalida Incrociata K-Fold (K-Fold Cross-Validation)**.

### Come Funziona
1.  Si dividono i dati disponibili in $K$ partizioni (o "pieghe") uguali (tipicamente $K=4$ o $5$).
2.  Si istanziano $K$ modelli identici.
3.  Si addestra ogni modello su $K-1$ partizioni e lo si valuta sulla partizione rimanente.
4.  Il punteggio di validazione finale del modello è la **media** dei $K$ punteggi ottenuti.

Questo approccio garantisce che ogni campione venga utilizzato esattamente una volta per la validazione e $K-1$ volte per l'addestramento, riducendo la dipendenza dalla suddivisione casuale dei dati.

### Implementazione
Ecco un esempio pratico di implementazione manuale del loop K-Fold:

```python
import numpy as np

def train_kfold(train_data, train_targets, k=4, num_epochs=100):
    num_val_samples = len(train_data) // k
    all_scores = []

    for i in range(k):
        print(f'Processing fold #{i}')
        
        # Prepara i dati di validazione: la partizione i-esima
        val_data = train_data[i * num_val_samples: (i + 1) * num_val_samples]
        val_targets = train_targets[i * num_val_samples: (i + 1) * num_val_samples]

        # Prepara i dati di training: tutte le altre partizioni concatenate
        partial_train_data = np.concatenate(
            [train_data[:i * num_val_samples],
             train_data[(i + 1) * num_val_samples:]], axis=0)
        partial_train_targets = np.concatenate(
            [train_targets[:i * num_val_samples],
             train_targets[(i + 1) * num_val_samples:]], axis=0)

        # Costruisci il modello (deve essere una funzione nuova per resettare i pesi)
        model = build_model()
        
        # Addestra il modello (verbose=0 per non intasare l'output)
        model.fit(partial_train_data, partial_train_targets,
                  epochs=num_epochs, batch_size=1, verbose=0)
        
        # Valuta sulla partizione di validazione corrente
        val_mse, val_mae = model.evaluate(val_data, val_targets, verbose=0)
        all_scores.append(val_mae)
    
    # Restituisce la media dei punteggi
    return np.mean(all_scores)
```

---

## Reti Neurali con TensorFlow

**TensorFlow (TF)** è una piattaforma open-source end-to-end per il machine learning sviluppata da Google.
Offre un ecosistema flessibile di strumenti, librerie e risorse della community che consente ai ricercatori di spingere lo stato dell'arte nel ML e agli sviluppatori di creare e distribuire facilmente applicazioni basate su ML.

### Caratteristiche Principali
*   **Tensori:** L'unità fondamentale di dati in TF è il *tensore*. Un tensore è un array multidimensionale (scalare, vettore, matrice, n-dimensionale). È simile agli array NumPy, ma i tensori sono immutabili e possono risiedere nella memoria della GPU per calcoli accelerati.
*   **Grafo Computazionale vs Eager Execution:**
    *   *Graph Execution (TF 1.x):* Si definiva prima un grafo statico delle operazioni e poi lo si eseguiva in una sessione. Più veloce e ottimizzabile, ma difficile da debuggare.
    *   *Eager Execution (TF 2.x Default):* Le operazioni vengono valutate immediatamente, restituendo valori concreti. Questo rende TF molto più intuitivo ("Pythonico") e facile da debuggare, simile a PyTorch.

### Workflow Standard con Keras (tf.keras)
Keras è l'API di alto livello ufficiale di TensorFlow. Il flusso di lavoro tipico per costruire una rete neurale prevede 5 passaggi:

1.  **Definizione dei Dati:** Caricare e pre-processare i dati (normalizzazione, reshaping).
2.  **Definizione del Modello:** Creare l'architettura della rete (es. usando `Sequential`).
3.  **Compilazione:** Specificare come il modello deve imparare.
    *   *Loss Function:* Cosa stiamo cercando di minimizzare.
    *   *Optimizer:* Come aggiornare i pesi (es. SGD, Adam).
    *   *Metrics:* Come valutare il successo (es. Accuracy).
4.  **Addestramento (Fit):** Il ciclo in cui il modello vede i dati e aggiorna i pesi.
5.  **Valutazione e Predizione:** Testare il modello su nuovi dati.

#### Esempio Completo (MNIST)

```python
import tensorflow as tf
from tensorflow import keras

# 1. Caricamento dati
mnist = keras.datasets.mnist
(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0 # Normalizzazione

# 2. Definizione Modello
model = keras.Sequential([
    keras.layers.Flatten(input_shape=(28, 28)), # Input layer
    keras.layers.Dense(128, activation='relu'), # Hidden layer
    keras.layers.Dropout(0.2),                  # Regolarizzazione
    keras.layers.Dense(10, activation='softmax')# Output layer
])

# 3. Compilazione
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

# 4. Addestramento
model.fit(x_train, y_train, epochs=5)

# 5. Valutazione
model.evaluate(x_test, y_test)
```
