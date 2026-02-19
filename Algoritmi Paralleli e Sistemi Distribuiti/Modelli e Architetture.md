---
aliases: [Modelli Architetture, MA]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## [[Architettura]] di Von Neumann

Per oltre 50 anni, ogni computer ha adottato un modello comune di [[Architettura|architettura]] noto come macchina di Von Neumann, dal nome del matematico ungherese John von Neumann. Una macchina di Von Neumann utilizza il concetto di "programma memorizzato". La CPU esegue un programma memorizzato che specifica una sequenza di letture e scritture in memoria.

## [[Architettura]] di base
La **memoria** viene utilizzata per memorizzare sia le istruzioni del programma che i dati. Le **istruzioni** nel programma codificano i dati che dicono al computer cosa fare. I **dati** sono semplicemente informazioni utilizzate dal programma. Una **Central Processing Unit (CPU)** preleva le informazioni e/o i dati dalla memoria, decodifica le istruzioni e le esegue in sequenza.

![[architetturaBase.png]]

## Tassonomia di Flynn

Nel 1966, Michael J. Flynn classifica i sistemi informatici in base alla molteplicità del flusso di istruzioni e del flusso di dati che può essere gestito in:
- SISD (Single Instruction Single Data)
- SIMD (Single Instruction Multiple Data)
- MISD (Multiple Instruction Single Data)
- MIMD (Multiple Instruction Multiple Data)

![[tassonomiaFlynn.png]]

## Single Instruction, Single Data (SISD)

**Istruzione singola:** viene eseguito un solo flusso di istruzioni durante un ciclo macchina. **Dati singoli:** viene utilizzato un solo flusso di dati come input durante un ciclo macchina. *Esecuzione deterministica*


*Esempi:* la maggior parte dei PC, delle workstation e dei mainframe con una singola CPU

![[SISD2.png]]

Macchine SISD sono UNIVAC, CRAY 1, PDP 1 e NOTEBOOK.

## Single Instruction, Multiple Data (SIMD)

**Istruzione singola:** tutte le unità di calcolo eseguono la stessa istruzione ad ogni clock **Dati multipli:** ogni elemento dell'unità di elaborazione può operare in una data diversa In genere, questa macchina è come un dispatcher di istruzioni, una rete interna a larghezza di banda elevata e un gran numero di piccole unità per le istruzioni. Utilizzato principalmente per problematiche "specialistiche" caratterizzate da un alto grado di regolarità, come l'elaborazione delle immagini (es. Schede GPU). *Esecuzione sincrona (lockstep) e deterministica.*

*Esempi:*
- *Array di processori:* macchina di connessione CM-2, MasPar MP-1, MP-2;
- *Pipeline vettoriali:* IBM 9000, Cray C90, Fujitsu VP, NEC SX-2, Hitachi S820;
- *Processori GPU di computer standard* (NVIDIA, ATI, ecc.) *o dedicati* (TESLA).

![[SIMD2.png]]

Macchine SIMD sono Cray XMP, Thinking Machines (CM2) e Cell Processors (GPU).

## Architetture della pipeline

```c
float x[100], y[100], z[100];
for(i=0; i<100; i++)
    z[i]= x[i] + y[i];
```

Una singola istruzione può consistere in:
1. recupero dell'operando *a* dalla memoria;
2. recupero dell'operando *b* dalla memoria;
3. sommatoria esponente;
4. sommatoria delle mantissa;
5. gestione overflow;
6. normalizzazione;
7. memorizzare *c*.

![[pipeline.png]]

## Architetture vettoriali?

```c
float x[100], y[100], z[100];
for(i=0; i<100; i++)
    z[i]= x[i] + y[i];
```

Diventa in Fortran 90: *z(1:100) = x(1:100) + y(1:100)*

## Programmazione SIMD

```c
for (i=0; i<1000; i++)
	if (y[i]!=0.0)
		z[i]=x[i]/y[i];
	else
        z[i]=x[i];
```

Supponendo un elemento per ogni processore, in esecuzione sincrono (in lockstep - ad ogni passo ogni processore fa la stessa cosa):

