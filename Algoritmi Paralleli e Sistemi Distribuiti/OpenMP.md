---
aliases: [openmp]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
In base alle direttive del compilatore, può utilizzare il codice seriale. Definito da un gruppo di società di hardware/software. L'API Fortran è stata rilasciata il 28 ottobre 1997. L'API C/C++ è stata rilasciata nel 1998. Dal rilascio di OpenMP 4, è supportata anche la GPGPU! Portatile / multipiattaforma, include Unix e Windows NT. Disponibile in C/C++ e Fortran. Può essere molto facile e semplice da usare. Consente un approccio incrementale al parallelismo (cioè la capacità di "convertire" un programma sequenziale in parallelo, "a poco a poco").

## Perché OpenMP?

- **Modelli di parallelismo incrementale:** è necessario proiettare il parallelismo all'intero programma, ma è possibile applicare direttive di condivisione del lavoro, ecc. Per estendere porzioni di codice e diverse strategie possono essere applicate nel codice;
- **Modello di parallelismo locale:** applicazioni di direttive di condivisione del lavoro che distribuiscono iterazioni di loop tra thread che sono computazionalmente costosi e funzioni che possono essere eseguite in parallelo (decomposizione di dati/funzioni).

Un parallelismo significativo può essere implementato utilizzando solo 3 o 4 direttive.

## Definizioni di base OpenMP: stack di soluzioni

![[stackSoluzioni.PNG]]

## OpenMP vs. MPI

| **Caratteristiche** | **OpenMP** | **MPI** |
| :--- | :---: | :---: |
| Adatto per multiprocessori | *SI* | *SI* |
| Adatto per multicomputer | *NO* | *SI* |
| Supporta la parallelizzazione incrementale | *SI* | *NO* |
| Codice extra minimo | *SI* | *NO* |
| Controllo esplicito della gerarchia della memoria | *NO* | *SI* |

## Modello concettuale di esecuzione

Il modello concettuale di OpenMP consiste in un'esecuzione alternata seriale e parallela, chiamata *"fork/join"*. I passi sono:

- all'inizio e alla fine c'è un processo seriale (processo master);
- in alcune parti (regioni parallele) vengono lanciati alcuni thread indipendenti (**fork**);
- ogni thread può eseguire operazioni simultanee;
- alla fine della parte parallela, il thread principale attende tutti i thread (**join**);
- il thread principale continua in sequenza.

## Modello fork/join

Costruzione regione parallela. Lo scopo è che una regione parallela è un blocco di codice che verrà eseguito da più thread. **Questo è il costrutto fondamentale di OpenMP!**

![[modelloForkJoin1.PNG]]

![[modelloForkJoin2.PNG]]

## Modello di memoria OpenMP

Tutti i thread hanno accesso alla stessa memoria, condivisa a livello globale. I dati possono essere condivisi o privati. I dati condivisi sono accessibili da tutti i thread. È possibile accedere ai dati privati solo dal thread che li possiede. Il trasferimento dei dati è trasparente per il programmatore. La sincronizzazione avviene, ma è per lo più implicita.

![[modelloMemoriaOpenMP.PNG]]

## Presentazione generale

- **direttive del compilatore:** considerato come commenti per le lingue supportate, per mantenere versioni seriali e compilatori OpenMP non compatibili;
- **funzioni di libreria:** permette di collegare il codice a OpenMP (i prototipi di funzione devono essere inclusi) e permette di determinare/modificare il numero di thread adottati;
- **variabili ambientali**.

## Componenti

![[componentiOpenMP.PNG]]

## Sintassi principale

La maggior parte dei costrutti in OpenMP sono direttive del compilatore.

```c
#pragma omp construct [clause[clause]...]
```

Ad esempio:

```c
#pragma omp parallel num_threads(4)
```

Prototipi e tipi di funzioni nel file:

```c
#include <omp.h>
```

