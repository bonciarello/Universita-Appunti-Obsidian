---
aliases: [Calcolo Parallelo, Introduzione Calcolo Parallelo, ICP]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## Impatto del miniaturizzazione del dispositivo
Cosa succede quando la dimensione dell'elemento si riduce di un fattore *x*?
- La frequenza di clock aumenta di *x* (realmente inferiore a *x*, a causa del consumo di energia);
- I transistor per unità di area aumentano di $x^2$
- Anche la dimensione dello stampo tende ad aumentare (tipicamente un altro fattore di *circa x*);
- La potenza di calcolo grezza del chip aumenta di *circa* $x^4$! (di cui $x^4$ è dedicato al **parallelismo** o alla **località**)

Ma, in realtà, ci sono forze limitanti. La legge di Moore regola anche i costi di produzione che, insieme ai problemi di resa, limitano l'uso della densità.

Un altro concetto importante è quello dello **yield** che indica la percentuale del dispositivo che è utilizzabile. Ad esempio, se lo yield del 90%, di questo nostro dispositivo possiamo sfruttare il 90%, l'altra percentuale rappresenta errori, impurità che non fanno funzionare al 100% il nostro dispositivo.

![[costiSemiconduttori.PNG]]

## Limiti fisici: quanto può essere veloce un computer seriale?

![[limitiFisici.PNG]]

Considera la macchina sequenziale da 1 Tflop/s:
- I dati devono percorrere una certa distanza, r, per passare dalla memoria alla CPU.
- Prendi 1 elemento dati per ciclo, questo significa $10^{12}$ volte al secondo alla velocità della luce, $c = 3*10^8 m/s$. Quindi $r < c/10^{12} = 0,3 mm$

Ora mettiamo 1 TB di spazio di archiviazione in un'area di *0,3* mm per *0,3 mm*:
> $0,3^2 mm^2 / 10^{12} = 9 10^{-2} 10^{-6} m^2/10^{12} = 9 10^{-20} m^2 = (3*10^{-10})^2 m^2 = 3^2 A^2$

Ogni byte occupa meno di 3 Angstrom quadrati, o la dimensione di un piccolo atomo! ($1 Angstrom = 10^{-10} m = 0,1 nanometri$)

## Parallelismo nel 2020
Questi argomenti non sono più teorici. *Tutti i principali fornitori di processori producono chip multi-core*: quindi, ogni macchina sarà presto una macchina parallela e, per continuare a raddoppiare le prestazioni, il parallelismo deve raddoppiare.

Quali applicazioni commerciali possono utilizzare questo parallelismo? Devono essere riscritti da zero? Tutti i programmatori dovranno essere programmatori paralleli? Sicuramente sarà necessario un nuovo modello di software e, alla fine, si cercherà di nascondere la complessità alla maggior parte dei programmatori. Nel frattempo, però, ho bisogno di capire il concetto di programmazione parallela.

## Parallelismo automatico nelle macchine moderne

Da diversi anni, esistono diversi parallelismi intrinsechi dove noi non abbiamo il controllo. Questi sono:
- **Parallelismo a livello di bit:** per operazioni in virgola mobile, ecc.;
- **Parallelismo a livello di istruzione (ILP):** esecuzione di più istruzioni per ciclo di clock;
- **Parallelismo del sistema di memoria:** sovrapposizione delle operazioni di memoria con il calcolo;
- **Parallelismo del sistema operativo:** più attività vengono eseguite in parallelo (ad esempio, thread).

Ovviamente ci sono dei limiti a questi parallelismi: quindi, per ottenere prestazioni elevate, il programmatore deve identificare, programmare e coordinare le attività dei dati paralleli e diventare un programmatore di calcolo parallelo!

