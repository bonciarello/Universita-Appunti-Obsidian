---
aliases: [Rete]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## [[Reti]] di interconnessione per computer paralleli

**Le [[Reti|reti]] di interconnessione trasportano i dati tra il processore e la memoria.** Le interconnessioni sono realizzate tramite **switch** e **link** (fili, fibra). Le interconnessioni sono classificate come **statiche** o **dinamiche**:

- Le **[[Reti|reti]] statiche** sono costituite da comunicazioni punto a punto tra i nodi e sono denominate [[Reti|reti]] dirette;
- Le **[[Reti|reti]] dinamiche** vengono implementate utilizzando interruttori e collegamenti di comunicazione. Sono anche chiamate [[Reti|reti]] **indirette**.

![[staticheEdinamiche.png]]

## Metricle per la valutazione delle [[Reti|reti]]

- **Diametro:** distanza massima tra 2 nodi (**meglio diametri piccoli**)
- **Connettività:** numero minimo di archi che devono essere rimossi per dividere la rete in 2 [[Reti|reti]] disconnesse (**migliore connettività alta**)
- **Larghezza di banda di bisezione:** applicato a una rete di archi pesati, dove i pesi indicano la quantità di dati che possono essere trasferiti. Volume minimo di comunicazioni consentito tra 2 metà di una rete (**meglio alto**)
- **Costo:** numero di collegamenti della rete (**meglio piccolo**)

## [[Reti]] dinamiche

### Topologie di rete: bus (buses)

Alcune delle prime e più semplici macchine parallele utilizzavano bus. Tutti i processori hanno un bus comune per lo scambio di dati. La distanza tra due nodi qualsiasi è *O(1)*. L'autobus fornisce anche un comodo mezzo di trasmissione. Tuttavia, la larghezza di banda del bus condiviso è un **collo di bottiglia significativo**. Le macchine basate su bus sono limitate ad alcune decine di nodi. *Esempi:* server Sun Enterprise e multiprocessori a bus condiviso basati su Intel (i nostri notebook!)

![[reteBuses.png]]

*Nota bene:* Dato che la maggior parte dei dati a cui il processore accede è locale, una memoria locale (es. cache) per ogni nodo può migliorare le prestazioni di tali macchine.

### Topologie di rete: barre trasversali (crossbars)

Una rete crossbar utilizza una **griglia *p*b*** di interruttori per collegare **p ingressi** a **b uscite** in modo non bloccante.

![[reteCrossbars.png]]

Il costo di una barra trasversale di processori p cresce all'aumentare di $O(p^2)$. Quindi, è generalmente **difficile** ottenere una **buona scalabilità** in termini di costo per valori elevati di p. *Esempi* di macchine che utilizzano barre trasversali sono Sun Ultra HPC 10000 e Fujitsu VPP500.

### Topologie di rete: [[Reti|reti]] multistadio

Le **barre trasversali (crossbars)** hanno eccellenti prestazioni di scalabilità ma scarsa scalabilità dei costi. I **bus (buses)** hanno un'eccellente scalabilità dei costi ma una ridotta scalabilità delle prestazioni. **Le [[Reti|reti]] multistadio cercano un equilibrio tra i due.**

![[reteMultistadio.png]]

### Topologie di rete: [[Reti|reti]] multistadio Omega

Una delle [[Reti|reti]] multistadio più conosciute è la rete **OMEGA**. Questa rete è costituita da *log p* passi, dove p è il numero di ingressi/uscite. In ogni fase, l'ingresso *i* è collegato all'uscita *j* se (**left_rotation**):

> $j = 2i$ se $0 \le i \le p/2 - 1$
>
> $j = 2i + 1 - p$ se $p/2 \le i \le p - 1$

**Ogni fase** della rete Omega implementa un **perfetto shuffle** come segue:

![[reteMultistadioOMEGA1.png]]

I** modelli di riproduzione casuale** perfetti vengono collegati tramite interruttori *2*2*. Gli interruttori funzionano in due modi: **pass-through** *(a)* o **crossover** *(b)*.

![[reteMultistadioOMEGA2.png]]

Una rete Omega completa con un perfetto shuffle.

![[reteMultistadioOMEGA3.png]]

Una rete omega ha *p/2*log p* nodi di commutazione e il costo di una rete di questo tipo cresce al crescere di *(p*log p)*.

### Topologie di rete: [[Reti|reti]] multistadio Omega - Routing