La maggior parte dei costrutti OpenMP si applica a un **blocco strutturato**: un blocco di una o più istruzioni con un punto di entrata in alto e un punto di uscita in basso. Va bene avere un *exit()* all'interno del blocco strutturato.

## Come interagiscono i thread?

OpenMP è un modello di indirizzi condivisi multi-threading, cioè i thread comunicano condividendo le variabili.

La condivisione involontaria dei dati causa condizioni di competizione cioè il risultato del programma cambia poiché i thread sono programmati in modo diverso.

Per controllare le condizioni di gara, si usa la sincronizzazione per proteggere i conflitti di dati.

La sincronizzazione è costosa, quindi si modifica la modalità di accesso ai dati per ridurre al minimo la necessità di sincronizzazione.

## Pragma: direttive di pre-elaborazione sovrascrivibili

Il pragma è una direttiva del compilatore in C o C++ e sta per "informazione pragmatica". È un modo per il programmatore di comunicare con il compilatore ed il compilatore è libero di ignorare i pragmi. La sintassi è la seguente:

```c
#pragma omp <rest of pragma>
```

In C/C++:

```c
#pragma omp directive [clause[[,] clause]...]
Structured block
```

## Mantieni la versione seriale!

Utilizzando le funzioni della libreria OpenMP, la versione seriale non viene preservata: nessuna compilazione ha luogo se il compilatore non supporta OpenMP.

Le soluzioni ampiamente utilizzate consistono nella cosiddetta compilazione condizionale: se il compilatore non supporta OpenMP verrà ignorato, diverso per C e per Fortran.

In C/C++:

```c
#ifdef_OPENMP
iam = omp_get_thread_num();
#endif
```

## Librerie runtime

- *OMP_GET_NUM_THREADS()* restituisce il numero corrente di thread;
- *OMP_GET_THREAD_NUM()* restituisce l'ID di questo thread;
- *OMP_SET_NUM_THREADS(n)* imposta il numero di thread desiderato;
- *OMP_IN_PARALLEL()* restituisce TRUE se all'interno della regione parallela;
- *OMP_GET_MAX_THREADS()* restituisce il numero di thread possibili.

## Quanti thread?
Il numero di thread in una regione parallela è determinato dai seguenti fattori:
1. utilizzo della clausola NUM_THREADS;
2. uso della funzione di libreria omp_set_num_threads();
3. impostazione della variabile d'ambiente OMP_NUM_THREADS;
4. l'impostazione predefinita dell'implementazione.

I thread sono numerati da 0 (thread principale) a N-1.

## Ottenere i tempi

Restituisce il numero di processori fisici (core?) disponibili per l'uso dal programma parallelo:

```c
int omp_get_num_procs(void);
```

Per ottenere i tempi (in secondi da un punto fisso nel passato):

```c
double omp_get_wtime();
```

## Esempio: omp_get_wtime

```c
#include "omp.h"
#include <stdio.h>

int main(){
	double start = omp_get_wtime();
	sleep(10);
	double end = omp_get_wtime();

	printf("start: %.16g\n end: %.16g\n Time:
		%.16g\n", start, end, end-start);

	return 0;
}
```

## Identificazione dei thread

La funzione OpenMP per identificare i thread è *omp_get_thread_num*. In C/C++:

```c
int omp_get_thread_num(void)
```

Ogni thread riceve un valore restituito diverso. Il thread master riceve 0 mentre gli altri thread ottengono: 1, 2, 3,... N-1 con thread.

## omp_get_thread_num

Come si usa? Si include l'intestazione del file:

```c
#include "omp.h"
```

In una regione parallela per consentire a ogni thread di lavorare su dati diversi. Cosa succede fuori dalla regione parallela? Restituisce 0.

## Modifica del numero di thread

La variabile d'ambiente OMP_NUM_THREADS specifica il numero massimo di thread che verranno creati nella regione parallela: per ragioni di efficienza, l'implementazione può ignorare la dichiarazione.

