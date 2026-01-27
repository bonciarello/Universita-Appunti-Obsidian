Una base di dati è generalmente molto più grande della capacità della memoria centrale, per cui il DBMS deve frequentemente accedere alla memoria secondaria durante l’esecuzione delle interrogazioni e degli aggiornamenti. Siccome il tempo medio di accesso alla memoria secondaria è migliaia di volte maggiore del tempo medio di accesso alla memoria principale, accessi ripetuti al disco diventano il collo di bottiglia dell’intero processo. Per tale motivo, la progettazione fisica si pone l’obiettivo di individuare una configurazione di indici tali da **minimizzare il numero di accessi alla memoria secondaria** (disco).

## Nozioni introduttive

### Memorizzazione di una relazione su disco

Le relazioni di una BD sono memorizzate come file nella memoria di massa (disco) dell’elaboratore. Un disco è organizzato in tracce e pagine (dette anche blocchi o settori). Una pagina è l’unità di trasferimento tra la memoria di massa e la memoria centrale, ed è una dimensione di qualche KB. Una relazione viene memorizzata su un certo numero di pagine, in funzione del numero di tuple e della loro lunghezza. Ogni pagina contiene un numero intero di tuple.

Quando una tupla deve essere acceduta su disco, l’intera pagina che la contiene viene trasferita nel buffer di memoria centrale (vedi par. successivo). Trasferendo tutta la pagina si ha il vantaggio della *locality of reference* - se le tuple sono organizzate sulla base dell’ordine con cui vengono accedute allora è probabile che le altre tuple contenute nella pagina trasferita siano quelle a cui si richiede di accedere subito dopo.

Ci sono dischi a testina fissa (una testina per ogni traccia), e dischi a testina mobile. I parametri delle prestazioni sono:

- tempo di seek (posizionamento): 3-10ms;
- tempo di latenza rotazionale: 2-5ms;
- tempo di trasferimeto: dipende dalla velocità di trasferimento dal disco alla memoria centrale (qualche GB al secondo) e dalla lunghezza di una pagina; ad esempio, se l pagina è di 4KB e la velocità di trasferimento è di un GB/sec, allora il tempo di trasferimento è di 0,031msec.

![[memorizzazioneRelazioneDisco.PNG]]

Il tempo di accesso è pari alla somma dei primi due. Esso è dell’ordine di qualche decina di ms, migliaia di volte più elevato dei tempi di accesso alla RAM.

## Buffer di memoria centrale

Il buffer è un’aria di memoria centrale in cui vengono trasferite le pagine della memoria secondaria.

*Quando si cerca una tupla t in una pagina P di una relazione, la pagina viene preventivamente trasferita dal disco al buffer di memoria centrale - se non già presente in esso. Solo a questo punto viene effettuata la ricerca di t in P.*
Il buffer è suddiviso in un certo numero di frame, ognuno della dimensione di una pagina (quindi un frame memorizza il contenuto di una pagina).

![[bufferMemoriaCentrale1.PNG]]

Il Gestore del buffer (GdB) si occupa del caricamento/scaricamento di pagine da e per la memoria secondaria. Durante l’esecuzione di una query, il modulo Esecutore di Query invia al GdB le sue richieste di accesso alle pagine della BD.

![[bufferMemoriaCentrale2.PNG]]

## Occupazione di memoria di una relazione

Data una relazione R formata da un numero di tuple *NT* di lunghezza *LT* e memorizzata su pagine di lunghezza *LP*, l’occupazione di memoria di R è (numero di pagine):
- Num tuple per pagina: $ TP = \lfloor LP/LT \rfloor	$;
- Occupazione memoria (numero di pagine): $ N = \lceil NT/TP \rceil $;

**ESEMPIO:** Si consideri una relazione R con *NT = 300.000*, ognuna di lunghezza *LT = 100 byte*, memorizzate su pagine di lunghezza *LP = 1024 byte*. Le tuple sono indivisibili. Pertanto, il numero di tuple per pagina è 10, ed il numero di pagine necessario per memorizzare R è pari a **N = 300.000/10 = 30.000**.

## Costo del trasferimento dei dati

L’esecuzione di una interrogazione richiede il trasferimento nel buffer di memoria di un certo numero di pagine della BD. Essendo i tempi di trasferimento da e per la memoria secondaria molto elevati rispetto ai tempi della CPU e di accesso alla memoria centrale, il tempo complessivo richiesto per l’esecuzione di una query è dominato dalle operazioni di accesso alla memoria secondaria.

**ESEMPIO:** si consideri la relazione Cittadino che memorizza tutti i cittadini italiani. Supponiamo che il numero di tuple sia pari a $ 60*10^6 $ distribuite su un numero di pagine *N = 106*. Il tempo complessivo per la lettura di tutte le tuple della relazione è determinato dal tempo richiesto per trasferire tutte le pagine su cui la relazione è memorizzata. Pertanto, se il tempo medio necessario per accedere ad una pagina e trasferirla in memoria centrale è mediamente di 10msec, il tempo totale è di 5.000sec (circa 1,5 ore).
Ciò premesso, considerato che il tempo di trasferimento delle pagine dalla memoria secondaria alla memoria centrale dipende dalle caratteristiche hardware del sistema di calcolo, nonché da altri fattori, come il carico di lavoro, ecc., il costo di una operazione di accesso ai dati si misura, non usando il tempo come unità di misura, ma in termini di numero di trasferimenti di pagine richiesti - che dipende solo dall’organizzazione fisica dei dati, ed è invariante rispetto ad altri fattori.

## Organizzazione fisica dei dati

Per ogni relazione R della BD, bisogna definire:

