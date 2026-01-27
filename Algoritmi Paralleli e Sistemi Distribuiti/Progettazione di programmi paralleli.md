Uno dei primi passi della progettazione di un programma parallelo consiste nel dividere il problema in "blocchi" di lavoro discreto che possono essere distribuiti a più attività. Questo si chiama **decomposizione** o **partizionamento**.

Esistono due modi principali per suddividere il carico computazionale tra attività parallele: **scomposizione funzionale (attività/lavoro)** e **scomposizione dei dati**.

## Distribuzione di lavoro e dati

![[distribuzioneLavoroEDati.PNG]]

## Decomposizione dei dati (1)

![[distribuzioneDati.PNG]]

Ad esempio, gli automi cellulari si prestano bene a questo tipo di parallelizzazione.

## Decomposizione di task

![[decomposizioneTask1.PNG]]

La scomposizione funzionale funziona bene su quei problemi che possono essere suddivisi in diversi compiti, come la modellazione dell'ecosistema.

![[decomposizioneTask2.PNG]]

## Esempio di problema non parallelizzabile

### Calcolo della serie di Fibonacci

> $(1, 1, 2, 3, 5, 8, 13, 21, ...)$

La formula è: $F(k + 2) = F(k + 1) + F(k)$.

Questo problema **non è facilmente parallelizzabile** perché il calcolo della sequenza di Fibonacci include il calcolo dipendente, piuttosto che indipendente.

Il calcolo del valore di *k+2* utilizza sia il valore *k+1* che *k*. Questi tre termini non possono essere calcolati indipendentemente e quindi, non in parallelo.

In OpenMP è piuttosto fattibile grazie alla **ricorsione**.

## Morale (1)

- **identificare gli hotspot del programma**, cerca di sapere dove viene svolto il lavoro "realmente". La maggior parte dei programmi scientifici di solito esegue la parte principale del lavoro in pochi punti (tipicamente, **per** i cicli!). Si concentra sulla parallelizzazione degli hotspot e ignora quelle parti del programma che utilizzano poca CPU;
- **identificare i colli di bottiglia nel programma**, ci sono aree che sono sproporzionatamente lente o causano l'interruzione o il ritardo del lavoro parallelizzato? Ad esempio, le operazioni di I/O di solito rallentano l'esecuzione del programma!

Potrebbe essere necessario ristrutturare il programma o utilizzare un algoritmo diverso per ridurre o eliminare le aree troppo "lente".

## Dipendenze dei dati

Esiste una **dipendenza dei dati** tra le istruzioni di un programma quando l'ordine di esecuzione delle istruzioni **influenza** i risultati del programma. Una dipendenza dai dati si verifica quando più attività utilizzano più volte le stesse posizioni di memoria. Le dipendenze sono importanti nel calcolo parallelo perché sono **uno dei maggiori inibitori del parallelismo**.

## Morale (2)

- **identificare gli inibitori del parallelismo**, una causa comune di inibitore è la dipendenza dai dati, come dimostrato nell'esempio della sequenza di Fibonacci;
- **indagare su altri algoritmi se possibile**, questa potrebbe anche essere l'unica alternativa quando si progetta un'applicazione parallela.

## Come affrontare le dipendenze dei dati?

Semplice! Ecco degli esempi:
- *architetture di memoria distribuita:* comunicano i dati nei punti di sincronizzazione
- *architetture di memoria condivisa:* sincronizza le operazioni di lettura / scrittura tra le attività

## Esempi di dipendenza dei dati

### Dipendenza dai dati del ciclo

```c
for (i=init; i<end; i++)
    a[j] = a[j-1] * 2.0
```

Il valore di *a[j-1]* deve essere calcolato prima del valore di *a[j]*, quindi *a[j]* mostra una dipendenza della data da *a[j-1]*. Il parallelismo è inibito. Se task 2 ha *a[j]* e task 1 ha *a[j-1]*, il calcolo del valore corretto di *a[j]* richiede:

- **nelle architetture di memoria distribuita:** task 2 deve ottenere il valore di *a[j-1]* dalla task 1 dopo che task 1 ha terminato l'elaborazione.
- **nelle architetture di memoria condivisa:** task 2 dovrebbe leggere *a[j-1]* dopo che task 1 è stata aggiornata.

### Dipendenza dai dati a ciclo indipendente