```c
1. if (local_y!=0.0)
2. if (local_y!=0.0) then local_z = local_x / local_y
   if (local_y==0.0) then do nothing
3. if (local_y!=0.0) then do nothing
   if (local_y==0.0) then local_z = local_x
```

Qui un altro esempio di programmazione SIMD:

![[programmazioneSIMD.png]]

## CUDA: modello di esecuzione

Un codice Cuda alterna porzioni del **codice seriale**, **eseguito dalla CPU** e **codice parallelo**, **eseguito sulla GPU**. Le porzioni di codice eseguite sulla GPU sono note come **kernels** (per esempio, funzioni in C / C + +). Un kernel è definito come una **griglia di blocchi** assegnati a vari multiprocessori e rappresenta un parallelismo a grana grossa. Ogni blocco esegue le unità di calcolo fondamentali, il **thread**. Un thread può appartenere a un solo blocco ed è identificato in modo univoco da un ID.

## Multiple Instruction, Single Data (MISD)

Esistono pochi esempi reali e pratici. Alcuni esempi potrebbero essere: filtri a frequenza multipla che operano su un unico segnale di flusso oppure algoritmi di crittografia multipli che tentano di decifrare un singolo messaggio.

![[MISD2.png]]

## Multiple Instruction, Multiple Data (MIMD)
Il tipo più comune di computer parallelo. **Istruzioni multiple:** ogni processore può eseguire un diverso flusso di istruzioni. **Dati multipli:** ogni processore può lavorare con un flusso di dati diverso L'esecuzione può essere sincrona o asincrona, deterministica o non deterministica.

*Esempi:* supercomputer attuali, "griglia" di computer paralleli basati su computer di rete e SMP multiprocessore, tutti i PC / notebook esistenti (ad es. Dual Core i7).

![[MIMD2.png]]

Macchine MIMD sono Compaq/HP Alpha server, AMD Opteron e IBM Blue Gene.

## Paradigmi MIMD

**MPMD:** ogni processore ha un programma diverso da eseguire. **SPMD:** ogni processore ha lo stesso programma in esecuzione, sebbene indipendente e asincrono. MPI (Message Passing Interface) si basa su questo paradigma. Tuttavia, con le tecniche di ramificazione, il modello può essere emulato in modo efficiente sui modelli MPMD SPMD.

## Modello SPMD
Il modello **SPMD (Single Program Multiple Data)** è un modello di programmazione parallela di alto livello basato sui modelli menzionati in precedenza. Un **programma** (attività) viene eseguito da tutti i singoli processi contemporaneamente. In qualsiasi momento, i processi possono eseguire le stesse istruzioni o altre istruzioni. I programmi **SPMD** di solito hanno una logica specifica che consente loro di eseguire, **"ramificando"**, solo quelle parti del programma per cui sono progettati. Cioè, il processo non ha bisogno di eseguire l'intero programma, ma solo una parte di esso. Tutte le attività di solito funzionano su dati diversi.

![[modelloSPMD.png]]

## Modello MPMD
Come il modello SPMD, il modello **MPMD (Multiple Program Multiple Data)** è un modello di programmazione parallela che può essere costruito su qualsiasi combinazione dei modelli precedenti. Le applicazioni tipiche di MPMD sono costituite da più programmi eseguibili. Mentre l'applicazione parallela è in esecuzione, ogni attività può eseguire lo **stesso programma o uno diverso**. Tutte le attività di solito funzionano su dati diversi.

![[modelloMPMD.png]]

## Modelli di comunicazione nelle piattaforme MIMD

Come abbiamo visto, esistono due tipi di scambio di dati tra attività parallele:
- accesso a uno **spazio dati condiviso**;
- **attraverso lo scambio di messaggi**.

Le piattaforme che forniscono uno spazio comune condiviso sono chiamate **spazio degli indirizzi condiviso o multiprocessori** (memoria condivisa). Le piattaforme che consentono lo scambio di messaggi sono chiamate **piattaforme di passaggio di messaggi o multi-computer** (memoria distribuita).

## Piattaforme di spazi di indirizzi condivisi (memoria condivisa)

