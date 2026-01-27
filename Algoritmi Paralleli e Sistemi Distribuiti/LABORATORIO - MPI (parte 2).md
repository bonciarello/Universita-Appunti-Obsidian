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
