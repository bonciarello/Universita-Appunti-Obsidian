Una **transazione** è una unità logica di elaborazione composta da una sequenza di operazioni di lettura/scrittura sulla BD.
Le sue proprietà sono:

- *Atomicità:* è indivisibile;
- *Consistenza:* lascia la BD in uno stato consistente;
- *Isolamento:* viene eseguita indipendentemente dalla presenza di altre transazioni concorrenti;
- *Durabilità:* i suoi effetti sulla BD sono permanenti, se si è conclusa correttamente;
Facciamo un esempio. Vogliamo spostare soldi da un conto corrente all'altro tramite *Bonifico*:

```sql
Transaction BONIFICO(IN X, Y, Z INTEGER)
BEGIN TRANSACTION // trasferisci Z euro dal c.c. X al c.c Y
SELECT saldo INTO S, MaxScoperto INTO W
FROM conto
WHERE num_cc = X
if S - Z >= W
{
    UPDATE conto
    SET saldo = saldo - Z
    WHERE num_cc = X;
    
    UPDATE conto
    SET saldo = saldo + Z
    WHERE num_cc = Y;
}
COMMIT
```
Oppure:

```sql
Transaction BONIFICO(IN X, Y, Z INTEGER)
BEGIN TRANSACTION // trasferisci Z euro dal c.c. X al c.c Y

UPDATE conto, SET saldo = saldo - Z
WHERE num_cc = X;

UPDATE conto, SET saldo = saldo + Z
WHERE num_cc = Y;

SELECT saldo INTO S, MaxScoperto INTO W
FROM conto
WHERE num_cc = X

if S < W
{
    Rollback Work;
}
COMMIT
```

## Perchè le transazioni

Per far si che una sequenza di operazioni di scrittura/lettura sulla BD costituisca una unità indivisibile bisogna usare *BEGIN* e *COMMIT*.

```sql
BEGIN TRANSACTION
r(X)
w(X)
...
w(Y)
COMMIT
```

Tutte le operazioni tra *BEGIN* e *COMMIT* sono viste come un tutt'uno dal punto di vista logico, quindi, è necessario che vengano eseguite *tutte o nessuna*.

In assenza del *BEGIN* e del *COMMIT*, ognuna delle suddette operazioni sarebbe considerata come una operazione a sé stante.

Utlizziamo un esempio:

```sql
Transaction BONIFICO(IN X, Y, Z INTEGER)
BEGIN TRANSACTION // trasferisci Z euro dal c.c. X al c.c Y
SELECT saldo INTO S, MaxScoperto INTO W
FROM conto
WHERE num_cc = X
if S - Z >= W
{
    UPDATE conto
    SET saldo = saldo - Z
    WHERE num_cc = X;
    
    UPDATE conto
    SET saldo = saldo + Z
    WHERE num_cc = Y;
}
COMMIT
```

I due *UPDATE* formano un'unica operazione logica: trasferimento dell'ammontare Z dal conto X al conto Y. Non hanno quindi senso separatamente!

L'inserimento di un dipartimento e del rispettivo direttore nella seguente BD è una operazione atomica. Infatti, c'è un problema di ciclicità per il rispetto dei vincoli di integrità. Utilizziamo lo schema relazionale:

- **Imp(<u>matr**, nome, dip*, stip)</u>
- **Dip(<u>cod**, nome, direttore*)</u>

```sql
BEGIN TRANSACTION
INSERT Imp(335, aldo, d1, 2.000)
INSERT Dip(d1, ricerca, 335)
COMMIT
```

Come in questo caso, durante l'esecuzione di una transazione, la BD può transitare attraverso stati non consistenti.

## Rappresentazione delle transazioni

- **READ(X):** lettura di un elemento X della BD e copia nella variabile X della transazione;
- **WRITE(X):** scrittura del valore della variabile X della transazione nell'elemento della BD denominato X.

![[rappresentazioneTransazioni.PNG]]

## Il controllo di affidabilità: perché serve?

L'esecuzione di una transazione può essere pregiudicata da eventi anomali causati da malfunzionamenti:

- **Transaction failure:** la transazione abortisce;
- **System failure:** guasto hardware o software che causa l'interruzione di tutte le transazioni.

Un malfunzionamento durante l'esecuzione di una transazione potrebbe compromettere l'*atomicità* delle transazioni e la *persistenza* degli aggiornamenti.

```sql
Transaction BONIFICO(IN X, Y, Z INTEGER)
BEGIN TRANSACTION // trasferisci Z euro dal c.c. X al c.c Y
SELECT saldo INTO S, MaxScoperto INTO W
FROM conto
WHERE num_cc = X
if S - Z >= W
{
    UPDATE conto
    SET saldo = saldo - Z
    WHERE num_cc = X;
    
    UPDATE conto
    SET saldo = saldo + Z
    WHERE num_cc = Y;
}
COMMIT
```

