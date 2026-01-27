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
