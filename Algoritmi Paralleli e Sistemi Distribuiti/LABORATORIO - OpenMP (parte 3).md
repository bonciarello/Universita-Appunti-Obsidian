## Consigli per OpenMP

Prova sempre pianificazioni diverse, ecc.
> **ESEMPIO:** la clausola *collapse* per *omp parallel for* specifica quanti cicli in un ciclo annidato devono essere compressi in un ampio spazio di iterazione e suddivisi in base alla clausola di pianificazione.
```c
#pragma omp parallel for private(j) collapse(2)
for(i = 0; i < 4; i++)
    for(j = 0; j < 100; j++)
```
> Il ciclo esterno ha solo quattro iterazioni. **Se hai più di quattro thread, alcuni andranno sprecati**. Ma quando si comprime i thread verranno distribuiti tra 400 iterazioni che è probabile che sia molto maggiore del numero di thread.

## Parallelismo funzionale

A questo punto tutta la nostra attenzione si è concentrata sullo sfruttamento del **parallelismo dei dati**. OpenMP ci consente di assegnare diversi thread a diverse parti di codice (**parallelismo funzionale**).

### Esempio

```c
v = alpha();
w = beta();
x = gamma(v, w);
y = delta();
printf ("%6.2f\n", epsilon(x,y));
```

Può eseguire alpha, beta e delta in parallelo.

![[parallelismoFunzionale.PNG]]

## Pragma parallel sections

Precede un blocco di *k* blocchi di codice che possono essere eseguiti contemporaneamente da *k* thread. Sintassi:

```c
#pragma omp parallel sections
```

Per impostazione predefinita, c'è una barriera alla fine delle "omp sections". Usa la clausola "nowait" per disattivare la barriera.

## Pragma section

Precede ogni blocco di codice all'interno del blocco di inclusione preceduto dal pragma *parallel sections*. Può essere omesso per la prima sezione parallela dopo il pragma *parallel sections*. Sintassi:

```c
#pragma omp section
```

### Esempio

```c
#pragma omp parallel sections
{
	#pragma omp section /* Optional */
		v = alpha();
	#pragma omp section
		w = beta();
	#pragma omp section
		y = delta();
}
x = gamma(v, w);
printf("%6.2f\n", epsilon(x,y));
```

![[parallelismoFunzionale.PNG]]

Esegui alpha e beta in parallelo. Esegui gamma e delta in parallelo. Il pragma *section* appare all'interno di un blocco parallelo di codice. Ha lo stesso significato del pragma delle sezioni parallele. Se ci sono più pragma *sections* all'interno di un blocco parallelo, si possono ridurre i costi di fork/join.

## Utilizzo delle pragma sections

```c
#pragma omp parallel
{
	#pragma omp sections
	{
		v = alpha();
		#pragma omp section
			w = beta();
	}
	#pragma omp sections {
		x = gamma(v, w);
		#pragma omp section
			y = delta();
	}
}
printf ("%6.2f\n", epsilon(x,y));
```

![[clausolaSection1.png]]

![[clausolaSection2.PNG]]

## Pragma single

Supponiamo di voler vedere l'output solo una volta. Il pragma *single* indica al compilatore che solo un singolo thread deve eseguire il blocco di codice che il pragma precede. Sintassi:

```c
#pragma omp single
```

```c
#pragma omp parallel private(i,j)
	for (i = 0; i < m; i++) {
		low = a[i];
		high = b[i];
		if (low > high) {
			#pragma omp single
				printf ("Exiting (%d)\n", i);
				break;
		}
		#pragma omp for
			for (j = low; j < high; j++)
				c[j] = (c[j] - a[i])/b[i];
}
```

## OpenMP: costrutto task

Dalla versione 3.0, OpenMP fornisce un modo più efficiente per gestire il parallelismo delle attività rispetto alle sezioni attraverso l'attività. **Entrare e uscire da regioni parallele con sections spesso comporta un sovraccarico maggiore rispetto all'esecuzione delle attività**. **Osservazione:** con le sezioni, il numero di thread utilizzati in parallelo per ciascuna regione viene determinato quando viene creata la regione parallela. Una volta creata una regione parallela, nessun thread del team può lasciare la regione fino alla fine della regione parallela. Inoltre, **nessun altro thread può unirsi al team**.

```c
#pragma omp task [clause [[,]clause] ...] structured-block
```

```c
#pragma omp taskwait
```

L'attività corrente sospende l'esecuzione fino a quando tutte le attività figlio, generate all'interno dell'attività corrente fino a questo punto, non hanno completato l'esecuzione.

Le task sono unità di lavoro indipendenti. Un thread viene assegnato per eseguire un'attività. Le task potrebbero essere eseguite immediatamente o potrebbero essere differite. Il sistema runtime decide quale delle precedenti.

## Osservazioni sul costrutto task