*Transaction failure:* se la transazione subisse una interruzione dopo il primo UPDATE, si creerebbe la seguente situazione:

- l'ammontare Z è stato detratto dal cc = X, ma non è mai giunto a destinazione sul cc = Y;
- *RISULTATO:* c'è un ammanco di Z euro!

*System failure:* Se il sistema subisce una interruzione dopo che la transazione ha raggiunto il COMMIT, ancorché tutti gli aggiornamenti siano stati eseguiti, non è garantito che gli effetti siano stati resi persistenti nella BD.

## Sistema di gestione dell'affidabilità

Il DBMS deve essere dotato di meccanismi che garantiscono il principio "tutto o niente", cioè: *tutti gli aggiornamenti contenuti in una transazione sono eseguiti ed i loro effetti sono resi permanenti (**durabilità**)* oppure *la transazione non viene completata e non si ha alcun effetto sulla BD*.

Il controllo di affidabilità garantisce due proprietà: *atomicità* e *durabilità* (o persistenza).

Il **sistema di gestione dell'affidabilità di un DBMS** implementa le politiche per il controllo dell'affidabilità.

## Gestione del buffer e affidabilità

Le dimensioni tipiche delle BD non consentono il loro trasferimento in memoria centrale (MC) per l’esecuzione efficiente delle interrogazioni.

È pertanto necessario accedere di volta in volta alla memoria secondaria (MS) per trasferire i dati richiesti.

I dati in MS sono memorizzati nelle pagine, che sono le unità di trasferimento della MS alla MC.

Per limitare gli accessi alle pagine, che sono relativamente molto lenti, il DBMS utilizza opportuni *metodi di accesso* e tecniche di *gestione della memoria*, queste ultime basate sull’uso di un buffer.

Il buffer è una zona della memoria centrale che gestisce l’interazione tra quest’ultima e la memoria di massa, di fatto svolge il ruolo di una memoria cache.

Una pagina trasferita nel buffer da una transazione può essere riutilizzata successivamente da altre transazioni.

Il buffer è suddiviso in *frame* – ogni *frame* contiene una pagina.

Il **Gestore del Buffer (GdB)** è un componente del DBMS che riceve (da parte del modulo di esecuzione delle istruzioni SQL) richieste di accesso (in lettura o scrittura) alle pagine della MS.
Quando il GdB riceve la richiesta di una pagina P:

1. cerca P nel buffer;
1. se P non è presente, provvede al trasferimento di P dalla MS ad un frame del buffer;
1. restituisce l’indirizzo del frame al modulo chiamante.

![[gestioneBuffer.PNG]]

Se la pagina P viene modificata (dall’esecutore SQL), gli aggiornamenti apportati "vivono" in memoria centrale (nel buffer) fintantoché P non viene copiata nel DB – solo a questo punto diventano persistenti.

*Quando una pagina modificata viene copiata nella BD?* Dipende dalla politica di gestione del buffer.

La maggior parte dei DBMS adotta la **politica steal - no force**:

- *steal:* quando una nuova pagina deve essere caricata nel buffer, e tutti i frame sono pieni, una pagina non utilizzata da altre transazioni viene sovrascritta, dopo essere stata copiata nel DB (se è stata modificata);
- *no-force:* una pagina modificata non viene copiata nel DB in modo sincrono, ma solo quando (a seguito della politica steal) viene sovrascritta da un’altra pagina – quindi in un tempo non prevedibile.
Quando una pagina modificata viene copiata nella BD?

- una nuova pagina Q deve essere trasferita nel buffer;
- il buffer è pieno;
- il gestore sceglie una pagina da rimpiazzare, ad esempio, P;
- P viene copiata nel DB;
- Q viene copiata nel frame di P.

![[politicaSteal.PNG]]

Una conseguenza di questo tipo di politica è che non è possibile sapere con certezza se, ad un certo istante, gli aggiornamenti apportati ad una pagina sono stati copiati nella BD e, quindi, resi persistenti, oppure esistono solo nel buffer di memoria centrale.

Ciò rende necessario, da parte del Gestore dell’Affidabilità, l’applicazione di tecniche di UNDO (disfacimento) e REDO (rifacimento) per poter garantire atomicità e persistenza in caso di interruzione delle transazioni.

Queste operazioni si basano sull’uso del file di log.

## File di log

Il **file di log** è un file sequenziale che contiene le operazioni di aggiornamento fatte dalle transazioni.

![[fileLog.PNG]]

- **Pid:** identificativo della pagina
- **Before(P):** pagina prima dell’aggiornamento
- **After(P):** pagina dopo l’aggiornamento

## Transaction failure

Nel caso di transaction failure, si usa solo l’operatore UNDO.

Quando una transazione T fallisce prima del punto di COMMIT, le pagine modificate potrebbero essere già state copiate nella BD (conseguenza della politica steal di gestione del buffer).

