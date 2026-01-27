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
## Comunicazione punto a punto (point to point)

![[comunicazionePuntoAPunto.png]]

È la funzione di comunicazione fondamentale fornita dalla libreria MPI.

È concettualmente semplice: il processo sorgente A invia un messaggio al processo di destinazione B, B riceve il messaggio da A.

La comunicazione avviene all'interno di un *comunicatore*.

Origine e destinazione sono identificate dal loro rango nel comunicatore.

![[PuntoAPuntoSendReceive.png]]

Vediamo due processi che eseguono la stessa funzione *lib()*: nella parte superiore, si manda in un primo istante con la funzione *send(destinatario)* alla funzione *receive(mittente)* in un secondo istante e viceversa. È più preferibile, però la parte inferiore.

## Possibile soluzione MPI

**Comunicatori:** utilizzati in MPI per tutti i comunicatori con passaggio di messaggi MPI punto-punto e collettivo.

Un comunicatore è un *dominio di comunicazione* che definisce un insieme di processi a cui è consentito comunicare tra di loro.

In questo modo, il dominio di comunicazione della libreria può essere separato da quello di un programma utente.

***Un'altra soluzione è specificare lo stesso tag per le corrispondenti operazioni di invio e ricezione.***

## Tipi di comunicatore

- **Intracommunicator:** per comunicare all'interno di un gruppo.
- **Intercommunicator:** per la comunicazione tra i gruppi.

Un processo ha un rango univoco in un gruppo (un numero intero da *0* a *m-1*, dove ci sono *m* processi nel gruppo). Un processo potrebbe essere un membro di più di un gruppo.

**Intracommunicator predefinito** - *MPI_COMM_WORLD*, esiste come primo comunicatore per tutto il processo esistente nell'applicazione. Nuovi comunicatori vengono creati sulla base di comunicatori esistenti. Esiste una serie di routine MPI per la formazione dei comunicatori.

## Elementi fondamentali: operazioni di invio e ricezione

I prototipi di funzioni (ipotetiche) sono:

```c
send(void *sendbuf, int nelems, int dest)
receive(void *recvbuf, int nelems, int source)
```

Consideriamo:

```c
// P0
	a = 100;
	send(&a, 1, 1);
	a = 0;
// P1
    receive(&a, 1, 0);
    printf("%d\n", a);
```

La semantica dell'operazione di invio richiede che il valore "ricevuto" dal processo P1 sia 100, invece di 0.

Sembrerebbe piuttosto "banale" garantire tale semantica, ma se si pensa che un'operazione di invio/ricezione potrebbe essere implementata tramite DMA (diretta senza utilizzare la CPU), non lo è!

## Tipica esecuzione di una comunicazione

![[tipicaEsecuzioneComunicazione.png]]

## Buffering?

![[buffering.png]]

## Comunicazioni punto a punto

Il messaggio contenente i dati è specificato da:
- Indirizzo di memoria iniziale
- Digitare un dato contenuto nel messaggio
- Lunghezza del messaggio

I processi di invio/ricezione sono descritti da:
- Nome del comunicatore
- Grado del processo

Il messaggio è identificato da un tag (valore intero) che può essere scelto dallo utente.

## Cosa succede in fase di esecuzione? (Invio / ricezione MPI standard)

- Il messaggio viene copiato in un buffer interno MPI e quindi trasferito al destinatario, in background.

oppure:

- Il messaggio rimane dov'è (nelle variabili del programma), fino a quando il processo di destinazione non è pronto a riceverlo. A questo punto, il messaggio viene trasferito al destinatario.

**Il primo caso consente al processo di invio di eseguire altre operazioni;** il secondo riduce al minimo la copia e l'utilizzo della memoria, MA può rendere INATTIVO il processo del mittente.

*Questo ritardo può anche avere un impatto significativo sulle prestazioni del programma.*

## Struttura dei messaggi

Un messaggio è un array definito dai dati MPI I tipi di dati MPI possono essere **di base** o **derivato**. I tipi derivati sono costruiti a partire dai tipi di base. **ATTENZIONE:** i tipi C sono diversi dai tipi Fortran.

I messaggi sono identificati da una busta: è possibile ricevere un messaggio solo se il destinatario specifica l'intestazione corretta.

![[strutturaMessaggi.PNG]]

## Dati MPI: tipi di base

![[datiMPI.PNG]]

## Invio e ricezione standard

Routine di comunicazione punto a punto di blocco di base in MPI.

```c
int MPI_Send(void *buf, int count, MPI_Datatype type, int dest, int tag, MPI_Comm comm);

int MPI_Recv(void *buf, int count, MPI_Datatype type, int dest, int tag, MPI_Comm comm, MPI_Status *status);
```

```c
Buf // array of type 'type'
Count // number of element of 'buf' to be sent
Type // MPI type of 'buf'
Dest // rank of the destination process
Tag // number identifying the message
Comm // communicator of the sender and receiver
Status // array of size MPI_STATUS_SIZE containing communication status information
```

## Successo di una comunicazione

- Il mittente deve specificare un grado di destinazione valido
- Il destinatario deve specificare un rango di origine valido
- I tag devono corrispondere
- I tipi di messaggio devono corrispondere
- Il buffer del ricevitore deve essere sufficientemente grande

## Busta di una comunicazione

Le informazioni sulla busta vengono restituite da MPI_RECV come stato.

![[bustaComunicazione.PNG]]

```c
int MPI_Get_count(MPI_Status status, MPI_Datatype datatype, int *count) // 'count' e' la dimensione del messaggio ricevuto
```

## Esempio di invio e ricezione

```c
#include <stdio.h>
#include <mpi.h>

void main(int argc, char* argv[]) {
    int err, nproc, myid;
    MPI_Status status;
    float a[2];

    err = MPI_Init(&argc, &argv);
    err = MPI_Comm_size(MPI_COMM_WORLD, &nproc);
    err = MPI_Comm_rank(MPI_COMM_WORLD, &myid);

    if(myid == 0) {
        a[0] = 3.0, a[1] = 5.0;
        MPI_Send(a, 2, MPI_FLOAT, 1, 10, MPI_COMM_WORLD);
    } else if(myid == 1) {
        MPI_Recv(a, 2, MPI_FLOAT, 0, 10, MPI_COMM_WORLD, &status);
        printf("%d: a[0] = %f a[1] = %f \n", myid, a[0], a[1]);
    }

    err = MPI_Finalize();
}
```

## Esercizio: ping pong

![[pingPong.png]]

```c
if (my_rank==0)     /* i.e., emulated multiple program */
	MPI_Send(... dest=1 ...)
	MPI_Recv(... source=1 ...)
else
	MPI_Recv(... source=0 ...)
	MPI_Send(... dest=0 ...)
fi
```

Più nel dettaglio:

