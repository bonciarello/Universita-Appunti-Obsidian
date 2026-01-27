## Esecuzione concorrente di transazioni

Un DBMS è un sistema che supporta l’esecuzione di più transazioni che operano su dati condivisi - sistema multiutente.
L’esecuzione seriale sarebbe troppo inefficiente.
Facciamo degli esempi:

- sistema di prenotazione di compagnie di treni, aerei, ecc.
- BD bancarie
- ...
L’esecuzione concorrente consente di aumentare il *throughput* del sistema (numero di transazione per unità di tempo).

L'esecuzione concorrente significa che delle operazioni di una transazione si alternano ad operazioni di altre transazioni.

![[esecuzioneCorrente.PNG]]

## Il controllo della concorrenza: perché serve?

L’*esecuzione seriale* di più transazioni produce sempre un risultato corretto (per definizione).
L’*esecuzione concorrente* potrebbe generare risultati scorretti, diversi da quelli di qualsiasi esecuzione seriale.
Il controllo della concorrenza serve per evitare che si generino risultati scorretti, mantenendo un certo grado di concorrenza

**ESEMPIO:** sul conto corrente cointestato al sig. Rossi e alla moglie ci sono 100 euro. Il marito fa un versamento di 20 euro. Contemporaneamente, la moglie preleva 80 euro. 

![[contoCorrenteControlloConcorrenza.PNG]]

- *RISULTATO FINALE:* X = 120 euro, invece di X = 40 euro! Come se il prelievo della moglie non fosse mai avvenuto;
- *ANOMALIA:* lost update;
- *PROBLEMA:* T2 legge un valore provvisorio di X che T1 ha letto per modificare, ma la modifica non è ancor avvenuta.
Una transazione legge due volte lo stesso dato e, senza averlo modificato, trova valori diversi, ad esempio, due valori diversi del saldo sul conto corrente. 

![[lettureIrripetibili.PNG]]

- T1 legge X = 100 dalla BD;
- T2 scrive X = 20 nella BD;
- T2 rilegge X dalla BD e trova il valore 20;
- *ANOMALIA:* letture irripetibili.

![[aggiornamentoFantasma.PNG]]

Una transazione legge un valore non ancora modificato nell'altra transazione.
- in una BD, i due oggetti X e Y rispettano il vincolo di integrità X + Y = 100;
- T2 modifica X e Y nel rispetto del vincolo;
- nella T1 il vincolo risulta alterato: X + Y= 120;
- *ANOMALIA:* aggiornamento fantasma.

## Equivalenza e serializzabilità

Due esecuzioni delle stesse transazioni sono *equivalenti* se producono (sempre) gli stessi risultati.

Una esecuzione concorrente è *serializzabile* se è equivalente a qualche esecuzione seriale delle stesse transazioni.

Quindi, una esecuzione serializzabile è corretta.

**ESEMPIO:** 

![[equivalenza1.PNG]]

- *T1:* trasferisce 20€ dal conto corrente X al conto corrente Y
- *T2:* incrementa del 10\% il saldo di X durante il trasferimento fondi
- *S1:* concorrente
- *S2:* seriale T1 $<$ T2
È facile vedere che S1 e S2 producono lo stesso risultato, in quanto in entrambi i casi T2 legge il valore definitivo di X prodotto da T1. Quindi, S1 e S2 sono equivalenti. Essendo S2 seriale, S1 è *serializzabile*.

**ESEMPIO:** sul conto corrente cointestato al sig. Rossi e alla moglie ci sono 100 euro. Il marito fa un versamento di 20 euro. Contemporaneamente, la moglie preleva 80 euro. 

![[contoCorrenteControlloConcorrenza.PNG]]

- *RISULTATO FINALE:* X = 120;
- Diverso da quello prodotto da entrambi gli schedule seriali;
- *T1 $<$ T2:* X = 40;
- *T2 $<$ T1:* X = 40;
- L’esecuzione concorrente di T1 e T2 *NON è serializzabile*.

**ESEMPIO:** 

![[equivalenza3.PNG]]

- **S1 NON è serializzabile:** T1 legge il valore di X non ancora modificato da T2 ed il valore di Y modificato da T2. Quindi non può essere equivalente a nessuna delle due esecuzioni seriali T1 $<$ T2 e T2 $<$ T1;
- **S2 è serializzabile:** equivalente a T2 $<$ T1 in quanto T1 legge i valori di X e Y prodotti da T2.

## Grafo delle precedenze e c-serializzabilità

Un grafo delle precedenze è formato come di seguito:

- un nodo per ogni transazione;
    - nella transazione *T1* c’è una *r(X)* e in *T2* una *w(X)*, e *r(X)* precede *w(X)*;
    - nella transazione *T1* c’è una *w(X)* e in *T2* una *r(x)* o una *w(X)*, e la *w(X)* in *T1* precede l’operazione in *T2*;
- lo schedule è *c-serializzabile* (conflict-serializzabile) se il grafo è *aciclico*;
- se uno schedule è c-serializzabile è anche serializzabile (quindi corretto).

**ESEMPIO:** 

