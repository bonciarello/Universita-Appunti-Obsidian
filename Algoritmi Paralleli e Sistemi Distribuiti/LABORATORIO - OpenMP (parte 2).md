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