Sia *s* la rappresentazione binaria del nodo di origine ed *d* il nodo di destinazione. I dati attraversano il collegamento al primo nodo dello switch. Se i bit più significativi di *s* e *d* sono gli stessi, i dati vengono instradati dallo switch in modalità **pass-through** o saranno in modalità **crossover**. Questo processo viene ripetuto per ciascuna delle fasi di commutazione del *log p* (prendendo in considerazione il successivo bit più significativo). Nota che questo non è un interruttore non bloccante (cioè, non va bene!).

![[reteRouting.png]]

Un esempio di blocco nella rete omega: uno dei messaggi (da 010 a 111 o da 110 a 100) è bloccato al collegamento AB.

## [[Reti]] statiche

### Topologie di rete: [[Reti|reti]] completamente interconnesse

**Ogni** processore è collegato a ogni altro processore. Il numero di collegamenti nella rete scala come $O(p^2)$. Sebbene la scalabilità delle prestazioni sia molto buona, la **complessità hardware** non è fattibile per valori **elevati** di *p*. In questo senso, queste [[Reti|reti]] sono la **controparte statica** della traversa.

### Topologie di rete: [[Reti|reti]] a stella

Ogni nodo è connesso a un nodo "centrale" comune. La distanza tra due nodi qualsiasi è $O(1)$. Tuttavia, il nodo centrale può diventare un **collo di bottiglia**. In questo senso, le [[Reti|reti]] a stella sono **controparti statiche** delle [[Reti|reti]] bus.

### Topologie di rete: array lineari, mesh e mesh k-d

In un **array lineare**, ogni nodo ha due vicini, uno a sinistra e uno a destra. Se i nodi terminali sono collegati, ci riferiamo a un **toro** o **anello 1-D**. Una **generalizzazione** a due dimensioni ha nodi con 4 vicini a nord, sud, est e ovest. Una **generalizzazione** a più dimensioni ha nodi con 2d vicini. Un caso speciale di mesh d-dimensionale è l'**ipercubo**. In questo caso, *d = log p*, dove p è il numero totale di nodi.

![[meshBidimensionali.PNG]]

![[meshTridimensionali.PNG]]

### Topologie di rete: ipercubi e loro costruzione

![[ipercubi.png]]

1. La **distanza** tra due nodi qualsiasi è al massimo *log p*;
2. Ogni nodo ha esattamente *log p* vicini
3. La **distanza** tra due nodi è data dal **numero di posizioni di bit** in cui differiscono i due nodi (es. 0110 e 0101 sono distanti 2 nodi)

### Topologie di rete: [[Reti|reti]] ad albero

![[reteAlbero.png]]

[[Reti]] ad albero binario completi: (a) una rete ad albero statico; e (b) una rete dinamica libera.

La **distanza** tra due nodi qualsiasi non è superiore a *2*log p*. I **collegamenti** che sono verso l'alto richiedono più comunicazioni di quelli situati nella parte inferiore dell'albero. Per questo motivo, una variante chiamata **fat-tree**. Gli alberi possono essere disposti in 2D **senza intersezioni**. *Questa è una proprietà molto importante*.

## Valutazione delle [[Reti|reti]] di interconnessione statica

![[valutazioneStatica.png]]

## Valutazione delle [[Reti|reti]] di interconnessione dinamica

![[valutazioneDinamica.png]]

## Costi di comunicazione

Insieme al tempo di inattività e alla contesa delle risorse, la **comunicazione** è la **principale causa** di overhead nei programmi paralleli (*la causa che non consente uno speedup = p*).

Il **costo** della comunicazione dipende da **diversi fattori**, tra cui la semantica del modello di programmazione, la topologia di rete, l'elaborazione dei dati e i protocolli software di routing adottati.

## Costi di comunicazione per il passaggio dei messaggi

Il tempo totale per trasferire un messaggio sulla rete comprende:

- **Tempo di avvio ($t_s$):** *tempo trascorso sui nodi mittente e destinatario* (esecuzione dell'algoritmo, router, ecc.);
- **Per-hop time ($t_h$):** *tempo impiegato dall'intestazione del messaggio per raggiungere il nodo successivo*. Questa volta è una funzione del numero di salti (nodi successivi) e include fattori come le latenze degli switch, i ritardi di rete, ecc.
- **Tempo di trasferimento per parola ($t_w$):** *dato da 1/r, dove r è la larghezza di banda (parole/s)*. Questa volta include tutte le spese generali determinate dalla lunghezza del messaggio. Ciò include la larghezza di banda dei collegamenti, il controllo e la correzione degli errori, ecc.

## Instradamento Store-and-Forward

Un messaggio che attraversa più hop viene completamente ricevuto in un hop intermedio prima di essere inoltrato all'hop successivo. Il costo totale della comunicazione per un messaggio di dimensione m per incrociare i collegamenti di comunicazione è:

> $t_{comm} = t_{s} + (mt_{w} + t_{h})l$

Nella maggior parte delle piattaforme, è piccolo e l'espressione può essere approssimata da:

> $t_{comm} = t_{s} + mlt_{w}$

## Routing dei pacchetti

La tecnica **store-and-forward** utilizza poco le risorse di comunicazione. Il **Packet Routing** suddivide i messaggi in pacchetti e li inoltra, **tipo pipeline** sulla rete (es. Internet) Poiché pacchetti diversi possono prendere percorsi diversi (come per il protocollo TCP/IP), ogni pacchetto deve contenere un'**intestazione** con informazioni su **instradamento, controllo degli errori, sequenziamento** e altre informazioni. Il tempo totale di comunicazione per l'instradamento dei pacchetti è approssimato da:

> $t_{comm} = t_{s} + t_{h}l + t_{w}m$

dove il fattore $t_{w}$ prende in considerazione **anche** gli overhead degli header di ogni pacchetto (che è diverso da quello del primo).

## Routing cut-through

Prende il concetto di instradamento dei pacchetti in un modo "estremo", **dividendo** ulteriormente i messaggi in unità di base chiamate **flits (4-32 byte)**. Ogni flit è **costretto** a prendere lo stesso percorso, in sequenza (per salvare le informazioni di instradamento). Poiché i flit sono in genere piccoli, l'**intestazione** del messaggio viene **minimizzato**. Un **messaggio di traccia** prima "programma" tutti i router intermedi. Successivamente, i flits prendono lo stesso percorso.

Il tempo totale di comunicazione al **cut-through** è approssimato da:

> $t_{comm} = t_{s} + t_{h}l + t_{w}m$

Questo è identico al routing dei pacchetti, sebbene $t_{w}$ sia tipicamente più piccolo. Molto meglio di store-and-forward, dove *l* ed *m* sono stati entrambi **moltiplicati**.

## Modello di costo semplificato per i messaggi di comunicazione

Il **costo** della comunicazione di un messaggio tra due nodi remoti (hop) utilizzando il routing **cut-through** è dato da:

> $t_{comm} = t_{s} + t_{h}l + t_{w}m$

In questa espressione, $t_{h}$ è tipicamente **minore** di $t_{s}$ e $t_{w}$. Per questo motivo, il secondo termine della formula $lt_{h}$ può essere omesso, quando *m* è grande. Inoltre, è spesso impossibile controllare l'instradamento (cioè il calcolo effettivo di *l*) e l'assegnazione dei compiti (ad es. l'utente ha scarso controllo sui meccanismi di comunicazione in MPI). Quindi, **in conclusione e in generale**, si può approssimare il costo di un trasferimento del messaggio:

> $t_{comm} = t_{s} + t_{w}m$

Guardando la formula:

> $t_{comm} = t_{s} + t_{h}l + t_{w}m$

implica che:

1. È meglio **aggregare** messaggi e non inviarne tanti piccoli (per evitare ogni volta $t_{s}$)
2. **Riduce** le dimensioni del messaggio (per ridurre al minimo $t_{w}$)
3. **Riduce** la distanza tra i salti (per diminuire *l*)

ma il punto 1 e 2 possono essere facilmente gestiti, ma non 3!

Ecco perché approssimiamo tutto in base a:

> $t_{comm} = t_{s} + t_{w}m$

## Modelli di costo per computer con spazio indirizzo condiviso

Sebbene i meccanismi di base per i costi siano validi per questo tipo di macchine, una serie di altri fattori possono rendere **difficile** una stima accurata:

- **il layout della memoria è in genere determinato dal sistema**;
- la dimensione **limitata** della cache può causare un **thrash della cache** (ad esempio i dati richiesti non sono presenti nella cache);
- i costi generali associati alle operazioni di annullamento e aggiornamento possono essere difficili da quantificare;
- la **località dello spazio** è difficile da modellare;
- la **falsa condivisione** e la **contesa** sono difficili da modellare.

## Meccanismi di instradamento

- **Instradamento:** algoritmo che viene utilizzato per determinare il percorso che un messaggio prenderà da un nodo di origine a uno di destinazione;
- **Minimo:** seleziona un percorso sempre più breve (ma può produrre congestione;
- **Non minimo:** prende percorsi più lunghi per evitare la congestione;
- **Deterministico:** determina un percorso unico;
- **Adattivo:** utilizza le informazioni riguardanti lo stato della rete

## Meccanismi di instradamento per [[Reti|reti]] di comunicazione

*Come si calcola il percorso fisico di un messaggio dal processore di origine a quello di destinazione?*

- il routing deve evitare deadlock: per questo motivo, utilizziamo il **routing ordinato per dimensione** (per le mesh) o il **routing E-cube** (per gli ipercubi);
- il percorso dovrebbe evitare gli **hot-spot**. Per questo motivo viene spesso utilizzato il **routing in due fasi**. In questo caso, un messaggio dalla **sorgente *s*** al **destinatario *d*** viene prima inviato a un **nodo intermedio *i*** e poi casualmente "inoltrato" alla **destinazione *d***.

![[meccanismoInstadamento.PNG]]

**Routing E-cube:** esegue rappresentazioni XOR di Ps e Pd e invia il messaggio lungo la direzione k del bit meno significativo che è diverso da zero nell'operazione XOR. Lo stesso si fa per i nodi intermedi (considerando Pi con Pd).

## Tecniche di mappatura per grafici

MPI (ma anche altre soluzioni) non consente di avere il controllo su **come i processi vengono mappati sui processori**. Spesso, abbiamo bisogno di **mappare** un modello di comunicazione su una topologia di interconnessione. Ad esempio, abbiamo un certo algoritmo progettato per una certa topologia e lo stiamo implementando su un'altra. A tal fine, è utile **comprendere** la mappatura tra i diversi grafici:

![[tecnicheMappaturaGrafici.PNG]]

## Tecniche di mappatura per grafici: metriche

Quando si **mappa** un grafico G(V, E) su un altro grafico G'(V', E'), le seguenti metriche sono importanti:

- il numero massimo di archi mappati a qualsiasi arco di E' è chiamato **congestione** della mappatura;
- il numero massimo di archi di E' che ogni lato di E è mappato è chiamato **dilatazione** della mappatura;
- il rapporto tra il numero di nodi in V' e l'insieme V è chiamato **espansione** della mappatura.

## Mappatura di una matrice lineare su un ipercubo

Un array lineare (o anello) costituito da nodi $2^d$ (etichettati da *0* a $2^d - 1$) può essere mappato su un ipercubo d-dimensionale mappando un nodo *i* del nodo G(i, d) dell'ipercubo utilizzando la funzione G(i, x) definito come segue:

> $G(0, 1) = 0$
>
> $G(1, 1) = 1$
>
> $G(i, x+1) = G(i, x)$ se $i < 2^x$
>
> $G(i, x+1) = 2^x + G(2^{x+1} - 1 -i, x)$ se $i \ge 2^x$

La funzione G è chiamata codice Binary Reflected Gray (RGC).

Con questa codifica, i nodi adiacenti (G(i, d) e G(i + 1, d)) differiscono di **una sola posizione** di bit, quindi i processori corrispondenti vengono mappati sui nodi vicini nell'ipercubo. Pertanto, *congestione, dilatazione ed espansione sono 1*.

## Mappatura di una mesh su un ipercubo

Una mesh toroidale $2^r * 2^s$ può essere mappata su un ipercubo $2^{r+s}$ nodi che mappano il nodo (i, j) del nodo mesh G(i, r-1) $\Vert$ G (j, s-1) dell'ipercubo (dove l'operatore $\Vert$ indica la concatenazione di due codici gray).

![[meshIpercubo.PNG]]

## Mappatura di una mesh su un array 1D

Dato che una mesh ha più lati di un array 1D, **non** avremo una mappatura con congestione/dilatazione ottimale. Analizziamo prima la mappatura di un array lineare su una mesh e successivamente invertiamo la mappatura. In termini di congestione, questa mappatura è, tuttavia, **ottimale**.

## Mappatura di un ipercubo su una mesh 2D

Ogni sottocubo di $\sqrt[]{p}$ nodi dell'ipercubo è mappato su una riga di $\sqrt[]{p}$ nodi della mesh. Questo viene fatto invertendo la mappatura dell'array lineare sull'ipercubo. Si può dimostrare che è ottimale!