- un metodo di accesso primario (o struttura primaria), che stabilisce il criterio in base al quale si memorizzano le tuple di R;
- un insieme di metodi di accesso secondari usati per velocizzare l’accesso alle tuple di R.

## Metodi di accesso primari

Un metodo di accesso primario è un meccanismo che definisce le modalità di memorizzazione delle tuple di una relazione. Esso può essere di tipo sequenziale, oppure ad accesso calcolato (funzione hash) – le strutture ad albero vengono invece prevalentemente utilizzate come metodi secondari.

In letteratura, i metodi di accesso calcolato e ad albero sono anche chiamati *indici*.

### Organizzazione sequenziale

È una tecnica di **memorizzazione** che prevede che le tuple vengano memorizzate nel file:

1. nell'ordine con cui sono inserite nel DB (la relazione è memorizzata come un heap - struttura disordinata o seriale), oppure;
1. secondo l’ordine di uno o più attributi (struttura ordinata).
Per ottenere quindi una relazione ordinata (rispetto a un qualche attributo) è necessario scegliere una organizzazione primaria sequenziale.

### Organizzazione ad accesso calcolato - funzione hash

È una tecnica di **memorizzazione** e **ricerca**. In quanto tecnica di memorizzazione, costituisce un metodo di accesso primario.

Data una relazione R, memorizzata su N pagine, ed un attributo A di R, un **indice hash** h su A è una funzione:

$
h: D_{A} \rightarrow I
$
che associa ad ogni valore di A nel dominio DA un valore nello lo spazio degli indirizzi $ I = [0, N-1] $. Più precisamente:

- **Dominio \begin{math** D_{A} $:} è detto anche spazio delle chiavi, cioè, l’insieme formato da tutti i valori che la chiave di indicizzazione può assumere. Per esempio, se la chiave è il CODICE\_FISCALE, lo spazio delle chiavi è costituito da tutti i possibili codici (pari a $ 25^9 * 10^7$) che si possono ottenere per una stringa del formato AAAAAANNANNANNNA, in cui A è un carattere alfabetico e N rappresenta una cifra.
- **Codominio \begin{math** I $:} detto anche spazio degli indirizzi, cioè, l’insieme formato da tutti gli indirizzi di pagine disponibili per la memorizzazione delle tuple di R. Lo *spazio degli indirizzi* è quindi costituito dall’insieme \{0,1, 2,...,N-1\}, se la relazione su cui memorizzare una data tupla consiste di N pagine.
Normalmente il numero di pagine presenti nella base di dati per memorizzare una relazione è molto minore della cardinalità dello spazio delle chiavi. Ne consegue che più tuple vengono in genere memorizzate nella stessa pagina (tale fenomeno è denominato “collisione” e le tuple che collidono vengono dette sinonimi). Quando una pagina non è più sufficiente per memorizzare tutti i sinonimi, si creano delle catene laterali (di overflow), che comportano un certo numero di accessi supplementari.

La funzione matematica che si usa per l’hash è la seguente:

$
h(a) = a\ mod\ N
$
dove:
- *a:* codifica numerica della chiave di indicizzazione;
- *N:* numero di pagine del file;
- *mod:* è la funzione modulo che restituisce il resto della divisione intera tra a e N - un valore quindi nell’intervallo [0, N-1].
Il valore *h(a)* è interpretato come il numero di pagina di memorizzazione/ricerca della tupla con *A=a*. L’hash è detto statico se **N** è costante.

Un indice hash viene utilizzato sia *(1)* per determinare la pagina su cui una nuova tupla t deve essere memorizzata sia *(2)* per cercare successivamente t sulla base del valore della chiave A.

### Esempi

**ESEMPIO 1:** assumiamo che sull’attributo chiave A di R sia definita una funzione hash - scelta fatta dal progettista in fase di progettazione fisica. 

![[funzioneHash1.PNG]]

Quando la tupla t con *A=a* (assumiamo che A sia chiave primaria) viene inserita nella relazione R (attraverso il comando *SQL INSERT(t)*), al fine di individuare la pagina su cui memorizzarla, viene calcolata la funzione $    h(a) = a\ mod\ N $, e memorizza la la tupla t nella pagina con indirizzo *h(a)*.

Quando successivamente si fa una ricerca $ \sigma_{A=a} R $, per trovare la tupla che soddisfa la condizione *A=a*, si calcola ancora la funzione **h(a)**, ottenendo in tal modo l’indirizzo della pagina in cui la tupla si trova (in quanto precedentemente memorizzata con la stessa funzione).

**ESEMPIO 2:** supponiamo che la relazione R sia memorizzata su *N = 10* pagine. Supponiamo, inoltre, che la chiave di indicizzazione A sia di tipo intero (ad es., la matricola). Pertanto, la tupla in cui *A = 31* (lo studente con matricola 31) viene memorizzata nella pagina *h(31) = 31 mod 10 = 1*. Analogamente, la tupla con chiave *A = 41* viene memorizzata nella pagina 1. Quindi 31 e 41 sono sinonimi. 

![[funzioneHash2.PNG]]

Se i sinonimi sono tanti da non poter essere memorizzati tutti nella stessa pagina, si creano delle catene di **overflow**, cioè, catene di pagine che contengono solo sinonimi (ad es., 11, 21, 51, ...). 

![[funzioneHash3.PNG]]

