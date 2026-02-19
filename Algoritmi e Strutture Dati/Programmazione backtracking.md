---
aliases: [PB]
tags: [algoritmi-e-strutture-dati]
---
La tecnica di backtracking estende la ricerca esaustiva nella risoluzione di problemi di ricerca attraverso l'introduzione di alcuni controlli per verificare il più presto possibile se una soluzione in via di costruzione soddisfi o meno le condizioni di ammissibilità in modo da ridurre lo spazio di ricerca.

Il problema tipico di ricerca risolto con il backtracking consiste nell'assegnare un valore, preso da un dominio V, a ciascuno degli elementi di un vettore soluzione X in modo tale che siano rispettati alcuni vincoli.

La differenza sostanziale con la **brute force** è che, a differenza di quest'ultimo, le soluzioni parziali cercano immediatamente di soddisfare i vincoli del problema e *non sempre* vengono generate tutte le possibili soluzioni.

## Requisiti

Per poter applicare la teoria del backtracking ad un problema, devono valere le seguenti condizioni:

- lo spazio delle soluzioni deve essere finito (l'insieme delle possibili configurazioni da verificare deve essere finito);
- la soluzione può essere rappresentata tramite una sequenza di soluzioni parziali *S1, S2, ..., Sn*;
- la dimensione massima delle soluzioni *n* è nota a priori;
- il valore di ciascuna sotto-soluzione (*Si*) deve appartenere ad un insieme finito di valori.

ATTENZIONE! Anche questa tecnica, nei casi peggiori, offre soluzioni non polinomiali (brute force k^n).
## Passi per svolgere al meglio i problemi con il backtracking

Tecnica di risoluzione esaustiva per problemi di ricerca: l'obiettivo è costruire una soluzione corretta.

Introduce dei **controlli** che servono a scartare tempestivamente una soluzione (in via di costruzione) che non soddisferà i criteri di ammissibilità.

Differenza con una tecnica esaustiva classica: **problema 3 colorabilità**.

- *Soluzione esaustiva:* (1) assegno tutti i possibili colori a tutti i possibili nodi, (2) controllo ogni assegnamento;
- *Soluzione backtracking:* (1) costruisco una soluzione dando un colore ad un nodo, (2) se nella costruzione di una soluzione due nodi adiacenti hanno lo stesso colore essa viene scartata

Il backtracking è un algoritmo generale che **costruisce in modo incrementale le soluzioni** e **scarta quelle non valide**.

Generalmente usato quando il problema pone dei vincoli di soddisfacimento (*constraint satisfaction problems*).