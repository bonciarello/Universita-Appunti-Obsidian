L'ordinamento può essere basato sul **confronto** o **non basato sul confronto**. L'operazione fondamentale dell'ordinamento basato sul confronto è lo scambio-confronto (confronto-scambio). Il limite inferiore di qualsiasi algoritmo di ordinamento sequenziale per n numeri è *O(n log n)*. Nel campo del calcolo parallelo, il massimo che possiamo aspettarci da n processori è *O(log n)*! In effetti si ottiene, ma con costanti molto grandi...

## Ordinamento di n numeri

```c
/* Bubble sort for integers */
#define SWAP(a, b) { int t; t = a; a = b; b = t; }
void SORT(int a[]m int n)
/* Pre-condition: a contains n items to be sorted */
    {
    int i, j;
    /* Make n passes thorugh the array */
    for(i = 0; i < n; i++)
        {
        /* From the first element to the end of the unsorted section */
        for(j = 1; j < (n-i); j++)
            {
            /* If adjacent items are out of order, swap them */
            if(a[j-1] > a[j] SWAP(a[j-1], a[j]);
            }
        }
```

## Ordinamento parallelo

```c
SORT(a[0 : n/2-1])
SORT(a[n/2 : n-1])
MERGE(a[0 : n/2-, a[n/2 : n-1])
```

![[ordinamentoParallelo.PNG]]

Sia *S = \{a1, a2, ..., an\}* una sequenza di n elementi. L'ordinamento di S consiste in una successione *S '= \{a1', a2 ', ..., an'\}* tale che *ai '<= aj'* per *1 <= i <= j <= n* e *S'* è a permutazione di *S*.

Base di ogni algoritmo basato sul confronto:
```c
if(A > B) {
    temp = A;
    A = B;
    B = temp;
}
```

## Le basi dell'ordinamento

Cos'è una **sequenza parallela ordinata**? Dove sono archiviati gli elenchi di input e output? Il presupposto principale è che gli elenchi di input e output siano **distribuiti**. Per convenzione, l'elenco ordinato è **partizionato** con la proprietà che ogni **sotto-elenco è ordinato** e ogni elemento dell'elenco del processore *Pi* è più piccolo di quelli in *Pj* se *i < j*. **Il fatto precedente è alla base di ogni algoritmo di ordinamento parallelo!**

Qual è la controparte di un confronto di sequenze parallele? Se ogni processore ha un elemento, l'operazione di **scambio di confronto** memorizza l'elemento più piccolo nel processore con **id più piccolo**. Questo viene fatto in tempo *ts + tw*. Se si dispone di più di un elemento per processore, questa operazione viene chiamata **confronta suddivisione**. Supponiamo che ciascuno dei due processori abbia elementi *n / p*. **Dopo l'operazione di confronto-suddivisione, gli elementi *n / p* più piccoli sono nel processore *Pi* mentre i maggiori *n / p* sono in *Pj*, dove *i < j*.** Il tempo per un'operazione di confronto-suddivisione è (*ts + tw*n / p*), assumendo che i due elenchi "parziali" siano stati inizialmente ordinati.

## Operazione di scambio di confronto parallelo

Negli **algoritmi di ordinamento sequenziale**, l'input e le eventuali sequenze ordinate (incluso l'output) vengono archiviati nella memoria dello **stesso processo**. Nel caso parallelo, i dati possono, ovviamente, essere memorizzati in altri processi, complicando ulteriormente l'algoritmo!

![[operazioneScambio.png]]

Un'operazione di scambio di confronto (un elemento per processore). I processi *Pi* e *Pj* inviano i loro elementi l'un l'altro. Il processo *Pi* mantiene *min\{ai, aj\}* e *Pj* mantiene *max\{ai, aj\}*.

## Confronta funzionamento in parallelo diviso

![[confrontaFunzionamento.png]]

Un'operazione di confronto e suddivisione. Ogni processo invia il proprio blocco di dimensione *n/p* a un altro processo. Ogni processo "fonde" il blocco che riceve con il proprio e mantiene solo la metà appropriata. In questo esempio, il processo *Pi* mantiene gli elementi minori e il processo *Pj* mantiene i maggiori.

## Sorting Networks

Sono reti "**comparatori**", progettate per lo smistamento. Un **comparatore è un dispositivo** con due ingressi *x* e *y* e due uscite *x'* e *y'*. Per un comparatore crescente, abbiamo *x' = min\{x, y\}* e *y' = max\{x, y\}* e viceversa. Indichiamo un comparatore crescente con $\oplus$ e un comparatore decrescente con $\Theta$. La "**velocità**" della rete è proporzionale alla sua profondità

### Comparatori

![[comparatori.PNG]]

### Bitonic Sort

Una rete di smistamento **bitonica** ordina n elementi nel tempo *$\Theta (log^2 n)$*. Una **sequenza bitonica** ha due "**tonalità**" - ascendente e discendente, o viceversa. Anche qualsiasi rotazione ciclica di tali reti è considerata **bitonica**. Ad esempio *\{1,2,4,7,6,0\}* è una sequenza bitonica, perché prima aumenta e poi diminuisce. *\{8,9,2,1,0,4\}* è un'altra sequenza bitonica, perché è uno spostamento ciclico di *\{0,4,8,9,2,1\}*. **Il cuore della rete è il riordino della sequenza bitonica in una sequenza ordinata.**

Sia *S = \{a0, a1,…, an-1\}* una sequenza bitonica tale che $a0 <= a1 <= ... <= an / 2-1$ e $an / 2 >= an / 2 + 1 >= ... >= an - 1$. Consideriamo le seguenti sotto-sequenze di *S*:
- *s1 = \{min\{a0 ,an/2\},min\{a1 ,an/2+1\},...,min\{an/2-1 ,an-1 \}\}*
- *s2 = \{max\{a0 ,an/2\},max\{a1 ,an/2+1\},...,max\{an/2-1 ,an-1 \}\}*

