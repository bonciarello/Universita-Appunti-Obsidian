## Complessità computazionale

Un aspetto importante che non può essere trascurato nella progettazione di un algoritmo è la caratterizzazione dell'efficienza con la quale l'algoritmo stesso viene eseguito su un elaboratore.

L'efficienza non è da intendersi solo in termini di velocità di esecuzione ma anche in funzione dell'utilizzo delle altre risorse.

## Tipi di complessità

Esistono 3 tipi di complessità:

- complessità temporale (modellare il tempo di esecuzione)
- complessità spaziale (modellare lo spazio di occupazione di memoria aggiuntiva, cioè la memoria necessaria oltre all'input dato)
- complessità input/output

Risulta essere necessario quindi definire dei parametri e dei criteri attraverso i quali valutare il nostro algoritmo. Bisogna redigere un **modello di costo**.

## Modello di costo

Con questo modella si pensa alla:

- definizione di dimensione dell'input (numero di operazioni da eseguire)
- definizione di istruzione di costo unitario
- complessità di caso migliore e di caso peggiore

In generale, uno stesso problema può essere risolto in più modi diversi, cioè con algoritmi differenti, i quali a loro volta hanno un grado di efficienza variabili. La caratterizzazione di un algoritmo, in funzione della dimensione dell'input, è data quando *n* tende all'*infinito*.

### Istruzioni di costo unitario

È una operazione che non dipende dai valori e dai tipi di variabili.

- lettura/scrittura (I/O)
- assegnamento, operazioni aritmetiche predefinite, *return*
- accesso ad elementi di array (built-on)
- valutazione di una qualsiasi espressione booleana

Queste sono utili per trovare un modo per esprimere quantitativamente il numero di operazioni necessarie per eseguire un algoritmo.

## Esempi di calcolo della complessità

```cpp
i = 1; // assegnamento esterno: 1
while (i <= n) // numero di test: n+1
    i = i + 1; // assegnamento interno n*1

// passi base: 2+2*n (funzione in termini della dimensione dell'input)
```

Si deve quindi determinare una funzione in termini della dimensione dell'input per rappresentarci la complessità dell'algoritmo.

```cpp
if(cond) { ... }

if(cond) { ... } else { ... }
```

Poiché qui la situazione è variabile dobbiamo introdurre un tipo di analisi chiamate **caso migliore** e **caso peggiore**.

Quindi ogni qual volta ci si presenta un algoritmo in cui il costo complessivo non dipende solamente dalla dimensione dei dati ma anche dal loro valore, dobbiamo applicare la **complessità nel caso migliore e nel caso peggiore**.

## Complessità asintotica

È un criterio matematico per partizionare gli algoritmi in classi di complessità.
Esistono 3 notazioni asintotiche:

- **θ - definizione stretta**: *f(x)* cresce esattamente come *g(n)*
- **O - definizione superiore**: *f(x)* cresce al più come *g(n)*
- **Ω - definizione inferiore**: *f(x)* cresce almeno come *g(n)*

Si dia che una funzione *f(n)* è di ordine *y(n)* e si serve:

> *f(n) = O(y(n))*

ed è valida se:

> ∃ c, n₀ > 0 | ∀ n > n₀     0 < f(n) < c * g(n)

---

Significa che vogliamo identificare una funzione *y(n)* tale per cui, a partire da un punto, n₀ vada a limitare superiormente la nostra funzione di potenza cioè:

![[ img/sezione4new/delimitazioneSuperiore.PNG]]

dove:

- f(n) ∈ O(1) → f(n) costante
- f(n) ∈ O(log n) → f(n) logaritmica
- f(n) ∈ O(n) → f(n) lineare
- f(n) ∈ O(n log n)
- f(n) ∈ O(n²) → f(n) quadratica
- f(n) ∈ O(2ⁿ) → f(n) esponenziale

Usiamo le notazioni asintotiche per caratterizzare il *running time* di un algoritmo e lo spazio occupato.

## Metodo di calcolo

- *istruzioni di costo unitario* → 1 → O(1)
- *blocco di istruzioni:* blocco f1, f2, f3 → max{f1, f2, f3}
- *costrutti di controllo del flusso:*
```cpp
if(cond) {
    ...  // Fcond + Ftrue
} else {
    ...  // Fcond + Ffalse
}

// CASO MIGLIORE: min
// CASO PEGGIORE: max
```

- *cicli:*
```cpp
while(cond) {
    ...  // Fcond + K * Fcorpo
    // K = numero di iterazioni

    // nell'ipotesi che il corpo rimanga costante
}
```
Nel caso in cui non fosse costante, bisogna "srotolarlo" e cioè individuare una funzione che vada a determinare il numero di operazioni tenendo conto della variazione del costo del blocco.

- *cicli innestabili:*
```cpp
for(int i...) // n cicli
    for(int j...) // n cicli

// n*n -> O(n^2)
```

- *cicli in sequenza:*
```cpp
for(int i...) // n cicli

for(int j...) // n cicli

// max tra i due blocchi
```

## Ricerca binaria

```cpp
bool ricercaBinaria(int v[], int x, int in, int fin) {
    if(in >= fin) { // O(1)
        return ((in == fin) && (x == v[in]));
    } else { // O(1)
        int medio = (in + fin) / 2;
        if(v[medio] < x)
            in = medio +1;
        else if(x < v[medio])
            fin = medio -1;
        else
            in = fin = medio;

        return ricercaBinaria(v, x, in, fin);
    }
}
```

Determina la complessità e quindi il numero di volte che viene richiamata la funzione *ricercaBinaria*.

| **Iterazioni** | **Complessità** |
|----------------|-----------------|
| 1 | n/2 |
| 2 | n/4 |
| 3 | n/8 |
| ... | ... |
| k | n/2ᵏ |

Quando *n = 1*,

> n/2ᵏ = 1 ⇒ n = 2ᵏ

e quindi avrà complessità:

> log₂ n = k

Quest'ultima è una relazione fondamentale della matematica e cioè il numero di volte *k* per cui un numero *x* può essere diviso per 2 affinché *x* sia uguale a 1 e dato da k·log₂ x