Una parte (o tutta) la memoria è direttamente accessibile da tutti i processori. I processori interagiscono modificando i dati memorizzati in questo "spazio" condiviso. Se il tempo impiegato da un processore per accedere a qualsiasi parola di memoria del sistema (locale o globale) è identico, la piattaforma è classificata come **accesso alla memoria uniforme (UMA)**, altrimenti come accesso alla memoria **non uniforme** della macchina **(NUMA)**.

## Piattaforme NUMA e UMA per spazi di indirizzi condivisi

![[piattaformNUMAeUMA.png]]

La distinzione tra le piattaforme UMA e NUMA è importante dal punto di vista della progettazione algoritmica. Le macchine NUMA richiedono la **"località"** negli algoritmi per ottenere buone prestazioni. La programmazione di queste piattaforme è più semplice (rispetto al Message Passing) perché le letture e le scritture in memoria sono **implicitamente visibili** ad altri processori. Tuttavia, le operazioni di lettura e scrittura sui dati condivisi devono essere **coordinate**. **Le cache di queste macchine richiedono l'accesso coordinato a più copie**. Questo porta al problema della **coerenza della cache**. Un modello più debole richiede una **mappa degli indirizzi**, invece di un accesso coordinato. Questi modelli sono chiamati macchine coerenti con la cache e non spazio di indirizzi condivisi.


## Classi di spazi di indirizzi condivisi

Abbiamo due tipi di accesso:
- **accesso alla memoria uniforme (UMA):**
    - rappresentato principalmente da macchine SMP (symmetric multiprocessor);
    - processori identici;
    - tempi di accesso alla memoria identici;
    - a volte chiamato CC-UMA - Cache Coherent UMA. Coerenza cache significa che se un processore aggiorna una posizione nella memoria condivisa, gli altri processori sono consapevoli della modifica. La coerenza della cache viene eseguita a livello di hardware.
- **accesso alla memoria non uniforme (NUMA):**
    - implementato collegando fisicamente due o più SMP;
    - un SMP può accedere direttamente alla memoria dell'SMP;
    - non tutti i processori hanno lo stesso tempo di accesso alla memoria;
    - tempi di accesso più lenti attraverso i collegamenti;
    - se la coerenza della cache viene mantenuta, viene chiamata CC-NUMA-Cache Coherent NUMA.

## Spazio indirizzo condiviso vs. macchine con memoria condivisa

È importante notare la **differenza** tra i termini spazio degli indirizzi condiviso e memoria condivisa. Il **primo** si riferisce al modello di programmazione e il **secondo** alla macchina fisica. È possibile ottenere, ad esempio, un indirizzo condiviso utilizzando una memoria fisicamente distribuita (ottenendo in pratica un NUMA).

## Pro e contro (Spazio indirizzo condiviso)

*Vantaggi:* l'indirizzamento dello spazio consente una programmazione di tipo user-friendly. La condivisione dei dati tra i task è veloce e uniforme grazie alla "vicinanza" della memoria alla CPU.

*Svantaggi:* lo svantaggio principale è la mancanza di scalabilità tra memoria e CPU. Aggiungendo più CPU, si verifica un aumento "geometrico" del traffico sulla CPU della memoria del bus condiviso e sulla cache sistemi coerenti, un aumento geometrico della cache / memoria del traffico. La responsabilità del programmatore di garantire che la sincronizzazione costruisca un accesso corretto alla memoria globale.

*Costo:* è difficile e costoso progettare e produrre macchine a memoria condivisa con un numero maggiore di processori... (max 64-128 proc).

## Metodi alternativi per la programmazione multiprocessore (memoria condivisa)
- **Thread** (Pthreads, Java, ecc.), in cui il programmatore scompone il programma in singole sequenze parallele, ciascuna delle quali è un "thread" e in grado di accedere a variabili dichiarate al di fuori di questo thread;
- Un **linguaggio sequenziale (standard)**, con direttive di compilazione del preprocessore, adatto per la specifica di variabili condivise e la specifica del parallelismo. ES: *[[OpenMP]]* (standard industriale);
- Un **linguaggio di compilazione (standard)** con librerie (ad esempio metodi, ecc.) Per la dichiarazione e l'accesso alle variabili condivise;
- Un **linguaggio di programmazione parallelo con sintassi del parallelismo** in cui il compilatore crea il codice di esecuzione appropriato per ogni processore;
- Un **linguaggio di programmazione sequenziale**, abbinato ad un compilatore "parallelo" ad hoc per la conversione in codice.

