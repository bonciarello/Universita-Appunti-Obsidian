---
aliases: [parte 2, Performance]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## Legge di Amdahl

**Bestia nera** dei ricercatori nel campo del calcolo parallelo fino al 1988. Ha “**limitato**” la velocità di una macchina parallela a 20-50, indipendentemente dal numero di processori! Nel 1988, tuttavia, alcuni ricercatori dei Sandia National Labs ottennero una velocità di 1000 su una macchina da 1024 processori **senza violare** la legge di Amdahl. Come hanno fatto?

![[leggeAmdahl.png]]

## Speedup

Misura di quanto più velocemente viene eseguito il calcolo rispetto al miglior codice seriale: *tempo seriale diviso per tempo parallelo*.

> **ESEMPIO:** dipingere una staccionata
> - 30 minuti di preparazione (seriale)
> - 1 minuto per dipingere un singolo picchetto
> - 30 minuti di pulizia (seriale)
>
> Quindi, 300 picchetti impiegano 360 minuti (tempo seriale)

## Speedup di calcolo

![[speedupCalcolo.png]]

Il potenziale aumento di speedup è limitato dalla parte seriale. E se il proprietario del recinto usasse una pistola a spruzzo per dipingere 300 picchetti in un'ora?

- Migliore algoritmo seriale;
- Se non sono disponibili pistole a spruzzo per più lavoratori, qual è l'accelerazione parallela massima?

## Problema scalabile

I problemi che aumentano la percentuale di tempo parallelo con la loro dimensione sono più scalabili dei problemi con una percentuale fissa di tempo parallelo.

> **ESEMPIO:** alcuni problemi dimostrano un aumento delle prestazioni aumentando la dimensione del problema
>
> *2D GRID CALCULATIONS* - 85 sec - 85%
>
> *SERIAL FRACTION* - 15 sec - 15%
>
> Aumentare la dimensione del problema raddoppiando le dimensioni della griglia e dimezzando i tempi:
> - quattro volte il numero di punti della griglia
> - il doppio del numero di passi temporali
>
> *2D GRID CALCULATIONS* - 680 sec - 97,84%
>
> *SERIAL FRACTION* - 15 sec - 2,16%

## Speedup massimo (Legge di Amdahl)

![[speedupMassimo.png]]

## Speedup VS processori

![[speedupVSprocessori.png]]

Anche con un numero infinito di processori, velocità massima limitata a *1/f*. Esempio: con solo il 5% del calcolo seriale, lo speedup massimo è 20, indipendentemente dal numero di processori.

## Scalabilità

Come possiamo **estrapolare le prestazioni** da **piccoli** problemi e piccoli sistemi a problemi più **grandi** con **configurazioni più grandi**? L'esempio seguente mostra l'evoluzione dell'accelerazione di **tre algoritmi** per una FFT (Fast Fourier Transform) a *n* punti su 64 processori.

![[scalabilita1.png]]

Per piccoli valori di n, sembrerebbe che gli algoritmi di scambio binario e di trasposizione 3D siano i migliori, ma per *n > 18000*, l'algoritmo di trasposizione 2D consente una migliore accelerazione. La **morale** è che è difficile dedurre la scalabilità dei dati da osservazioni e macchine "piccole".

L'efficienza di un programma parallelo può essere scritta come:

> $E = \frac{S}{p} = \frac{T_{S}}{p T p}$

oppure, ricordando il concetto di overhead parallelo $T_{0} = p*T_{P} – T_{S}$, si ha:

> $E = \frac{1}{1 + \frac{T_{0}}{T_{S}}}$

Per un dato problema di dimensione (cioè il valore $T_{S}$ rimane costante), se aumentiamo il numero di processori *p*, **$T_{o}$ aumenta**. Al contrario, l'**efficienza** totale del programma **diminuisce**. Questo è il caso di tutti i programmi paralleli.

### Esempio