**NOTA:** se l’attributo A di indicizzazione non è numerico, per calcolare *h(a)* si deve preliminarmente procedere ad una codifica numerica di *a*. Se a è una stringa $ c_{1}c_{2}...c_{n} $ di *n* caratteri, allora la trasformazione può far uso del codice numerico ASCII *code(\begin{math* c_{i} $)} associato ad ogni singolo carattere, ed ottenere la codifica numerica di a come, ad esempio, $ code(c_{1})*code(c_{2})*...*code(c_{n})$. Se ad esempio la stringa è *abc* allora la sua codifica numerica è pari a *97*98*99 = 941094*, essendo *code(a)=97*, *code(b)=98* e *code(c)=99*. Pertanto, quando si deve memorizzare una tupla di R in cui il valore di A è *abc*, assumendo *N = 100*, la pagina di memorizzazione è *h(941.094) mod 100 = 94*.

## Metodi di accesso secondari

I metodi secondari sono strutture solo di **ricerca** di tuple (non di memorizzazione). Su una relazione R, è possibile definire più metodi secondari di accesso ai dati per velocizzare l’esecuzione delle query. Questi metodi sono in genere strutture ad albero, B-tree o B+-tree (nel seguito ci limiteremo a considerare B+-tree). Anche la funzione hash può essere utilizzata come indice secondario.

### Funzione hash

Una funzione hash può essere utilizzata anche come metodo di accesso secondario di una relazione R - cioè, non come criterio di memorizzazione, ma solo per la ricerca delle tuple di R. A tal fine, si procede come segue: h(a) è il numero di pagina di un *file indice* che contiene i puntatori alle pagine del file che contiene le tuple della relazione R. Il file indice può contenere catene di overflow (per eccesso di sinonimi).

![[funzioneHashSecondario.PNG]]

### B+-tree

Un B+-tree è un indice tabellato (a differenza dell’indice hash che è calcolato). Un indice tabellato è un insieme di coppie (k, p), dove *k* è un valore della chiave e *p* è il puntatore alla pagina che contiene il record (o i record) con chiave *k*. Informalmente, un B+-tree è un indice a più livelli, con una struttura ad albero. Esso può essere definito su un qualsiasi attributo della relazione.

**ESEMPIO:** la chiave primaria A di una relazione R assume i seguenti valori: 1, 3, 5, 6, 7, 8, 9, 12. Si può costruire su R un indice ad albero (B+-tree) come quello rappresentato in figura. Come si può vedere, esso ha una radice, un livello intermedio formato da 2 nodi, e 5 nodi foglie. Ad ogni valore a della chiave A contenuto nelle foglie, è associato un puntatore che indica il numero di pagina su cui la tupla con A=a è memorizzata.  

![[bTree1.PNG]]

Un B+-tree di ordine m, costruito sull’attributo A, è un albero tale che:
    - *Vi* è un valore della chiave *A*, *pi-1* il puntatore al sottoalbero di sinistra e pi puntatore al sottoalbero di destra. Il sottoalbero di sinistra contiene valori *X $<$= Vi*, ed il sottoalbero di destra valori *X $>$ Vi* (oppure *X $<$ Vi* e *X $>$= Vi*, rispettivamente);
    - il numero di figli *k* è tale che $ \lceil m/2 \rceil <= k <= m$, se N non è la radice dell’albero, altrimenti *2 $<$= k $<$= m*;
    - i valori della chiave sono ordinati, cioè, *V1 $<$ V2 $<$ ... $<$ Vk-1*;
- ogni nodo foglia è del tipo *$<$V1, q1, ..., Vk-1, qk-1$>$*, dove *Vi* è un valore della chiave *A* e *qi* il puntatore alla pagina di R che contiene una tupla in cui *A=Vi*, e *k* è tale che $ \lfloor m/2 \rfloor <= k <= m-1$
Inoltre:

1. un B+-tree è bilanciato (le foglie sono tutte allo stesso livello);
1. ogni chiave appare in una foglia;
1. un nodo foglia ha un puntatore alla foglia successiva ed uno a quella precedente.
L’*altezza* (o *profondità*) di un B+-tree è pari al numero di nodi che si incontrano in un qualsiasi percorso dalla radice ad una foglia.

In maniera più informale, possiamo sintetizzare così alcune proprietà di un B+-tree di ordine *m*:

- ogni nodo non foglia ha massimo *m* figli;
- la radice ha minimo 2 figli;
- gli altri nodi non foglia hanno minimo $ \lceil m/2 \rceil $ figli, cioè, sono pieni almeno a metà;
- se un nodo non foglia ha *k* figli, ha *k-1* valori della chiave;
- un nodo foglia contiene minimo $ \lfloor m/2 \rfloor $ e massimo *m-1* chiavi - anche un nodo foglia è pieno almeno a metà.
Nella precedente figura, l’ordine è *m=3* e la profondità *h=3*. Inoltre:

- ogni nodo intermedio (non radice e non foglia) ha almeno 1 valore della chiave e 2 puntatori, ed al più 2 valori della chiave e 3 puntatori;
- la radice ha 1 valore della chiave e 2 puntatori;
- ogni nodo foglia ha minimo 1 e massimo 2 valori della chiave, con relativi puntatori al file.
*Memorizzazione di un B+-tree:* ogni nodo di un B+-tree viene memorizzato in una pagina del disco. L’ordine dell’albero dipende quindi dalla dimensione delle pagine (in genere tra 1 e 64 K), dalle lunghezze delle chiavi e dei puntatori.

**ESEMPIO:** si supponga che l’attributo di indicizzazione sia lungo 9 byte e che il puntatore alle pagine sia lungo 6 byte. Per definizione di B+-tree, ogni nodo può contenere al massimo *(m-1)* chiavi e m puntatori, essendo m l’ordine dell’albero. Pertanto, nell’ipotesi che una pagina sia lunga 2K byte, vale la relazione:

