---
aliases: [Generazione Casuale Bit, GCB]
tags: [network-security]
---
## 1. Strategie di Generazione
Ci sono due strategie fondamentalmente diverse per generare bit casuali o numeri casuali:

*   Una strategia, che fino a poco tempo fa dominava nelle applicazioni crittografiche, calcola i bit in modo deterministico utilizzando un algoritmo. Questa classe di generatori di bit casuali è nota come **generatori di numeri pseudocasuali (PRNG)**;
*   L’altra strategia consiste nel produrre bit in modo non deterministico utilizzando una sorgente fisica che produce una sorta di output casuale. Quest’ultima classe di generatori di bit casuali è nota come **generatori di numeri casuali reali (TRNG)**.

## 2. Requisiti per la [[Sicurezza]]
Numerosi algoritmi e protocolli di [[Sicurezza|sicurezza]] di rete basati sulla crittografia utilizzano numeri binari casuali. Queste applicazioni danno luogo a due requisiti distinti e non necessariamente compatibili per una sequenza di numeri casuali:

1.  **Casualità:** la preoccupazione è che la sequenza di numeri fosse casuale in un senso statistico ben definito. Due criteri vengono utilizzati per convalidare che una sequenza di numeri è casuale:
    *   **Distribuzione uniforme:** la distribuzione dei bit nella sequenza deve essere uniforme, ovvero, la frequenza di occorrenza di uno e zero dovrebbe essere approssimativamente uguale;
    *   **Indipendenza:** nessuna sotto sequenza nella sequenza può essere dedotta dalle altre;
2.  **Imprevedibilità:** il requisito non è solo che la sequenza di numeri sia statisticamente casuale, ma che i membri successivi della sequenza siano imprevedibili.

## 3. Tecniche di Generazione

### Tecniche Algoritmiche (PRNG)
Le applicazioni crittografiche in genere fanno uso di tecniche algoritmiche per la generazione di numeri casuali. Questi algoritmi sono deterministici e quindi producono sequenze di numeri che non sono statisticamente casuali. Tuttavia, se l’algoritmo è buono, le sequenze risultanti supereranno molti test di casualità. Tali numeri sono indicati come numeri pseudocasuali ma verranno considerati come casuali.

*   Un **PRNG** prende come input un valore fisso, chiamato **seme (seed)**, e produce una sequenza di bit di output utilizzando un algoritmo deterministico. Molto spesso, il seme è generato da un TRNG. La cosa importante da notare è che il flusso di bit di output è determinato esclusivamente dal valore o dai valori di input, in modo che un avversario che conosca l’algoritmo e il seme non possa riprodurre l’intero flusso di bit.

Due approcci che utilizzano un cifrario a blocchi per costruire un PNRG hanno ottenuto un’ampia accettazione: la modalità **CTR** e la modalità **OFB**. In ogni caso, il seme è composto da due parti: il valore della chiave di crittografia e un valore $V$ che verrà aggiornato dopo la generazione di ogni blocco di numeri pseudocasuali.
*   Nel caso CTR, il valore di $V$ viene incrementato di 1 dopo ogni crittografia.
*   Nel caso di OFB, il valore di $V$ viene aggiornato per eguagliare il valore del precedente blocco PRNG.
In entrambi i casi, i bit pseudocasuali vengono prodotti un blocco alla volta.

### Generatori Reali (TRNG)
Un **TRNG** prende come input una fonte che è effettivamente casuale e la sorgente è spesso indicata come **fonte di [[Entropia|entropia]]**: quest’ultima è ricavata dall’ambiente fisico del computer e potrebbe includere cose come schemi temporali di battitura, attività elettrica del disco, movimenti del mouse e valori istantanei dell’orologio di sistema.

### PRF (Pseudo-Random Function)
Una **PRF** viene utilizzata per produrre una stringa pseudocasuale di bit di lunghezza fissa. A parte il numero di bit prodotti, non c’è differenza tra un PRNG e un PRF: gli stessi algoritmi possono essere utilizzati in entrambe le applicazioni, entrambi richiedono un seme ed entrambi devono esibire casualità e imprevedibilità.

### Linear Congruential Generator
La tecnica di gran lunga più utilizzata per la generazione di numeri pseudocasuali è il **Linear Congruential Generator**: utilizza valori successivi da un’equazione iterativa. Dati valori adeguati dei parametri, possono produrre una lunga sequenza simile a quella casuale, ma ci sono solo un piccolo numero di tali buone scelte. Si noti che la sequenza, pur sembrando casuale, è altamente prevedibile.