## Soluzioni più "esotiche" all'orizzonte
Oltre le CPU multi-core, esistono altri dispositivi paralleli che vengono detti "esotici", cioè che non vengono mai utilizzati perché sono frutto di studi sperimentali. Abbiamo:
- **GPUs - Graphics Processing Units** (per esempio, NVidia) è un processore parallelo collegato al processore principale. Originariamente nato per scopo speciale, è diventato uno dei dispositivi più importante nei computer di oggi;
- **FPGAs – Field Programmable Gate Arrays**: uso inefficiente dell'area del chip, più efficiente del multi-core ora e l'euristica dell'instradamento dei cavi è ancora problematica;
- **Dataflow and tiled processor architectures**;
- **Cell**: la memoria controllata dal software utilizza la larghezza di banda in modo efficiente ma il modello di programmazione non era ancora maturo;
- **Quantum computing**: uso di fenomeni quantomeccanici, qubits e calcolo teorico o fisico.

## Gerarchia della memoria

Di seguito troviamo una tabella che ci fa capire quanti cicli macchina sono necessari per accedere ad un dato:

![[gerarchiaMemoria.PNG]]

## Processori vs. accesso alla memoria
Dal 1986 siamo riusciti a migliorare il tempo di accesso alla memoria RAM, ma non di troppo, ma il ciclo di clock è migliorato di tanto: appunto per questo, il rapporto tra i tempi di velocità è aumentato a dismisura. Di seguito i dati:
- *nel 1986:*
    - **ciclo del clock**: circa 120 nanosecondi;
    - **tempo di accesso alla DRAM**: circa 140 nanosecondi;
    - *1:1 ratio*
- *nel 1996:*
    - **ciclo del clock**: circa 4 nanosecondi;
    - **tempo di accesso alla DRAM**: circa 60 nanosecondi;
    - *20:1 ratio*
- *nel 2002:*
    - **ciclo del clock**: circa 0.6 nanosecondi;
    - **tempo di accesso alla DRAM**: circa 50 nanosecondi;
    - *100:1 ratio*