$
(m-1)*9 + m*6 <= 2048
$

da cui ricaviamo *m=137* quale valore massimo di *m*. 

![[bTree2.PNG]]

*Altezza minima di un B+-tree:* tutti i nodi sono pieni, cioè, contengono *m-1* chiavi

$ h_{min}(m, K) = \lceil \log_{m} K \rceil $

dove *m* è l’ordine del B+-tree e *K* il numero di valori della chiave di indicizzazione.

*Altezza Massima di un B+-tree:* tutti i nodi contengono il numero minimo di chiavi $ \lceil m/2 \rceil - 1 $ (e $ \lceil m/2 \rceil $ puntatori), tranne la radice che contiene una chiave e 2 puntatori

$ h_{max}(m, K) = \lceil \log_{m/2} K/2 \rceil + 1 $

Pertanto, per indicizzare un attributo con *K* valori diversi attraverso un albero di ordine *m*, la profondità *h(m, K)* dell’albero sarà $ h_{min}(m, K) <= h(m, K) <= h_{max}(m, K) $.

**ESEMPIO:** Si vuole indicizzare con un B+-tree una relazione su un attributo che può assumere *K=1.000.000* valori diversi. Assumendo *m=137*, le altezze minima e massima del B+-tree sono:

$ h_{min}(m, K) = \lceil \log_{m} K \rceil = \lceil \log_{137} 1.000.000 \rceil = 3 $

$ h_{max}(m, K) = \lceil \log_{69} 500.000 \rceil + 1 = 5 $

## Implementazione e costi dei principali operatori algebrici

Ad ogni operatore algebrico viene associato un costo, che dipende dalla sua implementazione. Il costo è espresso in termini di numero di accessi alle pagine della BD richiesti.

Le equazioni di costo riportate nel seguito di questo paragrafo sono delle approssimazioni che, ad esempio, non tengono conto della creazione del risultato.

### Operatore di selezione

L’operatore $ \sigma_{F}(R) $ seleziona le tuple di *R* che soddisfano la condizione F. Nel seguito ci limiteremo a considerare condizioni semplici del tipo *A=a*, dove *A* è un attributo di *R*.

### Ricerca sequenziale

Per accedere alle tuple che soddisfano F è sempre possibile fare una scansione sequenziale (o lineare) delle pagine di R (ciò diventa necessario in mancanza di indici su A).

    - accedi alla prima pagina P di R (tramite il Gestore del Buffer)
    - ricerca in P (nel buffer) la tupla t che soddisfa F
        - accedi alla pagina successiva P di R (tramite il Gestore del Buffer)
        - ricerca in P (nel buffer) la tupla t che soddisfa F
    - return t

**Costo medio =** $ \lceil N/2 \rceil $ richieste di accesso alle pagine di R

**ESEMPIO:** Si consideri la relazione Cittadino che memorizza tutti i cittadini italiani. Supponiamo che il numero di tuple sia pari a $ 60*10^6 $ distribuite su un numero di pagine $ N=10^6 $ . La selezione $ \sigma_{CF=xyz}(Cittadino)$ di un cittadino sulla base del codice fiscale costa in media 500.000 trasferimenti di pagine. Se il tempo medio necessario per accedere ad una pagina e trasferirla in memoria centrale è mediamente di 10msec, allora il tempo totale è di 5.000sec (circa 1,5 ore).

- *CASO 2 - L’attributo A non è chiave primaria:* esiste quindi un insieme di tuple che soddisfano la condizione A=a.

**Costo medio =** $ \lceil N/2 \rceil $ richieste di accesso alle pagine di R

**ESEMPIO:** si consideri ancora la relazione Cittadino, e assumiamo che sia ordinata rispetto all’attributo provincia di residenza. Quindi le tuple dei cittadini di una stessa provincia sono memorizzate l’una di seguito all’altra (formando un cluster). Considerato che le province sono 100, mediamente ogni cluster contiene $ 60*10^4 $ tuple (cioè, un centesimo dei cittadini), occupando un numero di pagine M pari ad *1/100* di N, cioè, M=104 pagine. Siccome *M $<<$ N*;il costo medio della ricerca $ \sigma_{prov=x}(Cittadino) $ si può approssimare a 500.000 pagine.

Se la relazione R *non è ordinata* rispetto ad A, le tuple in cui A=a sono distribuite in maniera casuale nelle pagine della relazione. Per reperirle tutte è quindi necessario scorrere tutta la relazione, ed il costo è uguale a N trasferimenti di pagine **Costo = N**.

**ESEMPIO:** e la relazione Cittadino di cui al primo esempio di organizzazione ad accesso calcolato non è ordinata rispetto alla provincia, il costo della ricerca $ \sigma_{prov=x}(Cittadino) $ è pari a 1.000.000 pagine, in quanto è necessario visitare tutte le pagine della relazione. 

![[ricercaSequenziale.PNG]]

### Ricerca binaria

L’algoritmo di ricerca binaria di una tupla richiede che la relazione sia ordinata rispetto all’attributo A di ricerca. Esso funziona come la ricerca binaria di un elemento in un array. Assumiamo che A sia chiave primaria (quindi esiste una unica tupla con A=a).

*Ricerca binaria di una tupla*

- Accedi (tramite il Gestore del Buffer) alla pagina mediana P di R. Se t è in P, allora la ricerca termina;
    - se *a* è minore di un qualsiasi valore di A presente nelle tuple in P, esegui la ricerca binaria nelle pagine che precedono P;
    - se *a* è maggiore di un qualsiasi valore di A presente nelle tuple in P, esegui la ricerca binaria nelle pagine successive a P