```c
#include "mpi.h"
#include <stdio.h>

int main(argc,argv)
int argc;
char *argv[]; {
    int numtasks, rank, dest, source, rc, count, tag;
    char inmsg, outmsg='x';
    MPI_Status Stat;

    MPI_Init(&argc,&argv);
    MPI_Comm_size(MPI_COMM_WORLD, &numtasks);
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);

    if (rank == 0) {
        dest = 1;
        source = 1;
        rc = MPI_Send(&outmsg, 1, MPI_CHAR, dest, 17, MPI_COMM_WORLD);
        rc = MPI_Recv(&inmsg, 1, MPI_CHAR, source, 23, MPI_COMM_WORLD, &Stat);
    } else if (rank == 1) {
        dest = 0;
        source = 0;
        rc = MPI_Recv(&inmsg, 1, MPI_CHAR, source, 17, MPI_COMM_WORLD, &Stat);
        rc = MPI_Send(&outmsg, 1, MPI_CHAR, dest, 23, MPI_COMM_WORLD);
    }
    rc = MPI_Get_count(&Stat, MPI_CHAR, &count);
    printf("Task %d: Received %d char(s) from task %d with tag %d \n", rank, count, Stat.MPI_SOURCE, Stat.MPI_TAG);
    MPI_Finalize();
}
```

## Caratteri jolly

*MPI_Recv* accetta i caratteri jolly:

- Per ricevere da qualsiasi fonte *MPI_ANY_SOURCE*
- Per ricevere messaggi con qualsiasi tag *MPI_ANY_TAG*

La sorgente del messaggio e i tag vengono salvati nel parametro di stato.

## Codice invio e ricezione standard

![[invioEricezioneStandard.PNG]]

## Layout master/slave

Il nodo Master riceve messaggi di "saluto" dagli slave.

![[layoutMasterSlave.png]]

## Esempio: Hello World

1. Struttura master/slave;
2. Avviare il sistema MPI;
3. Ottieni il grado e il numero di processi;
4. Se non sei il processo server:
    1. Crea una stringa "Hello World";
    2. Inviarlo al processo del server;
5. Se sei il processo server, per ciascuno dei processi client:
    1. Ricevi la sua stringa "Hello World";
    2. Stampa la sua stringa "Hello World";
6. Arrestare il sistema MPI.

```c
#include <stdio.h>
#include <string.h>
#include "mpi.h"
int main (int argc, char* argv[]) {
    const int maximum_message_length = 100;
    const int server_rank = 0;
    char message[maximum_message_length+1];
    MPI_Status status; /* Info about receive status */
    int my_rank; /* This process ID */
    int num_procs; /* Number of processes in run */
    int source; /* Process ID to receive from */
    int destination; /* Process ID to send to */
    int tag = 0; /* Message ID */
    int mpi_error; /* Error code for MPI calls */
    // work goes here
}
```

## Hello World Startup/Shut Down

```c
// header file includes
int main (int argc, char* argv[]) {
    // declarations
    mpi_error = MPI_Init(&argc, &argv);
    mpi_error = MPI_Comm_rank(MPI_COMM_WORLD, &my_rank);
    mpi_error = MPI_Comm_size(MPI_COMM_WORLD, &num_procs);
    if (my_rank != server_rank) {
        // work of each non-server (worker) process
    } /* if (my_rank != server_rank) */ else {
        // work of server process
    } /* if (my_rank != server_rank) ... else */
    mpi_error = MPI_Finalize();
}
```

## Hello World Client’s Work

```c
// header file includes
int main (int argc, char* argv[]) {
    // declarations
    // MPI startup (MPI_Init etc)
    if (my_rank != server_rank) {
        sprintf(message, "Greetings from process #%d!", my_rank);
        destination = server_rank;
        mpi_error = MPI_Send(message, strlen(message) + 1, MPI_CHAR, destination, tag, MPI_COMM_WORLD);
    } /* if (my_rank != server_rank) */ else {
        // work of server process
    } /* if (my_rank != server_rank) ... else */
    mpi_error = MPI_Finalize();
}
```

## Hello World Server’s Work

```c
// header file includes
int main (int argc, char* argv[]) {
    // declarations, MPI startup
    if (my_rank != server_rank) {
        // work of each client process
    } /* if (my_rank != server_rank) */ else {
        for (source = 0; source < num_procs; source++) {
            if (source != server_rank) {
                mpi_error = MPI_Recv(message, maximum_message_length + 1, MPI_CHAR, source, tag, MPI_COMM_WORLD, &status);
                fprintf(stderr, "%s\n", message);
            } /* if (source != server_rank) */
        } /* for source */
    } /* if (my_rank != server_rank)…else */
    mpi_error = MPI_Finalize();
}
```

## Comunicazione bloccante e non bloccante

Una comunicazione è completata quando:

- **Le posizioni di memoria utilizzate per il trasferimento dei messaggi possono essere utilizzate in sicurezza:**
    - *Invia:* la variabile che è stata inviata può essere riutilizzata
    - *Ricevi:* è possibile utilizzare la variabile ricevuta

La comunicazione può essere *bloccante o non bloccante*:

- **BLOCCANTE:** la routine restituisce il controllo se la comunicazione è avvenuta (completamento)
- **NON BLOCCANTE:** la routine ritorna immediatamente e l'utente deve controllare se tutto è andato bene!

## Ulteriori informazioni sul completamento del messaggio

- **MPI_RECV_ e MPI_SEND** bloccano il processo che li chiama fino al completamento della comunicazione;
- completamento **MPI_RECV**: il messaggio è arrivato e il processo può procedere utilizzando i dati ricevuti
- completamento **MPI_SEND**: il processo può continuare se i dati possono essere sovrascritti senza interferire con il messaggio:
    - Questo non significa che il messaggio sia stato realmente inviato.
    - In molte implementazioni MPI, i dati da inviare possono essere copiati in appositi buffer creati da MPI a seconda della dimensione del messaggio.
- *se il messaggio non viene inserito nel buffer*, una chiamata MPI_SEND implica un processo di sincronizzazione, altrimenti non avviene alcuna sincronizzazione;
- *il comportamento di MPI_SEND dipende dall'implementazione*.

## Modalità di comunicazione

- Vista globale:
    - *sincrono* (il mittente sa se il messaggio è stato ricevuto o meno, ad es. Fax)
    - *asincrono* (il mittente non sa se il messaggio è stato ricevuto, ad esempio la lettera)
- Vista locale (buffer di trasmissione):
    - *blocco* restituisce il controllo al processo che ha chiamato la funzione solo quando la comunicazione è stata completata (il buffer di uscita è stato svuotato).