- *dal 2003 il primo processore dual-core (della Power PC, non della Intel.*

## Cos'è il calcolo parallelo?
Il funzionamento si può descrivere in questo modo:
- dividere un lavoro in più attività;
- assegnare queste attività a molti processi che funzionano contemporaneamente;
- coordinare, controllare e monitorare questi processi.

Un esempio di processo parallelizzato è quello della costruzione di un muro. Sfortunatamente, non è sufficiente dare una "pala" (programma) a ciascun "lavoratore" (processore)! Le fasi della parallelizzazione di un lavoro (in prima approssimazione) sono:
1. decidi il modello di interconnessione tra il "processore" e la "memoria";
2. identifica e implementa il software di sistema per l'hardware;
3. identifica le strutture di dati e gli algoritmi per il nostro problema;
4. suddividi gli algoritmi e le strutture dati in sotto-problemi;
5. identifica le "comunicazioni" che saranno necessarie tra i problemi secondari;
6. assegna i problemi secondari a "processori" e moduli di memoria.

Tratteremo i punti da 3 a 6!

## Le ragioni principali del calcolo parallelo

Aumenta la dimensione e la complessità dei problemi che possono essere risolti:
- Problemi più grandi potrebbero non essere risolvibili su computer sequenziali in un tempo ragionevole (si decompone in problemi più piccoli (**ridimensionamento debole**))
- I grandi problemi potrebbero non essere contenuti nella memoria di un computer sequenziale (distribuire il problema su molte memorie di computer (**forte ridimensionamento**))

Riduci il tempo per risolvere un problema e risolvi i problemi più grandi più rapidamente.

## Perché le macchine veloci (parallele) funzionano lentamente?
- **latenza:** in attesa di accesso alla memoria o ad altre parti del sistema;
- **spese generali:** lavoro extra che deve essere fatto per gestire la concorrenza del programma e le risorse parallele;
- **inutilizzo:** lavoro insufficiente da eseguire a causa di un parallelismo insufficiente o di uno scarso bilanciamento del carico tra le risorse distribuite;
- **contesa:** ritardi dovuti a litigi su quale attività debba utilizzare una risorsa condivisa in seguito. La larghezza di banda della rete è un vincolo importante.

## Computer ad alte prestazioni

Di seguito troviamo uno schema che ci fa capire l'evoluzione dei computer ad alte prestazioni:
- *circa 40 anni fa:* $1*10^6$ operazioni in virgola mobile per secondo (Mflop/s) → computer scalari
- *circa 25 anni fa:* $1*10^9$ operazioni in virgola mobile per secondo (Gflop/s) → elaborazione vettoriale e memoria condivisa, miglioramento della larghezza di banda
- *circa 15 anni fa:* $1*10^{12}$ operazioni in virgola mobile per secondo (Tflop/s) → elaborazione distribuita, trasmissione di messaggi, basata su rete, altamente parallela; decomposizione dei dati, comunicazione/calcolo
- *oggi:* $1*10^{18}$ operazioni in virgola mobile per secondo (Pflop/s - Eflops/s) → combinazione/griglie e HPC; più adattivo, sensibile alla larghezza di banda, tolleranza ai guasti, precisione estesa, miglioramento dei nodi SMP (Symmetric Multi Processor)

## Transistor a microprocessore e parallelismo

![[transistorCPU.PNG]]

Questo schema ci fa capire lo sviluppo del parallelismo nel corso degli anni.

## Chi usa il calcolo parallelo?

![[usiParallelo.PNG]]

Questo schema ci spiega in che ambiti viene utilizzato il calcolo parallelo (ambito aerospaziale, biologia, finanza, ecc.).

## Cos'è un benchmark?

Confronto di diversi sistemi nella risoluzione di "qualcosa di difficile" come "sistemi densi di equazioni lineari". Il confronto coinvolge diversi computer ed i più famosi benchmark sono: LINPACK, che è un risolutore di equazioni lineari (il più famoso), e tipiche operazioni in virgola mobile e utilizzo di strutture dati (vettore/array).

La misurazione delle prestazioni di una determinata macchina parallela ha senso per:
- l'entità dei problemi "più grandi" (ovvero la dimensione del problema che "ha senso");
- le prestazioni riflettono il problema più grande che può essere eseguito su una data macchina

Gli indici e le metriche che entrano in gioco sono:
- $R_{max}:$ le prestazioni in Tflop del problema più grande che viene eseguito sulla macchina;
- $N_{max}:$ la dimensione del problema più grande che viene eseguito sulla macchina;
- $N_{1/2}:$ la dimensione dove si ottiene metà Rmax;
- $R_{peak}:$ prestazioni di picco teoriche (in Tflops) della macchina.

## Computer paralleli

I sistemi paralleli sono un'evoluzione dello schema di Von Nuemann, che si basa su: una CPU, una memoria e un bus di comunicazione.

I sistemi paralleli sono caratterizzati da una serie di processori che cooperano tra loro per la soluzione dei problemi mediante un'esecuzione simultanea.

![[schemaReal.PNG]]

## [[Architettura]] parallela generica
Qualsiasi macchina parallela può essere descritta, come da esempio:

![[archietturaGenerica.png]]

In questo schema, abbiamo delle CPU e delle memorie e sono interconnesse da un rete dedicata. Dove si trova fisicamente la memoria?

## Classificazione dei computer paralleli
La prima classificazione è quella di Flynn, che si basa sulle istruzioni e sui dati, e sono SISD, SIMD, MISD e MIMD. L'altra classificazione è quella strutturale, cioè su come è costruita la macchina:
- **basato sulla memoria** e abbiamo due tipi: memoria condivisa e memoria distribuita;
- **basato sulla tipologia di rete di interconnessione**: bus, traversa, [[Reti|reti]] dirette e indirette

## Classificazione di Flynn

Michael Flynn ha introdotto nel 1966 la seguente tassonomia, dove le classi di computer paralleli "interessanti" sono MIMD (Multiple Instruction stream-Multiple Data Stream) e SIMD (Single Instruction stream-Multiple Data Stream).

![[classificazioneFlynn.PNG]]

La *potenzialità dei 4 modelli* può essere rappresentata in figura:

![[4modelli.PNG]]

Nella macchina **SISD**, viene eseguita una *singola istruzione* sui *dati singoli*. Nella macchina **SIMD**, viene eseguita una *singola istruzione* ma su *molteplici dati*. Nella macchina **MISD**, vengono eseguite *molteplici istruzioni* ma su *singoli dati*. Nella macchina **MIMD**, vengono eseguite *molteplici istruzioni* su *molteplici dati*.

## SIMD

Si svolgono operazioni sincrone, cioè ad ogni passo del clock tutti i processori eseguono la stessa istruzione su dati differenti. Le macchine SIMD sono adatte per risolvere problemi di dati paralleli, dove la stessa istruzione viene applicata a partizioni distinte: per esempio somma tra due matrici.

![[SIMD.PNG]]

## MIMD

Viene definita la macchina parallela per antonomasia.

![[MIMD.PNG]]

Ogni processore opera in **modalità asincrona** sotto il controllo del programma e su dati differenti. I **computer MIMD con memoria condivisa** sono noti come macchine **multiprocessore** o strettamente accoppiate (es: tutti gli attuali computer multi-core, ecc.). I **computer MIMD basati sulla rete** sono noti come **multicomputer** o macchine ad accoppiamento lasco (es: Transputers, CRAY T3E, cluster Beowulf, ecc.)

## Classificazione strutturale basata sulla memoria

In un sistema parallelo, sono possibili 2 configurazioni di memoria tipiche:
- **memoria condivisa**: è presente un'unica memoria, alla quale possono accedere tutti gli **elementi di elaborazione (PE)**, gli scambi di messaggi tra i PE avvengono tramite scritture in questa memoria e, se il numero di PE è alto, la memoria può diventare un **bottleneck**.
- memoria distribuita: ogni PE ha la sua memoria locale e la comunicazione avviene tramite scambi di messaggi (**messaggio di passaggio**).

## Memoria condivisa vs. passaggio di messaggi

In entrambe le macchine SIMD e MIMD, la cooperazione si ottiene mediante lo scambio di dati. Ciò può verificarsi utilizzando della **memoria condivisa (SM)** e delle variabili condivise e utilizzando della **memoria distribuita (DM)** e del passaggio di messaggi.

Nel caso di SM, abbiamo un unico spazio di indirizzi, dove tutti i processori possono accedere ai dati ma avremo problemi legati alla scalabilità, parzialmente risolti dall'utilizzo della cache e la cooperazione adatta solo a pochi processori (2-32 multiprocessori MIMD).

Nel caso di DM, abbiamo un indirizzo privato per ogni processore, quindi il passaggio dei messaggi è necessario per la comunicazione. Questa è una modalità tipica per molti computer MIMD e cluster basati su workstation e, se necessario, un unico spazio di indirizzi può essere emulato da un software adatto (es. Memoria Condivisa Virtuale).

## Multiprocessori a memoria condivisa

Le operazioni di caricamento/archiviazione accedono direttamente a tutte le posizioni di memoria della memoria di sistema. Si ha un meccanismo semplice per la comunicazione e la condivisione: si ha una trasparenza rispetto alla posizione e le stesse operazioni sono disponibili dagli uni-processori. Si ha una memoria centrale o processori "vicini" distribuiti. Abbiamo due tipi di multiprocessori a memoria condivisa: costo di accesso alla memoria uniforme (UMA) o accesso alla memoria non uniforme (NUMA).

![[multiprocessoriMemoriaCondivisa.PNG]]

## Un tipico esempio di multicomputer: cluster di workstation

Comunemente si trova nelle catene di tecnologia, di solito costituito da macchine per merci. Rete locale o speciale (NIC PICI + switch esterni) solitamente adottata, con il vantaggio di avere un'elevata latenza ed efficienza di banda.

## Linux cluster

Perché utilizzare il Linux cluster? Costa poco, è facile da mantenere e installare, aggiungere nodi e mantenerli e si ha un ottimo rapporto prezzo/prestazioni.

Come viene utilizzato? Come un computer parallelo (MPI, [[OpenMP]], PVM), come un mucchio di CPU e come multiprocessore virtuale (Mosix).

Tuttavia le attuali architetture multi-core (esempio Intel i7) offrono buone soluzioni.

## Soluzioni cluster

- **Beowulf:** da un progetto originale della NASA, è oggi utilizzato per "ottenere" un computer parallelo da un cluster di workstation;
- **MOSIX / OpenMosix (abbandonato nel 2002):} sistema operativo distribuito proprietario, implementando in modo trasparente diversi meccanismi tipici, come il bilanciamento del carico, la migrazione dei processi, ecc. ;
- **Linux Virtual Server:** modifiche al kernel finalizzate all'ottenimento del bilanciamento del carico, ecc., nei cluster basati su Linux.

