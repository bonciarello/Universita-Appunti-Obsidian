---
aliases: [OI, Ottimizzazione Iperparametri]
tags: [deep-learning]
---
Nel [[Machine Learning]], ci sono due tipi di parametri:
1.  **Parametri (Weights):** I pesi e bias della rete, appresi automaticamente durante il training (backpropagation).
2.  **Iperparametri:** Le variabili che decidiamo *noi* prima del training e che controllano la struttura della rete e l'algoritmo di apprendimento.
    *   *Modello:* Numero di layer, numero di neuroni, tipo di attivazione (ReLU vs Tanh).
    *   *Algoritmo:* Learning rate, batch size, optimizer (Adam vs SGD), tasso di dropout.

Trovare la combinazione giusta è un'arte oscura. Il processo di ricerca automatica si chiama **Hyperparameter Tuning (Hypertuning)**.

## 1. Ottimizzazione con TensorBoard (HParams)
TensorBoard offre un plugin chiamato **HParams** per visualizzare i risultati di vari esperimenti.
Possiamo definire intervalli discreti o continui per ogni iperparametro e lanciare un ciclo di training per ogni combinazione.

```python
from tensorboard.plugins.hparams import api as hp

# Definizione dello spazio di ricerca
HP_NUM_UNITS = hp.HParam('num_units', hp.Discrete([16, 32, 64]))
HP_DROPOUT = hp.HParam('dropout', hp.RealInterval(0.1, 0.3))
HP_OPTIMIZER = hp.HParam('optimizer', hp.Discrete(['adam', 'sgd']))

# Funzione che addestra un modello con una data configurazione
def train_test_model(hparams):
    model = keras.Sequential([
        layers.Dense(hparams[HP_NUM_UNITS], activation='relu'),
        layers.Dropout(hparams[HP_DROPOUT]),
        layers.Dense(10, activation='softmax')
    ])
    # ... compile e fit ...
    return accuracy
```
Nella dashboard `HPARAMS` di TensorBoard, vedremo un grafico a coordinate parallele che mostra come ogni scelta ha influenzato l'accuratezza finale.

## 2. Keras Tuner
Scrivere cicli `for` manuali per testare parametri diventa presto ingestibile. **Keras Tuner** è la libreria ufficiale per automatizzare questo processo.

### Definizione dell'Ipermodello
Invece di un modello statico, definiamo una funzione che costruisce modelli parametrici.

```python
import keras_tuner as kt

def model_builder(hp):
    model = keras.Sequential()
    
    # hp.Int definisce un intero da cercare in un range
    hp_units = hp.Int('units', min_value=32, max_value=512, step=32)
    model.add(layers.Dense(units=hp_units, activation='relu'))
    
    # hp.Choice sceglie tra valori discreti
    hp_learning_rate = hp.Choice('learning_rate', values=[1e-2, 1e-3, 1e-4])
    
    model.compile(optimizer=keras.optimizers.Adam(learning_rate=hp_learning_rate),
                  loss='sparse_categorical_crossentropy',
                  metrics=['accuracy'])
    return model
```

### Strategie di Ricerca (Tuners)
Keras Tuner offre diversi algoritmi per esplorare lo spazio delle combinazioni:

#### 1. Random Search
Sceglie combinazioni di iperparametri in modo completamente casuale.
*   **Pro:** È sorprendentemente efficace. Spesso i parametri importanti sono pochi, e il campionamento casuale ha buone probabilità di trovarli.
*   **Contro:** Può sprecare tempo su combinazioni palesemente scarse.

#### 2. Bayesian Optimization
Usa la statistica Bayesiana per costruire un modello probabilistico della funzione obiettivo.
*   **Come funziona:** Guarda i risultati degli esperimenti passati per decidere quale combinazione provare dopo. Cerca di bilanciare *esplorazione* (provare zone ignote) e *sfruttamento* (affinare zone promettenti).
*   **Pro:** Molto efficiente in termini di numero di prove.

#### 3. Hyperband (Consigliato)
È una variante ottimizzata della Random Search basata sul principio dell'**Early Stopping**.
*   **Il Problema:** Addestrare un modello fino alla fine (es. 100 epoche) per scoprire che fa schifo è uno spreco di risorse.
*   **La Soluzione Hyperband:**
    1.  Genera molte configurazioni casuali.
    2.  Addestra tutte per poche epoche (es. 2).
    3.  Scarta la metà peggiore.
    4.  Addestra le sopravvissute per altre epoche.
    5.  Ripeti finché non ne resta una.
*   **Pro:** Permette di testare centinaia di configurazioni velocemente, concentrando le risorse computazionali solo su quelle che promettono bene.

```python
tuner = kt.Hyperband(model_builder,
                     objective='val_accuracy',
                     max_epochs=10,
                     factor=3,
                     directory='my_dir',
                     project_name='intro_to_kt')

# Avvia la ricerca (simile a model.fit)
tuner.search(img_train, label_train, epochs=50, validation_split=0.2)

# Recupera i migliori iperparametri
best_hps = tuner.get_best_hyperparameters(num_trials=1)[0]
print(f"Miglior numero di unità: {best_hps.get('units')}")
```
