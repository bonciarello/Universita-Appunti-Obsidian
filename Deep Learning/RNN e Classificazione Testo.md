---
aliases: [Classe]
tags: [deep-learning]
---
Le [[Reti]] Neurali Ricorrenti (RNN) sono l'[[Architettura|architettura]] d'elezione per elaborare dati sequenziali come il testo. A differenza delle immagini (dove i pixel vicini sono correlati spazialmente), nel testo le parole hanno significato solo nel contesto della frase che le precede (e le segue).

## Tipologie di Architetture RNN
A seconda del problema, le RNN possono essere configurate in modi diversi:

1.  **One-to-One:** Una rete feedforward classica. Input fisso $\to$ Output fisso.
2.  **One-to-Many:** Input fisso (es. immagine) $\to$ Sequenza di output (es. generare una didascalia "Un cane che corre").
3.  **Many-to-One:** Sequenza di input (es. recensione di un film) $\to$ Output fisso (es. Sentimento Positivo/Negativo). È il caso della **Classificazione del Testo**.
4.  **Many-to-Many:** Sequenza di input $\to$ Sequenza di output.
    *   *Sincrona:* Etichettatura video frame per frame.
    *   *Asincrona (Seq2Seq):* Traduzione automatica (leggo tutta la frase in inglese, poi genero quella in italiano).

## Preprocessing del Testo
Le [[Reti|reti]] neurali non capiscono le stringhe. Dobbiamo convertire il testo in numeri.

### 1. Tokenizzazione e Indicizzazione
Dividiamo il testo in parole (token) e assegniamo a ogni parola univoca un numero intero (indice).
*   "Il gatto mangia" $\to$ [1, 2, 3]
*   "Il cane mangia" $\to$ [1, 4, 3]

### 2. Padding
Le [[Reti|reti]] neurali richiedono input di dimensione fissa (tensori rettangolari). Ma le frasi hanno lunghezze diverse.
Usiamo il **Padding**: aggiungiamo zeri alla fine (o all'inizio) delle frasi più corte per renderle tutte lunghe quanto la frase più lunga (o una lunghezza massima prefissata `maxlen`).

```python
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

sentences = [
    'I love machine learning',
    'I hate bugs in code so much'
]

# Creiamo il dizionario delle parole (max 1000 parole)
tokenizer = Tokenizer(num_words=1000, oov_token="<OOV>")
tokenizer.fit_on_texts(sentences)

# Convertiamo in numeri
sequences = tokenizer.texts_to_sequences(sentences)
# [[2, 3, 4, 5], [2, 6, 7, 8, 9, 10, 11]]

# Padding
padded = pad_sequences(sequences, maxlen=10, padding='post', truncating='post')
# [[2, 3, 4, 5, 0, 0, 0, 0, 0, 0], 
#  [2, 6, 7, 8, 9, 10, 11, 0, 0, 0]]
```

### 3. Word Embeddings
Rappresentare le parole come semplici interi (1, 2, 3...) non va bene perché implica un ordine che non esiste (la parola 2 non è "doppia" della parola 1).
Usare **One-Hot Encoding** (vettori di zeri con un solo 1) è inefficiente e sparso.

La soluzione è l'**Embedding**: mappiamo ogni parola in un vettore denso di numeri reali (es. lunghezza 16 o 64).
Questi vettori vengono *appresi* dalla rete. Parole semanticamente simili (es. "re" e "regina") finiranno per avere vettori vicini nello spazio geometrico.

## Costruzione del Modello RNN (Many-to-One)

```python
import tensorflow as tf

vocab_size = 1000
embedding_dim = 16
max_length = 10

model = tf.keras.Sequential([
    # Layer 1: Embedding. Trasforma interi in vettori densi.
    # Input: (batch, 10) -> Output: (batch, 10, 16)
    tf.keras.layers.Embedding(input_dim=vocab_size, output_dim=embedding_dim, input_length=max_length),
    
    # Layer 2: LSTM. Elabora la sequenza.
    # Di default restituisce solo l'ultimo stato (Many-to-One).
    # Input: (batch, 10, 16) -> Output: (batch, 32)
    tf.keras.layers.LSTM(32),
    
    # Layer 3: Classificatore denso.
    tf.keras.layers.Dense(6, activation='relu'),
    tf.keras.layers.Dense(1, activation='sigmoid') # Binario: Positivo/Negativo
])

model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])
```