Il costo è, nel caso peggiore:

**\begin{math** Costo = \lceil \log (N) \rceil $}

**ESEMPIO:** la ricerca binaria sulla relazione di cui all’esempio 1 costa $ \log_2(30.000) = 15 $ accessi a pagine (nel caso peggiore), contro i 15.000 in media necessari nel caso della ricerca lineare.
La ricerca binaria di un cittadino richiede, nel caso peggiore, 20 accessi, anziché i 500.000 mediamente richiesti da una scansione sequenziale.

### Ricerca tramite accesso calcolato - hash

Assumiamo che sull’attributo di ricerca A sia definito un indice primario di tipo hash. In tal caso, come già accennato, la ricerca delle tuple che soddisfano la condizione A=a può essere effettuata semplicemente accedendo alla pagina della relazione di indirizzo h(a), ed eventualmente scandendo la relativa catena di overflow. Nel caso migliore, il costo di accesso è **Costo = 1**.

Se sull’attributo di ricerca A è definito un indice secondario di tipo hash, allora il costo della ricerca è pari, nel caso migliore (non ci sono catene di overflow) a 2 accessi alle pagine, uno al file indice ed uno alla pagina della relazione che contiene la tuple cercata. Quindi, **Costo = 2**.

### Ricerca tramite B+Tree

Assumiamo che sull’attributo di ricerca A sia definito un B+-tree di profondità *h*. Nel seguito ci limiteremo a considerare il caso in cui A è chiave primaria.

La ricerca di una tupla inizia partendo dal nodo radice, seguendo poi i puntatori verso i livelli inferiori, sulla base del valore *a* cercato, fino ad arrivare ad una foglia dove, in corrispondenza di *a*, si trova il puntatore alla pagina che contiene la tupla con A=a. Una schematizzazione del processo di ricerca è riportata di seguito.

*Ricerca tramite B+-tree*

- sia N il nodo corrente all’interno del quale si cerca il valore *a* della chiave di ricerca; siano *v1...vk* (con *k $<$ m*, dove *m* è l’ordine dell’albero) i valori della chiave A contenuti in N;
    - se *a=vi*, per qualche *i*, restituisci il puntatore associato a *vi* - cioè, il numero della pagina in cui si trova la tupla che soddisfa la condizione A=a;
    - se *a* non è in N allora restituisci *null* – cioè, non esiste una tupla che soddisfa la condizione A=a.
    - se *a $<$= V1*, cerca ricorsivamente a nel sottoalbero sinistro di *V1*;
    - se *a $>$ Vk*, cerca ricorsivamente a nel sottoalbero destro di *Vk*;
    - Se *Vi $<$ a $<$= Vi+1*, per qualche i tale che *1 $<$ i $<$ k*, allora ricorsivamente cerca a nel sottoalbero che si trova tra *Vi* e *Vi+1*.

Nella seguente figura è rappresentata la ricerca del valore 9 tramite B+-tree.

![[ricercaBTree.PNG]]

*Costo della ricerca.* Come sopra descritto, si tratta di accedere alla radice, e da questa seguire i puntatori per raggiungere il nodo foglia in cui è memorizzato il valore a della chiave. Siccome ogni nodo del B+-tree è memorizzato su una pagina, per raggiungere il nodo foglia dalla radice è necessario trasferire *h* pagine nel baffer di memoria, dove *h* è la profondità dell’albero. Dal nodo foglia è poi necessario accedere, tramite il puntatore associato al valore a, alla pagina di R che contiene la tupla con A=a. Il costo della ricerca è pertanto **Costo = h+1**.

**ESEMPIO:** La ricerca di una tupla in una relazione con 1.000.000 tuple, attraverso un albero di ordine *m=137*, costa $ h_{min}+1 = 4 $ accessi nel caso migliore, e $ h_{max}+1 = 6 $ nel peggiore. Se si facesse una ricerca binaria, nell’ipotesi che la relazione sia memorizzata su 100.000 pagine (e le tuple siano ordinate), il costo sarebbe pari a 17 pagine (caso peggiore), mentre una ricerca sequenziale costerebbe mediamente 50.000 pagine.

## Operatore di Join

Un altro operatore fondamentale dell’algebra relazionale è il join. Ricordiamo che si tratta di un operatore commutativo, cioè, $ R \bowtie S \equiv S \bowtie R $.

Esistono vari algoritmi per implementare il join, fra cui il Nested Loop, l’index Join, l’Hash Join e il SortMerge. Di seguito riportiamo una descrizione dei primi due metodi.

### Nested Loop

Il Nested Loop è una tecnica che prevede che, per ogni pagina della relazione *esterna*, vengano trasferite nel buffer tutte le pagine della relazione *interna*.

*Algoritmo NestedLoop* (con R è relazione esterna)
```sql
for each page P of R {
    transfer P in main memory buffer
    for each page Q of S {
        transfer Q in main memory buffer
        join the tuples in r with those in s
    }
}
```
Il costo del join effettuato con il nested loop è il seguente:

- *R relazione esterna:* bisogna trasferire in memoria ogni pagina di R e, per ognuna di esse, tutte le pagine di S; quindi **Costo = *NPag(R)+NPag(R)*NPag(S)***
- *S relazione esterna:* simmetricamente abbiamo **Costo = *NPag(S)+NPag(R)*NPag(S)***
Confrontando le due equazioni, si evince facilmente che conviene usare come relazione esterna quella più piccola (cioè, memorizzata su un numero minore di pagine).

1. *R esterna:* Costo = NPag(R) + NPag(R)*NPag(S) = 10.001.000 pag
1. **S esterna:** Costo = NPag(S) + NPag(R)*NPag(S) = 10.010.000 pag