Pertanto, il gestore dell’affidabilità, grazie al file di Log, deve ripristinare uno stato consistente della BD eliminando l’effetto degli aggiornamenti prodotti da T.

### UNDO

Per annullare gli aggiornamenti fatti (fino al momento dell'interruzione), il sistema scandisce a ritroso il file di log e ripristina le pagine prima che fossero modificate (*before(P)* nel file di log).

![[undo1.PNG]]

La transazione *T1* fallisce al tempo *t0*.

Non avendo raggiunto il punto di COMMIT, viene fatto l’UNDO, ripristinando nel DB la pagina *P20*, per esempio, *P20 = before(P20)*.

## System failure

Nel caso di *system failure*, il sistema si blocca, tutte le transazioni in esecuzione vengono interrotte, e le pagine nel buffer vengono perse.

Per il rispristino della BD bisogna compiere azioni sia per le transazioni che hanno raggiunto il Commit, sia per quelle che non lo hanno raggiunto.

Nel primo caso di procede ad un REDO, nel secondo caso si procede ad un UNDO.

### UNDO

![[undo3.PNG]]

Il sistema si ferma al tempo *t1*.

Si fa l’UNDO di *T2*, in quanto NON ha raggiunto il punto di COMMIT, a differenza di *T1*.

A tal fine, vengono copiate nel DB le pagine *before(P25)* e *before(P20)*.

### REDO

Quando una transazione raggiunge il punto di COMMIT, ha eseguito tutti gli aggiornamenti e li ha registrati nel file di log.

Non si ha tuttavia alcuna certezza sulla persistenza degli effetti degli aggiornamenti eseguiti, in quanto le pagine modificate nel buffer di memoria potrebbero non essere state ancora copiate nella BD (politica *steal*).

La transazione che ha eseguito il COMMIT si impegna comunque a rendere effettivi gli aggiornamenti fatti (*persistenza*).

Il sistema di gestione dell’affidabilità deve quindi provvedere, con l’ausilio del log, a rendere effettivi *tutti* gli aggiornamenti fatti.

Per ogni transazione T il cui record di COMMIT è nel file di Log, esegue un REDO, cioè, T viene "rifatta".

A tal fine, si ricaricano le pagine modificate (*after(P)* nel file di Log).

![[redo1.PNG]]

Il sistema si ferma al tempo *t1*.

Si fa il REDO di *T1*, in quanto ha raggiunto il punto di COMMIT.

A tal fine, viene copiata nel DB la pagina *after(P40)*.

Per limitare il numero di transazioni di cui fare il REDO, periodicamente viene eseguito un **checkpoint (CKP)**: tutte le pagine nel buffer modificate dopo l’ultimo CKP vengono copiate su disco.

In tal modo, gli aggiornamenti fatti fino a quel momento sono resi persistenti.

![[redo2.PNG]]

### Ripresa a caldo

Il checkpoint viene registrato nel log.

![[redo3.PNG]]

- il sistema cade al tempo *t0*;
- per le transazioni che hanno fatto il COMMIT prima dell'ultimo CKP non bisogna fare niente (*T3*);
- le transazioni che hanno eseguito il COMMIT nell'intervallo tra *t0* e l'ultimo CKP devono essere rifatte (REDO(T1, T4));
- le transazioni che al tempo *t0*, non hanno eseguito il COMMIT vanno disfatte (UNDO(T2)).
Il processo base per la ripresa a caldo è:

1. si ripercorre il log a ritroso fino all’ultimo CKP;
1. *setUNDO = \{transazioni attive al CKP\*}, *SetREDO = \{\*};
    - per ogni *Begin(T)*, metti *T* in *setUNDO*;
    - per ogni *Commit(T)*, sposta *T* da *setUNDO* a *setREDO*;
1. *Fase UNDO:* si torna indietro sul file di log disfacendo le transazioni in *setUNDO*, fino al *begin* della transazione più vecchia;
1. *Fase REDO:* di vs in avanti sul file di log rifacendo le transazioni in *setREDO*;
Riprendiamo l'**esempio del file di log di sopra**:

1. si ripercorre il log a ritroso fino all’ultimo CKP;
1. *setUNDO = \{T2,T4\*}, *setREDO = \{\*};
    - **Begin(T1):** *setUNDO = setUNDO + \{T1\* = \{T1,T2,T4\}};
    - **Commit(T4):** *setUNDO = setUNDO - \{T4\* = \{T1,T2\}}, *setREDO = setREDO + \{T4\* = \{T4\}};
    - **Commit(T1):** *setUNDO = setUNDO - \{T1\* = \{T2\}}, *setREDO = setREDO + \{T1\* = \{T1,T4\}};
    - *P20 = Before(P20)*;
    - *P30 = Before(P30)*;
    - *P50 = After(P50)*;
    - *P40 = After(P40)*.