## Coerenza della cache nei sistemi multiprocessore

Le [[Reti|reti]] di interconnessione **forniscono** i meccanismi di base per il trasferimento dei dati. Tuttavia, nel caso di macchine con spazio di indirizzi condiviso, è necessario hardware aggiuntivo per **coordinare l'accesso** ai dati che possono avere **più copie nella rete**. Quando il valore di una variabile cambia, tutte le altre copie devono essere **invalidate** o **aggiornate**.

![[coerenzaCache.png]]

## Coerenza della cache: aggiorna o invalida i protocolli

Se un processore legge un valore solo una volta e non è più necessario, un protocollo di aggiornamento può generare un **overhead significativo**. Se due processori che lavorano in modalità "interleaved" accedono a una variabile, è meglio utilizzare un protocollo di aggiornamento. Entrambi i protocolli soffrono di sovraccarichi di **falsa condivisione (false sharing)**, un tipico modello di utilizzo che riduce le prestazioni. *La maggior parte delle macchine utilizzano protocolli invalidanti!*

## Falsa condivisione (False Sharing)

Parti diverse di un blocco (linea cache) viene richiesto da processori diversi ma non gli stessi dati. In questo caso, se un processore scrive una parte del blocco, le copie del blocco completo in altre cache devono essere aggiornate o invalidate, degradando le prestazioni del sistema, dato che i dati effettivi (cioè l'intero blocco) non sono condivisi.

![[falseSharing.PNG]]

L'uso dei protocolli di aggiornamento è migliore, sebbene la maggior parte delle macchine moderne adotti protocolli non validi.

### Esempio

```c
struct foo {
    int x;
    int y;
};

static struct foo f;
/* The two following functions are running concurrently: */

int sum_a(void)
{
    int s = 0;
    int i;
    for (i = 0; i < 1000000; ++i)
        s += f.x;
    return s;
}

void inc_b(void)
{
    int i;
    for (i = 0; i < 1000000; ++i)
        ++f.y;
}
```

Qui, *sum_a* potrebbe aver bisogno di rileggere continuamente *x* dalla memoria principale (invece che dalla cache) anche se la modifica simultanea di *y* di *inc_b* dovrebbe essere irrilevante.

## Il mantenimento della coerenza invalidando i protocolli

Ogni copia dei dati è associata a uno **stato**. I possibili stati di una variabile sono: **condivisa, non valida o sporca**. Nello **stato condiviso**, sono presenti più copie valide dei dati. Se un processore modifica un valore, deve essere generato un invalidamento su altre copie di altri processori. La copia si sporca. Nello **stato non valido**, la copia dei dati non è valida e una lettura genera una richiesta di dati. Nello *stato sporco*, esiste solo una copia e quindi non deve essere generata alcuna invalidazione.

![[mantenimentoCoerenza.png]]

## Il mantenimento della coerenza

![[esCoerenza.png]]

## Sistemi di cache Snoopy

Come vengono inviati gli invalidi al processore giusto? Nelle **cache snoopy** (bus, squilli) è presente un dispositivo di trasmissione che "ascolta" e invalida tutte le richieste di letture, eseguendo le opportune operazioni sulla base dello schema precedente.

![[snoopyCache.PNG]]

## Piattaforme di passaggio dei messaggi (memoria distribuita)
Queste piattaforme includono un set di processori con una propria **memoria esclusiva** (dal punto di vista logico). Esempi sono cluster di workstation e multicomputer con spazio indirizzo non condiviso (multicomputer spazio indirizzo non condiviso). Queste piattaforme sono programmate utilizzando (varianti di) le primitive di **invio** e **ricezione**. Librerie come MPI e PVM forniscono tali primitive. Non necessitano, ovviamente, dell'utilizzo di protocolli di cache-coherence.

![[piattaformePassaggioMessaggi.PNG]]

## Architetture ibride

**Le architetture di computer più potenti utilizzano memoria condivisa e distribuita.** Il componente di memoria condivisa è solitamente un SMP coerente con la cache e quindi i processori di un dato SMP accedono a una memoria di indirizzi globali. Il componente di memoria distribuita è la rete di SMP. La tendenza attuale è l'utilizzo di questo tipo di macchine. **Vantaggi e svantaggi:** ciò che è comune ai vari componenti presi singolarmente. Uso congiunto MPI - [[OpenMP]] (e GPGPU)!

![[architetturaIbride.png]]

## Modelli (software) in programmazione parallela

- Passaggio del messaggio
- Dati paralleli
- Memoria condivisa
- Discussioni
- Ibrido (Es: [[OpenMP]] + MPI)
- SPMD (Single Program Multiple Data)
- MPMD (Multiple Program Multiple Data)

Sebbene non evidenti, tali modelli non sono specifici per il particolare tipo di macchina o [[Architettura|architettura]] di memoria. Ad esempio è possibile emulare Message Passing su architetture di memoria condivisa e viceversa (anche se in maniera molto "costosa").

## Sincronizzazione

È un operazioni di comunicazione sincrona. Coinvolge solo quelle attività che eseguono un'operazione di comunicazione. Quando un'attività esegue una comunicazione, è necessaria una forma di coordinamento con altre attività coinvolte nella comunicazione. Ad esempio, prima che un'attività possa eseguire un'operazione di invio, deve prima ricevere un riconoscimento dall'attività ricevente, che indica che "va bene inviare...".

*Barriera:*
- di solito coinvolge tutte le attività;
- ogni attività esegue il codice finché non "raggiunge" una barriera. Successivamente, si ferma o "si blocca";
- quando l'ultima attività raggiunge la barriera, tutte le attività vengono sincronizzate.

*Blocco/semaforo:*
- coinvolge un numero qualsiasi di attività;
- viene generalmente utilizzato per **serializzare** (proteggere) l'accesso ai dati globali o a una sezione di codice. Solo un'attività alla volta può utilizzare (proprio) il blocco/semaforo/flag;
- la prima attività acquisisce il blocco lo imposta;
- questa attività può quindi, in modalità provvisoria, accedere a dati o codice;
- altre attività possono tentare di acquisire il blocco, ma devono attendere fino a quando l'attività che lo possiede lo rilascia;
- potrebbe **bloccanti** o non **bloccanti**.

![[sincronizzazione.PNG]]

La sincronizzazione è necessaria ma può causare tempi di inattività su alcuni processori e sovraccarico per eseguire la primitiva di sincronizzazione.

## Message Passing Model
1. Un insieme di attività che utilizzano la loro memoria durante il calcolo. Più attività possono risiedere sulla stessa macchina fisica e su macchine diverse;
2. Le attività scambiano dati tramite comunicazioni inviando e ricevendo messaggi;
3. Il trasferimento dei dati richiede solitamente operazioni cooperative per ogni processo. Ad esempio, un'operazione di invio deve avere una ricezione corrispondente.

![[messagePassingModel.PNG]]

Dal punto di vista del programmatore, le implementazioni di passaggio di messaggi di solito includono una libreria di funzioni incorporate nel codice. Il programmatore è responsabile della determinazione del parallelismo. MPI (Message Passing Interface) è lo standard de-facto per il modello Message Passing. Un possibile approccio Single Program Multiple Data (SMPD):

```c
if (my_process_rank ==0)
    MPI_Send(&x, 1, MPI_FLOAT, 1, 0, MPI_COMM_WORLD);
else
    if (my_process_rank ==1)
        MPI_Recv(&x, 1, MPI_FLOAT, 0, 0, MPI_COMM_WORLD, &status);
```

## Il paradigma MPI
Ciascun processore in un programma di trasmissione di messaggi esegue un sottoprogramma scritto in un linguaggio sequenziale convenzionale, ad esempio C o Fortran e tipicamente lo stesso su ciascun processore (SPMD).

Tutto il lavoro e la distribuzione dei dati si basa sul valore di myrank restituito dalla routine di libreria speciale.

La comunicazione avviene tramite routine di invio e ricezione speciali (Message Passing).

![[paradigmaMPI.PNG]]

## Comunicazione e celle fantasma/halo

```c
Do i = 2, 99
    b(i) = a(i) + f*(a(i-1) + a(i+1) - 2*a(i))
Enddo
```

La comunicazione è necessaria sui confini.

![[comunicazione.PNG]]

![[halo.PNG]]

## Shared Memory Model

Nel modello di memoria condivisa, le attività condividono uno spazio di indirizzi comune, in cui leggono e scrivono in modo sincrono. Vari meccanismi, come semafori o blocchi, possono essere utilizzati per controllare l'accesso alla memoria condivisa.

**PRO:** poiché non esiste la nozione di "proprietà" dei dati, non è necessaria una comunicazione esplicita tra le attività. Lo sviluppo di programmi (sviluppo di programmi) può essere spesso semplificato. Parallelismo incrementale... ([[OpenMP]]!).

**CONTRO:** può diventare difficile gestire la località dei dati (ad esempio, sezioni critiche, ecc.)

Si necessita di strumenti che permettono di creare processi statici o dinamici (fork, exec, joins, ecc.) Il coordinamento tra i processi viene tipicamente effettuato tramite 3 primitive:
- variabili condivise;
- sezione critica;
- sincronizzazione (barriera).

### Esempio

```c
int private_x; //variabile locale
shared int sum = 0;
...
sum = sum + private_x;

//codice eseguito su ogni processo
```

Facciamo un esempio, un possibile scenario su 2 processi:

![[esempioSMM.PNG]]

Il processo 0 restituisce *sum* pari a 2 (fino a qui corretto) ma il processo 1 restituisce *sum* pari a 3 e non è corretto: *sum* dovrebbe essere pari a 5.

Per risolvere questo problema, possiamo risolvere con il semaforo binario: l'operazione *sum = sum + private_x* DEVE essere eseguita da un processo alla volta!

```c
shared int s = 1;
while (!s); //wait until s=1
s=0; //close access
sum = sum + private_x; //critical sect.
s = 1; //reopen access
```

**Atomicità:** mentre un processo controlla s = 1, per verificare se va bene entrare nella sezione critica, un altro processo potrebbe memorizzare s = 0! Vengono adottate due funzioni speciali:

```c
void P(int* s);
void V(int* s);
```

- *P* ha l'effetto di impedire ad altri processi di accedere a s una volta che un processo esce dal ciclo;
- *V* imposta s a 1, ma ATOMICAMENTE.

Identificata da Dijkstra nel 1968, la lettera P in olandese sta per **passeren**, che significa "vai" e la lettera V deriva dall'olandese **vrijgeven**, che significa "liberazione".

Infine, per attendere che tutti i processi siano terminati prima di stampare, utilizziamo una barriera:

```c
int private_x;
shared int sum =0;
shared int s = 1;
...
P(&s);
sum = sum + private_x;
V(&s);
Barrier();
if (I am process 0)
    printf("sum = %d\n", sum);
```

## Modello thread
Nel modello thread, un singolo processo può avere diversi "percorsi" simultanei.

Il programma principale *a.out* è pianificato per essere eseguito dal sistema operativo nativo. *a.out* viene caricato e acquisisce il sistema e le risorse utente necessarie per essere eseguito.

*a.out* esegue il lavoro in sequenza, quindi crea una serie di attività (ad esempio, thread) che possono essere pianificate ed eseguite dal sistema operativo contemporaneamente.

Ogni thread ha dati locali, ma condivide anche le intere risorse di *a.out*. Ciò consente di risparmiare l'overhead generato dalla replica delle risorse del programma per ogni thread. Ogni thread beneficia anche della visione globale della memoria condivisa di *a.out*.

I thread sono comunemente associati alle architetture di memoria condivisa e ai [[Sistemi Operativi|sistemi operativi]].

## Implementazioni di thread
Dal punto di vista della programmazione, le principali implementazioni del thread includono:
- una libreria di subroutine chiamate dall'interno del codice parallelo;
- una serie di direttive del compilatore incorporate nel codice parallelo e sequenziale.

### POSIX Threads

Basato su biblioteche; richiede la codifica parallela. Specificato da IEEE POSIX 1003.1c (1995). Solo per il linguaggio C. Comunemente noto come Pthreads. La maggior parte dei fornitori di hardware offre Pthread oltre alle loro implementazioni proprietarie. **Parallelismo molto esplicito, richiede un livello di dettaglio molto significativo da parte del programmatore.**

```
