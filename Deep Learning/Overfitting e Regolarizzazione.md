La questione fondamentale nell'apprendimento automatico è la tensione tra **ottimizzazione** e **generalizzazione**.
*   L'**ottimizzazione** è il processo di adattamento di un modello per ottenere le migliori prestazioni possibili sui dati di addestramento (il learning vero e proprio).
*   La **generalizzazione** si riferisce alla capacità del modello addestrato di performare bene su dati mai visti prima.

L'obiettivo finale è ottenere una buona generalizzazione, ma non possiamo controllarla direttamente; possiamo solo modificare il modello basandoci sulle sue prestazioni sui dati di addestramento.
All'inizio dell'addestramento, ottimizzazione e generalizzazione sono correlate: più il modello apprende, meglio generalizza. Tuttavia, dopo un certo numero di epoche, si verifica un punto di rottura. Il modello inizia a **overfittare** (sovradattarsi): impara pattern specifici del training set che sono fuorvianti o irrilevanti per i nuovi dati (rumore).

Per impedire a un modello di apprendere pattern fuorvianti, la soluzione migliore è raccogliere più dati di addestramento. Quando ciò non è possibile, dobbiamo ricorrere alla **regolarizzazione**.

## 1. Ridurre la Capacità della Rete
Il modo più semplice per prevenire l'overfitting è ridurre la dimensione del modello, ovvero il numero di parametri apprendibili (numero di strati e numero di unità per strato).
In deep learning, il numero di parametri è spesso chiamato "capacità" del modello. Intuitivamente:
*   Un modello con **troppi parametri** ha una "capacità di memoria" elevata: può imparare facilmente una mappatura perfetta tipo dizionario tra input e output, senza alcuna capacità di generalizzazione.
*   Un modello con **pochi parametri** sarà costretto a imparare rappresentazioni compresse che catturano gli aspetti più significativi dei dati.

Tuttavia, bisogna fare attenzione: se il modello è troppo piccolo, andrà in **underfitting** e non riuscirà ad apprendere nemmeno i pattern essenziali. Non esiste una formula magica per determinare la dimensione giusta; bisogna valutare diverse architetture (sul validation set).

Ecco come ridurre la dimensione di una rete in Keras:

```python
from keras import models
from keras import layers

# Modello originale (riferimento)
original_model = models.Sequential()
original_model.add(layers.Dense(16, activation='relu', input_shape=(10000,)))
original_model.add(layers.Dense(16, activation='relu'))
original_model.add(layers.Dense(1, activation='sigmoid'))

# Modello più piccolo (minore capacità)
smaller_model = models.Sequential()
smaller_model.add(layers.Dense(4, activation='relu', input_shape=(10000,)))
smaller_model.add(layers.Dense(4, activation='relu'))
smaller_model.add(layers.Dense(1, activation='sigmoid'))
```
Confrontando le curve di perdita (loss) di validazione, vedremmo che il modello più piccolo inizia a overfittare più tardi rispetto a quello di riferimento e le sue prestazioni degradano più lentamente una volta iniziato l'overfitting.

## 2. Regolarizzazione dei Pesi (Weight Regularization)
Il principio del "Rasoio di Occam" suggerisce che, tra due spiegazioni per un fenomeno, la più semplice è solitamente quella corretta. Questo si applica anche ai modelli neurali: un modello semplice è meno propenso all'overfitting di uno complesso.

Un "modello semplice" in questo contesto è un modello in cui la distribuzione dei valori dei pesi ha un'entropia bassa (o che ha meno parametri).
La **regolarizzazione dei pesi** consiste nel forzare i pesi della rete ad assumere solo valori piccoli, aggiungendo alla funzione di perdita un costo associato alla grandezza dei pesi stessi.

Esistono due tipi principali:
*   **Regolarizzazione L1 (Lasso):** Il costo aggiunto è proporzionale al *valore assoluto* dei coefficienti dei pesi ($Cost = \lambda \cdot |w|$). Tende a spingere alcuni pesi esattamente a zero, rendendo il modello sparso.
*   **Regolarizzazione L2 (Ridge):** Il costo aggiunto è proporzionale al *quadrato* dei coefficienti dei pesi ($Cost = \lambda \cdot w^2$). Tende a penalizzare pesantemente i picchi, distribuendo il peso su più connessioni e rendendoli piccoli ma non nulli.

In Keras, la regolarizzazione viene aggiunta passando un'istanza di regolarizzatore ai layer:

```python
from keras import regularizers

l2_model = models.Sequential()
# Aggiungiamo regolarizzazione L2 con fattore 0.001
l2_model.add(layers.Dense(16, kernel_regularizer=regularizers.l2(0.001),
                          activation='relu', input_shape=(10000,)))
l2_model.add(layers.Dense(16, kernel_regularizer=regularizers.l2(0.001),
                          activation='relu'))
l2_model.add(layers.Dense(1, activation='sigmoid'))
```
`l2(0.001)` significa che ogni coefficiente nella matrice dei pesi del livello aggiungerà `0.001 * weight_coefficient_value` alla perdita totale della rete.

## 3. Dropout
Il **Dropout**, sviluppato da Hinton e il suo team, è una delle tecniche di regolarizzazione più efficaci e comunemente usate.
L'idea è semplice ma potente: durante l'addestramento, si azzerano casualmente (si "droppano") un certo numero di output del livello (spesso il 50%).

*   **Durante il training:** Una frazione p (es. 0.5) delle unità viene spenta.
*   **Durante il test:** Nessuna unità viene spenta, ma i valori di output vengono ridotti di un fattore p per bilanciare il fatto che ora sono attive più unità rispetto al training.

L'intuizione è che l'introduzione di rumore negli output di uno strato rompe le "cospirazioni" accidentali tra neuroni (pattern di attivazione che non sono significativi ma casuali). Costringe ogni neurone a imparare caratteristiche utili in modo indipendente dagli altri.

```python
dpt_model = models.Sequential()
dpt_model.add(layers.Dense(16, activation='relu', input_shape=(10000,)))
# Aggiungiamo Dropout al 50%
dpt_model.add(layers.Dropout(0.5))
dpt_model.add(layers.Dense(16, activation='relu'))
dpt_model.add(layers.Dropout(0.5))
dpt_model.add(layers.Dense(1, activation='sigmoid'))
```