Alla riga di comando:
- *setenv OMP_NUM_THREADS 5* for csh
- *export OMP_NUM_THREADS=5* for bash

## Threads: fork e join

La direttiva PARALLEL crea una regione parallela (**fork**), dove oltre a un **master thread** (che esegue il codice seriale) viene creato un **numero variabile di thread**. Da qui, tutti i thread eseguono il codice **contemporaneamente** e **indipendentemente**: ogni thread esegue lo stesso codice. Solo il thread principale continua alla fine della regione parallela (**join**).

```c
#pragma omp parallel [clause list]
/* blocco strutturato */
```

## Pthreads? No grazie!

![[pthreads.PNG]]

Confronto tra OpenMP e il programma corrispondente scritto con Pthreads.

## Regioni parallele

![[regioniParallele1.PNG]]

![[regioniParallele2.PNG]]

## Sviluppo di "Hello World"

```c
#include <omp.h>
#include <stdio.h>
int main() {
		int nthreads, tid;

	/* Fork a team of threads with each thread having a private tid variable */
	#pragma omp parallel private(tid)
	{
		/* Obtain and print thread id */
		tid = omp_get_thread_num();
		printf("Hello World from thread = %d\n", tid);
		/* Only master thread does this */
		if (tid == 0)
		{
			nthreads = omp_get_num_threads();
			printf("Number of threads = %d\n", nthreads);
		}
	} /* All threads join master thread and terminate */
}
```

Per compilare:

```bash
gcc -fopenmp hello.c -o hello
```

## Thread: contesto di esecuzione

Ogni thread ha il proprio **contesto di esecuzione**. Il contesto di esecuzione è lo spazio degli indirizzi contenente tutte le variabili a cui un thread può accedere. Contenuti del contesto di esecuzione:
- variabili statiche
- strutture dati allocate dinamicamente nell'heap
- variabili nello stack di runtime
- stack di run-time aggiuntivo per le funzioni invocate dal thread

## Variabili condivise e private

La **variabile condivisa** ha lo stesso indirizzo nel contesto di esecuzione di ogni thread. La **variabile privata** ha un indirizzo diverso nel contesto di esecuzione di ogni thread. Un thread non può accedere alle variabili private di un altro thread!

Privata (elenco):
- nessuna associazione di archiviazione con l'oggetto originale;
- tutti i riferimenti sono all'oggetto locale;
- i valori non sono definiti in entrata e in uscita.

Condivisa (elenco):
- i dati sono accessibili da tutti i thread del team;
- tutti i thread accedono allo stesso spazio degli indirizzi.

![[variabiliCP1.PNG]]

![[variabiliCP2.PNG]]

Per impostazione predefinita, le variabili sono di tipo condiviso, mentre le variabili di ciclo sono private.

## Clausola "private"

*private(var)* crea una copia locale di var per ogni thread: il valore non è inizializzato e la copia privata NON è associata all'archiviazione dell'originale.

![[clausolaPrivate.PNG]]

## Calcolo della divisione: costrutti di condivisione del lavoro

Divide l'esecuzione della regione chiusa tra i membri della squadra che la incontrano. I costrutti di condivisione del lavoro non avviano nuovi thread. Nessuna barriera implicita all'ingresso in un costrutto di condivisione del lavoro. Tuttavia, esiste una barriera implicita alla fine del costrutto di condivisione del lavoro (a meno che non venga utilizzato **nowait**).

## Costrutti di condivisione del lavoro: parallelismo del ciclo

Parallelismo a livello di loop: parallelizza solo i loop! È facile da implementare, il codice altamente leggibile, le prestazioni non ottimali (a volte) e molto spesso è utilizzato in OpenMP.

![[parallelismoCiclo.PNG]]

## parallel/for

