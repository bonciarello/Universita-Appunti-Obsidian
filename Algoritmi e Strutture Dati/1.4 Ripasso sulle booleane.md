---
aliases: [RB, Ripasso booleane]
tags: [algoritmi-e-strutture-dati]
---
Andiamo ad analizzare l'uso di booleane per alcuni tipi di problemi. Ci troveremo ad affrontare tipi di problemi in cui dobbiamo determinare se si verifica una certa condizione. Le tipologie di richieste che ci ritroveremo ad affrontare sono:
- almeno uno / tutti / nessuno soddisfano una certa condizione
- espressioni generalizzate con esiste / per ogni / mai

Il primo scoglio sarà di riuscire a leggere per bene la traccia e comprenderla in modo da trovare una soluzione ottimale. Una delle cattive abitudini dei programmatori è quella di utilizzare il *break* o il *return*: non è un'abitudine buona, perché molto spesso, per altre problematiche, possono essere combinati in cascata. Imparare l'uso delle booleane è **fondamentale**: per esempio:

```cpp
bool condizione_da_verificare = caso_difficile;
Ciclo di verifica {
	if(condizione facile)
		condizione_da_verificare = caso_facile;
}
```

Se dobbiamo trovare un elemento, il nome della variabile più ottimale può essere "trovato": questo perché deve essere facilmente leggibile. Il caso_facile è il valore da verificare mentre il caso difficile dipende dalla frequenza del dato che verifichiamo nella condizione facile.

Facciamo un altro esempio:

```cpp
bool condizione_da_verificare = caso_difficile;
Ciclo di verifica {
	if(condizione facile)
		condizione_da_verificare = caso_facile;
	else
		condizione_da_verificare = caso_difficile;
}
```

Con questo programma si va a verificare solo la condizione facile dell'ultimo elemento, quindi errato se cerchiamo di realizzare un programma per trovare un elemento.
## Esempio

Scriviamo una funzione che, data una matrice, determini se esistono due righe esattamente identiche:

```cpp
const int rowSize = 3;
const int colSize = 4;

bool verifica (const int m[][colSize], int rowSize, int colSize) {
	bool trovate = false;
	for(int r1 = 0; r1< rowSize && !trovate; ++r1)
		for(int r2 = r1+1; r2 < rowSize && !trovate; ++r2) {
			bool identiche = true;
			for(int c=0; c < colSize && identiche;++c) {
				if(m[r1][c] != m[r2][c]) Identiche = false;

				if(identiche) trovate = false;
			}
		}

	return trovate;
}
```
