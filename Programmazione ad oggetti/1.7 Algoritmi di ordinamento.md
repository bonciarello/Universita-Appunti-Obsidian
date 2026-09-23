---
aliases: [AO, Algoritmi ordinamento]
tags: [programmazione-ad-oggetti]
---
Vengono presentati tre algoritmi fondamentali per ordinare un array.
### Bubble Sort
Scorre l'array confrontando ogni elemento con il successivo. Se non sono in ordine, li scambia. Ripete il processo finché non ci sono più scambi da effettuare .
```c++
void bubblesort(int a[], int n) {
    int it = 1;
    bool swap = true;
    while(swap) {
        swap = false;
        for(int i=0; i < n - it; i++) {
            if(a[i] > a[i+1]) {
                // Scambio
                int temp = a[i];
                a[i] = a[i+1];
                a[i+1] = temp;
                swap = true;
            }
        }
        it++;
    }
}
```
### Insertion Sort
Divide l'array in una parte ordinata e una non ordinata. Prende un elemento dalla parte non ordinata e lo "inserisce" nella posizione corretta della parte ordinata .
```c++
void insertionsort(int a[], int n) {
    for(int i=1; i < n; i++) {
        int tmp = a[i];
        int j = i - 1;
        while(j >= 0 && follow(a[j], tmp)) {
            a[j+1] = a[j];
            j--;
        }
        a[j+1] = tmp;
    }
}
```
### Selection Sort
Cerca l'elemento più grande (o più piccolo) nell'array e lo sposta nell'ultima posizione disponibile, riducendo progressivamente la porzione di array da analizzare.