La differenza tra *parallel*, *parallel for* e *for* è la seguente: un team è il gruppo di thread che vengono eseguiti attualmente; all'inizio del programma, il team è composto da un unico thread; una direttiva parallela divide il thread corrente in un nuovo gruppo di thread per la durata del blocco/istruzione successivo, dopodiché il gruppo si fonde di nuovo in uno solo.
- *for* divide il lavoro del ciclo for tra i thread del **team corrente**. Non crea thread, divide solo il lavoro tra i thread del team attualmente in esecuzione;
- *parallel for* è un'abbreviazione per due comandi contemporaneamente: *parallel* e *for*;
- *parallel* crea una nuova squadra e, per le divisioni, quella squadra gestisce parti diverse del ciclo.

Quindi, se il tuo programma **non contiene mai una direttiva parallela**, non c'è mai più di un thread; il thread principale che avvia il programma e lo esegue, come nei programmi non threading.

## Costrutto combinato parallelo/condivisione del lavoro

Scorciatoia OpenMP mette la direttiva "parallela" e di condivisione del lavoro sulla stessa riga.

![[costruttoCombinatoParallelo.PNG]]

Per esempio:

![[esempioCCP.PNG]]

![[risultatoCCP.PNG]]

## Costrutti di condivisione del lavoro in loop

I costrutti di condivisione del lavoro in loop dividono le iterazioni del loop tra i thread di un team.

![[costruttiCondivisione.PNG]]

Un esempio:

![[costruttiCondivisioneEsempio.PNG]]

## Condivisione dei dati: clausola Firstprivate

*Firstprivate* è un caso speciale di private: inizializza ogni copia privata con il valore corrispondente dal thread principale.

![[firstprivate.PNG]]

## Condivisione dei dati: clausola Lastprivate

*Lastprivate* passa il valore di un private dall'ultima interazione a una variabile globale.

![[lastprivate.PNG]]

## Esempio: Matrix - prodotto vettoriale

![[prodottoVettoriale.png]]

## Clausola if

L'*if (espressione scalare)* esegue in parallelo solo se l'espressione è vera, altrimenti, esegue in serie.

![[clausolaIF.PNG]]

## Clausola barrier

Supponiamo di eseguire ciascuno di questi due loop in parallelo su i.

![[barrier1.PNG]]

Questo potrebbe darci una risposta sbagliata (un giorno): PERCHÉ?

Dobbiamo prima aver aggiornato tutto A[], prima di utilizzare A[].

![[barrier2.PNG]]

Tutti i thread attendono al punto di barriera e continuano solo quando TUTTI i thread hanno raggiunto il punto di barriera. Se c'è la garanzia che la mappatura delle iterazioni sui thread è identica per entrambi i cicli, non ci sarà alcuna corsa di dati.

La sintassi della barriera sarà:

![[barrier3.PNG]]

## Clausola nowait

Per ridurre al minimo la sincronizzazione, alcune direttive/pragma OpenMP supportano la clausola nowait opzionale: se presenti, i thread non si sincronizzano per quel particolare costrutto.

![[clausolaNoWait.PNG]]

### Esempio

![[esempioNoWaitBarrier.PNG]]

## Sincronizzazione: barrier

Barrier: ogni thread attende l'arrivo dei thread.

![[sincronizzazioneBarrier.PNG]]

## Quando usare le barriere?

Vengono usati se i dati vengono aggiornati in modo asincrono e l'integrità dei dati è a rischio. Esempi:
- tra le parti del codice che leggono e scrivono la stessa sezione di memoria;
- dopo un timestep / iterazione in un risolutore.

Sfortunatamente, le barriere tendono ad essere costose e potrebbero non essere scalabili a un numero elevato di processori. Le serrature che possono essere utilizzate sono:

```c
omp_init_lock(), omp_set_lock(), omp_unset_lock(), omp_test_lock(), omp_destroy_lock()
```