La direttiva della **task** può richiedere una **clausola "if"** per far sì che l'attività venga eseguita immediatamente o rinviata; un thread può essere "cablato" a un'attività (chiamato "legato") o può essere "slegato", il che consente a qualsiasi thread disponibile nel pool di thread di avviare l'esecuzione dell'attività.

Tasking ha **prestazioni e scalabilità molto migliori** per algoritmi paralleli e ricorsivi annidati, rispetto alle sezioni parallele.

C'è **molto più sovraccarico** nella creazione e distruzione delle regioni parallele annidate (tasking di sezioni parallele) rispetto all'esecuzione di attività che sono tutte create da una singola regione parallela contenente una direttiva di attività.

È possibile **controllare il numero totale di thread** (OMP_NUM_THREADS) con le tasking, mentre con le regioni parallele nidificate (sezioni), con ogni regione appena creata si ottengono nuovi thread OMP_NUM_THREADS e questo può facilmente sottoscrivere in eccesso l'host.

## Esempi di revisione sui task

### Esempio 1

```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    printf("Hello ");
    printf("World ");
    printf("\n");
    return 0;
}
```

Cosa restituisce?

```bash
$ cc hello.c
$ ./a.out
Hello World
$
```

### Esempio 2

```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    #pragma omp parallel
    {
        printf("Hello ");
        printf("World ");
    }
    printf("\n");
    return 0;
}
```

Cosa restituisce?

```bash
$ cc-xopenmp -fast hello.c
$ export OMP_NUM_THREADS = 2
$ ./a.out
Hello World Hello World
```

Nota che questo programma potrebbe anche stampare "Hello Hello World World".

### Esempio 3

```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    #pragma omp parallel
    {
        #pragma omp single
        {
            printf("Hello ");
            printf("World ");
        }
    }
    printf("\n");
    return 0;
}
```

Cosa restituisce?

```bash
$ cc-xopenmp -fast hello.c
$ export OMP_NUM_THREADS = 2
$ ./a.out
Hello World
```

Ma ora viene eseguito solo 1 thread.

### Esempio 4

```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    #pragma omp parallel
    {
        #pragma omp single
        {
            #pragma omp task
            {
                printf("Hello ");
            }
            #pragma omp task
            {
                printf("World ");
            }
        }
    }
    printf("\n");
    return 0;
}
```

Cosa restituisce?

```bash
$ cc-xopenmp -fast hello.c
$ export OMP_NUM_THREADS = 2
$ ./a.out
Hello World
$
./a.out
Hello World
$
./a.out
World Hello
$
```

Le task possono essere eseguite in ordine arbitrario.

### Esempio 5

```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    #pragma omp parallel
    {
        #pragma omp single
        {
            #pragma omp task
            {
                printf("Hello ");
            }
            #pragma omp task
            {
                printf("World ");
            }
            printf("\nThank You ")
        }
    }
    printf("\n");
    return 0;
}
```

Cosa restituisce?

```bash
$ cc-xopenmp -fast hello.c
$ export OMP_NUM_THREADS = 2
$ ./a.out

Thank You World Hello
$
./a.out

Thank You Hello World
$
./a.out

Thank You World Hello
$
```

Le task vengono eseguite in un punto di esecuzione delle task.

### Esempio 6

```c
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    #pragma omp parallel
    {
        #pragma omp single
        {
            #pragma omp task
            {
                printf("Hello ");
            }
            #pragma omp task
            {
                printf("World ");
            }
            #pragma omp taskwait
                printf("\nThank You ")
        }
    }
    printf("\n");
    return 0;
}
```

Cosa restituisce?

```bash
$ cc-xopenmp -fast hello.c
$ export OMP_NUM_THREADS = 2
$ ./a.out
World Hello
Thank You
$
./a.out
World Hello
Thank You
$
./a.out
Hello World
Thank You
$
```

Le task vengono eseguite per prime ora.

## Quando finiscono le attività?

- alla thread barrier implicita
- alla thread barrier esplicita (*#pragma omp barrier*)
- alla task barrier (*#pragma omp taskwait*)

## Esempio di Fibonacci

I numeri di Fibonacci sono definiti come segue:
```c
F(0) = 1
F(1) = 1

F(n) = F(n-1) + F(n-2) (n=2, 3, 4, ...)

// sequenza: 1, 1, 2, 3, 5, 8, 13, 21, 34, ...
```

Un codice risolutivo potrebbe essere:

```c
long comp_fib_numbers(int n) {
    long fnm1, fnm2, fn;

    if(n == 0 || n == 1) return n;
    if(n < 20) return comp_fib_numbers(n-1) + comp_fib_numbers(n-2);

    #pragma omp task shared(fnm1) {
        fn1 = comp_fib_numbers(n-1);
    }
    #pragma omp task shared(fnm2) {
        fn2 = comp_fib_numbers(n-2);
    }
    #pragma omp taskwait
    fn = fn1 + fn2;

    return fn;
}

int main() {
    #pragma omp parallel shared(nthreads) {
        #pragma omp simple nowait {
            result = comp_fib_numbers(n);
        }
    }
}
```