Notare che *s1* e *s2* sono entrambi bitonici e che ogni elemento di *s1* è più piccolo di ogni elemento di *s2*. Possiamo riapplicare ricorsivamente la procedura su *s1* e *s2* per ottenere sottosequenze di dimensione 1, ovvero la **sequenza ordinata** (**unione bitonica**).

![[bitomicSort1.png]]

L'ordinamento di una sequenza bitonica avviene in *log n* passaggi!

Possiamo ora costruire una rete di **smistamento** per implementare l'**algoritmo di unione bitonica**.

Questa rete è chiamata **rete bitonica di unione**.

Questa rete contiene ** *log n* colonne**. Ogni colonna contiene *n/2* **comparatori** ed esegue un passaggio della fusione bitonica.

Indichiamo con **$\oplus$BM[n]** una rete bitonica di unione con n ingressi

Sostituendo i comparatori $\oplus$ con i comparatori $\Theta$ otteniamo una sequenza di output discendente: questa rete è chiamata **$\Theta$BM[n]**.

*$\oplus$BM[n]* è anche noto come **Co-Ex-Low**; *$\Theta$BM[n]* è anche noto come** Co-Ex-High**.

![[bitomicSort2.png]]

*Come si ordina una sequenza non ordinata utilizzando una fusione bitonica?* Dobbiamo prima costruire una singola sequenza bitonica dalla sequenza data. **Notiamo che una sequenza di lunghezza 2 è bitonica.** Quindi qualsiasi sequenza di elementi non ordinata può essere pensata come una **concatenazione** di sequenze bitoniche! Una sequenza bitonica di lunghezza 4 può essere costruita ordinando i primi due elementi utilizzando **$\oplus$BM[2]** e i due successivi con *$\Theta$BM[2]*. Questo processo può essere **ripetuto** per generare più sequenze bitoniche.

![[bitomicSort3.png]]

Quindi, l'**algoritmo completo (ordinamento bitonico generale)** consiste in:
- **creazione di una sequenza bitonica** a partire da una non ordinata (fase 1);
- applicazione della **rete bitonica di fusione** a tale sequenza, con profondità n.

La fase 1 ordina totalmente *n/2* elementi. Quindi, risolvendo la seguente relazione di ricorrenza (d = profondità dell'albero):

> $d(n) = d(\frac{n}{2}) + \log n$
>
> $d(n) = \sum_{i=1}^{\log n} i = \frac{\log^{2} n + \log n}{2} = \Theta(log^2 n)$
>
> $T(n) = O(\log(n)*\log(n))$

## Bitonic Sort per ipercubi

Considera il caso in cui abbiamo **un elemento** per processore. Notare che l'operazione di confronto-scambio avviene tra due "connessioni" (quindi processori) che differiscono esattamente di un bit! È quindi **naturale** implementare una mappatura diretta connessioni-processori utilizzando un ipercubo dove, ricordiamolo, due processi sono "vicini" se le loro rappresentazioni binarie differiscono esattamente di un bit!

![[bitomicSort4.png]]

Caratteristiche delle comunicazioni in ordinamento bitonico su un ipercubo. Durante ogni fase dell'algoritmo, i processori comunicano nelle dimensioni mostrate in figura.

![[bitomicSort5.png]]

Formulazione parallela di ordinamento bitonico su un ipercubo con $n = 2^d$ processi:

```c
procedure BITONIC_SORT(label, d)
begin
    for i := 0 to d-1 do
        for j := i downto () do
            if(i+1) bit of label != j bit of label then
                comp_exchange_max(j);
            else
                comp_exchange_min(j);
end BITONIC_SORT
```

Durante ogni fase dell'algoritmo, ogni processo esegue un'operazione di confronto-scambio (singola comunicazione al vicino "diretto"). Poiché ogni passo richiede *$\Theta$(1)*, il tempo parallelo è *Tp = $\Theta(log^2 n)$*. L'algoritmo è ottimale in termini di costi rispetto alla versione sequenziale, ma non rispetto al miglior algoritmo di ordinamento parallelo.

## Bitonic Sort per griglie

La connettività di una griglia/mesh è inferiore a quella di un ipercubo, quindi ci aspettiamo un po 'di overhead. Una mappatura delle righe principali mescolata viene solitamente considerata sui processi.

## Mappatura del Bitonic Sort alle mesh

![[mappaturaMesh.PNG]]

Diversi modi di mappare i fili di ingresso della rete di smistamento bitonica su una mesh di processi: *(a)* mappatura riga-maggiore, *(b)* mappatura simile a serpente maggiore di riga e *(c)* mappatura mescolata riga-maggiore.

![[mappaturaMeshLast.PNG]]

L'**ultima fase** dell'algoritmo di ordinamento bitonico per *n = 16* su una mesh, utilizzando la mappatura mescolata delle righe maggiori. Durante ogni fase, le coppie di processi si confrontano e si scambiano i loro elementi. Le frecce indicano le coppie di processi che eseguono operazioni di scambio di confronto.

## Blocco di elementi per processore

Ad ogni processo viene assegnato un blocco di *n/p* elementi. Il primo passaggio è una sorta di locale del blocco locale (v. Applicazioni). Ogni successiva operazione di confronto-scambio viene sostituita da un'operazione di confronto-suddivisione. Possiamo effettivamente visualizzare la rete bitonica come avente *(1 + log p)(log p)/2* passi.