```
## Assegnazione di iterazioni ai thread

La clausola di pianificazione della direttiva for si occupa dell'assegnazione di iterazioni ai thread. La forma generale della direttiva di pianificazione è:

```c
pianificazione (scheduling_class [, parameter])
```

OpenMP supporta quattro classi di pianificazione: statica, dinamica, guidata e runtime.

## Pianificazione statica e dinamica

*Pianificazione statica:* basso sovraccarico è può mostrare un elevato squilibrio del carico di lavoro. *Pianificazione dinamica:* sovraccarico più alto e può ridurre lo squilibrio del carico di lavoro.

## Chunks

Un chunk è un intervallo contiguo di iterazioni. L'**aumento** della dimensione del blocco riduce il sovraccarico e può aumentare la percentuale di riscontri nella cache (buono!). La **riduzione** della dimensione del blocco consente un bilanciamento più preciso dei carichi di lavoro (buono!).

## Clausola schedule static

```c
Schedule (static[,n])
```

Gruppo di n numeri interi consecutivi assegnati una volta per tutte ai thread sulla base del loro id *(omp_get_thread_num)*, ciclicamente fino a coprire il numero di iterazioni totali.

Se n non è specificato, le iterazioni vengono divise in un gruppo di n interi consecutivi di dimensioni che sono approssimativamente uguali e assegnate ai thread sulla base del loro id.

## Clausola schedule dynamic, guided e runtime

```c
Schedule (dynamic[,n])
```

Set di n thread consecutivi assegnati al primo thread che li richiede, fino al termine di tutte le iterazioni. Ogni thread chiede un gruppo di thread ed esegue, chiede, esegue, ecc: il partizionamento non è determinato a priori, dipende dall'esecuzione... Se n non è specificato, il valore è 1.

```c
Schedule (guided[,n])
```

Come dinamico, con parti crescenti.

```c
Schedule (runtime)
```

La scelta dei tre tipi disponibili è determinata dalle variabili d'ambiente...

![[clausolaRuntime.PNG]]

## Esempio di Schedule

![[esempioSchedule.PNG]]

## Quando usarli?

- **Clausola di pianificazione *static*:** da utilizzare quando predeterminato e prevedibile dal programmatore (lavoro minimo in fase di esecuzione: pianificazione eseguita in fase di compilazione);
- **Clausola di pianificazione *dynamic*:** da utilizzare in caso di lavoro imprevedibile e altamente variabile per iterazione (la maggior parte lavora in fase di esecuzione: logica di pianificazione complessa utilizzata in fase di esecuzione);
- **Clausola di pianificazione *guided*:** da utilizzare quando un caso speciale di dinamico per ridurre l'overhead di pianificazione.

## Esempio

![[esempioClausoleSchedule.PNG]]

## Sezioni critiche in OpenMP

La direttiva **critica** viene utilizzata per eseguire parte di un codice un thread alla volta. Protegge l'accesso alle **variabili condivise**. Non fornisce una **barriera implicita** alla fine:
- ogni thread entra nella parte di codice "critica";
- esegue le istruzioni;
- una volta terminata questa parte, continua senza attendere altri thread.

Se sum è una variabile condivisa, questo ciclo non può essere eseguito in parallelo.

```c
for(i = 0; i < n; i++) {
    ...
    sum += a[i];
    ...
}
```

Dobbiamo utilizzare una regione critica per questo:

```c
for(i = 0; i < n; i++) {
    ...
    // uno alla volta puo' procedere
    sum += a[i];
    // il prossimo in linea, per favore
    ...
}
```

Il costrutto *atomic* è utile per evitare una race condition o per eseguire I / O (ma ha ancora un ordine casuale) Tieni presente che esiste un costo associato a una regione critica.

![[sezioniCriticheAtomic.PNG]]

In C/C++:

```c
#pragma omp critical[(name)] new-line
    structured-block
