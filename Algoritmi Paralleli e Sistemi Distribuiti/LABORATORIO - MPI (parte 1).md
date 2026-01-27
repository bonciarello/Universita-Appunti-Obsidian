## Informazioni su MPI

Vedremo solo la prima versione! Seconda versione e terza versione hanno caratteristiche avanzate interessanti, ma i fondamenti di MPI sono sufficientemente coperti già per la prima versione.

## Paradigma del Message Passing

![[paradigmaMessagePassing.PNG]]

Ogni processo esegue un processo. I processi non condividono segmenti di memoria, nel senso che non possono.

Indirizzare singole celle di memoria. I processi comunicano tra loro tramite scambio di messaggi, cioè di valori variabili contenuti in memoria.

Message Passing è diffuso sui sistemi di distribuzione, ma anche su macchine con memoria condivisa e cluster di workstation.

## Message Passing Interface

La programmazione della memoria condivisa è concettualmente semplice ma le macchine SMP sono molto costose!

È più economico e pratico "costruire" macchine a memoria distribuita tipo cluster di workstation/computer.

Come programmarli? Passaggio esplicito di messaggi: difficile da usare, ma consente la massima flessibilità!

## Vantaggi del Message Passing

- **Universalità:** il modello MP si adatta bene a processori separati collegati da una rete veloce/lenta. Corrisponde all'hardware della maggior parte dei supercomputer paralleli odierni e alla rete di workstation;
- **Espressività:** è stato riscontrato che MP è un modello utile e completo in cui esprimere algoritmi paralleli. Fornisce il controllo mancante dai modelli paralleli di dati o basati su compilatore;
- **Facilità di debug:** il debug di programmi paralleli rimane un'area di ricerca impegnativa. Il debug è più facile per il paradigma MPI rispetto al paradigma della memoria condivisa (anche se è difficile da credere);
- **Performance:**
    - Questo è il motivo più convincente per cui MP rimarrà una parte permanente dell'ambiente di elaborazione parallela;
    - man mano che le CPU moderne diventano più veloci, la gestione delle cache e della gerarchia della memoria è la chiave per ottenere il massimo da esse;
    - MP consente al programmatore di associare esplicitamente dati specifici ai processi e consente al compilatore e all'hardware di gestione della cache di funzionare completamente;
    - le applicazioni legate alla memoria possono mostrare una velocità superlineare quando vengono eseguite su più PE rispetto a un singolo PE di macchine MP.

## Caratteristiche del paradigma del Message Passing

Le caratteristiche principali sono:

- spazio degli indirizzi partizionato;
- parallelizzazione esplicita.

Le implicazioni sono:

- incoraggia il concetto di località per sfruttare le caratteristiche NUMA della maggior parte delle architetture parallele;
- cooperazione tra processi espliciti (lettura/scrittura bidirezionale), quindi complessità del programma;
- invito esplicito al programmatore a ridurre al minimo le comunicazioni!

Tutto il parallelismo è esplicito: il programmatore è responsabile dell'identificazione corretta del parallelismo e dell'implementazione di algoritmi paralleli utilizzando costrutti MPI.

## Cos'è MPI?

Il **Message-Passing Interface (MPI)** è uno standard per esprimere il parallelismo distribuito tramite il passaggio di messaggi.

MPI è costituito da un *file header*, una *libreria di routine* e un *ambiente di runtime*.

Quando si compila un programma che contiene chiamate MPI, il compilatore si collega a un'implementazione locale di MPI e quindi si ottiene il parallelismo; se la libreria MPI non è disponibile, la compilazione fallirà.

MPI può essere utilizzato in Fortran, C e C++.

## Nozioni di base su MPI

È “difficile” scrivere un programma diverso per ogni processo in un'applicazione parallela. Solitamente, viene adottato l'approccio SPMD (Single Program on Multiple Data):

- uno stesso programma viene eseguito su ogni processore che partecipa all'applicazione del programma
- ogni processo ha un attributo identificativo chiamato rango

## Funzioni base di MPI

Un programma MPI è costituito da più istanze di un programma seriale che comunicano tramite una chiamata di libreria. La chiamata può essere approssimativamente suddivisa in quattro classi:

1. chiamate utilizzate per inizializzare, gestire e terminare le comunicazioni;
2. chiamate utilizzate per comunicare tra coppie di processori (comunicazione di coppia);
3. chiamate utilizzate per comunicare tra gruppi di processori;
4. chiamate per creare tipi di dati.

## Decomposizione dei domini e MPI

MPI è particolarmente adatto per un approccio di scomposizione del dominio, dove esiste un unico flusso di programma.

Il calcolo parallelo consiste in una serie di processi, ciascuno dei quali lavora su alcuni dati locali. Ogni processo ha variabili puramente locali (nessun accesso alla memoria remota).

La condivisione dei dati avviene tramite il passaggio di messaggi, l'invio e la ricezione esplicita di dati tra i processi.

Ovviamente, MPI è adatto anche per implementazioni di algoritmi basati sull'approccio della scomposizione funzionale.

## Parallelismo dei dati

Decomposizione del dominio cioè ogni problema è (equamente se possibile) diviso tra i processi e ogni processo esegue il programma utilizzando la sua porzione di dati.

![[parallelismoDati.PNG]]

## Parallelismo funzionale

Modello Master/Slave: permette di specificare l'attività che ogni processo deve svolgere.

![[parallelismoFunzionaleEsempio.PNG]]

## MPI è adatto per il paradigma SPMD!

![[MPISPMD.PNG]]

I 2 principali costrutti del paradigma SPMD:
- identificatore del processo (rango)
- costrutto condizionale

In C++:

```cpp
main(int argc, char **argv) {
    if(myrank < ...) /* il processo dovrebbe eseguire il modello oceanico */ {
        ocean(/* argomenti */);
    } else {
        weather(/* argomenti */);
    }
}
```

![[MPIfunzionamento.PNG]]

## Le 6 routine fondamentali

Le "fantastiche" 6 routine fondamentali:

- Inizializzazione: *MPI_INIT*
- Informazioni sui processi appartenenti al comunicatore: *MPI_RANK* e *MPI_COMM_SIZE*
- Commutazioni punto a punto: *MPI_SEND* e *MPI_RECV*
- Finalizzazione: *MPI_FINALIZE*

È inoltre possibile creare comunicazioni collettive e ridefinire i tipi di dati.

## Concetti principali

- La **comunicatore** è un astrazione che indica un insieme di processi che possono comunicare tra loro: un processo può appartenere a più comunicatori. *MPI_COMM_WORLD* è il comunicatore predefinito;
- Il **grado di processo** è un identificatore del processo interno al comunicatore: l'utente può creare una *topologia virtuale* basata sui ranghi del processo.

## Comunicatore e grado

Insieme di processi a cui è consentito comunicare tra loro. Un messaggio può essere inviato solo a processi appartenenti allo stesso comunicatore. Nome predefinito: *MPI_COMM_WORLD*.

![[comunicazioneGrado.PNG]]

## Invia e ricevi in MPI

Il Message Passing equivale a trasferimento dati + sincronizzazione.

![[inviaEricevi.PNG]]

Esiste cooperazione tra invio e ricezione di messaggi. Il sistema di comunicazione basato su due operazioni di base:
- *send(message)*;
- *receive(message)*;

## MPI_Comm_size / MPI_Comm_rank

Quanti processi sono associati a un comunicatore?

```c
MPI_Comm_size(MPI_Comm comm, int *num);
// DOVE num E' L'OUPUT
```

Qual è l'identificativo di ogni processo?

```c
MPI_Comm_rank(MPI_Comm comm, int *me)
// DOVE me E' L'OUTPUT
```

## File Header (file di intestazione)

Tutti i sottoprogrammi che contengono chiamate alla subroutine MPI devono includere il file di intestazione MPI. In C++:

```cpp
#include <mpi.h>
```

In Fortran:

```fortran
include 'mpif.h'
```

Il file di intestazione contiene le definizioni delle costanti MPI, dei tipi e delle funzioni MPI.

## Inizializzazione e cessazione di MPI

**Inizializzazione dell'ambiente MPI** in *C++*:

```cpp
int MPI_Init(int *argc, char ***argv);
```

Inizializzazione dell'ambiente MPI in *Fortran*:

```fortran
INTEGER IERR
CALL MPI_INIT(IERR)
```

**Finalizzazione dell'ambiente MPI** in *C++*:

```cpp
int MPI_Finalize();
```