## Panoramica delle architetture parallele

![[architettureParallele.PNG]]

Questo schema ci rappresenta le principali macchine parallele. SISD è tratteggiato perchè non è una vera e propria macchina parallela.

## Modelli computazionali

Le architetture mono-processore hanno un **unico modello computazionale**. Le architetture parallele hanno molti modelli computazionali, i più adottati sono:
- **modello di passaggio dei messaggi**;
- **modello parallelo di dati**.

Sono stati sviluppati anche linguaggi dipendenti dal modello. *Non è necessaria* una corrispondenza tra il particolare hardware e il modello computazionale adottato.

## Message Passing Model

Il programma è organizzato in **processi**. Uno o più processi sfruttano nel tempo la condivisione di ogni singolo processore. I processi possono comunicare tra loro *esclusivamente* tramite **messaggi**. Nessuna parte della memoria è condivisa tra i processi.

## Modello parallelo di dati

I dati vettoriali o di tipo array vengono distribuiti tra i processi. Tutti i processori eseguono la stessa operazione, ciascuno sui propri dati locali **(modalità SPMD)**. Il programmatore a volte può specificare una condizione tale che solo alcuni processori che lo soddisfano eseguano la seguente istruzione, mentre altri rimangono inattivi (ramificazione - tipica di MIMD).