```

Un thread attende all'inizio di un'area critica finché nessun altro thread nel team esegue un'area critica con lo stesso nome. Tutte le direttive *critical* senza nome vengono mappate sullo stesso nome non specificato.

![[sezioniCriticheCodice.PNG]]

## Costrutto Atomic

- *Critical*, tutti i thread eseguono il codice, ma solo uno alla volta:

```c
#pragma omp critical [(name)]
{ <code-block> }
```

Non ci sono barriere implicite all'ingresso o all'uscita!

- *Atomic*, solo i carichi e la memoria sono atomici:

```c
#pragma omp atomic
    <statement>
```

Questa è una forma speciale e leggera di una sezione critica. Per esempio:

```c
#pragma omp atomic
    a[indx[i]] += b[i];
```

## Sincronizzazione: Atomic
*Atomic* fornisce l'esclusione reciproca, ma si applica solo all'aggiornamento di una posizione di memoria (l'aggiornamento di X nell'esempio).

```c
#pragma omp parallel
{
    double tmp, B;
    B = DOIT();
    tmp = big_ugly(B);
#pragma omp atomic
    X += tmp;
    // atomic protegge solo la lettura / aggiornamento di X
}
```

## Calcolo del PI-GRECO, programma seriale e condizioni di gara

![[calcoloPiGreco.png]]

```c
static long num_steps = 100000;
double step;
int main() {
    int i; double x, pi, sum = 0.0;
    step = 1.0/(double) num_steps;
    for(i = 0; i < num_steps; i++) {
        x = (i+0,5)*step;
        sum = sum + 4.0/(1.0+x*x);
    }
    pi = step*sum;
}
```

Abbiamo impostato una condizione di gara in cui un processo può "correre più avanti" di un altro e non vedere il suo cambiamento nella variabile condivisa *sum*.

![[raceCondition.PNG]]

![[raceConditionTimeline.PNG]]

## VERSIONE 1: un semplice programma PI-GRECO parallelo

```c
#include <omp.h>
static long num_steps = 100000; double step;
#define NUM_THREADS 2

void main() {
    int i, nthreads; double pi, sum[NUM_THREADS];
    /*
    promuovere scalare in un array dimensionato in base al numero di thread per evitare condizioni di competizione
    */
    step = 1.0/(double) num_steps;
    omp_set_num_threads(NUM_THREADS);
    #pragma omp parallel
    {
        int i, id, nthrds;
        double x;
        id = omp_get_thread_num();
        nthrds = omp_get_num_threads();

        // distribuzione ciclica

        for(i=id, sum[id]=0.0; i<num_steps; i=i+nthrds) { // #pragma omp for?

            /*
            questo e' un trucco comune nei programmi SPMD per creare una distribuzione ciclica di iterazioni di loop
            */

            x = (i+0.5)*step;
            sum[id] += 4.0/(1.0+x*x);
        }
    }

    for(i=0, pi=0.0; i<nthrds; i++) pi += sum[i]*step;
}
```

## Partizionamento ciclico e a blocchi

Il precedente ciclo for può essere partizionato in modo ciclico o a blocchi.

![[partizionamentoCiclicoABlocchi.PNG]]

## VERSIONE 2 con i block

```c
#include <omp.h>
static long num_steps = 100000; double step;
#define NUM_THREADS 2

