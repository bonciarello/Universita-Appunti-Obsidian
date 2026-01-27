Un **algoritmo sequenziale** viene valutato dal suo runtime, tipicamente espresso **in funzione del suo input**.

In ogni caso, il tempo di funzionamento asintotico è lo stesso per qualsiasi macchina.

Il **tempo di esecuzione** di un programma parallelo dipende invece, oltre dalla **dimensione dell'ingresso**, dal **numero di processori** e dai **parametri di comunicazione** della macchina (cioè ANCHE dal tipo di architettura).

Per questo motivo le "regole" per l'analisi asintotica (es. "L'algoritmo di bubble sort è $O(n^2)$", ecc.) per i programmi sequenziali non sono più **valide**: dobbiamo analizzare l'algoritmo nel **contesto** della macchina che lo esegue.

Un **sistema parallelo** è la combinazione di un algoritmo parallelo e della piattaforma parallela sottostante.

Alcune misure per le prestazioni di algoritmi paralleli sono intuitive:

- **WALL CLOCK TIME** è il tempo di elaborazione trascorso dall'inizio del primo processore all'ora finale dell'ultimo processore. Cosa succede se modifichi il numero di processori o se modifichi la tua architettura?

## Fonti di sovraccarico nei programmi paralleli

Se usi due processori, perché il mio programma non viene eseguito due volte più velocemente?

Sfortunatamente ci sono alcune "spese generali" da considerare, come calcoli eccessivi, comunicazione e inattività.

![[fontiSovraccaricoProgrammiParalleli.png]]

- **Calcolo eccessivo:** calcolo non eseguito nella versione seriale dell'algoritmo. Questo potrebbe essere il caso quando abbiamo a che fare con un algoritmo sequenziale difficile o impossibile da parallelizzare; quindi, dobbiamo fare affidamento su un algoritmo parallelo poco efficiente.
- **Comunicazioni:** per problemi non banali, INEVITABILE!
- **Inattività:** alcuni processi/processori possono essere inattivi a causa di squilibrio del carico, sincronizzazione o componenti seriali (ad esempio, I/O).

## Tempi di esecuzione

- **Tempo di esecuzione sequenziale:** tempo trascorso (trascorso) tra l'inizio e la fine dell'esecuzione su un computer sequenziale.
- **Tempo di esecuzione parallela:** tempo trascorso dall'inizio del primo processo al momento in cui termina l'ultimo processo.

Di regola indichiamo con $T_s$ il tempo di esecuzione del seriale, e parallelamente al $T_p$. Per essere precisi, $T_s (n)$ e $T_p (n, p)$ sono funzioni, con *n* la dimensione dell'ingresso e *p* il numero di processori.

## Overhead parallelo totale

Sia $T_{all}$ il tempo totale di tutti i processori. Sia $T_s$ la sequenza temporale. $T_{all} - T_s$ è il tempo totale utilizzato da tutti i processi per il lavoro senza scopo di lucro. È definito come l'overhead totale $T_{O}$.

Notiamo che: $T_{all} = p*T_{P}$ (*p* è il numero di processori).

Così: $T_{O} = p*T_{P} - T_{S}$.

## Speedup

È **l'indicatore principale** (preliminare) per verificare se l'algoritmo parallelo "**beneficia**" del parallelismo.

Lo speedup (S) è il **rapporto** tra il tempo impiegato per risolvere il problema (sequenziale) di un **singolo processo** e il tempo impiegato per risolvere lo stesso problema su un computer **parallelo** con *p* elementi di elaborazione identici. Nella maggior parte dei casi, viene considerata la migliore versione sequenziale (anche se è accettabile considerare una versione "buona").

> $S = T_{S} / T_{P}$

Ad esempio, si consideri il problema di **aggiungere *n* numeri utilizzando *n* processori**.

Se ***n*** è una potenza di 2, possiamo farlo in ***log n*** passi, propagando le somme parziali lungo un albero binario "logico" di processori.

### Esempio

![[esempioSpeedup.PNG]]

Se un'addizione richiede un tempo costante, $t_{c}$, e la comunicazione di una singola parola $t_{s} + t_{w}$, il tempo parallelo è:

> $T_{P} = O (log n)$

Sappiamo che $T_{S} = O (n)$.

Quindi, lo speedup *S* è dato da $S = O(n / \ln n)$

**NB:** $t_s =$ tempo di avvio, $t_w =$ tempo di trasferimento per parola.

### Riferimento al miglior algoritmo sequenziale

Abbiamo detto che lo speedup è riferito al **miglior algoritmo sequenziale**. Perché?

Consideriamo un'istanza di ordinamento a bolle parallele (chiamato "ordinamento dispari-pari").

- Lascia che il **tempo seriale** per un Bubble sort sia di 150 secondi;
- Lascia che il **tempo parallelo** dell'ordinamento dispari-pari (parallelizzazione efficiente dell'ordinamento a bolle) sia di 40 secondi.

Lo **speedup** sembra essere 150/40 = 3,75: è una valutazione equa e onesta del sistema parallelo? Forse no.

Se prendiamo un quick sort seriale di 30 secondi? In questo caso, l'aumento di velocità è 30/40 = 0,75. **Ciò rappresenta una valutazione più accurata del sistema!**

### Più grande di p?

Lo **speedup** minima può essere 0 (la versione parallela del programma non finisce mai!)

D'altra parte, lo **speedup** in teoria dovrebbe essere delimitata sopra da *p*: dopotutto, dovremmo aspettarci un'accelerazione ***p*-volte** quando usiamo risorse *p*!

L'accelerazione maggiore di p è possibile solo se ogni processo (processore) impiega meno di $T_{S} / p$ per risolvere il problema (**speedup superlineare**).