### Index Join

Si tratta di una versione efficiente del Nested Loop, possibile nel caso in cui sugli attributi di join sono definiti degli indici (hash o B+-tree). Supponiamo che la condizione di join sia *R.A = S.B*, l’attributo B sia chiave primaria di S, e su B sia definita una funzione hash on un B+-tree. Questa è una situazione molto frequente, in quanto molti dei join richiesti nella pratica operativa sono equijoin fatti su attributi chiave (primaria e secondaria). In tal caso, il join può essere eseguito secondo il seguente schema:

- esegui una scansione sequenziale di R (relazione esterna)
- per ogni tupla t di R, usa il valore t.A come input all’indice su B per trovare la tupla t’ di B tale che *t’.B = t.A*

```sql
for each page P of R
    transfer P in main memory buffer
    for each tuple t in P
        Q = index_lookup(t.A) /* Q e' la pagina che contiene la tupla t' exist S s.t. t'.B=t.A */
        transfer Q in main memory buffer
        Find t' in Q and join t and t' /* crea una nuova tupla */
```
Il precedente algoritmo richiede la scansione di ogni pagina P di R (*for each page P of R*) e, per ogni tupla in P (*for each tuple t in P*), un accesso ad una pagina di S (effettuato tramite indice). Pertanto, il costo del join è il seguente:

***Costo = NPag(R) + Ntuple(R) * CostoAccessoTupla(S)***
dove:

- *NPag(R):* numero pagine di R
- *NTuple(R):* numero tuple di R
- *CostoAccessoTupla(S):* costo di accesso ad una tupla di S tramite indice

*Caso 1:* l’indice è di tipo hash. CostoAccessoTupla(S) = 1 (caso ideale), e pertanto:

***Costo = NPag(R) + Ntuple(R)***

*Caso 2:* l’indice è un B+-tree. CostoAccessoTupla(S) = h+1, e pertanto:

***Costo = NPag(R) + NT(R) * (h+1)***

- *Index join(hash):* Costo = NPag(R) + Ntuple(R) = 100 + 1.000 = 1100 pag;
- *Index join (B+-tree):* Costo = NPag(R) + NT(R)*(h+1) = 100 + 4*1.000 = 4100 pag
È utile confrontare I suddetti costi con quelli del Nested Loop.

## Cenni sulla progettazione fisica di una BD

Scopo principale della progettazione fisica di una BD è l’individuazione dei metodi di accesso alle relazioni al fine di rendere efficienti le operazioni più costose e frequenti. Più precisamente, per ogni relazione, si tratta di scegliere:

1. la struttura primaria – sequenziale o hash;
1. un insieme di indici secondari.

Nell’effettuare tali scelte, il progettista deve tenere conto dell’insieme delle query e delle relative frequenze. Infatti, la scelta di un indice può avvantaggiare determinate operazioni, a scapito di altre. Si tratta quindi di trovare il giusto compromesso tra vantaggi e svantaggi. Un problema di ottimizzazione estremamente difficile dal punto di vista computazionale.

A titolo esemplificativo, si consideri un contesto applicativo nel quale vi sono prevalentemente due tipi di accesso alle tuple di R:

- accessi diretti del tipo "seleziona la tupla in cui *A=a*", dove A è chiave primaria;
- accessi per intervallo del tipo "seleziona tutte le tuple per cui vale la condizione $ b1 <= B <= b2$";

Nel primo caso, è evidente che la definizione di una funzione hash su A (assumiamo come metodo di accesso primario) risulterebbe molto vantaggiosa, in quanto consentirebbe il reperimento di una specifica tupla di R con un singolo accesso (nel caso ideale).

Nel secondo caso, sarebbe invece conveniente ordinare le tuple rispetto allo attributo B (con una struttura primaria di tipo sequenziale). Ciò perchè le selezioni per intervallo $ \sigma_{b1 <= B <= b2}(R) $ traggono vantaggio dall’ordinamento, in quanto tutte le tuple che soddisfano la condizione di selezione si trovano disposte in maniera contigua (formano un *cluster*), limitando in tal modo lo spazio di ricerca.

**ESEMPIO:** si consideri la relazione Studente, e assumiamo che sull’attributo matricola sia definito un metodo primario di tipo hash. Pertanto, trovare un singolo studente attraverso la sua matricola costa un singolo accesso in memoria secondaria (nel caso migliore), mentre per trovare tutti gli studenti con età compresa tra 18 e 28 anni, è richiesta una scansione di tutte le pagine della relazione. D’altro canto, se si usa un metodo di accesso primario sequenziale (che garantisce l’ordinamento su età), la ricerca di un singolo studente richiede una scansione sequenziale, mentre risulta avvantaggiata la seleziona degli studenti che si trovano in un certo intervallo di età (per via dell’ordinamento).

Per il progettista, si tratta quindi di decidere se conviene favorire gli accessi diretti (tramite funzione hash), o gli accessi per intervallo. Ciò dipende evidentemente dalla frequenza con cui i diversi tipi di query si presentano.

Si possono tuttavia trovare soluzioni di compromesso. Ad esempio, usando una struttura primaria di tipo sequenziale, per favorire le query intervallo, e definendo un indice secondario, hash o B+-tree, su matricola, per favorire gli accessi diretti.

