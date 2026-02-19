---
aliases: [Seq2Seq, Sequence-to-Sequence]
tags: [deep-learning]
---
Il modello **Seq2Seq** (o Encoder-Decoder) è l'[[Architettura|architettura]] che ha reso possibile la moderna traduzione automatica (Google Translate pre-2016), i chatbot e il riassunto automatico di testi.
L'obiettivo è mappare una sequenza di input $X$ (di lunghezza $N$) in una sequenza di output $Y$ (di lunghezza $M$), dove $N$ può essere diverso da $M$.

## [[Architettura]] Base

### 1. Encoder
È una RNN (spesso LSTM o GRU bi-direzionale) che legge la sequenza di input un token alla volta.
Il suo compito è comprimere tutto il significato della frase di input in un singolo vettore finale, chiamato **Vettore di Contesto** (o *Thought Vector*).
$$ h_t = RNN_{enc}(x_t, h_{t-1}) $$
Il vettore di contesto è semplicemente l'ultimo stato nascosto $h_N$.

### 2. Decoder
È un'altra RNN che genera la sequenza di output.
*   Viene inizializzata con il vettore di contesto dell'encoder (quindi "parte" avendo in mente il significato della frase input).
*   A ogni passo, predice la parola successiva e aggiorna il suo stato interno.
$$ s_t = RNN_{dec}(y_{t-1}, s_{t-1}) $$
$$ y_t = Softmax(Dense(s_t)) $$

## Il "Collo di Bottiglia" e l'Attenzione
Il problema dell'[[Architettura|architettura]] base è che il Vettore di Contesto deve contenere *tutte* le informazioni della frase. Se la frase è molto lunga (es. 50 parole), è impossibile comprimerla in un vettore di dimensione fissa (es. 256 numeri) senza perdere dettagli. Questo è il collo di bottiglia.

Il **Meccanismo di Attenzione (Attention Mechanism)** (Bahdanau et al., 2014) risolve questo problema.
Invece di passare al decoder solo l'ultimo stato dell'encoder, gli permettiamo di accedere a **tutti gli stati nascosti** dell'encoder ($h_1, h_2, ..., h_N$).

A ogni passo di decodifica $t$, il decoder:
1.  Confronta il suo stato corrente con tutti gli stati dell'encoder.
2.  Calcola dei **pesi di attenzione** ($\alpha$): "Quanto è importante la parola input $i$ per generare la parola output $t$?".
3.  Calcola una media ponderata degli stati dell'encoder (il Vettore di Contesto Dinamico).
4.  Usa questo vettore per generare la parola.

In pratica, il modello impara ad "allineare" parole input e output (es. quando genera "gatto", presta molta attenzione a "cat").

## Altre Ottimizzazioni

### Beam Search
Durante la generazione (inferenza), se scegliamo sempre la parola più probabile (Greedy Search), potremmo finire in un vicolo cieco.
La **Beam Search** esplora $K$ ipotesi parallele. Mantiene le $K$ sequenze parziali più probabili a ogni passo e alla fine sceglie la migliore.

### Teacher Forcing
Durante l'addestramento, il decoder ha bisogno dell'output al passo precedente ($y_{t-1}$) per generare il passo successivo ($y_t$).
Se usiamo la parola predetta dal modello stesso (che all'inizio è sbagliata), l'errore si propaga e il training è lento.
Con il **Teacher Forcing**, passiamo al decoder la parola *reale* (ground truth) al passo $t-1$, indipendentemente da cosa il modello ha predetto. Questo stabilizza l'addestramento.
