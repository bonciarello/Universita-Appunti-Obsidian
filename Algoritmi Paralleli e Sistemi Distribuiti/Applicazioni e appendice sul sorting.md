---
aliases: [Applicazioni appendice sorting, AAS]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## Bitonic Sort generale

### Fase 1

Per trasformare un vettore di 8 elementi in uno bitonico, suddividerlo in 4 coppie, oppure in 4 sottosequenze bitoniche di lunghezza 2 e ordinarle in modo da avere 2 sottosequenze bitoniche di lunghezza 4. Il vettore iniziale si presenta come di seguito:

![[applicazioni1.PNG]]

![[applicazioni2.PNG]]

Utilizzando Co-Ex-Lo e Co-Ex-Hi,

![[applicazioni3.PNG]]

..otteniamo:

![[applicazioni4.PNG]]

Ordiniamo ciascuno dei due sottovettori in modo da avere un unico vettore bitonico.

![[applicazioni5.PNG]]

Il primo in ordine crescente il secondo in ordine decrescente. A fine fase 1 abbiamo:

![[applicazioni6.PNG]]

### Fase 2

Ordinamento di una sequenza bitonica di lunghezza 8 ottenuta prima (con bitonic sort):

![[applicazioni7.PNG]]

Dopo *log n* passaggi otteniamo la lista ordinata globalmente:

![[applicazioni8.PNG]]

## [[Sorting]] in parallelo: esempio generale

*Come possiamo implementare un algoritmo di ordinamento parallelo, a partire da un qualsiasi algoritmo **sequenziale**?* La strategia generale è divisa in 2 fasi:

- **Fase 1 ([[Sorting|sorting]] locale):** ogni processore ordina il proprio vettore con un algoritmo di ordinamento standard (ad esempio, quicksort);
- **Fase 2(merging):** i (sotto)vettori sono opportunamente combinati per ottenere un vettore globale ordinato

### Esempio

Partiamo con *p=4* e *n=20*:

![[applicazioni9.PNG]]

Procediamo con la *fase 1 ([[Sorting|sorting]] locale)* dove ogni processore ordina in modo **crescente** la propria **sottolista**.

![[applicazioni10.PNG]]

Continuiamo con la *fase 2 (merging)* dove si applica l'algoritmo GBS a tutto il vettore.

![[applicazioni11.PNG]]

![[applicazioni12.PNG]]

![[applicazioni13.PNG]]

![[applicazioni14.PNG]]

![[applicazioni15.PNG]]

Dopo questi passaggi la lista è globalmente ordinata.

![[applicazioni16.PNG]]

## Bubble Sort e sue varianti

In primo luogo, il numero maggiore è stato spostato alla fine della lista da una serie di confronti e scambi, a partire dall'estremità opposta. Azioni ripetute con i numeri successivi, fermandosi appena prima del numero precedentemente posizionato. In questo modo, i numeri più grandi si spostano ("bolla") verso un'estremità.

L'algoritmo di ordinamento delle bolle sequenziale confronta e scambia gli elementi adiacenti nella sequenza da ordinare:

```c
procedure BUBBLE_SORT(n)
begin
    for i := n-1 downto 1 do
        for j := 1 to i do
            compare-exchange(aj, aj+1);
end BUBBLE_SORT
```

![[bubbleSort.PNG]]

La complessità del Bubble Sort è $\Theta(n^2)$. Il bubble sort è difficile da parallelizzare perché l'algoritmo non ha un parallelismo esplicito! Una semplice variante (chiamata trasposizione pari-dispari), tuttavia, rivela un parallelismo implicito...

## Trasposizione pari-dispari

![[oddEven.PNG]]

Ordinamento di *n=8* elementi, utilizzando l'algoritmo di ordinamento della trasposizione pari-dispari. Durante ciascuna fase vengono confrontati *n=8* elementi.

```c
procedure ODD-EVEN(n)
begin
    for i := 1 to n do
    begin
        if i is add then
            for j := 0 to n/2-1 do
                compare-exchange(a2j+1, a2j+2)
        if i is even then
            for j := 1 to n/2-1 do
                compare-exchange(a2j, a2j+1)
end ODD-EVEN
```

Dopo *n* fasi di scambi dispari-pari, la sequenza viene ordinata. Ogni fase dell'algoritmo (dispari o pari) richiede confronti $\Theta(n)$. La complessità seriale è $\Theta(n^2)$.