Il problema della progettazione diventa ancora più complesso nel momento in cui si considerano anche le operazioni di modifica della BD. Infatti, mantenere un file ordinato, così come mantenere un indice tabellato (come il B+-tree) aggiornato, a seguito di operazioni di inserimento, cancellazione o modifica, ha un costo tutt’altro che trascurabile. Pertanto, quando si opera la scelta, ad esempio, di un indice tabellato, bisogna tenere conto non solo dei vantaggi per le operazioni di ricerca, ma anche dei costi degli aggiornamenti. Anche in questo caso, cioè, si tratta di fare un bilanco di tipo costi/benefici.

In conclusione, nella definizione degli indici è necessario trovare il giusto punto di equilibrio tra effetti positivi ed effetti negativi. Per tale ragione, il problema della scelta degli indici è un task particolarmente complesso (complessità esponenziale), che non può essere risolto in maniera esatta attraverso l’analisi di tutte le combinazioni possibili di indici.

## Esecuzione di interrogazioni

Il **Gestore delle Interrogazioni** (GdI) ha il compito di compilare ed eseguire in maniera efficiente le interrogazioni SQL. L’esecuzione efficiente richiede la preparazione di un piano di esecuzione 'ottimo' basato sulla organizzazione fisica della BD.

I passi canonici per l’esecuzione di una query SQL Q sono i seguenti:

    - Traduzione di Q in una forma algebrica;
    - Ottimizzazione logica;
    - Ottimizzazione fisica e generazione di un piano di esecuzione;
- Esecuzione bottom up del piano di esecuzione.

*Ottimizzazione logica:* l’ottimizzazione logica consiste nella trasformazione di una espressione algebrica in una equivalente, ma più ‘conveniente’. Ad esempio, facendo l’anticipazione della selezione rispetto al join, come nella seguente espressione:

$
\sigma_{F}(R \bowtie S) \equiv (\sigma_{F} R) \bowtie S
$
dove *F* è un predicato che contiene solo attributi della relazione *R*.

Il vantaggio della espressione a destra, rispetto a quella originaria, è che, riducendo la lunghezza di R attraverso la selezione, il costo del join viene ridotto. L’ottimizzazione logica si basa su un insieme di regole di equivalenza dell’algebra relaziona, come quella sopra mostrata.

*Ottimizzazione fisica:* riceve in ingresso una espressione algebrica (ottimizzata), e produce un piano ottimo di esecuzione. Questa fase dipende dai metodi di accesso (primari e secondari) definiti sulle relazioni della BD nella fase di progettazione fisica. In generale, quando esistono più alternative per eseguire una data operazione algebrica, il sistema deve scegliere, sulla base di una stima dei costi, quella più efficiente.

Ad esempio, si consideri la seguente espressione:

$
\pi_{A}(R \bowtie \sigma_{F}S) - \pi_{A}(R \bowtie \sigma_{G}S)
$
di cui è riportata una rappresentazione ad albero nella figura seguente. Come si può notare, i nodi foglia rappresentano relazioni, mentre i nodi interni rappresentano operatori algebrici.

![[esecuzioneInterrogazioni.PNG]]

Nella fase di ottimizzazione fisica, il GdI associa ad ogni nodo operatore (nodo interno) dell’albero una scelta implementativa (ad esempio, l’algoritmo di join). A tal fine, vengono valutate le diverse ipotesi di implementazione (che dipendono dall’organizzazione fisica dei dati) utilizzando le equazioni di costo. Quindi si procede alla scelta dell’ipotesi migliore (quella a costo più basso, cioé, che richiede il minore numero di trasferimenti di pagine). Il risultato di questa fase è il cosiddetto piano ottimo di esecuzione, cioè, un albero come quello sopra riportato in cui ad ogni operatore (nodo interno) è associato un metodo di implementazione (il ‘migliore’ secondo la stima dei costi).

In sintesi, per ogni nodo operatore:
- si considerano le diverse alternative implementative;
- per ognuna di esse si valuta il costo e si sceglie quella a costo minimo.
Inoltre, si stima la lunghezza della relazione risultato, da utilizzare come input al passo successivo.

La valutazione della query avviene secondo un approccio bottom up (dalle foglie alla radice).
## Fasi e step del progetto

    - raccolta e Analisi dei requisiti;
    - progettazione dello schema entità-relazione (E-R);
    - specifica delle funzioni per operare sui dati;
    - stima del carico applicativo del sistema informatico.
    - ristrutturazione dello schema E-R in uno schema E-R semplificato orientato al modello relazionale;
    - traduzione dello schema E-R ristrutturato in schema relazionale.
    - definizione dello schema della base di dati sul DBMS relazionale open-source MySQL mediante SQL-DDL;
    - popolamento della base di dati;
    - realizzazione delle interrogazioni per operare sui dati.

## Modelli dei dati adottati

    - modello entità-relazione (E-R).
    - modello relazionale;
    - *DDL:* SQL-standard.
    - modello relazionale + strutture fisiche;
    - *DDL:* SQL supportato dal DBMS scelto (MySql)

## SQL

Originariamente acronimo di *"Structured Query Language"*, ora nome proprio. È uno standard (1986, poi 1989, 1992, 1999 e infine 2003) ma esistono varie versioni (DBMS diversi implementano versioni diverse). È un linguaggio **dichiarativo** (a differenza dell’algebra relazionale che è di tipo procedurale).

- **DDL** (Data Definition Language);
- **DML** (Data Manipulation Language);
- **DCL** (Data Control Language).

### Funzionalità DDL

È una funzionalità che consente di definire lo schema dei dati. L'istruzione più importante del DDL di SQL è:

```sql
CREATE TABLE
```

- definisce uno schema di relazione, specificando attributi e vincoli);
- crea un’istanza vuota dello schema.

## Definizione di tabelle