*X* e *Y* sono variabili condivise.

| **task 1** | **task 2** |
| :---: | :---: |
| X = 2 | $X = 4$ |
| ... | ... |
| Y = X**2 | $Y = X**3$ |

Come nell'esempio precedente, il parallelismo è inibito. Il valore corretto di *Y* dipende da:

- **nell'architettura della memoria distribuita:** se o quando il valore di *X* viene comunicato tra le attività.
- **nell'architettura della memoria condivisa:** quale attività memorizza il valore di *X* per ultimo.

## Principi di progettazione di algoritmi paralleli

### Decomposizione del task (Task decomposition)

Consideriamo un prodotto matrice-vettore:

> $y[i] = \displaystyle\sum_{j=1}^{n} A([i, j] * b[j])$

Vengono considerati n task, come il numero di righe della matrice. Le tasks sono indipendenti e possono essere calcolate in qualsiasi ordine.

![[taskDecomposition.PNG]]

### Modello grafico task (Task Parallelism)

Basato sul grafico delle dipendenze delle task, è utile per ridurre il grado di interazione.

Utilizzato quando la quantità di dati che una task deve calcolare è grande rispetto al costo computazionale.

Le task sono associate staticamente, per ridurre al minimo lo scambio di dati tra le task.

Funziona meglio se applicato a un'architettura di memoria condivisa.

*Esempio:* Quicksort parallelo.

### Grafico dipendenza task

Il grafico delle dipendenze viene utilizzato per esplicitare quali task richiedono il risultato di altre task e il loro ordine di esecuzione. È un DAG, grafico ciclico diretto. I nodi rappresentano le task. Gli archi rappresentano la dipendenza tra le task.

*Qual è il grafico delle dipendenze dell'esempio precedente?* In questo caso, il grafico è scollegato (arco impostato = 0) poiché tutte le task sono indipendenti l'una dall'altra.

### Esempio: query su database

Consideriamo un DB relazionale per auto:

![[DBrelazionale.PNG]]

Consideriamo la query:

```sql
MODEL = "civic" AND YEAR = "2001" AND (COLOR = "Green" OR COLOR = "White"
```

![[graficoBD.PNG]]

In alternativa...

![[graficoAlternativo.PNG]]

### Granularità

Granularità della scomposizione del compito e dipende sia dal numero che dalle dimensioni delle attività.
- **grana fine:** quando ho poca computazione e più comunicazioni
  ![[granaFine.PNG]]

- **grana grossa:** quando ho tanta computazione e meno comunicazioni
  ![[granaGrossa.PNG]]

### Concorrenza

È collegato alla granularità: quando la granularità va bene, il grado di concorrenza tra le attività **aumenta**.

*Massimo grado di concorrenza:* numero massimo di attività che possono essere eseguite contemporaneamente.

*Grado medio di simultaneità:* numero medio di attività eseguibili contemporaneamente, calcolato sulla durata complessiva del programma.

A parità di granularità, il grado di concorrenza non è lo stesso: dipende anche dalla dipendenza dal task.

### Percorso critico

È un aspetto della dipendenza dalle task che determina il grado medio di concorrenza per una determinata granularità. Supponiamo che nel grafico delle dipendenze sia associato un peso ad ogni nodo che dipende dalla quantità di lavoro che una task deve svolgere.

![[percorsoCritico.PNG]]

Il percorso critico è il percorso più lungo tra ogni coppia di nodi iniziale e finale nel grafo delle dipendenze. La lunghezza del percorso critico è di 27. Il lavoro totale è di 63. Il grado medio di concorrenza è 63/27 = 2,33.

### Limiti delle prestazioni

Sembrerebbe che il tempo parallelo possa essere ridotto in modo arbitrario semplicemente rendendo la **granularità più fine**.

In pratica, esiste un limite inferiore su "quanto fine" può essere la granularità del calcolo. Ad esempio, nel caso della moltiplicazione di una matrice densa con un vettore, *non ha senso utilizzare più di ($n^2$) attività simultanee*.

Inoltre, le attività simultanee possono anche avere la necessità (ovvia!) Di scambiare dati con altre attività. Ciò comporta un sovraccarico di comunicazione.

*Il compromesso tra la granularità di una scomposizione e l'overhead associato determinerà spesso i limiti delle prestazioni*.

### Grafici di interazione tra task

