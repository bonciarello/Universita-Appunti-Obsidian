---
aliases: [Classe]
tags: [deep-learning]
---
Fino ad ora abbiamo utilizzato quasi esclusivamente il modello `Sequential` di Keras. Sebbene sia semplice e potente, il modello sequenziale fa un'assunzione forte: che la rete sia una pila lineare di strati, dove ogni strato ha esattamente **un tensore di input** e **un tensore di output**.

Tuttavia, molte architetture avanzate di [[Deep Learning]] non rispettano questa topologia lineare. In questo capitolo esploreremo due modi più flessibili per costruire modelli in Keras: l'**API Funzionale** e il **Model Subclassing**.

## 1. I Limiti del Modello Sequenziale
Il modello `Sequential` non è adatto quando:
*   Il modello ha **input multipli** (es. un modello che prende un'immagine e i suoi metadati testuali).
*   Il modello ha **output multipli** (es. un modello che classifica il genere di una persona e ne stima l'età contemporaneamente).
*   Il grafo dei livelli contiene **cicli** o **connessioni non lineari** (es. le connessioni residue nelle ResNet, dove l'output di un layer viene sommato all'input di un layer successivo, saltando quelli intermedi).

## 2. API Funzionale
L'API Funzionale è il modo "standard" per costruire modelli complessi in Keras.
Si chiama "funzionale" perché trattiamo i livelli come **funzioni**: prendono un tensore come argomento e restituiscono un tensore come risultato.

### Esempio: Modello Multi-Input (Question Answering)
Immaginiamo di voler costruire un sistema che risponde a domande basandosi su un testo di contesto.
*   **Input 1:** La domanda (sequenza di parole).
*   **Input 2:** Il testo di contesto (sequenza di parole).
*   **Output:** La risposta (una parola dal vocabolario).

```python
from keras import layers, models, Input

# Input 1: La domanda (sequenza di lunghezza variabile)
question_input = Input(shape=(None,), dtype='int32', name='question')
embedded_question = layers.Embedding(10000, 64)(question_input)
encoded_question = layers.LSTM(32)(embedded_question)

# Input 2: Il contesto (sequenza di lunghezza variabile)
context_input = Input(shape=(None,), dtype='int32', name='context')
embedded_context = layers.Embedding(10000, 64)(context_input)
encoded_context = layers.LSTM(32)(embedded_context)

# Concateniamo le due rappresentazioni vettoriali
concatenated = layers.concatenate([encoded_question, encoded_context], axis=-1)

# Aggiungiamo un classificatore sopra la concatenazione
answer = layers.Dense(10000, activation='softmax')(concatenated)

# Istanziamo il modello specificando input e output
model = models.Model(inputs=[question_input, context_input], outputs=answer)

model.summary()
```

### Esempio: Modello Multi-Output
Immaginiamo di analizzare un post sui social media per prevedere:
1.  L'età dell'autore ([[Regressione]]).
2.  Il reddito dell'autore ([[Regressione]]).
3.  Il genere dell'autore (Classificazione).

```python
# ... (parte comune di feature extraction) ...
base_features = layers.Dense(128, activation='relu')(some_input)

# Tre teste di uscita separate che partono dalle stesse feature
age_output = layers.Dense(1, name='age')(base_features)
income_output = layers.Dense(1, name='income')(base_features)
gender_output = layers.Dense(1, activation='sigmoid', name='gender')(base_features)

model = models.Model(inputs=some_input, 
                     outputs=[age_output, income_output, gender_output])

# Compilazione con loss diverse per ogni output
model.compile(optimizer='rmsprop',
              loss={'age': 'mse', 'income': 'mse', 'gender': 'binary_crossentropy'},
              loss_weights={'age': 0.25, 'income': 1., 'gender': 10.})
```

## 3. Model Subclassing
L'approccio **Subclassing** (sottoclasse) è quello che offre la massima flessibilità ed è molto simile a come si lavora in **PyTorch**.
Invece di definire staticamente il grafo, si scrive una classe Python che eredita da `tf.keras.Model`.

*   Nel metodo `__init__`, definiamo i layer che useremo.
*   Nel metodo `call`, definiamo la logica del passaggio in avanti (forward pass).

Questo permette di usare costrutti Python imperativi come `if`, `for`, `while` all'interno del grafo, cosa impossibile con l'API Funzionale (che costruisce un grafo statico).

```python
import tensorflow as tf

class MyDynamicModel(tf.keras.Model):
    def __init__(self, units=32, **kwargs):
        super(MyDynamicModel, self).__init__(**kwargs)
        self.dense1 = layers.Dense(units, activation='relu')
        self.dense2 = layers.Dense(10, activation='softmax')
        self.dropout = layers.Dropout(0.5)

    def call(self, inputs, training=False):
        # Possiamo usare argomenti booleani come 'training'
        x = self.dense1(inputs)
        if training:
            x = self.dropout(x)
        return self.dense2(x)

model = MyDynamicModel()
```
**Svantaggio:** Poiché la struttura è nascosta nel codice Python, Keras non può ispezionarla facilmente (es. `model.summary()` è meno dettagliato, non si può salvare/caricare facilmente il modello come grafo statico). È consigliato solo se serve davvero quella flessibilità extra (es. ricerca avanzata, loop dinamici).

---

## Strumenti Avanzati di Personalizzazione

### Custom Loss (Perdita Personalizzata)
A volte le loss standard (MSE, Crossentropy) non bastano.
Esempio: Vogliamo penalizzare molto di più le predizioni che sovrastimano il valore rispetto a quelle che lo sottostimano.

```python
def custom_asymmetric_loss(y_true, y_pred):
    error = y_true - y_pred
    # Se sovrastima (errore < 0), penalizza x10
    sq_error = tf.square(error)
    return tf.where(error < 0, sq_error * 10, sq_error)

model.compile(optimizer='adam', loss=custom_asymmetric_loss)
```

### Custom Layers (Livelli Personalizzati)
Se dobbiamo implementare un'operazione matematica nuova.
Un Layer ha uno stato (i pesi `self.weights`) e una computazione (`call`).

```python
class MyDense(layers.Layer):
    def __init__(self, units=32):
        super(MyDense, self).__init__()
        self.units = units

    def build(self, input_shape):
        # build() viene chiamato la prima volta che il layer vede un input
        # Qui creiamo i pesi sapendo la dimensione dell'input
        self.w = self.add_weight(shape=(input_shape[-1], self.units),
                                 initializer='random_normal', trainable=True)
        self.b = self.add_weight(shape=(self.units,),
                                 initializer='zeros', trainable=True)

    def call(self, inputs):
        # Logica: y = x * w + b
        return tf.matmul(inputs, self.w) + self.b
```
