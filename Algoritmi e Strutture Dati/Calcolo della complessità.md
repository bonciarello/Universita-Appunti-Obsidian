---
aliases: [CC, Calcolo complessità]
tags: [algoritmi-e-strutture-dati]
---
## Notazioni asintotiche

Usiamo le notazioni asintotiche per caratterizzare il *running time* di un algoritmo: ci interessiamo solo del comportamento asintotico e, per quanto riguarda il running time, generalmente ci interessa il *caso migliore* e il *caso peggiore*.

Esistono tre diverse notazioni asintotiche:

> Θ, O, Ω

![[ img/sezione4/notazioniAsintotiche1.PNG]]

## Notazioni O

Useremo la notazione *O* ("o grande", "big-o"): solitamente interessa dare un limite superiore (più comodo nelle analisi) ed è molto informativa nel caso peggiore.

![[ img/sezione4/notazioniAsintotiche2.PNG]]

### Esempio

Troviamo la complessità per queste funzioni.

```cpp
// ESERCIZIO 1
bool foo(int c, int p) {
    for(int i = 0; i < p; i++) // O(p)
        cout<<c<<endl; // O(1)
}
```

La complessità dell'ESERCIZIO 1 è pari a ***O(p)*** (*O(p) * O(1)*).

```cpp
// ESERCIZIO 2
bool foo(int c, int n) {
    for(int i = 0; i < 100; i++) // O(100) => O(1)
        cout<<c*n<<endl; // O(1)
}
```

La complessità dell'ESERCIZIO 2 è pari a ***O(1)*** (*O(1) * O(1)*).

## Esercizi

### Esercizio (num. 1)

Studiare la complessità nel caso migliore e nel caso peggiore della funzione *elabora*.

```cpp
bool elabora(int M[][N], int V[N]) {
    bool b = false;
    int i = 1;
    while(i <= N && !b) {
        if(somma(M) || V[i-1] == 0)
            b = true;
        i = i+2;
    }
    return b;
}

bool somma(int M[][N]) {
    int s=0;
    for (int i=0; i<N; i++)
        if (M[i][i]!=0)
            for (int j=0; j<N; j++)
                s += M[i][j];
    if (s > 0)
        return true;
    else
        return false;
}
```

Innanzitutto verifichiamo la complessità della funzione *somma*, quindi vediamo il caso peggiore e il caso migliore di *somma*.

```cpp
bool somma(int M[][N]) {
    int s=0; // O(1)
    for(int i=0; i<N; i++) // O(N)
        if(M[i][i]!=0) // O(1)
            for(int j=0; j<N; j++) // O(N)
                s += M[i][j]; // O(1)
    if(s > 0) // O(1)
        return true;
    else
        return false;
}
```

Per quanto riguarda la funzione *somma*, il caso peggiore equivale a *O(N) * O(N)* cioè O(N²) mentre il caso migliore equivale a *O(N) * O(1)* cioè *O(N)* perché ipotizziamo che svolge il for esterno ma mai quello interno.

Adesso che sappiamo la complessità della funzione *somma*, possiamo studiare la complessità di *elabora*:

```cpp
bool elabora(int M[][N], int V[N]) {
    bool b = false; // O(1)
    int i = 1; // O(1)
    while(i <= N && !b) { // O(N/2)
        if(somma(M) || V[i-1] == 0) // O(N^2) oppure O(N)
            b = true; // O(1)
        i = i+2; // O(1)
    }
    return b; // O(1)
}
```

Per quanto riguarda la funzione *elabora*, il caso peggiore equivale a O(N/2) * O(N²) cioè O(N³) mentre il caso migliore equivale a *O(1) * O(N)* cioè *O(N)* perché ipotizziamo che svolge il for esterno ma mai quello interno.

### Esercizio (num. 2)

Studiare la complessità nel caso migliore e nel caso peggiore della funzione *f*.

```cpp
int f(int V1[N], int V2[N]) {
    bool b = false;
    int i = 1;
    while(!b && i < N) {
        b = g(V1[i], V2) && g(V2[i], V1);
        i = i * 2;
    }
    return i;
}

bool g(int val, int &V[N]) {
    bool b = false;
    for(int i=0; i < N && !b; i++)
        if(val == V[i])
            b = true;
    return b;
}
```

Innanzitutto verifichiamo la complessità della funzione *g*, quindi vediamo il caso peggiore e il caso migliore di *g*.

```cpp
bool g(int val, int &V[N]) {
    bool b = false; // O(1)
    for(int i=0; i < N && !b; i++) // O(N)
        if(val == V[i]) // O(1)
            b = true; // O(1)
    return b; // O(1)
}
```

Per quanto riguarda la funzione *g*, il caso peggiore equivale a *O(N)* mentre il caso migliore equivale a *O(1)* perché ipotizziamo che entra nel for solo una volta e il valore di *b* diventa *true* dopo il primo ciclo.

Adesso che sappiamo la complessità della funzione *somma*, possiamo studiare la complessità di *f*:

```cpp
int f(int V1[N], int V2[N]) {
    bool b = false; // O(1)
    int i = 1; // O(1)
    while(!b && i < N) { // O(log N)
        b = g(V1[i], V2) && g(V2[i], V1); // O(N) + O(N) oppure O(1) + O(1)
        i = i * 2; // O(1)
    }
    return i; // O(1)
}
```

Per quanto riguarda la funzione *f*, il caso peggiore equivale a O(log₂ N) * (O(N + N)) cioè O(N * log₂ N) mentre il caso migliore equivale a *O(1) * (O(1 + 1))* cioè *O(1)*.