void main() {
    int i, nthreads; double pi, sum[NUM_THREADS];
    /*
    promuovere scalare in un array dimensionato in base al numero di thread per evitare condizioni di competizione
    */
    step = 1.0/(double) num_steps;
    omp_set_num_threads(NUM_THREADS);
    #pragma omp parallel
    {
        int i, id, nthrds;
        double x;
        id = omp_get_thread_num();
        nthrds = omp_get_num_threads();

        // distribuzione ciclica

        // for(i=id, sum[id]=0.0; i<num_steps; i=i+nthrds)
        /*
        Si usa
            #pragma omp for
        mettendo
            for(i=0; i<num_steps: i++)
        */
        {
            x = (i+0.5)*step;
            sum[id] += 4.0/(1.0+x*x);
        }
    }

    for(i=0, pi=0.0; i<nthrds; i++) pi += sum[i]*step;
}
```

## SPMD: Single Program Multiple Data

Eseguire lo stesso programma su elementi di elaborazione P (thread, here)m dove P può essere arbitrariamente grande. Utilizza il rango, un ID compreso tra 0 e (P-1), per scegliere tra un insieme di attività e per gestire eventuali strutture dati condivise.

> Questo modello è molto generale ed è stato utilizzato per supportare la maggior parte (se non tutti) i modelli di strategia dell'algoritmo.
> I programmi MPI usano quasi sempre questo pattern... è probabilmente il pattern più comunemente usato nella storia della programmazione parallela.

## gcc/g++ -O ottimizzazioni del compilatore

![[ottimizzazioniCompilatore.PNG]]

Modificando le ottimizzazioni del compilatore, è possibile ottenere tempi di esecuzione diversi per la versione seriale... ma anche per quella parallela! Prendi tempi diversi con diverse opzioni del compilatore per valutare gli aumenti di velocità, ecc...

## Perché un ridimensionamento così scarso? Falsa condivisione!

Se gli elementi di dati indipendenti si trovano sulla stessa riga di cahe, ogni aggiornamento memorizzerà le righe nella cache per scorrere avanti e indietro tra i thread, e questo porta ad una falsa condivisione.

![[falseSharing.PNG]]

Se promuovi gli scalari in un array per supportare la creazione di un programma SPDM, gli elementi dell'array sono contigui nella memoria e quindi condividono le linee della cache... si traduce in una scarsa scalabilità. La soluzione: gli array di pad in modo che gli elementi che vedi si trovino su linee di cache distinte.

## Padding e il risultato

```c
#include <omp.h>
static long num_steps = 100000; double step;
#define PAD 8 // assume 64 byte L1 cache line size
#define NUM_THREADS 2

void main() {
    int i, nthreads; double pi, sum[NUM_THREADS][PAD];
    /*
    riempire l'array in modo che ogni valore della somma si trovi in una linea di colore diversa
    */
    step = 1.0/(double) num_steps;
    omp_set_num_threads(NUM_THREADS);
    #pragma omp parallel
    {
        int i, id, nthrds;
        double x;
        id = omp_get_thread_num();
        nthrds = omp_get_num_threads();

        for(i=id, sum[id]=0.0; i<num_steps; i=i+nthrds) {
            x = (i+0.5)*step;
            sum[id][0] += 4.0/(1.0+x*x);
        }
    }

    for(i=0, pi=0.0; i<nthrds; i++) pi += sum[i][0]*step;
}
```

Ecco qui la tabella con i tempi di esecuzione dei threads:
![[paddingTempi.PNG]]

## Il padding serve davvero?

Il riempimento degli array richiede una conoscenza approfondita dell'architettura della cache. Problemi di portabilità se il programma viene eseguito su un'architettura diversa (diverse dimensioni delle righe della cache!). Deve esserci un modo migliore per affrontare la falsa condivisione...

## Sezioni critiche per rimuovere la falsa condivisione e il nuovo risultato

```c
#include <omp.h>
static long num_steps = 100000; double step;
#define NUM_THREADS 2