In questo caso, un singolo processore eseguirà il programma più **velocemente** della versione seriale, il che **contraddice** la nostra ipotesi che speedup si riferisca al "miglior algoritmo sequenziale".

**Tuttavia, non è raro ottenere un aumento dello speedup superlineare: è buono o no?**

Una possibile ragione è che lo speedup superlineare della versione parallela esegue presumibilmente "meno" lavoro rispetto alla versione sequenziale corrispondente.

![[piuGrandeP.PNG]]

L'**speedup superlineare** può anche essere dovuta a motivi hardware.

Ad esempio, i dati di un problema potrebbero essere **troppo grandi** per essere contenuti nella cache di un singolo processore, **riducendo** le prestazioni a causa dell'utilizzo di elementi di memoria più lenti. Tuttavia, se suddivise tra processori, le singole partizioni possono essere abbastanza piccole da entrare nella cache.

Un esempio, quando si lavora con strutture regolari (vettori, matrici) in problemi di dati paralleli come somma di elementi, automi cellulari, ecc.

## Efficienza

L'efficienza è una misura della frazione di tempo in cui un elemento di elaborazione (processore) viene effettivamente utilizzato. Matematicamente:

> $E = S / p$

A causa dei limiti inferiore e superiore dell'accelerazione, è un **numero compreso tra 0 e 1**.

Se *E = 1*, il programma ha una **speedup lineare**. Se *E < 1/p*, il programma mostra un **rallentamento** (cioè "il calcolo parallelo è inutile!")

## Costo di un sistema parallelo

Il **costo** di un sistema parallelo è il **prodotto** del tempo parallelo e del numero di **processi** ($p x T_{P}$).

Riflette la quantità di tempo che ogni processo impiega per risolvere il problema.

Il **costo seriale** di un problema eseguito su una macchina sequenziale è banalmente il **runtime sequenziale** (del miglior algoritmo).

Un sistema parallelo è ottimale **in termini di costi** se il costo della risoluzione di un problema su una macchina parallela è asintoticamente (in termini di O), pari al costo seriale

Poiché $E = T_{S} / p*T_{p}$, abbiamo sistemi per costi ottimali:

> $E = O(1)$

Il **costo** è anche chiamato **lavoro**.

## Effetto della granularità sulle prestazioni

Spesso, paradossalmente, l'uso di meno processi **aumenta le prestazioni** dei sistemi paralleli.

L'uso di un numero di processori inferiore al massimo per eseguire un algoritmo parallelo è chiamato **ridimensionamento**.

Un **modo semplice** (anche se poco pratico) per farlo, chiamato **Scaling by Emulation**, è implementare un algoritmo che fornisce **un elemento** di input per il processore, e successivamente utilizzare meno processi per simulare un numero maggiore.

Se abbiamo *n* input e *p* processori *(p < n)*, possiamo assumere *n* processi virtuali e utilizzare *p* processori fisici per simulare *n/p* processi virtuali.

Quando il numero di processori **diminuisce** di un fattore *n/p*, il carico computazionale di ciascun processore aumenta di un fattore *n/p*. Pertanto, il costo $(p x T_{p})$ non aumenta, poiché $T_{p}$ aumenta al massimo *n/p*.

I **costi di comunicazione** non dovrebbero aumentare dello stesso fattore poiché spesso i processi virtuali assegnati allo stesso processore **comunicano tra loro** (diminuendo così i costi effettivamente).

Quindi, se il sistema all'inizio era **ottimale** in termini di costi, ora sarà lo stesso.

**Allo stesso modo, questo metodo non funzionerà se il sistema non era inizialmente ottimale in termini di costi.**

### Esempio

Considera il problema di aggiungere *n* numeri su *p* processori tali che *p < n* e *p* e *n* siano potenze di 2.

Usiamo sempre il solito algoritmo per n processori, solo ora pensiamo a loro come **processi virtuali**.

Per ogni processore *p* assegniamo *n/p* processori virtuali.

I primi *log p* dei *log n* passi dell'algoritmo originale sono simulati in *(n/p) log p* passi sui processori "reali" *p*.

![[granularitaPrestazioni1.PNG]]

I restanti passaggi *log n - log p* non richiedono comunicazione (i numeri vengono sommati "localmente").

Quindi, l'algoritmo prende quindi *O((n / p) log p)* per i passaggi che richiedono la comunicazione, quindi ogni **singolo** processore deve aggiungere numeri *p/n* nel tempo *O(n/p)*.

Quindi, il tempo parallelo totale è *O((n/p)log p + n/p) = O((n/p) log p)*.

Di conseguenza, il costo è *O(n log p)*, asintoticamente maggiore del costo *O(n)* della somma sequenziale dei numeri. Pertanto, il sistema non è ancora ottimale in termini di costi.

![[granularitaPrestazioni2.PNG]]

## Scalabilità intelligente

Possiamo "costruire" la granularità nell'esempio precedente al fine di ottenere un algoritmo ottimale in termini di costi? L'esempio precedente non è ottimale in termini di costi perché essenzialmente "simula" anche i passaggi di comunicazione (non necessario, dato che siamo sullo stesso processore!) Un'altra possibile soluzione è la seguente (cosa faresti in primo luogo):

- ogni processore somma i propri numeri *n/p* nel tempo *O(n/p)*;
- le somme parziali *p* su *p* processori vengono aggiunte nel tempo *O(log p)*;
- il tempo parallelo è $T_{p} = O(n/p + \lg p)$;
- il costo è O(n + p log p);

che è ottimale in termini di costi, almeno fino a quando non vale:

> $n = \Omega (p \lg p)$

![[scalabilitaIntelligente.PNG]]