**ATTENZIONE: blocco diverso dalla sincronizzazione!**

    - *non bloccante:* i ritorni controllano il processo che ha invocato la funzione quando la primitiva di comunicazione è stata eseguita (senza controllo sull'effettivo completamento, da verificare in seguito). Il processo chiamante può nel frattempo eseguire altre operazioni.

MPI offre diverse combinazioni di SEND e RECEIVES sincroni e asincroni, bloccanti e non bloccanti.

## Regole per le modalità di comunicazione

![[defModalitaComunicazione.PNG]]

- Invio standard (MPI_SEND):
    - tempo di trasferimento minimo;
    - può bloccarsi a causa della modalità sincrona;
    - $
ightarrow$ rischi con invio sincrono
- Invio sincrono (MPI_SSEND):
    - rischio di deadlock
    - rischio di serializzazione
    - rischio di attesa $
ightarrow$ tempi morti
    - alta latenza / migliore larghezza di banda
- Invio con buffer (MPI_BSEND):
    - bassa latenza / scarsa larghezza di banda
- Pronto per l'invio (MPI_RSEND):
    - non usare **mai**, tranne per il fatto che hai una garanzia del 200% che Recv è già chiamato nella versione corrente e in tutte le versioni future del tuo codice

## Message Order Preservation

*MPI garantisce che i messaggi non si superino a vicenda.*

Se un mittente invia due messaggi (Messaggio 1 e Messaggio 2) in successione alla stessa destinazione ed entrambi corrispondono alla stessa ricezione, l'operazione di ricezione riceverà il Messaggio 1 prima del Messaggio 2.

Se un destinatario invia due ricezioni (Ricevi 1 e Ricevi 2), in successione, ed entrambi cercano lo stesso messaggio, Ricevi 1 riceverà il messaggio prima di Ricevi 2.

Le regole dell'ordine non si applicano se sono presenti più thread che partecipano alle operazioni di comunicazione.

![[MessageOrderPreservation.PNG]]

## Message Fairness

MPI non garantisce l'equità, spetta al programmatore prevenire "il mancato funzionamento". **Esempio:** l'attività 0 invia un messaggio all'attività 2. Tuttavia, l'attività 1 invia un messaggio concorrente che corrisponde alla ricezione dell'attività 2. Solo uno degli invii verrà completato.

![[MessageFairness.PNG]]

## Invio sincrono

Il mittente riceve le informazioni sulla ricezione del messaggio. Analogo al segnale acustico o al foglio di conferma di un fax.

![[invioSincrono.PNG]]

## Blocco delle operazioni di passaggio dei messaggi non bufferizzate (standard o sincrono)

![[bloccoOperazioniMessNonBuff.PNG]]

Handshake per un invio/ricezione bloccante senza buffer. È chiaro che quando il mittente e il destinatario non comunicano negli stessi tempi, potrebbe esserci un considerevole sovraccarico di inattività.

## Operazioni di blocco con buffer

![[operazioniBloccoBuffer.PNG]]

Protocolli di comunicazione bloccanti:

- in presenza di hardware di comunicazione con buffer;
- in assenza di comunicazione hardware: il mittente interrompe il ricevitore e pone i dati nel buffer del ricevitore;

Buffered è uguale ad invii asincroni: sappi solo che il messaggio è partito.

![[inviiAsincroni.PNG]]

## Operazioni non bloccanti (standard, bufferizzato o sincronizzato)

![[operazioniNonBloccanti1.PNG]]

Operazioni di invio e ricezione non bufferizzate non bloccanti:

- in assenza di hardware di comunicazione;
- in presenza di hardware di comunicazione;

**Operazione non bloccante:** ritorna immediatamente e consente al sottoprogramma di eseguire altre operazioni. In un momento successivo, il sottoprogramma deve testare o attendere il completamento dell'operazione di non blocco.

![[operazioniNonBloccanti2.PNG]]

Il *vantaggio* di utilizzare la **comunicazione non bloccante** risiede nel fatto che è molto più semplice scrivere programmi senza deadlock.

Lo *svantaggio* è che aumenta la complessità del codice, rendendo più difficile il debug di qualsiasi programma di manutenzione.
## Comunicazione collettiva

È un tipo di comunicazione che coinvolge un gruppo di processi. Deve essere chiamato da tutti i processi in un comunicatore. Esempi:
- sincronizzazione delle barrier;
- broadcast, scatter, gather;
- somma globale, massimo globale, ecc.

## Caratteristiche

- Le comunicazioni collettive coinvolgono tutti i processi nel comunicatore;
- Sono **bloccanti ma anche non bloccante da MPI 3.0**;
- Non richiedono tag;
- Non può essere utilizzato con i tipi MPI derivati;
- Il buffer di ricezione deve avere **esattamente** le dimensioni corrette

## Sincronizzazione delle barrier

![[barrier.PNG]]

In C++:

```c
int MPI_Barrier(MPI_Comm comm)
```

*MPI_Barrier* normalmente non è mai necessario: tutta la sincronizzazione viene eseguita automaticamente dalla comunicazione dati (un processo non può continuare prima di avere i dati di cui ha bisogno).

![[funzionamentoBarrier.PNG]]

## Broadcast

**Comunicazione uno-a-molti.** Gli stessi dati vengono inviati dal processo di root a tutti i processi del comunicatore

In C++:

```c
int MPI_Bcast (void * buf, int count, MPI_Datatype datatypem, int root, MPI_Comm comm)
```

Tutti i processi devono specificare lo stesso valore per *root*, *rank* e *comm*.

![[broadcast.PNG]]

![[esempioBroadcast1.PNG]]

## Scatter

**Comunicazione uno-a-molti.** Il processo di root invia i dati a tutti i processi del comunicatore. Gli argomenti della funzione sono gli stessi di altri MPI, ma *sndcount* è il numero di elementi inviati da root a ciascun processo, non la lunghezza di *sndbuf*, che dovrebbe essere *sndcount* volte il numero di processi del comunicatore. Gli argomenti del mittente hanno un significato solo per il processo di root.

```c
MPI_Scatter(send_buffer, send_count, send_type, recv_buffer, recv_count, recv_type, rank, comm)

/*
send_buffer     in     indirizzo iniziale del buffer di trasmissione
send_count     in     numero di elementi nel buffer di invio da inviare a ciascun processo
send_type     in     tipo di dati degli elementi del buffer di invio
recv_buffed     out     indirizzo iniziale del buffer di ricezione
recv_count     in     numero di elementi nel buffer di ricezione
recv_type     in     tipo di dati degli elementi nel buffer di ricezione
rank     in     grado del processo di ricezione
comm     in     comunicatore mpi
*/

int MPI_Scatter(void* send_buffer, int send_count, MPI_datatype send_type, void* recv_buffer, int recv_count, MPI_Datatype recv_type, int rank, MPI_Comm comm)
```

### Esempio

![[scatter.PNG]]

```c
send_count = 1;
recv_count = 1;
send_rank = 0;
MPI_Scatter (&a, send_count, MPI_REAL, &a, recv_count, MPI_REAL, send_rank, MPI_COMM_WORLD);
```

## Gather

**Comunicazione molti-a-uno.** Il processo di root riceve i dati da tutti i processi del comunicatore: opposto a *MPI_Scatter*.

Gli argomenti della funzione sono gli stessi di altri MPI, ma *rcvcount* è il numero di elementi raccolti da root per ogni processo, non la lunghezza di *rcvbuf*, che dovrebbe essere *rcvcount* volte il numero di processi del comunicatore. Gli argomenti del destinatario hanno il significato solo per il processo di *root*.

```c
MPI_Gather (send_buffer, send_count, send_type, recv_buffer, recv_count, recv_count, recv_rank, comm)

/*
send_buffer     in     indirizzo iniziale del buffer di trasmissione
send_count     in     numero di elementi nel buffer di invio
send_type     in     tipo di dati degli elementi del buffer di invio
recv_buffed     out     indirizzo iniziale del buffer di ricezione
recv_count     in     numero di elementi nel buffer di ricezione per una singola ricezione
recv_type     in     tipo di dati degli elementi nel buffer di ricezione
rank     in     grado del processo di ricezione
comm     in     comunicatore mpi
*/

int MPI_Gather(void* send_buffer, int send_count, MPI_datatype send_type, void* recv_buffer, int recv_count, MPI_Datatype recv_type, int rank, MPI_Comm comm)
```

### Esempio

![[gather.PNG]]

```c
send_count = 1;
recv_count = 1;
recv_rank = 0;
MPI_Gather (&a, send_count, MPI_REAL, &a, recv_count, MPI_REAL, recv_rank, MPI_COMM_WORLD);
```

## AllGather

*MPI_Gather* "raccoglie" i messaggi sul processo di root. Se necessario, è possibile eseguire una trasmissione per inviare i dati a tutti i processi.

In questo caso, usa *MPI_Allgather* (stessa sintassi di *MPI_Gather*)

![[allGather.PNG]]

## Operazioni di riduzione (reduction)

Utilizzato per eseguire operazioni sui dati distribuiti sui processi:

- raccogliere dati da ogni processo;
- **riduce i dati a un singolo valore**;
- salva i dati sul processo di root o su tutti i processi;
- operazioni predefinite (*MPI_MAX*, *MPI_MIN_MPI_SUM*, ecc.) con cui l'utente ha la possibilità di definire nuove operazioni.

```c
MPI_Reduce (send_buffer, recv_buffer, count, data_type, reduction_operation, rank_of_receiving_process, communicator)

/*
send_buffer     in     indirizzo del buffer di trasmissione
recv_buffer     out     indirizzo del buffer di ricezione
count     in     numero di elementi nel buffer di trasmissione
datatype     in     tipo di dati degli elementi nel buffer di trasmissione
operation     in     operazione di riduzione
rank     in     grado del processo di root
communicator     in     comunicatore mpi
*/

int MPI_Reduce(void* send_buffer, void* recv_buffer, int count, MPI_Datatype datatype, MPI_Op operation, int rank, MPI_Comm comm)
```

### Esempio (1)

![[reduction1.PNG]]

```c
count = 1;
rank = 0;
MPI_Reduce (&a, &x, count, MPI_REAL, MPI_SUM, rank, MPI_COMM_WORLD);
```

### Esempio (2)

![[reduction2.PNG]]

```c
MPI_Reduce (sbuf, rbuf, 6, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);
```

## Esempio di broadcast e reduce: somma di vettori

```c
#include "mpi.h"
#include <stdio.h>

#define MAXSIZE 10

/* vector maximum */

int main(int argc, char *argv[]) {
  int myid, numprocs;
  int data[MAXSIZE], i, x, low, high, myresult, result, result_temp;
  MPI_Init(&argc,&argv);
  MPI_Comm_size(MPI_COMM_WORLD, &numprocs);
  MPI_Comm_rank(MPI_COMM_WORLD, &myid);

  result = 0;
  myresult = 0;

  if(myid == 0) { for(i = 0; i < MAXSIZE; i++) { data[i] = i; } }

  MPI_Bcast(data, MAXSIZE, MPI_INT, 0, MPI_COMM_WORLD);

  x = MAXSIZE/numprocs;
  low = myid * x;
  high = low + x;

  for(i = low; i < high; i++)
    myresult = myresult + data[i];

  MPI_Reduce(&myresult, &result, 1, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);
  if(myid == 0)
    printf("La somma e' %d.\n", result);

  MPI_Finalize();
  return 0;
}
```
## Deadlock

Hai un deadlock quando 2 processi vengono bloccati perché uno è in attesa che l'altro "faccia" qualcosa.

![[deadlock.png]]

La sintassi in ogni processo MPI:

```c
MPI_Ssend(..., right_rank, ...)
MPI_Recv(..., left_rank, ...)
```

Si bloccherà e non tornerà mai più, perché *MPI_Recv* non può essere chiamato nel processo MPI di destra.

![[deadlockProcesso.png]]

Stesso problema con la modalità di invio standard (*MPI_Send*) se l'implementazione MPI sceglie il protocollo sincrono.

```c
double a[100], b[100];

...

if(my_rank != 0) {
    // receive, the send a message
    MPI_Recv(b, 100, MPI_DOUBLE, 1, 19, MPI_COMM_WORLD, &status);
    MPI_Send(a, 100, MPI_DOUBLE, 1, 17, MPI_COMM_WORLD);
} else {
    // receive, the send a message
    MPI_Recv(b, 100, MPI_DOUBLE, 0, 17, MPI_COMM_WORLD, &status);
    MPI_Send(a, 100, MPI_DOUBLE, 0, 19, MPI_COMM_WORLD);
}
```

## Deadlock evitato

![[deadlockEvitato.png]]

```c
double a[100], b[100];

...

if(my_rank != 0) {
    // receive a message, then send one
    MPI_Recv(b, 100, MPI_DOUBLE, 1, 19, MPI_COMM_WORLD, &status);
    MPI_Send(a, 100, MPI_DOUBLE, 1, 17, MPI_COMM_WORLD);
} else {
    // send a message, the receive one
    MPI_Send(a, 100, MPI_DOUBLE, 0, 19, MPI_COMM_WORLD);
    MPI_Recv(b, 100, MPI_DOUBLE, 0, 17, MPI_COMM_WORLD, &status);
}
```

```c
double a[100], b[100];

...

if(my_rank != 0) {
    // receive a message, then send one
    MPI_Send(a, 100, MPI_DOUBLE, 1, 17, MPI_COMM_WORLD);
    MPI_Recv(b, 100, MPI_DOUBLE, 1, 19, MPI_COMM_WORLD, &status);
} else {
    // send a message, the receive one
    MPI_Recv(b, 100, MPI_DOUBLE, 0, 19, MPI_COMM_WORLD, &status);
    MPI_Send(a, 100, MPI_DOUBLE, 0, 17, MPI_COMM_WORLD);
}
```

Se MPI sceglie il buffering, nessun deadlock. Se MPI sceglie il sincronismo, deadlock.

## Comunicazioni bloccanti

- **Sincrono (*MPI_Ssend*):** la routine viene eseguita quando viene ricevuto il messaggio. Il messaggio viene "immesso" sulla rete e il destinatario attende una conferma;
- **Buffered (*MPI_Bsend*):** la routine è completata quando il messaggio viene inserito in un buffer MPI dedicato. Possibile overflow del buffer se molti Bsend e nessuna ricezione;
- **Leggi (*MPI_Rsend*):** deve iniziare solo se la corrispondenza ricevuta è iniziata (nessuna conferma);
- **Standard (*MPI_Send*):** MPI decide se bufferizzato o sincrono.

## Comunicazioni non bloccanti

Abbiamo visto comunicazioni bloccanti: le funzioni di chiamata ritornano solo quando le operazioni sono state completate alla ricezione o non appena il messaggio è stato inserito in un buffer MPI.

MPI ha anche comunicazioni non bloccanti:

- le funzioni chiamanti vengono restituite immediatamente;
- è necessario utilizzare un'altra funzione per controllare i completamenti;
- è possibile eseguire altre istruzioni in attesa della terminazione.

### Funzioni generali

Funzioni di inizializzazione delle comunicazioni: *MPI_Isend*, *MPI_Issend*, *MPI_Ibsend*, *MPI_Irsend*, *MPI_Irecv*.

Funzioni per controllare il completamento della comunicazione:

- la funzione non ritorna fino a quando non si verifica la ricezione del messaggio:
```c
MPI_WAIT (/* MPI blocks here! */)
```
- la routine restituisce VERO o FALSO se la ricezione del messaggio è avvenuta o meno:
```c
MPI_TEST
```

### Funzioni MPI

Invio/ricezione non bloccante:
```c
int MPI_Isend(void *buf, int count, MPI_Datatype dtype, int dest, int tag, MPI_Comm comm, MPI_Request *request);

int MPI_Irecv(void *buf, int count, MPI_Datatype dtype, int source, int tag, MPI_Comm comm, MPI_Request *request);
```

- Inviare semplicemente una richiesta al sistema per avviare la comunicazione.
- Tuttavia, la comunicazione non è stata ancora completata.
- Non può interferire con la memoria fornita in queste chiamate fino a quando la comunicazione non è completata chiamando MPI_Wait o MPI_Test ecc.

![[sintassiNonBloccanti.PNG]]

Funzioni di test:
```c
int MPI_Wait(MPI_Request *request, MPI_Status *status);
int MPI_Test(MPI_Request *request, int *flag, MPI_Status *status);
```

### MPI_Wait

```c
int MPI_Wait(MPI_Request *request, MPI_Status *status)

// *request e' un handle restituito da MPI_Isend, MPI_Irecv ecc
```

Si bloccherà finché la comunicazione non sarà completata (o fallita).

Se la richiesta proviene da *MPI_Isend*, *MPI_Irecv* ecc, Distribuirà l'oggetto richiesta, imposterà la richiesta su *MPI_REQUEST_NULL*.

Restituirà nello stato le informazioni sullo stato: per *MPI_Irecv* conserva informazioni aggiuntive, per *MPI_Isend*, non c'è molto da usare.

```c
MPI_Request req;
MPI_Status stat;
...
MPI_Irecv(..., &req);
MPI_Wait(&req, &stat);
```

### MPI_Test

```c
int MPI_Test(MPI_Request *request, int *flag, MPI_Status *status)
```

*flag* è uguale a TRUE se la comunicazione è completa; FALSE se non ancora: se TRUE, l'oggetto richiesta verrà de-allocato e impostato su MPI_REQUEST_NULL. *status* contiene le informazioni sullo stato, se completo.

Non blocca, torna immediatamente. Fornire un meccanismo per la sovrapposizione di comunicazione e calcolo.

Svolge calcoli utili; controlla periodicamente lo stato della comunicazione; se non completo, torna al calcolo.

### Varianti MPI_Wait

- Gestire array di *MPI_Requests*:
```c
MPI_Request req [4];
```
- *MPI_Waitall:*
```c
MPI_Waitall(int count, MPI_Request *request, MPI_Status *status)
```

Si blocca fino al completamento di tutte le richieste attive nell'array; restituire lo stato di tutte le comunicazioni. Dealloca oggetti *request*, impostato su *MPI_REQUEST_NULL*.

- *MPI_Waitany:*
```c
MPI_Waitany(int count,MPI_Request *req, int *index, MPI_Status *stat)
```

Si blocca fino al completamento di una delle richieste attive nell'array; restituisce il suo indice in array e lo stato di completamento della richiesta; dealloca quell'oggetto richiesto. Se nessuno viene completato, restituire *index = MPI_UNDEFINED*.

- *MPI_Waitsome:*
```c
MPI_Waitsome(int incount, MPI_Request *req, int *outcount, int *array_indices, MPI_Status *array_status)
```

Si blocca fino al completamento di almeno una delle comunicazioni attive; restituire gli indici associati e lo stato delle comunicazioni completate; dealloca gli oggetti. Se nessuno viene completato, *outcount = MPI_UNDEFINED*.

- *MPI_Testall:*
```c
MPI_Testall(int count, MPI_Request *array_req, int *flag, MPI_Status *array_stat)
```

Restituisce *flag = true* se tutte le richieste attive vengono completate; restituisce *flag = false* altrimenti. Se è TRUE, disallocerà gli oggetti richiesti, impostati su *MPI_REQUEST_NULL*.

- *MPI_Testany:*
```c
MPI_Testany(int count, MPI_Request *array_req, int *index, int *flag, MPI_Status *stat)
```

Se una delle comunicazioni attive viene completata, restituisce *flag = true* l'indice e lo stato del completamento della comunicazione; poi dealloca quell'oggetto. Restituisce *flag = false*, *index = MPI_UNDEFINED* se nessuno viene completato Restituisce *flag = true*, *index = MPI_UNDEFINED* se nessuna richiesta attiva.

- *MPI_Testsome:*
```c
MPI_Testsome(int incount, MPI_Request *array_req, int *outcount, int *array_indices, MPI_Status *array_stat)
```

Restituire in *outcount* il numero di comunicazioni attive completate e gli indici associati e lo stato di completamento delle comunicazioni. Se nessuno viene completato, restituisce il *outcount = 0*, se nessuna comunicazione attiva *outcount = MPI_UNDEFINED*.

### Esempio

Per inviare un intero x dal processo 0 al processo 1 e consentire al processo 0 di continuare.

```c
MPI_Comm_rank(MPI_COMM_WORLD, &myrank); /* find process rank */
if(myrank == 0) {
    int x;
    MPI_Isend(&x, 1, MPI_INT, 1, msgtag, MPI_COMM_WORLD, req1);
    compute();
    MPI_Wait(req1, status);
} else if(myrank) {
    int x;
    MPI_Recv(%x, 0, MPI_INT, 1, msgtag, MPI_COMM_WORLD, status);
}
```

## Soluzione parallela: comunicazione sovrapposta al calcolo

```c
void main (int argc, char **argv) {
    int myrank;
    MPI_Request request;
    MPI_Status status;
    double a[100], b[100];

    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &myrank);
    if(my_rank != 0) {
        MPI_Irecv(b, 100, MPI_DOUBLE, 1, 19, MPI_COMM_WORLD, &request);
        MPI_Send(a, 100, MPI_DOUBLE, 1, 17, MPI_COMM_WORLD);
        MPI_Wait(&request, &status);
    } else {
        MPI_Irecv(b, 100, MPI_DOUBLE, 0, 17, MPI_COMM_WORLD, &request);
        MPI_Send(a, 100, MPI_DOUBLE, 0, 19, MPI_COMM_WORLD);
        MPI_Wait(&request, &status);
    }

    MPI_Finalize();
}
```

Nella soluzione precedente si ipotizza l'utilizzo della **comunicazione bloccante** per gli slave. La comunicazione bloccante attende il "completamento" del processo di comunicazione prima di eseguire l'istruzione successiva.

Nella soluzione precedente, i processi adiacenti PRIMA comunicano i dettagli del confine, POI aggiornano la loro parte dell'array.

I **tempi di comunicazione** possono essere drasticamente ridotti (complicando però il codice!) attraverso l'uso di comunicazioni non bloccanti. Le comunicazioni non bloccanti consentono l'esecuzione del calcolo MENTRE la comunicazione è in corso.

In questa seconda soluzione, ogni processo aggiorna la parte interna del proprio array durante la comunicazione della scheda, aggiornando la sua porzione di confine DOPO che la comunicazione è terminata.

![[comunicazioneSovrappostaAlCalcolo1.PNG]]

![[comunicazioneSovrappostaAlCalcolo2.PNG]]

## Persistent Communication

Struttura per chiamate non bloccanti:

- *MPI_Ixxxx* alloca *MPI_Request*;
- *MPI_Wait* o *MPI_Test* completa e disalloca gli oggetti richiesta.

Spesso una comunicazione con gli stessi argomenti viene eseguita ripetutamente (Es: scambio di frontiera in CA): per esempio, ogni passo temporale o ogni iterazione.

Per evitare ridondanza nell'impostazione del messaggio ogni volta che viene inviato.

Può creare una richiesta persistente che non verrà de-allocata da *MPI_Wait*. Può ridurre le spese generali.

```c
MPI_Send_init, MPI_Recv_init  // crea richiesta persistente
MPI_Start  // inizia la comunicazione
...
MPI_Wait, MPI_Test  // comunicazione completa
MPI_Request_free  // richiesta persistente gratuita
```

## Avvia comunicazione, richiesta gratuita

```c
int MPI_Start(MPI_Request *request)
```

*request* è una richiesta persistente creata da *MPI_Send_init* ecc.

Avviare la comunicazione su oggetto *request}.