Finalizzazione dell'ambiente MPI in *Fortran*:

```fortran
INTEGER IERR
CALL MPI_FINALIZE(IERR)
```

Questi due sottoprogrammi dovrebbero essere chiamati da tutti i processi e nessun'altra chiamata MPI è consentita prima di *mpi_init* e dopo *mpi_finalize*.

## Possibile modello per programmi MPI

```c
#include <stdio.h>
#include <mpi.h>

void main(int argc, char *argv[]) {
    int err, nproc, myid;

    err = MPI_Init(&argc, &argv);
    err = MPI_Comm_size(MPI_COMM_WORLD, &nproc);
    err = MPI_Comm_rank(MPI_COMM_WORLD, &myid);

    /*** INSERISCI QUI IL TUO CODICE PARALLELO ***/

    err = MPI_Finalize();
}
```

## Un banale Hello World (1)

```c
#include <stdio.h>
#include <mpi.h>

void main(int argc, char * argv[]) {
    int err;

    err = MPI_Init (&argc, &argv);
    printf("Hello world!\n");
    err = MPI_Finalize();
}
```

## Esempio di programma MPI che adotta SPMD

Stampa una volta il numero di processi attivi e le coppie di ranghi altrimenti stampa ranghi dispari.

```c
#include <mpi.h>

main(int argc, char **argv) {
    int numproc, myid;
    MPI_init (&argc, &argv);
    MPI_comm_size(MPI_COMM_WORLD, &numproc);
    MPI_comm_rank(MPI_COMM_WORLD. &myid);
    if(myid == 0) printf("numero processi = %d \n", numproc);
    if((myid % 2) == 0) printf("rank processo = %d \n", myid);
    else printf("rank processo dispari \n");
    MPI_finalize();
    exit(0);
}
```

## Variabili globali e locali

Qualsiasi dichiarazione globale di variabili verrà duplicata in ogni processo.

Le variabili che non devono essere duplicate dovranno essere dichiarate all'interno del codice eseguito solo da quel processo.

```c
MPI_Comm_rank(MPI_COMM_WORLD, &myrank); /* trova il rango del processo */
if(myrank == 0) { /* elabora 0 azioni/variabili locali */
    int x, y;
    .
    .
} else if(myrank == 1) { /* elabora 0 azioni/variabili locali */
    int x, y;
    .
    .
}
```

Qui, *x* e *y* nel processo 0 sono variabili locali diverse da *x* e *y* nel processo 1.

## Programma MPI in C++

```cpp
#include <unist.h>
#include <mpi.h> // SEMPRE INCLUSO

main(int argc, char **argv) {
    int my_rank, n;
    char hostname[128];
    MPI_Init (&argc, &argv); // CHIAMATO PRIMO, SOLO UNA VOLTA
    MPI_Comm_size(MPI_COMM_WORLD, &my_rank);
    MPI_Comm_rank(MPI_COMM_WORLD. &n);
    gethostname(hostname, 128);
    if(my_rank == 0) { /* master */
        printf("Sono il master: %s \n", hostname);
    } else { /* worker */
        printf("Sono il worker: %s (rank=%d%d)\n", hostname, my_rank, n-1);
    }
    MPI_finalize(); // CHIAMATO ULTIMO, SOLO UNA VOLTA
    exit(0);
}
```

## Un banale Hello World (2)

```c
#include <stdio.h>
#include <mpi.h>

void main(int argc, char *argv[]) {
    int myrank, size;

    MPI_Init (&argc, &argv); /* Inizializza MPI */
    MPI_Comm_size(MPI_COMM_WORLD, &myrank); /* Ottieni il mio grado */
    MPI_Comm_rank(MPI_COMM_WORLD. &size); /* Ottieni il numero totale di processori */

    printf("Processor %d of %d: Hello World!\n", myrank, size);

    MPI_finalize(); /* Terminare MPI */
}
```

## Compilazione ed esecuzione

```bash
% mpicc -o hello_world_mpi hello_world_mpi.c
% mpirun -np 1 hello_world_mpi
% mpirun -np 2 hello_world_mpi
Greetings from process #1!
% mpirun -np 3 hello_world_mpi
Greetings from process #1!
Greetings from process #2!
% mpirun -np 4 hello_world_mpi
Greetings from process #1!
Greetings from process #2!
Greetings from process #3!
```
