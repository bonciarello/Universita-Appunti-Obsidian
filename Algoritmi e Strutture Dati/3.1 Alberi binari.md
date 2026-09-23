---
aliases: [Albero]
tags: [algoritmi-e-strutture-dati]
---
Sono strutture gerarchiche in cui ad ogni oggetto (nodo padre) ha associato più oggetti (nodo figli).
I figli hanno solo un padre tranne l'oggetto iniziale che costituisce la radice dell'albero. I nodi senza figli sono detti foglie e i nodi appartenenti allo stesso padre sono detti fratelli.

## Caratteristiche degli alberi binari

- **Livello di un nodo:** la radice ha livello 1 ed ogni nodo N ha il livello *p+1*, dove *p* è il livello del nodo padre.
    ![[img/sezione6/livelloNodo.PNG]]

- **Profondità:** è il massimo livello dei suoi nodi. Ipotizzando di avere un albero binario di profondità K, possiamo ricavare il massimo numero di nodi cioè:
    - min = k
    - max = 2^k - 1

    Inoltre, in funzione del numero di nodi, è possibile ricavare la profondità minima e cioè O(log n).

- **Albero pieno:** un albero che ha tutti i nodi (2^k - 1) è detto pieno. Cioè tutti i nodi del penultimo livello hanno figli. Un esempio:
    ![[img/sezione6/alberoPieno.PNG]]

- **Albero completo:** tutti i livelli tranne l'ultimo sono riempiti e l'ultimo livello è riempito da sinistra a destra. Un esempio:
    ![[img/sezione6/alberoCompleto.PNG]]

- **Albero bilanciato:** per ogni nodo la profondità del sotto-albero di sinistra differisce di al più uno dalla profondità del sotto-albero destro. DEVE VALERE PER OGNI SOTTO ALBERO.
    ![[img/sezione6/alberoBilanciato.PNG]]

## Rappresentazione degli alberi

### Strutture indicizzate

- **Vettore dei padri:**
    - *PRO:* rappresentazione di alberi generici, accesso diretto ad un qualunque nodo e accesso *O(1)* al padre
    - *CONTRO:* ricerca figli ha complessità *O(Dim)*

    ![[img/sezione6/vettorePadri.PNG]]

- **Vettore posizionale:** si può definire una formula che a partire dalla posizione (V) di un nodo posso conoscere i figli. *P[V] => i-esimo figlio di V = P[d*v+1]*
    - *PRO:* accesso in *O(1)* ai figli di un nodo, accesso ai padri attraverso formula inversa ⌊v/d⌋ e struttura compatta
    - *CONTRO:* alberi generici, spazio libero e stima della profondità per il dimensionamento dell'array

    ![[img/sezione6/vettorePosizionale.PNG]]

### Rappresentazioni collegate (memoria dinamica)

1. SOLUZIONE: puntatore ai figli
2. SOLUZIONE: lista di figli
3. SOLUZIONE: primo figlio-fratello

Ad ogni nodo vengono associati 2 o più puntatori ai figli.

![[img/sezione6/rappresentazioneCollegate.PNG]]

![[img/sezione6/primoFiglioFratello.PNG]]

Nella prima soluzione i pro sono: accesso murato ai figli, accesso da un singolo punto e versatilità. Nella seconda e terza soluzione il contro è che hanno accesso tramite scorrimento delle liste ai figli.

La struttura più opportuna dipende dal programma che dobbiamo sviluppare.