La chiamata ritorna immediatamente. **Avvia una comunicazione non bloccante.** Non dovrebbe accedere al buffer dopo questa chiamata fino al completamento.

Comunicazione completa tramite *MPI_Wait*, *MPI_Test* ecc. *MPI_Wait*, *MPI_Test* non disallocherà la richiesta al termine della comunicazione.

Disalloca la richiesta persistente utilizzando *MPI_Request_free* alla fine.

```c
int MPI_Request_free(MPI_Request *request)
MPI_REQUEST_FREE(request)
    integer REQUEST
```

## Tipi di comunicazioni e subroutine MPI

![[subroutineMPI.PNG]]
## Comunicatori e gruppi

Un **comunicatore** può essere pensato come un insieme (gruppo) di processi che possono comunicare tra loro (**intra-comunicazione**). Ricorda che le **inter-comunicazioni** sono quelle tra comunicatori (non trattate in questo corso). Ogni processo nel gruppo è identificato da un **rank** *(0, ..., n-1)*. Ogni messaggio deve specificare un comunicatore MPI. Utile per applicazioni che richiedono una **comunicazione** che deve avvenire solo in alcuni processi (es. Scambio di righe, colonne, blocchi di matrici).

Il comunicatore:

- consente di **strutturare** meglio il codice;
- consente **comunicazioni collettive**;
- consente di gettare le basi di **topologie virtuali** (dopo);
- consente comunicazioni **sicure**.