## Linguaggi di programmazione

- **Message Passing Interface (MPI):** condivisione esplicita di variabili da un processore all'altro, in modo che ogni processo possa gestire un segmento di memoria del programma;
- **[[OpenMP]]:** gestisce la distribuzione del processo e dei dati nelle macchine SMP, le comunicazioni avvengono tramite memoria condivisa;
- **High Performance Fortran:** crea un singolo segmento di memoria condivisa virtuale tra i processori, il programmatore sceglie come distribuire i dati in memoria;
- **CUDA/OpenCL:** linguaggi di programmazione de-facto per sistemi basati su GPGPU.

## Terminologia

- **TASK** è una sezione logica discreta del lavoro computazionale. Un'attività è tipicamente un programma o una "parte" del programma che viene eseguita da un processore;
- **TASK PARALLELO** è un'attività che può essere eseguita da più processori in una "cassaforte", cioè, fornisce risultati corretti);
- **ESECUZIONE SERIALE** è l'esecuzione sequenziale di un programma, un'istruzione alla volta. Nel senso più semplice, questo è ciò che accade utilizzando una macchina con un processore. Tuttavia, praticamente tutte le attività parallele hanno sezioni di un programma parallelo da eseguire in modo seriale;
- **ESECUZIONE PARALLELA** è l'esecuzione di un programma consiste di più attività con ciascuna attività che è "in grado" di eseguire le stesse istruzioni o diverse allo stesso tempo;
- **MEMORIA CONDIVISA:** da un \underline{punto di vista hardware}, descrive un'[[Architettura|architettura]] in cui ogni processore ha accesso diretto (solitamente tramite bus) a una memoria fisica comune. Dal \underline{punto di vista del software}, descrive un modello in cui le attività parallele hanno la stessa "immagine" di memoria e possono accedere e indirizzare le stesse posizioni di memoria logica, indipendentemente da dove si trovano;
- **MEMORIA DISTRIBUITA:** da un \underline{punto di vista hardware} si intende un accesso alla memoria basato su rete, per una memoria fisica non comune. Da un \underline{punto di vista software}, l'attività può solo "vedere", da un punto di vista logico, la memoria locale della macchina e può utilizzare le comunicazioni per accedere alla memoria di altre macchine su cui sono in esecuzione altre attività;
- **COMUNICAZIONI:** le attività parallele in genere richiedono lo scambio di dati. Esistono diversi modi per ottenere ciò, ad esempio tramite un bus di memoria condivisa o tramite una rete. Tuttavia, lo scambio di dati è segnalato come **comunicazione efficace**, indipendentemente dal metodo utilizzato;
- **SINCRONIZZAZIONE** è il coordinamento di attività parallele in tempo reale, spesso associate alle comunicazioni. Viene spesso implementato stabilendo un punto di sincronizzazione in un'applicazione in cui un'attività non può continuare fino a quando un'altra attività (o più) non raggiunge lo stesso punto (o logicamente equivalente). La sincronizzazione di solito implica l'aspettativa di almeno un'attività e può quindi causare un aumento del "wall-clock" delle applicazioni parallele;
- **GRANULARITÀ:** nel calcolo parallelo, la granularità è una misura qualitativa della relazione tra calcolo e comunicazione. *COARSE-GRAIN:* porzioni relativamente grandi di lavoro di calcolo vengono eseguite tra la comunicazione. *FINE-GRAIN:* porzioni relativamente piccole di lavoro di calcolo vengono eseguite tra la comunicazione;
- **PARALLEL OVERHEAD** è il tempo impiegato per coordinare attività parallele, in contrasto con il lavoro utile. I fattori determinanti sono:
    1. tempo di avvio dell'attività
    2. sincronizzazioni
    3. comunicazioni dei dati
    4. overhead software imposto da compilatori paralleli, librerie, strumenti, [[Sistemi Operativi|sistemi operativi]], ecc.
    5. tempo di conclusione dell'attività