L'**interazione tra le task** è un fattore limitante per avere una **velocità infinita**.

Le task in cui un algoritmo viene scomposto possono **condividere input, output e altri dati intermedi**.

Le task che sembrano indipendenti potrebbero richiedere la condivisione dei dati (in cui scrivere, ad esempio).

Nel caso della moltiplicazione matrice-vettore, tutti i task devono accedere al vettore B, quindi è necessario un adeguato scambio di dati.

L'interazione tra le task **cattura il modello di interazione tra le attività**.

Questo grafico di solito contiene il **grafico delle dipendenze delle task come sotto-grafo**.

In effetti, potrebbero esserci interazioni tra le attività anche se non ci sono dipendenze.

Queste interazioni di solito si verificano a causa di accessi a dati condivisi.

### Esempio di grafici di interazione tra task

Considera il problema di moltiplicare una **matrice sparsa *A*** con un vettore ***b***. Si possono fare le seguenti osservazioni:
- come prima, il calcolo di ogni elemento del vettore risultato può essere visto come una **task indipendente**.
- a differenza di un prodotto matrice-vettore denso, tuttavia, solo gli elementi diversi da zero della matrice ***A*** partecipano al calcolo.
- se, per l'ottimalità della memoria, partizioniamo anche ***b*** tra i compiti, allora si può vedere che **il grafico di interazione del task del calcolo è identico al grafico della matrice *A*** (il grafico per cui ***A*** rappresenta la struttura di adiacenza).

![[esempioGraficoInterazioneTask.PNG]]

### Grafici di interazione tra attività, granularità e comunicazione

In generale, se la granularità di una scomposizione è più fine, il sovraccarico associato (come rapporto del lavoro utile associato a una task) aumenta.

> **ESEMPIO:** si consideri l'esempio del prodotto matrice-vettore sparse. Supponiamo che ogni nodo richieda 1 unità di tempo di calcolo e ogni interazione (bordo) causi un overhead di 1 unità di tempo.
> - la visualizzazione del nodo 0 come un'attività indipendente implica un calcolo utile di un'unità di tempo e un overhead (comunicazione) di tre unità di tempo (rapporto 3/1).
> - ora, se consideriamo i nodi 0, 4 e 5 come un'unica attività, l'attività ha un calcolo utile per un totale di tre unità di tempo e la comunicazione corrispondente a cinque unità di tempo (cinque fronti). Chiaramente, questo è un rapporto più favorevole rispetto al primo caso (rapporto 5/3).

**Quindi, sembra che usare meno task sia meglio?** All'estremo, un compito è meglio di molti task?!

## Processori e mappatura

Le task sono più o meno i processi (non processore o core!). Durante la sua esecuzione, un processo può sincronizzarsi e comunicare con altri processi. *Il meccanismo in cui le task vengono assegnate al processo per la loro esecuzione è chiamato mappatura.* I grafici di interazione tra task e dipendenza sono utili per determinare una buona mappatura per un algoritmo parallelo.

In generale, il numero di task di una scomposizione **supera** il numero di processi disponibili Per questo motivo, un algoritmo parallelo deve fornire anche una mappatura delle tasks sui processi.

> **NOTA:** ricorda che ci riferiamo alla mappatura tra tasks e processi e non ai processori. Questo perché, d'altra parte, le API utilizzate tipiche (ad esempio OpenMP, MPI) non consentono un facile collegamento delle tasks ai processori fisici. Piuttosto, cercano di aggregare i tasks ai processi, **dando al sistema il compito di mappare i processi ai processori (per efficienza)**.

Si parla di processi, non nel senso stretto di UNIX / LINUX, ma come raccolta di tasks e dati associati.

Una mappatura appropriata delle tasks sui processori è **fondamentale** per le prestazioni di un algoritmo parallelo. Le mappature sono determinate sia da grafici di dipendenza da tasks che da grafici di interazione tra tasks. I **grafici di dipendenza delle tasks** possono essere utilizzati per garantire che il lavoro sia distribuito uniformemente sui processi in qualsiasi punto (minimo minimo e bilanciamento del carico ottimale). I **grafici d'interazione tra tasks** possono essere utilizzati per garantire che i processi richiedano un'interazione minima con altri processi (comunicazione minima).

Una mappatura appropriata deve ridurre al minimo l'esecuzione e il tempo parallelo con:

1. assegnazione di compiti indipendenti su diversi processi
2. assegnare il compito dei percorsi critici ai processori non appena diventano disponibili
3. ridurre al minimo l'interazione tra i processi mappando i processi con interazioni dense sullo stesso processo

**NOTA:** *questi criteri sono, sfortunatamente, spesso in conflitto tra loro*. Ad esempio, come un estremo, la scomposizione di una task **senza decomposizione** riduce al minimo le interazioni ma non si traduce in alcun aumento della velocità!

![[ProcessoriMappatura.PNG]]

*Mappatura delle tasks della query precedente ai processi ottenuti dal grafico delle dipendenze in termini di livelli (nessun nodo in un livello ha dipendenze).* Compiti dello stesso livello sono assegnati a processi diversi (grado di concorrenza grado = numero foglie = 4). È inutile aumentare il numero di processori più di 4 poiché il grado di concorrenza è 4!

## Metodi di decomposizione

Abbiamo due scopi:
- **scopo generale:**
    - decomposizione dei dati;
    - decomposizione ricorsiva;
- **scopo speciale:**
    - decomposizione esplorativa;
    - decomposizione speculativa;
    - decomposizione ibrida.

## Decomposizione dei dati (2)

La decomposizione dei dati può essere eseguita in diversi modi. Tutti i metodi dovrebbero essere valutati e decidere possibili partizioni che determinano una **decomposizione naturale ed efficiente**. Per quanto riguarda il **partizionamento dei dati di output**:
- nel caso in cui gli output possano essere calcolati indipendentemente l'uno dall'altro, senza la necessità di rielaborarli per il risultato finale.
- il partizionamento dei dati di output induce una partizione di sotto-problemi nelle attività, in cui a ciascuna attività viene assegnato il compito di calcolare la sua porzione di output.

### Esempio: moltiplicazione di matrici

![[moltiplicazioneMatrici.PNG]]

La matrice è partizionata in sotto-matrici *2*2*. Le tasks forniscono output indipendenti l'uno dall'altro.

Facciamo altre due scomposizioni:

![[moltiplicazioneMatriciScom.PNG]]

Stessa scomposizione dei dati, ma diversa scomposizione delle tasks.

## Decomposizione ricorsiva

Adatto per algoritmi *divide et impera*.

Ogni problema secondario generato in ogni fase della divisione diventa una task.

Quanto sono buone le decomposizioni prodotte? Concorrenza media? Percorso critico? Quanto è parallelizzabile l'ordinamento rapido?

### Esempio: Quick Sort

Ecco l'algoritmo seriale:

![[quickSortAlgoritmo.PNG]]

![[quickSortAlbero.PNG]]

La concorrenza aumenta con la profondità dell'albero. Inizialmente è necessario un solo processo per la prima partizione. A volte è meglio formulare l'algoritmo in termini di *divide et impera* anche se non è intrinsecamente di questo tipo.

### Esempio: minimo di una sequenza

![[minimoSequenza.PNG]]

*Possiamo definire (o trasformare) algoritmi di divisione e conquista anche per problemi che non sono risolvibili con questo approccio.*

Per quanto riguarda la parallelizzazione:

![[minimoSequenzaParallelizzazione.PNG]]

Ogni nodo del grafico rappresenta la task che calcola il minimo tra due numeri.

## Partizione dei dati di input

- **quando** l'output è dato da un risultato non partizionabile: *min, max, sum, ecc.*
- **quando** la "posizione" degli elementi di output non è nota "a priori": *es: ordinamento, ecc.*

Viene creata una task per ciascuna partizione di input e queste vengono calcolate nel modo più indipendente possibile... La soluzione a ciascuna task prodotta dal partizionamento dell'input non poteva risolvere direttamente il problema originale. Di solito, è necessaria una ricombinazione dei risultati.

## Decomposizione esplorativa

Si verifica quando viene generato uno **spazio di ricerca** per trovare la soluzione. Lo spazio di ricerca è suddiviso in diverse parti e soluzione trovata su *ognuna di esse*, **contemporaneamente**. Una volta trovata la soluzione, le *altre ricerche terminano*.

![[decomposizioneEsplorativa.PNG]]

### Esempio: problema di 15-puzzle

![[15puzzle.PNG]]

- a partire dalla configurazione iniziale vengono generati tutti i possibili successori (1, 2, 3, 4)
- i primi livelli vengono generati in sequenza
- un'attività è assegnata a ogni nodo