Considera il solito problema di sommare *n* numeri su *p* processori (**algoritmo di scala intelligente**). Abbiamo visto che (evitiamo l'analisi asintotica, lavoriamo con le costanti):

> $T_{p} = \frac{n}{p} + 2*\log p$
>
> $S = \frac{n}{\frac{n}{p} + 2*\log p}$
>
> $E = \frac{1}{1 + \frac{2*p*\log p}{n}}$

Se visualizziamo l'accelerazione per varie dimensioni di input, otteniamo:

![[scalabilita2.png]]

L'accelerazione tende a **saturare** e l'**efficienza diminuisce** come conseguenza della legge di Amdahl.

### Caratteristiche

La funzione di overhead totale $T_0$ è una funzione sia di $T_S$ (cioè la dimensione del problema) sia del numero di processori *p*.

In molti casi $T_0$ cresce in modo sub-lineare rispetto a $T_S$. In tali casi, l'**efficienza aumenta** se aumentano le dimensioni del problema, mantenendo costante il numero di processori. Per questi sistemi, possiamo **aumentare contemporaneamente** le dimensioni del problema e il numero di processori per mantenere costante l'efficienza (ma come?).

Tali sistemi sono chiamati **sistemi paralleli scalabili**.

In un sistema scalabile, l'efficienza rimane costante con l'aumento sia di *p* che di *n*. Ad esempio, nel caso della somma di *n* numeri su *p* processori, l'efficienza rimane costante a 0,8 se:

> $n = 8*p*\log p$

![[scalabilita3.png]]

### Conclusioni

Ricordiamo che i sistemi paralleli che sono **ottimali in termini di costi** hanno efficienza $\Theta(1)$. Pertanto, la **scalabilità** e l'**ottimizzazione dei costi** sono strettamente correlate. Un sistema parallelo **scalabile** può essere **ottimizzato in termini di costi** se il numero di processori e la dimensione del problema sono scelti in modo appropriato.

**In sintesi**, per una data dimensione del problema, se **aumentiamo** il numero di processori, l'efficienza complessiva del sistema **diminuisce** (per tutti i sistemi) e, per alcuni sistemi, **l'efficienza di un sistema parallelo aumenta se aumentano le dimensioni del problema mentre il numero di processi rimane costante**.

## Isoefficienza

I due concetti precedenti possono essere illustrati dalla seguente tabella:

![[isoefficienza1.png]]

Qual è il **tasso di crescita** della dimensione del problema rispetto al numero di processori per mantenere costante l'efficienza?

Questa velocità determina la **scalabilità** del sistema. Più piccolo è, meglio è. Prima di formalizzare questo concetto, dobbiamo definire formalmente quali sono le "**dimensioni del problema**".

Possibili soluzioni: **dimensioni dell'input? Non bene!**

Facciamo un esempio: sia *n* la dimensione dell'input di un problema di calcolo matriciale (es. somma di matrici, prodotto matrice-matrice). Se *n* viene raddoppiato, si avrà un incremento di 8 volte per un'operazione che coinvolge il **prodotto matrice – matrice**, mentre 4 volte se l'operazione è **somma di matrici**. Quindi, "dipende" dal problema. A tal fine, definiamo la **dimensione del problema W** il **numero asintotico di operazioni** associate al miglior algoritmo seriale per risolvere il problema (in pratica è il tempo sequenziale!).

### Formalismo

Il tempo parallelo può essere scritto come (ricorda che $W = T_S$):

> $T_p = \frac{W + T_0 (W, p)}{p}$

Quindi, lo speedup è:

> $S = \frac{W}{T_p} = \frac{W_p}{W + T_0 (W, p)}$

Alla fine, l'espressione diventa:

> $E = \frac{S}{p} = \frac{W}{W + T_0 (W, p)} = \frac{1}{1 + T_0 (W, p)/W}$

Per i sistemi paralleli scalabili, l'**efficienza** può essere mantenuta ad un valore fisso (tra 0 e 1), se il rapporto To/W è mantenuto costante. Per un dato valore ed efficienza:

> $E = \frac{1}{1 + T_0 (W, p)/W}$
>
> $\frac{T_0 (W, p)}{W} = \frac{1 - E}{E}$
>
> $W = \frac{E}{1-E}T_0 (W, p)$

Se *K = E/(1 - E)* è una costante che dipende dall'efficienza da mantenere, dato che $T_o$ è una funzione di *W* e *p*, si ha:

> $W = KT_0 (W, p)$

La formula precedente prende il nome di funzione **isoefficienza**.

Da questa formula risulta chiaro che le **dimensioni del problema W** possono essere ottenute in funzione di *p* tramite passaggi algebrici, in modo da mantenere **costante l'efficienza**.

Questa funzione determina la "**facilità**" con cui un sistema parallelo può mantenere un'efficienza costante e, di conseguenza, raggiungere uno **speedup** che aumenta **proporzionalmente al numero di processori**.

### Esempio

La funzione di overhead per il problema di sommare *n* numeri su *p* processori è approssimata da *2*p*log p*. Sostituendo $T_0$ con *2*p*log p*, si ottiene:

> $W = K*2*p*\log p$

Quindi, la funzione di isoefficienza asintotica per questo sistema parallelo è:

> $\Theta(p*\log p)$

Se il numero di processori viene **aumentato** da *p* a *p'*(cioè di un fattore *p'/p*), la dimensione del problema (in questo caso, *n*) deve essere aumentata di un fattore *(p'*log p')/(p*log p)* per ottenere la **stessa efficienza** dei processori *p*. In questo modo l'accelerazione **aumenta** di un fattore *p'/p*.