- **MASSIVE PARALLELISM** si riferisce all'hardware che include un dato sistema parallelo costituito da molti processori fisici. "Molti" si riferisce al mondo d'oggi in sistemi con più di 10.000 processori (ad esempio, GPGPU);
- **SCALABILITÀ** si riferisce alla capacità dei sistemi paralleli (hardware e / o software) di consentire un **aumento proporzionale** della velocità con l'aumento dei processori. I fattori che contribuiscono alla scalabilità includono:
    - *hardware:* in particolare la larghezza di banda della memoria, le larghezze di banda della CPU e le comunicazioni di rete;
    - l'algoritmo utilizzato;
    - il sovraccarico parallelo;
    - caratteristiche della tua specifica applicazione e codifica.

## Granularità
è il rapporto elaborazione/comunicazione.

![[granularita.PNG]]

## Scalabilità
- **RIDIMENSIONAMENTO FORTE:**
    - la dimensione totale del problema rimane fissa man mano che vengono aggiunti più processori;
    - l'obiettivo è eseguire la stessa dimensione del problema più velocemente;
    - il ridimensionamento perfetto significa che il problema viene risolto in un tempo *1/P* (rispetto al seriale);
- **RIDIMENSIONAMENTO DEBOLE:**
    - la dimensione del problema per processore rimane fissa man mano che vengono aggiunti più processori;
    - l'obiettivo è eseguire un problema più ampio nella stessa quantità di tempo;
    - scalabilità perfetta significa che il problema *Px* viene eseguito contemporaneamente al funzionamento di un singolo processore.

**I fattori hardware giocano un ruolo significativo nella scalabilità:**
- larghezza di banda del bus di memoria-cpu su una macchina SMP;
- larghezza di banda della rete di telecomunicazioni;
- quantità di memoria disponibile su qualsiasi macchina o gruppo di macchine;
- velocità del processore.