void main() {
    double pi;
    step = 1.0/(double) num_steps;
    omp_set_num_threads(NUM_THREADS);
    #pragma omp parallel
    {
        int i, id, nthrds;
        double x, sum;
        /*
        questa volta sum e' privato per ogni thread

        creare un locale scalare per ogni thread per accumulare somme parziali
        */
        id = omp_get_thread_num();
        nthrds = omp_get_num_threads();
        if(id == 0) nthreads = nthrds;

        for(i=id, sum[id]=0.0; i<num_steps; i=i+nthrds) { // #pragma omp for?
            x = (i+0.5)*step;
            sum[id] += 4.0/(1.0+x*x);
            // nessun array, quindi nessuna falsa condivisione
        }
    }
    #pragma omp critical
        pi += sum*step;
        /*
        Sum va "fuori campo" oltre la regione parallela... quindi devi sommarlo qui. Deve proteggere la somma in pi in una regione critica in modo che gli aggiornamenti non siano in conflitto.
        */
}
```

Ecco qui la tabella con i tempi di esecuzione dei threads:
![[criticalSectionsTempi.PNG]]

Dai attenzione a dove metti una sezione critica! Cosa succederebbe se inserissi la sezione critica all'interno del ciclo for? Critical viene eseguito "troppe" volte e quindi bisogna fare attenzione.

## Riduzioni
Le riduzioni sono così comuni che OpenMP fornisce il supporto per esse. Può aggiungere clausole di riduzione al parallelo per pragma. Specificare un'**operazione di riduzione** e una **variabile di riduzione**. OpenMP si occupa di memorizzare i risultati parziali in variabili private e combinare i risultati parziali dopo il ciclo.

![[riduzione.PNG]]

## Clausola reduction

La clausola *reduction* ha questa sintassi:

```c
reduction (<op> :<variable>)
```

Le operazioni che si possono fare sono:
- + (somma);
- * (prodotto);
- & (bitwise and);
- $|$ (bitwise or);
- $\wedge$ (b itwise exclusive or)
- && (and logico);
- $||$ (or logico)

## Esempio di reduction
```c
#include <omp.h>
#define NUM_THREADS 2

void main() {
    int i;
    double ZZ, func(), sum=0.0;

    omp_set_num_threads(NUM_THREADS);
    #pragma omp parallel for reduction(+:sum) private(ZZ)

    for(i=0; i < 1000; i++) {
        ZZ = func(i);
        sum = sum + ZZ;
    }
}
```

## PI-GRECO-ricerca del codice con clausola reduction e altro metodo per il calcolo del PI-GRECO

```c
double area, pi, x;
int i, n;
...
area = 0.0;
#pragma omp parallel for \
    private(x) reduction(+:area)
for (i = 0; i < n; i++) {
    x = (i + 0.5)/n;
    area += 4.0/(1.0 + x*x);
}
pi = area / n;
```

Un altro metodo di calcolo del PI-GRECO è il calcolo di Monte Carlo: utilizzo di numeri casuali per risolvere problemi complessi. Che calcoli svolge? Campiona un dominio problematico per stimare aree, calcolare probabilità, trovare valori ottimali, ecc. Facciamo un esempio: calcoliamo il PI-GRECO con un bersaglio per freccette digitale:

![[metodoMonteCarlo.PNG]]

## Migliorare le prestazioni

In generale:
- troppi fork/join possono ridurre le prestazioni;
- l'inversione dei loop può aiutare le prestazioni se:
    - il parallelismo è nel ciclo interno;
    - dopo l'inversione, l'anello esterno può essere reso parallelo;
    - l'inversione non riduce significativamente la percentuale di riscontri nella cache;
- se il ciclo ha troppe poche iterazioni, il sovraccarico di fork/join è maggiore del risparmio di tempo derivante dall'esecuzione parallela;
- la clausola *if* indica al compilatore di inserire il codice che determina in fase di esecuzione se il ciclo deve essere eseguito in parallelo; per esempio, *#pragma omp parallel for if(n $>$ 5000)*;
- possiamo usare la clausola di pianificazione per specificare come devono essere allocate le iterazioni di un ciclo nei thread;
- **pianificazione statica:** tutte le iterazioni vengono assegnate ai thread prima dell'esecuzione di qualsiasi iterazione;
- **pianificazione dinamica:** solo alcune iterazioni vengono assegnate ai thread all'inizio dell'esecuzione del ciclo. Le iterazioni rimanenti vengono assegnate ai thread che completano le iterazioni assegnate.
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