### Esempio (dal libro Pacheco)

Considera la versione sequenziale della regola trapezoidale:

```c
h = (b - a) / n;
integral = (f(a) + f(b)) / 2.0;
x = a;
for(i = 1; i <= n-1; i++) {
    x = x + h;
    integral = integral + f(x);
}
integral = integral * h;
```

Il tempo di esecuzione può essere approssimato da:

> $T_S (n) = c_1 + c_2 (n-1) = k_1 n + k_2 \approx	k_1 n$
>
> **dove *n* è il numero del trapezio**

Consideriamo la versione parallela della regola del trapezio:

```c
h = (b - a) / n;
local_n = n / p;

local_a = a + my_rank * local_n * h;
local_b = local_a + local_n * h;
integral = Trap(local_a, local_b, local_n, h);

MPI_Reduce(&integral, &total, 1, MPI_FLOAT, MPI_SUM, 0, MPI_COMM_WORLD);
```

Il tempo di esecuzione può essere approssimato da:

> $T_S (n, p) = k_1 n/p + k_2 \log_2 (p) + k_3$

Essendo:

> $T_0 = p * T_P - T_S$

Abbiamo (trascurando $k_3$):

> $T_0 = k_1 n + k_2 p \log_2 (p) - k_1 n = k_2 p \log_2 (p)$

Dalla definizione di efficienza:

> $E = \frac{1}{1 + \frac{T_0}{W}} = \frac{1}{1 + \frac{k_2 p \log_2 (p)}{k_1 n}} \Rightarrow n = \frac{E}{1 - E} \frac{k_2}{k_1} p \log_2 (p)$

La formula:

> $E = \frac{1}{1 + \frac{T_0}{W}} = \frac{1}{1 + \frac{k_2 p \log_2 (p)}{k_1 n}}$

ci dice che, per mantenere un'efficienza costante, *n* dovrebbe aumentare come $p \log_2 (p)$.

Ad esempio, sia *p=4* che *n=512*. Se vogliamo mantenere la stessa efficienza con 8 processori (cioè *2 p*), dobbiamo imporre (invece di raddoppiare *n* come si potrebbe pensare):

> $\frac{k_2 4 \log_2 (4)}{k_1 512} = \frac{k_2 8 \log_2 (8)}{k_1 n} \Rightarrow n = 1536$
