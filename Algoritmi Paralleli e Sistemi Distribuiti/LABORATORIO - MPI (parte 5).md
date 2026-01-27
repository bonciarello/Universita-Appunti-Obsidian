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
