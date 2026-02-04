## 1. Introduzione
Un **cifrario a flusso (Stream Cipher)** utilizza una breve chiave segreta e un flusso di bit generato in modo pseudocasuale, computazionalmente indistinguibile da un flusso di cifre casuali e sono utili quando è necessario crittografare grandi quantità di dati in streaming veloce.

In un cifrario a flusso tradizionale, ci sono tre elementi interni:

*   Una **funzione di transizione di stato** $f$, ad ogni tempo di generazione di bit, calcola un nuovo valore di stato dal vecchio valore di stato;
*   Una **funzione di output** $g$ produce il flusso di bit utilizzato per la crittografia e la decrittografia;
*   Una **chiave segreta** $K$ fornisce l'input al cifrario a flusso e viene utilizzata per inizializzare lo stato.

Esiste anche uno stato segreto $s$ (cioè, la memoria) che si evolve nel tempo durante la cifratura e la decifratura; lo stato iniziale è designato come $s_0$. Il cifrario a flusso è simile al one-time pad, la differenza è che un one-time pad utilizza un vero e proprio flusso di numeri casuali, mentre un cifrario a flusso utilizza un flusso di numeri pseudocasuali.

## 2. Considerazioni di Progettazione
Importanti considerazioni di progettazione per un cifrario a flusso:

1.  **Periodo lungo:** la sequenza di crittografia dovrebbe avere un periodo lungo: un PRNG utilizza una funzione che produce un flusso deterministico di bit che alla fine si ripete. Più lungo è il periodo di ripetizione, più difficile sarà eseguire la crittoanalisi;
2.  **Casualità:** il keystream dovrebbe approssimare il più possibile le proprietà di un vero flusso di numeri casuali: più il flusso di chiavi appare casuale, più il testo cifrato è randomizzato, rendendo più difficile la crittoanalisi;
3.  **Lunghezza della chiave:** l'output del PRNG è condizionato dal valore della chiave di input: per proteggersi dagli attacchi bruteforce, la chiave deve essere sufficientemente lunga. Pertanto, con la tecnologia attuale, è auspicabile una lunghezza della chiave di almeno 128 bit.

Con un PRNG correttamente progettato, un cifrario a flusso può essere sicuro quanto un cifrario a blocchi di lunghezza di chiave comparabile. Un potenziale vantaggio di un cifrario a flusso è che sono in genere più veloci e utilizzano molto meno codice rispetto ai cifrari a blocchi.

## 3. RC4
**RC4 (Rivest Cipher 4)** è un algoritmo di cifratura a flusso ampiamente utilizzato. Una delle caratteristiche distintive di RC4 è il suo algoritmo di generazione del keystream basato su una permutazione casuale dei byte in un vettore di stato. Questo algoritmo consente una generazione efficiente del keystream e lo rende adatto per applicazioni con restrizioni di risorse. Tuttavia, nel corso degli anni sono state scoperte diverse debolezze in RC4 che ne hanno compromesso la sicurezza come, ad esempio, correlazioni statistiche tra i byte del keystream.

> **Commentato [CP2]:** Un algoritmo di generazione del keystream è un metodo usato in crittografia per produrre una sequenza di bit (keystream) che viene combinata con il testo in chiaro per creare il testo cifrato. Questo keystream deve essere imprevedibile e simile al rumore casuale per garantire la sicurezza del messaggio cifrato.
