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
