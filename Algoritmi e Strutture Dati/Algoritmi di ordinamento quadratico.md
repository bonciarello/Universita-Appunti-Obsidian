---
aliases: [AOQ, Algoritmi ordinamento quadratico]
tags: [algoritmi-e-strutture-dati]
---
## Problemi di ricerca

- **Ricerca lineare** ⇒ no requisiti sul dato → O(n)
- **Ricerca binaria** ⇒ array ordinato → O(log₂ n), da aggiungere il costo di ordinamento
- **Ricerca uniforme** ⇒ array ordinato → O(n) se la distribuzione è uniforme allora *circa* O(1)
- **Tabelle Hash** ⇒
    - necessità di chiave di ricerca
    - generare la struttura dati → O(n)

    → O(1) se funzione hash e fattore di carico sono scelti bene

- **[[Alberi binari]] di ricerca** ⇒
    - costruzione dell'albero
    - bilanciamento albero

    → O(log₂ n)

## Algoritmi di ricerca

- **Bubble Sort**
```cpp
bool scambio; int x = n; int last = 0;
do {
    scambio = false;
    for(int i = 0; i < x-1; i++) {
        if(a[i] > a[i+1]) {
            tmp = a[i];
            a[i] = a[i+1];
            a[i+1] = tmp;
            scambio = true;
            last = i+1;
        }
    }
    x = last;
} while(scambio)
```
- *CASO PEGGIORE:* O(n²)
- *CASO MIGLIORE:* O(n)

Si contraddistingue nell'avere un elevato numero di istruzioni elementari che nel caso peggiore incrementano, in qualche modo, il "peso" della complessità.

> (n-1) + (n-2) + (n-3) + ... + 1 ⇒ n(n+1)/2 = ~ O(n²)

- **Selection Sort**
```cpp
for(int i = 0; i < n-1; i++) {
    int min = i;
    for(int j = i+1; j < n; j++) {
        if(a[j] < a[min]) {
            min = j;
        }
    }
    int temp = a[min];
    a[min] = a[i];
    a[i] = temp;
}
```
- *CASO PEGGIORE:* O(n²)
- *CASO MIGLIORE:* O(n²)

A differenza del Bubble Sort, il numero di istruzioni elementari viene eseguito *n* volte e non n² volte. Quindi in un certo senso questo algoritmo è migliore.

- **Insertion Sort**
```cpp
for(int i = 0; i < n; i++) {
    temp = a[i];
    j = i-1;
    while(j >= 0 && a[j] > temp) {
        a[j+1] = a[j];
        j--;
    }
    a[j+1] = temp;
}
```
- *CASO PEGGIORE:* O(n²) (array al contrario)
- *CASO MIGLIORE:* O(n)

Ipotizzando un caso intermedio, l'esecuzione del ciclo *while* si interrompe prima dell'indice *j*. Quindi il numero di iterazioni del *while* sarà minore del ciclo *for* del Selection Sort.

Inoltre non vengono fatti scambi ma bensì uno shifting sinistro e infine il numero di operazioni di base è inferiore ai precedenti algoritmi.

- **Merge Sort**: è una classica applicazione della tecnica *[[Divide et impera|divide et impera]]*. Dato un vettore di *n* elementi si suddivide in due parti. A questo punto si ordinano ricorsivamente i due vettori e quindi il vettore complessivo viene ordinato per "fusione" dei due sotto-vettori.
```cpp
void mergeSort(Vettore<T>& v, int in, int fin) {
    if((fin-in) < 20) {
        insertionSort(V, in, fin);
    } else {
        int medio = (in + fin)/2;
        mergeSort(v, in, medio);
        mergeSort(v, medio+1, fin);
        merge(v, in, fin, medio);
    }
}

void merge(Vettore<T>& v, int in, int fin, int medio) {
    Vettore<T> a(1, fin-in+1);
    int medio1 = medio + 1;
    int i1 = in;
    int i2 = mredio + 1;
    int i3 = 1;
    while((i1 <= medio && i2 <= fin)) {
        if(v[i1] < v[i2]) {
            a[i3] = v[i1];
            i1++;
        } else {
            a[i3] = v[i2];
            i2++;
        }
        i3++;
    }

    while(i1 <= medio) {
        a[i3] = v[i1];
        i1++;
        i3++;
    }

    while(i2 <= fin) {
        a[i3] = v[i2];
        i2++;
        i3++;
    }

    for(i3 = 1; i1 = in; i1 <= fin; i3++, i1++) {
        v[i1] = a[i3];
    }
}
```

Questo algoritmo ha una complessità pari a O(n·log₂ n) sia nel caso peggiore che in quello migliore. L'unica differenza rispetto ai precedenti è che non è *ON SITE*, cioè si appoggia su una struttura dati ausiliare e compie un grande numero di istruzioni elementari.

- **Quick Sort**: si prende in esame un elemento *x* del vettore e si determina la posizione esatta che gli compete nell'ordinamento. Contemporaneamente si portano alla sua sinistra tutti gli elementi che lo precedono e alla sua destra quelli che lo seguono ottenendo così due sotto-vettori *v1* e *v2*.

A questo punto, con la stessa tecnica, si procede ad esaminare gli altri sotto-vettori. L'algoritmo cruciale è la partizione del vettore.

A tale scopo si scandisce da sinistra a destra fino a che non si trova un elemento *y* che segue il nostro elemento e contemporanemante si scandisce da destra a sinistra fino a che non si trova un numero minore (*z*).

A questo punto, *y* e *z* si scambiano.
```cpp
void quickSort(Vettore<T>& v, int in, int fin) {
    if((fin - in) < 20)
        insertionSort(v, in, fin);
    else {
        int PosOrd = partiziona(v, in, fin);
        quickSort(v, in, PosOrd-1);
        quickSort(v, PosOrd+1, fin);
    }
}

int partiziona(Vettore<T>& v, int in, int fin) {
    int i = in; int j = fin + 1;
    while(i < j) {
        do j--;
        while(v[in] < v[j]);

        do i++;
        while(v[in] >=  v[i] && i < j);

        if(i < j) scambia(v[i], v[j]);
        scambia(v[in], v[j]);
        return j;
    }
}
```

La complessità dipende dalla funzione *partiziona*. In generale è O(n) ma adesso il numero di suddivisioni non è più costante come nel Merge Sort. Possiamo infatti individuare 3 complessità:
- *CASO MIGLIORE:* O(n·log n) (array disordinato)
- *CASO PEGGIORE:* O(n²) (array ordinato)
- *CASO MEDIO:* O(n·log₂ n) (array abbastanza disordinato)

Per evitare di ricadere nel caso peggiore paradossalmente basta disordinare il nostro array così da migliorare le prestazioni. In termini di costanti questo è migliore del Merge Sort ed è *ON SITE*.