![[grafoPrecedenze1.PNG]]

**ESEMPIO:** 

![[grafoPrecedenze2.PNG]]

- *T1* trasferisce 20 euro da X a Y;
- Mentre ciò avviene, *T2* incrementa del 10\% sia X che Y;
- lo schedule *S* non è serializzabile: *T1* precede *T2* rispetto alla variabile X, mentre *T2* precede *T1* rispetto a Y;
- Quindi S non può produrre lo stesso risultato né di *T1 $<$ T2* né di *T2 $<$ T1*;
- il problema è che *T2* legge un valore provvisorio di Y.

![[grafoPrecedenze3.PNG]]

## Come controllare la concorrenza

Per evitare i problemi legati alla concorrenza, i DBMS usano comunemente la tecnica dei *lock* (blocchi) – la gestione del grafo delle precedenze sarebbe troppo costosa.
Per eseguire una operazione read o write è necessario acquisire prima un lock sulla risorsa cui si vuole accedere:

- **Read\_lock(X):** lock *condiviso* necessario per leggere l’oggetto X;
- **Write\_lock(X):** lock *esclusivo* necessario per modificare l’oggetto X.
Per rilasciare una risorsa X precedentemente acquisita si utilizza **Unlock(X)**.

I lock e gli unlock non sono visibili al programmatore (vengono implicitamente generati).

## Protocollo 2PL

Il **protocollo 2PL** (2-phase locking) prevede che dopo un’operazione di unlock non possa più essere effettuata una operazione di lock.
Se gli unlock vengono fatti tutti dopo il COMMIT, si parla di 2PL stretto.

**ESEMPIO:** 

![[protocollo2PL.PNG]]

- **T1** è una transazione 2PL *ben formata*;
- **T2** *non è ben formata*, in quanto vi è un *w\_lock* preceduto da un unlock;
- **T3** il 2PL è *stretto* (unlock dopo il commit).

## Sistema di gestione della concorrenza

Il Sistema di Gestione della Concorrenza (SGC) è un componente del DBMS che implementa la politica di gestione dei lock.

Quando una transazione deve fare una operazione su X, invia al SGC una richiesta di lock su X; in particolare:

- una richiesta di *r\_lock(X)* se l’operazione è *read(X)*;
- una richiesta di *w\_lock(X)* se l’operazione è *write(X)*;
Il SGC è un automa a stati finiti e risponde alle richieste di lock secondo il seguente schema:

*Su X un’altra transazione ha un lock di tipo:* 

![[SGC.PNG]]

## Protocollo 2PL genera schedule c-serializzabili

Lo schedule generato dall’applicazione del 2PL (stretto) è  c-serializzabile – quindi corretto.

![[protocollo2PLschedule1.PNG]]

- S1 non è c-serializzabile;
- S2, risultante dalla applicazione del 2PL, è c-serializzabile;

![[protocollo2PLschedule2.PNG]]

Facciamo un altro esempio:

![[protocollo2PLschedule3.PNG]]

- La modifica di X è posticipata
- In tal modo, la T1 legge lo stesso valore di X

## Problema della Lettura Sporca (dirty read)

La transazione T1 aggiorna X nella BD, poi fa rollback annullando l’aggiornamento nella BD.
La transazione T2 legge X prima del rollback della T1; quindi legge un valore *provvisorio* di X – che poi non sarà più presente nella BD.

![[dirtyRead1.PNG]]

- T1 accredita 20€ sul cc X, portandolo, ad es., a 120€;
- T2 legge 120€ e su tale valore opera, ad es., una trattenuta in percentuale. Ma 120€ è un valore provvisorio, poi riportato a 100, quindi l’addebito risulta più alto del dovuto (cioè, di quanto sarebbe stato se applicato a 100€).

Il 2PL *stretto* risolve questo problema.

![[dirtyRead2.PNG]]

- nel 2PL stretto, l’unlock avviene dopo il Commit
- quindi se T1 va in abort (prima del Commit), su X risulta un *w\_lock*, e pertanto T2 NON può leggere il valore provvisorio di X (essendo in wait);
- infatti T2 legge il valore di X prodotto da T1 solo se questa ha raggiunto il punto di Commit;
- altrimenti T2 legge il valore di X ripristinato dopo il rollback;

## Deadlock

L’applicazione del 2PL può generare deadlock (abbraccio mortale).

![[deadlock1.PNG]]

- T1 blocca T2 in scrittura;
- T2 blocca T1 in scrittura;
- T1 attende che T2 rilasci X, e viceversa;
- Abbraccio mortale!

## Come prevenire il deadlock

Utilizzare direttamente il *w\_lock*, senza passare per il *r\_lock* – quando ciò è possibile.

Ciò a scapito della efficienza, in quanto si rischia di bloccare troppi oggetti inutilmente.

![[deadlock2.PNG]]

## Come risolvere una situazione di deadlock

Si può risolvere con il **time-out**: ad ogni richiesta di lock è associato un tempo massimo di attesa; scaduto il tempo, la richiesta viene negata e la transazione "uccisa".

Questa è la tecnica più semplice e diffusa.