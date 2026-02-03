## Definizione

Nella definizione più generale, le **Reti Neurali (NN)**, spesso chiamate semplicemente reti neurali artificiali, sono modelli matematici e computazionali ispirati al funzionamento dei neuroni nel cervello umano.

Nella definizione specifica, le NN sono un insieme di unità interconnesse, chiamate **neuroni artificiali** o nodi, organizzate in strati o livelli.
*   Questi neuroni elaborano informazioni attraverso operazioni matematiche su dati di input.
*   Trasmettono l'output ai neuroni successivi attraverso **pesi sinaptici**.
*   Gli strati intermedi, noti come **strati nascosti**, consentono alla rete di apprendere rappresentazioni complesse.

Le reti neurali apprendono dai dati mediante l'addestramento, regolando i pesi delle connessioni sinaptiche per minimizzare l'errore tra l'output previsto e quello desiderato.

## Modelli Storici

### Modello neurale di McCulloch & Pitt (1943)
È una rappresentazione semplificata di un neurone biologico che descrive un neurone artificiale composto da:
1.  **Input:** Segnali in ingresso (attivazioni 1 o 0).
2.  **Pesi sinaptici:** Importanza del collegamento.
3.  **Funzione di somma:** Somma ponderata dei segnali di input.
4.  **Funzione di attivazione:** Determina se il neurone deve attivarsi (tutto-o-nulla).

### Perceptron di Frank Rosenblatt
Un algoritmo di apprendimento automatico basato sulla struttura del neurone. L'obiettivo è classificare gli input in due categorie (Classe A e Classe B). L'addestramento avviene aggiornando i pesi in base agli errori commessi.

## Componenti Fondamentali

### Funzioni di Attivazione
Introducono la non linearità nella rete, permettendo di apprendere modelli complessi.
*   **Sigmoid:** Curva a "S", mappa l'input tra 0 e 1. Usata per classificazione binaria.
*   **ReLU (Rectified Linear Unit):** $f(x) = \max(0, x)$. Restituisce l'input se positivo, 0 altrimenti. Molto usata nel Deep Learning per la sua semplicità e gestione del gradiente.
*   **Tanh (Tangente iperbolica):** Mappa l'input tra -1 e 1.

### Struttura della Rete (Feedforward)
Nelle reti **Feedforward**, l'informazione fluisce in una sola direzione (input $\to$ hidden $\to$ output) senza cicli.
Ogni neurone in uno strato $k$ è connesso ai neuroni dello strato successivo.

### Funzione di Perdita (Loss Function)
Misura l'errore tra l'output prodotto dalla rete e quello desiderato. L'obiettivo dell'addestramento è minimizzare questa funzione.

**Per la Regressione:**
1.  **MAE (Mean Absolute Error):** Media degli errori assoluti.
2.  **MSE (Mean Squared Error):** Media degli errori al quadrato (penalizza i grandi errori, differenziabile).
3.  **SAE (Smooth Absolute Error):** Ibrido tra MAE e MSE.

**Per la Classificazione:**
1.  **Binary Cross Entropy (BCE):** Per output binari (0 o 1).
2.  **Categorical Cross Entropy (CCE):** Per $K$ classi.

### Ottimizzatore: Gradient Descent
Per minimizzare la funzione di perdita, si utilizza il **Gradient Descent**.
L'algoritmo aggiorna i pesi muovendosi nella direzione opposta al gradiente della funzione di perdita.
La formula di aggiornamento è:
$$ W \leftarrow W - \eta \cdot \nabla Loss $$
dove $\eta$ è il **tasso di apprendimento (learning rate)**.

### Inizializzazione dei Pesi
L'inizializzazione è cruciale. Se i pesi partono tutti da zero, i neuroni apprenderanno le stesse caratteristiche (simmetria).
*   **Inizializzazione Semplice (Casuale):** Rompe la simmetria, ma può causare la scomparsa/esplosione del gradiente.
*   **Inizializzazione di He:** Ottimizzata per reti con attivazione ReLU. Imposta la varianza per mantenere il flusso del segnale stabile.
*   **Inizializzazione di Xavier (Glorot):** Ottimizzata per Sigmoid/Tanh, bilancia la varianza di input e output.