Un processo può appartenere a **più** comunicatori (in ogni caso avrà un rango unico all'interno di ciascun comunicatore). Dal punto di vista del programmatore, un comunicatore è uguale ad un gruppo ma in realtà un comunicatore è formato da:

- gruppo (insieme di processi);
- contesto (oggetto definito dal sistema che identifica il comunicatore);
- ecc.

![[comunicatoriEgruppi.png]]

## MPI_Comm_create

*MPI_Comm_create* crea un nuovo comunicatore *newcomm* e i membri del gruppo sono definiti in una struttura chiamata *group*.

```c
int MPI_Comm_create(old_comm, group, &newcomm)
```

*MPI_Group_excl/MPI_Group_incl* consentono di creare facilmente gruppi di processi.

```c
int MPI_Group_incl(MPI_Group group, int count, int *members, MPI_Group *nre_group)
```

*MPI_Comm_split* divide il comunicatore in una nuova parte.

## MPI_Group_incl

```c
int MPI_Group_incl(MPI_Group old_group, int count, int *members, MPI_Group *new_group)
```

![[MPIGroupIncl1.PNG]]

Facciamo un esempio: creare un nuovo gruppo da un gruppo esistente.

```c
#include "mpi.h"
MPI_Group group_world, odd_group, even_group;
int i, p, Neven, Nodd, members[8], ierr;

MPI_Comm_size(MPI_COMM_WORLD, &p);
MPI_Comm_group(MPI_COMM_WORLD, &group_world);

Neven = (p+1)/2;
Nodd = p - Neven;
for(i = 0; i < Neven; i++) {
    members[i] = 2*i;
}
MPI_Group_incl(group_world, Neven, members, &odd_group);
```

![[MPIGroupIncl2.PNG]]

## MPI_Group_excl

```c
int MPI_Group_excl(MPI_Group group, int count, int *nonmembers, MPI_Group *new_group)
```

![[MPIGroupExcl.PNG]]

Facciamo un esempio: creare un nuovo gruppo da un gruppo esistente.

```c
#include "mpi.h"
MPI_Group group_world, odd_group, even_group;
int i, p, Neven, Nodd, nonmembers[8], ierr;

MPI_Comm_size(MPI_COMM_WORLD, &p);
MPI_Comm_group(MPI_COMM_WORLD, &group_world);

Neven = (p+1)/2;
Nodd = p - Neven;
for(i = 0; i < Neven; i++) {
    nonmembers[i] = 2*i;
}
MPI_Group_excl(group_world, Neven, nonmembers, &odd_group);
```

## Creazione di comunicatori: utilizzo tipico

1. **Estrazione** di "Gruppo globale" da *MPI_COMM_WORLD* a *MPI_Comm_group*;
2. **Formazione** di un nuovo gruppo come sottoinsieme del gruppo globale tramite *MPI_Group_incl*;
3. **Creare** un nuovo comunicatore per il nuovo gruppo tramite *MPI_Comm_create*;
4. **Determinare** il nuovo grado nel nuovo comunicatore utilizzando *MPI_Comm_rank* (o *MPI_Group_rank*);
5. **Eseguire le comunicazioni** utilizzando le funzioni Comunicazioni MPI (tramite il comunicatore giusto!);
6. **(Facoltativo)** Al termine, deallocare il comunicatore e il gruppo con *MPI_Comm_free* e *MPI_Group_free*;

![[creazioneComunicatori.PNG]]

## Partizionamento

A volte è necessario **limitare** le comunicazioni a sottoinsiemi di processi (ad es. Comunicazione tra righe in una matrice). La funzione collettiva:

```c
int MPI_Comm_split(MPI_Comm comm, int color, int key, MPI_Comm *newcomm);
```

consente di **partizionare** la comunicazione del comunicatore in molti sottogruppi in base al color. Se la key di un processo è più piccola della chiave di un altro, il rango del primo processo nel nuovo comunicatore sarà inferiore nel secondo; in caso contrario, MPI assegnerà arbitrariamente il loro grado.

Deve essere chiamato da tutti i processi che devono essere partizionati!

![[partizionamento.png]]

## Topologie virtuali

Oltre al **gruppo** e al **contesto**, altri attributi possono essere associati a un comunicatore, come la **topologia**. Rappresenta un meccanismo per associare un **diverso schema di indirizzamento** (di default, **lineare**), utile se il programma utilizza uno specifico pattern di comunicazione (es. Operazioni con matrice, automi cellulari, ecc.). **Non c'è corrispondenza** tra l'effettiva struttura fisica della rete e la topologia di interconnessione. Ecco perché si chiama virtuale! Tuttavia, **MPI cercherà di “ottimizzare” al massimo la topologia fisica esistente**. Principalmente, è possibile creare due tipi di topologie virtuali: **topologie cartesiane/a griglia** e topologie **a grafo**.

La topologia di comunicazione identifica i **principali modelli** di comunicazione che si verificano in un codice parallelo. Le **topologie virtuali MPI** sono uno strumento utile per risparmiare tempo, evitare deadlock/errori logici e codice struttura migliore. Per impostazione predefinita, MPI assegna a ciascun processo di un gruppo un rango da *0* a *n-1* (**topologia lineare**). Con funzioni adeguate, MPI supporta anche **topologie** e **grafici cartesiani**. Utile quando i modelli di comunicazione seguono una o più **strutture particolari**.

## Topologie delle reti: ipercubi e loro costruzione

Ricordiamo la seguente importante proprietà degli ipercubi: "La distanza tra due nodi è data dal numero di posizioni di bit in cui i due nodi differiscono (es. 0110 e 0101 sono distanti 2 nodi)".

![[topologieReti.png]]

## Topologie e incorporamenti

![[incorporamenti.png]]

> Diversi modi per mappare un insieme di processi su una griglia bidimensionale: *(a)* e *(b)* riga e colonna; *(c)* curve di riempimento dello spazio; *(d)* mostra un processo di mappatura in cui i "vicini" sono collegati all'ipercubo.

**ESEMPIO:** se hai bisogno di una topologia in cui ogni processo comunichi con i suoi vicini lungo xey, e l'architettura della macchina parallela per la comunicazione reale è quella dell'ipercubo, la mappatura della figura (d) risulta essere la migliore.

I processi sono ai nodi di una griglia N-dimensionale. Le caratteristiche della topologia sono:
- numero di dimensioni;
- numero di processi in ogni dimensione;
- toroidalità in ogni dimensione.

Per creare una topologia cartesiana:

```c
int MPI_Cart_create(MPI_Comm comm_old, int ndims, int *dims, int *periods, int reorder, MPI_Comm *comm_cart)
```

Dove:

- *comm_old* è il vecchio comunicator (es: *MPI_COM_WORLD*)
- *comm_cart* è il nuovo comunicatore (cartesiano)
- *ndims* è il numero di dimensioni del topoloy (es. 2 - bi)
- *dims* matrice che rappresenta la lunghezza di ogni dimensione
- *periods* rappresenta la toroidalità (periodi [i] = 1 se la dimensione i è toroidale)
- *reorder* se falso, il rango di ogni processo del nuovo comunicatore è identico a quello vecchio

**NB:** se il numero di processi specificato in *dims* è inferiore ai processi presenti in *comm_old*, alcuni processi non apparterranno al nuovo.

![[topologiaCartesiana.PNG]]

Poiché le funzioni invio/ricezione richiedono il rango di processi in formato "unidimensionale", MPI consente la conversione di **coordinate-rango** e **rango-coordinate** (rispettivamente):

```c
int MPI_Cart_rank(MPI_Comm comm_cart, int *coords, int *rank);

int MPI_Cart_coord(MPI_Comm comm_cart, int rank, int maxdims, int *coords);
```

Analogamente a *MPI_Comm_split*, anche le topologie cartesiane possono essere partizionate, in modo da consentire la comunicazione tra sottoprocessi.

```c
int MPI_Cart_sub(MPI_Comm comm_cart, int *keep_dims, MPI_Comm *comm_subcart)
```

Se *keep_dims[i]* è vero, la dimensione i-esima viene mantenuta nella nuova topologia secondaria. Le coordinate di un processo nella nuova sotto-topologia creata da *MPI_Cart_sub* si ottengono semplicemente dalle coordinate nelle topologie originali, scartando le coordinate "non mantenute". *MPI_Comm_split* è decisamente più generale "!

Una delle operazioni più comuni nelle topologie cartesiane è lo "spostamento" dei dati lungo una dimensione (ES: il concetto di vicinato negli automi cellulari). Per determinare il rango di "vicini" di un processo (chiamato sorgente e destinatario) utilizziamo:

```c
int MPI_Cart_shift(MPI_Comm comm_cart, int dir, int s_step, int *rank_source, int *rank_dest)
```

dove:

- *dir* è la direzione lungo lo spostamento;
- *s_step* è il passaggio di spostamento (+ o -);
- *rank_source* processo di "invio";
- *rank_dest* processo di "ricezione".

*MPI_Cart_shift* non esegue uno shift di dati: permette di determinare i ranghi corretti per uno shift che verrà utilizzato nelle comunicazioni successive - in pratica, trova i "vicini".
## Raggruppamento dei dati per la comunicazione

Meno messaggi inviamo, meglio è! Vedremo tre metodi per raggruppare i dati in singoli messaggi:
- Parametri *count*;
- Tipi di dati derivati;
- *MPI_Pack*/*MPI_Unpack*.

Ricordiamo che i tipi di dati primitivi sono contigui (es. array in C): i metodi sopra elencati consentono di "costruire" messaggi con dati contigui o di costruire nuovi tipi in modo efficiente e facile da dati non contigui.

## Trasmissione dati

Il **problema** è che è possibile utilizzare meccanismi di comunicazione MPI di base per inviare o ricevere una sequenza di elementi dello stesso tipo e contigui in memoria. Solitamente è opportuno inviare dati di diverso tipo, non omogenei e anche non contigui. *Come possiamo specificare l'invio di dati non contigui dello stesso tipo? Oppure dati contigui di diverso tipo? Oppure dati non contigui di tipo misto?*

Le possibili soluzioni sono:
- utilizzo di più chiamate alle routine MPI per inviare e ricevere ogni elemento di dati;
- utilizzo di *MPI_PACKED* e *MPI_UNPACKED*;
- utilizzo di *MPI_BYTE*;

Queste soluzioni sono però molto lente, poco pratiche e utilizzano molta memoria! Il modo ottimale per evitare tutti i problemi di cui sopra è per mezzo di **tipi di dati derivati**.

![[derivatedDatatypes.png]]

## Tipi di dati derivati

I tipi di dati derivati sono nuovi tipi di dati a partire dai tipi MPI predefiniti fondamentali o da altri tipi derivati. Per definire un nuovo tipo di dati, è necessario:
- una sequenza di tipi di dati fondamentali;
- una sequenza di numeri interi (byte) per gli spostamenti.

Un modo semplice per immaginarlo è rappresentato da una sequenza di coppie: **tipo di dati fondamentale - spostamento**. In MPI, questo è chiamato *typemap*.

![[tipiDatiDerivati.png]]

## Salvataggio di un tipo di dati

Prima che un handle del tipo di dati venga utilizzato nella comunicazione con passaggio di messaggi, è necessario eseguirne il commit con *MPI_TYPE_COMMIT*. Questo deve essere fatto solo una volta.

```c
int MPI_Type_commit(MPI_Datatype *datatype);
```

## Esempio impacchettamento dati

![[esempioImpacchettamentoDati.png]]

Se dobbiamo inviare un unico messaggio alle tre variabili, sono richieste le seguenti informazioni:

- Ci sono tre elementi da trasmettere;
-
    - Il primo elemento è un *float*;
    - Il secondo elemento è un *float*;
    - Il terzo elemento è un *int*;
-
    - Il primo elemento ha indirizzo *&a*;
    - Il secondo elemento ha indirizzo *&b*;
    - Il terzo elemento ha indirizzo *&n*;

## Giocare con il parametro count

C garantisce che gli elementi dell'array siano contigui in memoria (per riga). Ad esempio possiamo sfruttare questa caratteristica per inviare "la seconda metà di un array di 100 elementi dal processo 0 al processo 1.

![[giocareParametroCount.png]]

## Dati non contigui (tipi di dati derivati da MPI)

Il metodo sopra descritto è valido solo per dati contigui (es. vettori in C). Se dichiariamo tre variabili (es. *float a, b, int n*), sappiamo che il compilatore non le mette in memoria contigua - sicuro al 99,99%. Potrei costruire una struttura (tramite typedef struct in C), ma MPI accetta solo tipi MPI (primitivi o derivati)!

**Primo metodo:** usa *MPI_Type_struct*. Rappresenta il costruttore di tipi di dati più generale in MPI, ma necessita di una descrizione completa di ogni elemento del tipo di dati (cioè tipo, spostamenti, indirizzi di ogni elemento, ecc.).

![[datiNonContigui.png]]

## Costruttori di tipi di dati

Quando ti occupi principalmente di vettori e array, il *MPI_Type_struct* è troppo "pesante" per specificare il nuovo tipo di dati. Puoi utilizzare altri tre costruttori:

- *MPI_Type_contiguous* (usato per costruire dataype derivato da elementi adiacenti di un array);
- *MPI_Type_vector* (usato per costruire dataype derivato da elementi equidistanti in un array);
- *MPI_Type_indexed* (usato per costruire dataype derivato da elementi arbitrari di un array)

## Dati contigui e MPI_Type_contigous

Il tipo di dati derivato più semplice. È costituito da una serie di elementi contigui dello stesso tipo di dati.

```c
int MPI_Type_contiguous8int count, MPI_Datatype oldtype, MPI_Datattype *newtype)
```

![[datiContigui.PNG]]

Creazione di un tipo di dati che rappresenta una riga di un array e distribuzione delle righe ai processi.

![[codiceDatiContigui.PNG]]

## Tipi di dati vettoriali

![[tipiDatiVettoriali1.PNG]]

```c
int MPI_Type_vector(int count, int blocklength, int stride, mPI_Datatype oldtype, MPI_Datatype *newtype)
```

![[tipiDatiVettoriali2.PNG]]

Creazione di un tipo di dati che rappresenta la colonna di un array e invio ai processi.

![[tipiDatiVettoriali3.PNG]]

## MPI_Type_indexed

![[indexed1.PNG]]

![[indexed2.PNG]]

## Dettagli sui tipi di dati

Sebbene si tratti di un tipo reale, per dichiarare una variabile del nuovo tipo di dati è necessario utilizzare una dichiarazione a *MPI_Datatype* e NON al nuovo tipo.

> **ESEMPIO:** inviare al processo 1 una variabile di tipo *new_type*:
>
```c
...
MPI_Type_commit(&new_type);
MPI_Datatype a; // or pointer if
		         // vector/array!
...
MPI_Send(&a, 1, new_type, 1, tag, MPI_COMM_WORLD);
...
```

## Pack/UnPack

*MPI_Pack* e *MPI_Unpack* consentono di "comprimere" e "dividere" esplicitamente i dati da posizioni di memoria contigue/non contigue. Non consente di definire un nuovo tipo di dati. Usato come "ultima risorsa" (overhead più elevato per il raggruppamento, sebbene facile da usare). Evita che MPI utilizzi il buffering.

![[packUnpack.png]]
