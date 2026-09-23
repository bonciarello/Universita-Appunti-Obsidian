---
aliases: [PG]
tags: [algoritmi-e-strutture-dati]
---
La tecnica golosa è utilizzata generalmente per risolvere problemi di ottimizzazione cioè problemi in cui bisogna ricercare una soluzione che soddisfi alcuni vincoli e che massimizzi/minimizzi il valore di una funzione obiettivo.

> max F = Σ xᵢ * Vᵢ (x₁ ... xₙ)
> vincoli:
> - c(x₁ ... xₙ) ≤ cₘₐₓ
> - 0 ≤ xᵢ ≤ qᵢ | i = 1, ..., n

La tecnica golosa, invece di effettuare una scelta globale degli oggetti più convenienti, determina la soluzione in **stadi** in ciascuno dei quali effettua una scelta parziale di ottimo (*scelta golosa*) che successivamente aggiunge alla soluzione.

Si determina al primo stadio l'oggetto che "sembra" più conveniente attraverso il suo **valore specifico** (vᵢ/cᵢ). Al secondo e i successivi si reitera questa scelta fino a che non vengono violati i vincoli o non ci sono più oggetti.

*Un grande difetto di questa tecnica è che non si ha una visione d'insieme dell'intero problema ma bensì limitata allo stadio corrente. Questo potrebbe inficiare la soluzione stessa, da qui **GREEDY**, perchè vado a escludere potenziali soluzioni migliori.*

Un aspetto importante nell'utilizzo di un algoritmo goloso è dimostrare che la soluzione è ottima e, nel caso non sia verificare, se essa sia comunque sub-ottima cioè vicina alla ottima. Nel primo caso (*soluzione ottima*) si parla di **algoritmo esatto**; nel secondo caso si parla di **algoritmo euristico/approssimato**.

> **ESEMPIO:**
> ```cpp
> Tcambio cambio(const vector<float>& tagli, float valoreAssegno) {
> 	int n = tagli.size();
> 	Tcambio sol(n);
> 	for(int i = 0; i < n && sol.valore < valoreAssegno; ++i) {
> 		sol.nMonete[i] = (valoreAssegno - sol.valore)/tagli[i];
> 		sol.valore += sol.nMonete[i]*tagli[i];
> 	}
> 	if(sol.valore < valoreAssegno) {
> 		sol.nMonete[n-1]++;
> 		sol.valore += tagli[n-1];
> 	}
> } // complessita' O(n), n = numero dei tagli
> ```

Questo algoritmo ha complessità di *O(n)* perché diamo per scontato che i tagli siano già ordinati in senso decrescente del valore specifico.

Più in generale la complessità di un algoritmo goloso dipende dal valore specifico il quale può essere:

- **STATICO:** se già ordinato *O(n)*, altrimenti *O(n log n) + x + complessità dy*;
- **DIPENDENTE DALLE SCELTE:** ricalcolare il valore specifico per ogni scelta *O(n²)*;

L'algoritmo proposto sopra potrebbe non generare la soluzione ottima. Infatti è strettamente influenzata dai "tagli" ed anche per la natura "greedy" dell'algoritmo stesso.

> **ESEMPIO:** cambiare 8 euro con:
>
> | | **Risposta** | **Ottima** |
> |---|---|---|
> | 5 | 1 | 0 |
> | 4 | 0 | 2 |
> | 2 | 1 | 0 |
> | 1 | 1 | 0 |