*Qual'è la differenza con la scomposizione dei dati?* Nei problemi di scomposizione dei dati ogni attività contribuisce alla soluzione finale, nella scomposizione esplicativa c'è una ricerca simultanea della soluzione e ogni attività non deve necessariamente terminare il suo lavoro, tranne una!

## Decomposizione ibrida

Ad esempio, trova il minimo su un array di grandi dimensioni, possiamo usare:

![[decomposizioneIbrida.PNG]]

## Assegnazioni di task a processi paralleli: mappatura

Caratteristiche che influenzano una buona mappatura delle attività ai processi sono:

- **Generazione di compiti** che possono essere *statici* (LU matrix decomposition) o *dinamici* (decomposizione ricorsiva di quick-sort).
- **Grandezza delle tasks** (tempo di esecuzione) che possono essere uniformi (moltiplicazione di matrici) e non uniformi (quick-sort tasks)
- **Conoscenza del tempo di esecuzione delle tasks**: per esempio, nel problema dei 15-puzzle il tempo di esecuzione non è noto a priori.
- **Conoscenza della dimensione dei dati** per evitare un sovraccarico elevato dovuto allo scambio di dati (es: l'input dell'attività potrebbe essere piccolo mentre l'output grande o viceversa)

*Ha senso assegnare i processi in modo casuale?* No!

- Un'assegnazione opportuna e adeguata è fondamentale, perché il problema principale è ridurre al minimo l'overhead del processo: se *Tp* è il tempo di esecuzione sui processori *p* e *Ts* il tempo seriale dello stesso algoritmo, l'**overhead totale** *To* è dato da:
> $To= p * Tp – Ts$
È causa di overhead, con squilibrio del carico e comunicazione errata tra i processi;
- Idealmente, massimizza la concorrenza, riducendo i costi generali dovuti alla parallelizzazione, e massimizza il potenziale aumento della velocità.

## Perché la mappatura può essere complicata?

Una mappatura adeguata deve tenere conto dei seguenti fattori:

- *Grafico delle dipendenze delle task:*
    - I task sono concepibili a priori? (statico vs. dinamico)
    - Tempo di calcolo delle task? (uniforme vs. non uniforme)
    - Quanti dati sono associati a ciascuna task?
- *Grafico di interazione tra task:* quanta interazione tra le task?
    - statico o dinamico?
    - conosciuta a priori?
    - le loro istanze sono indipendenti?
    - sono regolari o irregolari?
    - sono di sola lettura o di lettura-scrittura?

## Tecniche di mappatura per il bilanciamento del carico

Lo squilibrio del carico è una delle principali cause di sovraccarico: **alcuni processi devono attendere altri**.

Le possibili soluzioni sono:

- riduci i tempi di comunicazione tra i processi;
- riduci il tempo di attesa complessivo del processo.

*Ma sono in contrasto tra loro!*

Se l'obiettivo di ridurre al minimo le interazioni può essere risolto assegnando gruppi di attività che devono interagire tra loro allo stesso processo (all'estremo, tutti i processi sono assegnati alla stessa attività - sequenziale), ciò causa uno **sbilanciamento del carico**!

## Tecniche di mappatura per minimizzare al minimo

La mappatura deve ridurre al minimo il minimo e il bilanciamento del carico. Solo il bilanciamento del carico non riduce al minimo il minimo!

> **ESEMPIO:** le ultime 4 attività possono essere eseguite solo dopo la conclusione delle prime 8. Ad ogni processo sono assegnate 3 attività, ma la mappatura (a) è migliore di (b).
>
> ![[minimizzareMinimo.PNG]]

## Tecniche di bilanciamento del carico (load balancing)

### Statico

Le tasks vengono distribuite ai processi prima dell'esecuzione. Applicabile per attività che:
- sono stati generati staticamente
- i requisiti di calcolo sono noti a priori e/o uniformi

### Dinamico

Le tasks vengono distribuite ai processi durante l'esecuzione dell'algoritmo, cioè, i dati e le attività possono migrare. Applicabile per attività che:

- sono stati generati dinamicamente
- i requisiti di calcolo non sono noti

## Diagrammi per la mappatura statica: distribuzione di array

Adatto per algoritmi che adottano la scomposizione dei dati e avere input, output e dati intermedi come array.