```sql
CREATE TABLE NomeTabella (
	NomeAttributo Dominio [ Vincoli ] 
	{, NomeAttributo Dominio [ Vincoli ]}
	[ AltriVincoli ])
```
Facciamo un esempio:

```sql
CREATE TABLE Impiegato(
	Matricola 	CHAR(6) 	PRIMARY KEY, 
	Nome 	    CHAR(20) 	NOT NULL,  
 	Cognome 	CHAR(20) 	NOT NULL,  
	Dipart 	    CHAR(15),	
	Stipendio 	NUMERIC(9) 	DEFAULT 0,  

	FOREIGN KEY(Dipart) REFERENCES Dipartimento(NomeDip),  
  	UNIQUE (Cognome,Nome) )
```

### Domini

Abbiamo due tipi di domini:

- domini **elementari** (predefiniti);
- domini **definiti dall'utente** (semplici, ma riutilizzabili);

### Domini elementari

- *Carattere:* singoli caratteri o stringhe, anche di lunghezza variabile: 

![[dominiElementari1.PNG]]

- *Numerici:* esatti e approssimati: 

![[dominiElementari2.PNG]]

    - date/time/timestamp;
    - time with timezone/timestamp with timezone.
    - bit(n);
    - bit varying(n).
    - boolean;
    - BLOB, CLOB (binary/character large object) per grandi immagini e testi.

### Definizione di domini

```sql
CREATE DOMAIN
```

- definisce un dominio (semplice), utilizzabile in definizioni di relazioni, anche con vincoli e valori di default.
Ecco la sintassi:

```sql
CREATE DOMAIN NomeDominio
    AS DominioPreesistente [ Default ] [ Vincoli ]
```
Facciamo un esempio:

```sql
CREATE DOMAIN Voto 
    AS SMALLINT DEFAULT NULL
    CHECK ( value >=18 AND value <= 30)
```

## Vincoli intrarelazionali

- **NOT NULL**;
- **UNIQUE:** definisce attributi che identificano una tupla;
- **PRIMARY KEY:** chiave primaria (una sola, implica NOT NULL e UNIQUE);
- **CHECK:** consente di verificare che una condizione venga rispettata.

### UNIQUE e PRIMARY KEY

    - **unique** dopo la specifica del dominio;
    - **primary key:** dopo la specifica del dominio.
    - *unique (Attributo,..., Attributo)*;
    - *primary key (Attributo,..., Attributo)*.

### Alternative del PRIMARY KEY

Facendo riferimento all'esempio del paragrafo 19.3.2, si può scrivere:

```sql
CREATE TABLE Impiegato (
    Matricola character(6) PRIMARY KEY,
    ...)
```
oppure:

```sql
CREATE TABLE Impiegato (
    Matricola character(6),
    ...
    PRIMARY KEY (Matricola) )
```

## Vincoli interelazionali

- **CHECK**;
- **REFERENCES** e **FOREIGN KEY:** permettono di definire vincoli di integrità referenziale. Di nuovo due sintassi: per ogni attributi e su più attributi.
È possibile definire politiche di reazione alla violazione.

### REFERENCES e FOREIGN KEY

Permettono di definire vincoli di integrità referenziale.

- per singoli attributi: **references** dopo la specifica del dominio
- riferimenti su più attributi: *FOREIGN KEY(Attributo,...,Attributo) REFERENCES altratabella(A1,…,AN)*

Gli attributi referenziati nella tabella di arrivo (master) devono formare una chiave (PRIMAY KEY o UNIQUE).

Ogni combinazione (senza NULL) di valori per gli attributi nella tabella di partenza (slave) deve comparire nella tabella di arrivo (master).

## Modifiche degli schemi

- **ALTER DOMAIN**;
- **ALTER TABLE**;
- **DROP DOMAIN**;
- **DROP TABLE**;
La tabella da cui partiremo per fare esempi è:

```sql
CREATE TABLE Infrazioni(
	Codice 	    CHAR(6) 	NOT NULL    PRIMARY KEY, 
 	Data 	    DATE 		NOT NULL,  
  	Vigile 	    INTEGER 	NOT NULL    REFERENCES Vigili(Matricola),  
 	Provincia 	CHAR(2),  	
  	Numero 	    CHAR(6),
  	
	FOREIGN KEY(Provincia, Numero) REFERENCES Auto(Provincia, Numero)  
 )

```

### ALTER TABLE

Permette di modificare lo schema di una tabella. Facciamo un esempio:

```sql
CREATE TABLE Infrazioni(
	Codice 	    CHAR(6) 	NOT NULL    PRIMARY KEY, 
 	Data 	    DATE 		NOT NULL,  
  	Vigile 	    INTEGER 	NOT NULL    REFERENCES Vigili(Matricola),  
 	Provincia 	CHAR(2),  
  	Numero 	    CHAR(6) )

	ALTER TABLE Infrazioni 
	ADD CONSTRAINT MioVincolo FOREIGN KEY (Provincia, Numero) REFERENCES Auto(Provincia,Numero)
```

### DROP TABLE

Elimina una tabella. La sua sintassi è:

```sql
DROP TABLE NomeTabella restrict | cascade
```
Facciamo degli esempi:

```sql
DROP TABLE Infrazioni [restrict]
```
```sql
DROP TABLE Infrazioni cascade 
```

## Definizione degli indici

La definizione degli indici è rilevante dal punto di vista delle prestazioni ma è a livello fisico e non logico.

La sua sintassi è:
```sql
CREATE [UNIQUE] INDEX NomeIndice ON
    NomeTavola(col_1, col_2, ..., col_n)
```
Facciamo un esempio:
```sql
CREATE index IndiceIP ON Infrazioni(Provincia)
```