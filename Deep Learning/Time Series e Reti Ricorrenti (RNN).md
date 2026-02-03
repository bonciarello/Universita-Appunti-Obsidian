Le reti neurali viste finora (Dense, ConvNets) sono **Feedforward**: non hanno memoria. Elaborano ogni input indipendentemente dagli altri. Se mostriamo loro una scena di un film, non capiscono cosa è successo nella scena precedente.
Le **Reti Neurali Ricorrenti (RNN)** affrontano questo limite: sono reti con un "loop", progettate per elaborare sequenze (testo, audio, serie temporali finanziarie, video).

## 1. Funzionamento di una SimpleRNN
Una RNN elabora la sequenza iterando sugli elementi e mantenendo uno **stato nascosto** (hidden state) che contiene informazioni su ciò che ha visto finora.
Matematicamente, al tempo $t$:
$$ h_t = \tanh(W \cdot x_t + U \cdot h_{t-1} + b) $$
$$ y_t = \sigma(V \cdot h_t + c) $$
*   $x_t$: Input al tempo corrente.
*   $h_{t-1}$: Stato al tempo precedente (memoria).
*   $h_t$: Nuovo stato aggiornato.
*   $W, U$: Matrici di pesi condivise per tutti i passi temporali.

In pratica, è come un ciclo `for` che riutilizza gli stessi pesi ad ogni iterazione.

## 2. Il Problema del Gradiente Scomparendo (Vanishing Gradient)
Le SimpleRNN teoricamente possono ricordare informazioni viste molto tempo fa. In pratica, però, falliscono su sequenze lunghe. Perché?

Durante l'addestramento (Backpropagation Through Time), il gradiente dell'errore deve retro-propagarsi dal tempo $T$ fino al tempo $0$.
Applicando la regola della catena, moltiplichiamo la matrice dei pesi $W$ per se stessa $T$ volte.
*   Se i pesi sono piccoli (< 1): Il gradiente decade esponenzialmente a zero (**Vanishing**). La rete smette di imparare le dipendenze a lungo termine (es. il soggetto di una frase all'inizio del paragrafo).
*   Se i pesi sono grandi (> 1): Il gradiente esplode (**Exploding**).

## 3. LSTM (Long Short-Term Memory)
Introdotte da Hochreiter e Schmidhuber (1997), le LSTM risolvono il problema del Vanishing Gradient introducendo una via preferenziale per il flusso delle informazioni: il **Cell State** ($C_t$), che scorre attraverso la rete con interazioni lineari minime (come un nastro trasportatore).

Una cella LSTM usa tre **Porte (Gates)** per regolare il flusso di informazioni (cosa tenere e cosa buttare):

1.  **Forget Gate ($f_t$):** Decide cosa dimenticare dello stato precedente.
    *   Guarda input corrente e stato precedente, produce un numero tra 0 (dimentica tutto) e 1 (tieni tutto).
    *   *Esempio:* Se incontriamo un nuovo soggetto nella frase, dimentichiamo il genere del soggetto precedente.
2.  **Input Gate ($i_t$):** Decide quali nuove informazioni memorizzare nel Cell State.
    *   Combina una sigmoide (scelta) e una tanh (candidato valore).
3.  **Output Gate ($o_t$):** Decide cosa emettere come stato nascosto ($h_t$) basandosi sul Cell State filtrato.

Grazie a questa struttura, il gradiente può fluire inalterato per molti passi temporali, permettendo alla rete di ricordare eventi accaduti molto prima.

```python
from keras.layers import LSTM, Embedding, Dense
from keras.models import Sequential

model = Sequential()
model.add(Embedding(input_dim=10000, output_dim=32))
# LSTM gestisce internamente la complessità dei gate
model.add(LSTM(32)) 
model.add(Dense(1, activation='sigmoid'))
```

## 4. GRU (Gated Recurrent Unit)
Le GRU (Cho et al., 2014) sono una variante semplificata delle LSTM.
*   Fondono il Forget Gate e l'Input Gate in un unico **Update Gate**.
*   Fondono il Cell State e l'Hidden State.
Risultato: meno parametri da addestrare, prestazioni spesso paragonabili alle LSTM, ma più veloci. Sono ottime per dataset più piccoli.

## 5. Stacking di Livelli Ricorrenti
Per aumentare la capacità rappresentativa della rete, possiamo impilare più livelli RNN (Deep RNN).
Affinché funzioni, i livelli intermedi devono restituire l'intera sequenza di output ($h_0, h_1, ..., h_t$) invece che solo l'ultimo stato, in modo che il livello successivo abbia una sequenza su cui lavorare.

```python
model = Sequential()
model.add(Embedding(10000, 32))
# return_sequences=True passa l'intera storia al livello successivo
model.add(LSTM(32, return_sequences=True)) 
model.add(LSTM(32, return_sequences=True))
# L'ultimo livello restituisce solo il vettore finale per la classificazione
model.add(LSTM(32)) 
model.add(Dense(1, activation='sigmoid'))
```