- Block
- Distribuzione ciclica (dopo...)
- Distribuzione a blocchi ciclica (dopo...)
- Distribuzione a blocchi casuali (dopo...)

## Distribuzione a blocchi

È il modo più semplice per distribuire un array e assegnare porzioni uniformi e contigue a processi diversi.

- Distribuzione unidimensionale di un array su 8 processi:
  ![[distribuzioneBlocchi1.PNG]]

- Distribuzione bidimensionale di una matrice su un processo *4*4* e griglie di processo *2*8*:
  ![[distribuzioneBlocchi2.PNG]]

## Blocco di distribuzione per la moltiplicazione di due matrici n-dimensionali

- L'output è suddiviso in blocchi di 16 righe: richiedono lo stesso tempo di elaborazione. Dati necessari per ogni processo: *$n^2/p$ elem of A + $n^2$ elem of B* - *1 proc uses $n^2/p + n^2$*
  ![[bloccoDistribuzione1.PNG]]

- L'output è partizionato in 16 sotto-matrici della stessa dimensione: richiedono lo stesso tempo di elaborazione. Dati necessari per ogni processo: *$n^2/p$ elem of A + $n^2/p$ elem of B* - *1 proc uses $2*n^2/p$*
  ![[bloccoDistribuzione2.PNG]]

## Distribuzioni cicliche

Con l'eliminazione Gaussiana...

![[distribuzioneCiclica.PNG]]

## Distribuzioni a blocchi casuali

A volte il calcolo viene eseguito solo in determinate parti di un array. Ad esempio, moltiplicazione matrice-matrice sparsa.

![[blocchiCasuali1.PNG]]

È possibile ottenere un migliore bilanciamento del carico tramite una distribuzione a blocchi casuale.

![[blocchiCasuali2.PNG]]

## Partizionamento grafico

È possibile ottenere una mappatura partizionando direttamente il grafico di interazione dell'attività. Ad esempio, calcoli basati su mesh agli elementi finiti.

![[partizionamentoGrafico1.PNG]]

![[partizionamentoGrafico2.PNG]]

## Esempio: vettore-matrice sparsa

![[vettoreMatriceSparsa.PNG]]

## Diagrammi per la mappatura dinamica

Schemi centralizzati per il bilanciamento del carico (layout Master-Slave):
- tutte le tasks vengono gestite in una struttura dati centralizzata o mantenute da un processo speciale o da un gruppo di processi;
- se un processo speciale è dedicato alla manutenzione del pool di tasks, si chiama **nodo master**;
- altri processi che dipendono dal master, per eseguire un lavoro, sono chiamati **slave**;
- quando uno schiavo finisce un lavoro, ne chiede al padrone uno nuovo;
- quando viene creata una nuova task, viene aggiunta alla struttura centralizzata o assegnata al master;
- questi schemi centralizzati hanno una **scalabilità limitata**: più processi vengono utilizzati, maggiore è l'interazione con il master, che **diventa un collo di bottiglia** per il calcolo.

Schemi distribuiti:
- la serie di attività eseguibili viene distribuita ai processi che scambiano un'attività in fase di runtime, per bilanciare il carico computazionale.

## Dynamic Load Balancing (DLB)

Quando la fase LB deve essere eseguita, ogni nodo invia la sua temporizzazione ad uno specifico nodo master, che ha il compito di stabilire l'appropriato scambio di colonne tra i nodi.

![[DLB1.PNG]]

```c
LoadBalancingCriterion == TRUE

if IamLBMaster then
    LBInfo=ReceiveExecInfo();
    newRegionSizes = LoadBalance(LBInfo);
    allSequenceFlows = ComputeFlows(NewRegionSizes);
```

Il nodo Master restituisce ai nodi il numero esatto di colonne che devono essere scambiate.

![[DLB2.PNG]]

```c
if IamLBMaster then
    [...]
    SendFlows(allSequenceFlows)
```

I "flussi di colonne" vengono scambiati tra i nodi e dopo che lo spazio del nodo CA appropriato è stato regolato e può aver luogo la normale esecuzione CA.

![[DLB3.PNG]]

La normale esecuzione della CA viene eseguita con il nuovo partizionamento del nodo CA fino a quando non viene soddisfatto un altro criterio di bilanciamento del carico.

![[DLB4.PNG]]
