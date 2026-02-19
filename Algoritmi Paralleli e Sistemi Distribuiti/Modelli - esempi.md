---
aliases: [ME]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## ESEMPI: elaborazione di array paralleli

![[parallelArrayProcessing.png]]

Il codice seriale potrebbe essere della forma:

```c
for(i = 0; i < m: i++)
	for(j = 0; j < n; j++)
        a[i][j] = fcn(i,j);
```

Il calcolo degli elementi è indipendente l'uno dall'altro: questo porta a una situazione **imbarazzantemente parallele**.

## Imbarazzantamente parallela

*Imbarazzantamente parallela* è la condizione perfetta di parallelismo, tipicamente (ma non sempre) quando non ci sono comunicazioni tra elementi, dipendenze di dati, ecc...

## ESEMPIO: frattale Julia Set

Questo esempio è un piano complesso, dove questi colori vengo dati da quante iterazioni sono necessarie per far convergere il punto ad un certo valore.

![[julia.PNG]]

## Soluzione parallela per l'elaborazione di array (num. 1)

Gli elementi dell'array sono distribuiti in modo che ogni processore abbia una parte di un array (**sotto-array**). Il **calcolo indipendente** degli elementi dell'array assicura che non richieda comunicazioni tra le attività (*parallelamente imbarazzante*). Il **modello di distribuzione viene scelto in base ad altri criteri**, ad esempio, il passo unitario (passo 1) tra i sotto-array. L'unità falcata massimizza l'uso della cache / memoria. Poiché è desiderabile avere un'unità di misura tra i sotto-array, la scelta dello schema di distribuzione dipende dal linguaggio di programmazione. *ES: nell'archiviazione C nelle righe, Fortran nelle colonne*. Dopo che l'array è stato distribuito, ogni attività esegue la parte del ciclo corrispondente ai suoi dati. Ad esempio, con la distribuzione a blocchi:

```c
for (i = mystart; i <= myend: i++)
	for (j = 0; j < n; j++)
        a[i][j] = fcn(i,j);
```

## Soluzione parallela per l'elaborazione di array (num. 1) (paradigma master-slave statico)

Una possibile soluzione:
- implementazione del modello **SPMD**;
- il **processo Master** inizializza l'array, invia le informazioni al lavoratore e riceve i risultati;
- i **processi Worker** ricevono le informazioni, eseguono parte del calcolo e inviano i risultati al master;
- usiamo la distribuzione array del diagramma a blocchi.

### Modello Master Slave

![[modelloMasterSlave.png]]

```c
// scopri se sono MASTER o LAVORATORE

/*
if sono MASTER
	inizializza l'array
	invia ogni informazione WORKER su una parte dell'array che possiede
	invia ad ogni WORKER la sua porzione di matrice iniziale
    ricevere da ogni WORKER i risultati
*/

/*
else se sono LAVORATORE
	ricevere da MASTER informazioni su una parte dell'array che possiedo
    ricevere da MASTER la mia porzione di array iniziale
*/

// calcola la mia porzione di array
for(i = mystart; i <= myend: i ++)
	for(j = 0; j < n; j ++)
        a[i][j]:= fcn(i, j);

/*
	send risultati MASTER
else if
*/
```

In
```
color{green}
verde
```

le modifiche "parallele" rispetto alla versione sequenziale.

## Bilancio del carico

![[arraySparsi.png]]

*Array sparsi:* alcune attività avranno dati effettivi su cui lavorare mentre altre hanno per lo più "zeri".

![[grigliaAdattivi.png]]

*Metodi di griglia adattivi:* alcune attività potrebbero dover perfezionare la loro mesh mentre altre no.

## Pool di compiti
*Processo principale (MASTER):*
- mantiene il pool di attività che devono eseguire processi slave
- invia un'attività a uno slave quando richiesto
- raccoglie i risultati dagli slave

*Processo di lavoro (WORKER):* viene eseguito in sequenza:
- raccogliere un'attività dal processo principale
- esegue il calcolo
- invia i risultati al master

I processi slave non sanno quale parte dell'array gestiranno prima del runtime o quante attività verranno eseguite su di esso. Il **bilanciamento dinamico del carico si verifica in fase di esecuzione**: attività (o processi) più veloci richiederanno più lavoro.

## Approccio al pool di attività dello scheduler

![[poolAttivitaScheduler.png]]

Quando ogni attività termina il suo lavoro, riceve un nuovo pezzo dalla coda di lavoro.

```c
//scopri se sono MASTER o WORKER

/*
if sono MASTER
	do fino a quando non ci saranno più lavori
		invia a WORKER il prossimo lavoro
		 ricevere risultati da WORKER
	end do

	Tell WORKER no more jobs
altrimenti se sono LAVORATORE
    do fino a quando non ci saranno più lavori
		 ricevere da MASTER il prossimo lavoro */
		calcola l'elemento dell'array: a[i, j] = fcn(i, j)
	  /*invia i risultati a MASTER
	end do
endif
*/
```

## Calcolo di PI-GRECO

Il calcolo di PI-GRECO può essere effettuato in diversi modi. Usiamo un metodo Monte Carlo (algoritmi che utilizzano il campionamento statistico per la risoluzione):

- inscrivi un cerchio in un quadrato;
- genera punti casuali nella piazza;
- determina il numero N di punti nel quadrato che sono ANCHE nel cerchio;
- l'area del cerchio è *Ac*, *As* è quella del quadrato;
- possiamo dire che *PI-GRECO circa 4 * (Ac / As)*;
- notare che più punti vengono generati, migliore è l'approssimazione.

![[piGreco.png]]

Il codice pseudo-seriale sarebbe:

![[piGrecoCodice.png]]

Osserviamo questo codice:

- la maggior parte del calcolo viene effettuato per l'esecuzione del ciclo;
- porta a una soluzione "imbarazzantemente parallela";
- computazionalmente "difficile";
- comunicazione minima;
- I/O minimo.

## Soluzione parallela del calcolo di PI-GRECO

In questo caso posso adottare una sorta di *strategia parallela*, cioè spezza il ciclo in parti che possono essere eseguite dall'attività. Perciò:

- ogni attività esegue la sua parte del ciclo un certo numero di volte...;
- **NOTA:** ogni attività può eseguire calcoli SENZA richiedere informazioni da altre attività (non ci sono *dipendenze dai dati*);
- utilizza il modello SPMD. Inoltre, un'attività funge da master e raccoglie il risultato.

![[soluzionePiGreco.png]]

## Automi cellulari

Gli automi cellulari (CA) sono modelli computazionali paralleli discreti, ampiamente utilizzati per modellare e simulare sistemi complessi. Inventato da John von Neumann e Stanislaw Ulam al Los Alamos National Lab (primi anni '50). Basato sul lavoro di Alan Turing, la maggior parte delle ricerche di base sull'CA venne svolta negli anni '50 e '60.

Tre [[Eventi|eventi]] principali nella ricerca sulla CA:

- l'automa che si riproduce da solo di *John von Neumann*;
- "The Game of Life" di *John Conway*;
- classificazione di *Stephen Wolfram* degli automi cellulari.

Concepito negli anni '50 da John von Neumann per lo studio dei problemi di auto-riproduzione (von Neumann, 1966). Sono un modello computazionale parallelo, discreti nello spazio e nel tempo. CA può essere descritta come una matrice di semplici unità di elaborazione, le celle, ciascuna interagente con le sue vicine.

*CA* = un reticolo di celle identificate da punti in uno spazio euclideo.

$X = \{ \epsilon_1, ..., \epsilon_{m-1} \}$ è l'indice di vicinato così che, data una cella generica *c*, l'insieme *N(X, c)* delle celle adiacenti è:

> $N(X, c) = N(c) = \{c, c+\epsilon_1, ..., c+\epsilon_{m-1} \}$

Per ogni cella *c*:

- *S(c)* è l'insieme finito di stati possibili;
- $\sigma(c, N(c)): S^m \rightarrow S$ è la funzione di transizione.

![[automaVN.PNG]]

All'istante *t=0*, le celle si trovano in stati arbitrari che definiscono la **condizione iniziale** del sistema. CA si evolve cambiando gli stati delle cellule a fasi discrete applicando simultaneamente a ciascuna cella la stessa **funzione di transizione**, in modo che la sua evoluzione è determinata dalle interazioni locali tra le loro parti costituenti. La dinamica complessiva **emerge** come conseguenza delle applicazioni simultanee della funzione di transizione a ciascuna cellula.

## CA Dynamics

Classificazione di Wolfram del comportamento CA (più popolare) unidimensionali:

1. spazialmente stabile;
2. sequenza di strutture stabili o periodiche;
3. comportamento aperiodico caotico;
4. strutture localizzate complicate.

*Problema:* l'appartenenza alla classe di una data regola è indecidibile.

## The Game of Life

The **Game of Life** è un automa cellulare sviluppato dal matematico inglese **John Conway** (1927-2020). Il suo scopo è mostrare come **comportamenti complessi possano emergere da regole semplici** e interazioni multi-corpo, un principio che sta alla base dell'eco-biologia, che rimanda anche alla **teoria della complessità**.

Le regole semplici portano ad un comportamento complesso.

Le regole sono:

- *Sopravvivenza:* 2 o 3 vicini vivi;
- *Nascita:* esattamente 3 vicini vivi
- *Morte:* tutti gli altri casi

## Equivalenza di Turing del "The Game of Life"

È stato dimostrato che il "Game of life" ha la stessa potenza di calcolo di una macchina di Turing. Regole semplici, eseguite in ogni fase temporale:

- una cella viva con 2 o 3 vicini vivi sopravvive al round successivo;
- una cella viva con 2 o 3 vicini vivi sopravvive al round successivo;
- una cella viva con 1 o 0 vicini muore di isolamento;
- una cella vuota con esattamente 3 vicini diventa una cella viva nel round successivo.

## Implementazione seriale

Per mantenere gli stati effettivi delle celle durante la fase CA, viene considerata una struttura dati a doppia matrice per ogni sottostato:

- i valori effettivi vengono letti dalla matrice principale;
- i nuovi valori vengono memorizzati sulla matrice di supporto.

Alla fine di ogni fase della CA, la matrice di supporto diventa quella principale e il processo continua.

![[implementazioneSeriale.png]]

![[implementazioneSerialeCont.png]]

Questo è un ESEMPIO, non è riferito al "Gioco della vita"!

## Esecuzione parallela di CA

![[esecuzioneParallela.png]]

## Partizionamento monodimensionale vs. bidimensionale

![[partizionamento.PNG]]

Nel partizionamento bidimensionale, la cella che sta al centro può comunicare con la cella di sopra, di sotto, di sinistra e di destra.

## Celle fantasma e bordi di una regione

![[celleFantasma.PNG]]

*Partizionamento 1-D:* quando mi ritrovo sull'orlo, il Core 1 comunica la colonna sull'orlo al Coro 2 e viceversa

*Partizionamento 2-D:* quando mi ritrovo sull'orlo, un core comunica le colonne e le righe che sono sull'orlo con gli altri core.

In [[OpenMP]] è semplice sviluppare il concetto del bordo della regione: tutti i dati sono condivisi, non sono necessari halo/borders e si parallelizzano i loop. In MPI invece è più complicato sviluppare questo concetto: c'è il problema delle cellule fantasma, messaggi bloccanti e non bloccanti.

## Equazione del calore

É un automa cellulare. Come sappiamo, la maggior parte dei problemi nel calcolo parallelo richiede (sfortunatamente) comunicazioni tra le attività. Alcuni di loro richiedono ulteriori comunicazioni con il compito "vicini".

L'equazione del calore è un'equazione differenziale parziale che descrive la variazione di temperatura nel tempo (su una piastra per esempio), data la distribuzione iniziale della temperatura e le condizioni al contorno.

Uno schema del metodo delle differenze finite (FDM) viene utilizzato per risolvere numericamente l'equazione su una regione quadrata.

La temperatura iniziale è impostata a zero ai bordi e alta al centro.

Il limite di temperatura viene mantenuto a zero (in modo da simulare l'aria, ad esempio).

Viene utilizzato un algoritmo iterativo. Gli elementi di una matrice bidimensionale che rappresentano le temperature nei punti del quadrato.

![[eqCalore.PNG]]

### Programma seriale
Il calcolo di un elemento dipende dai valori dei vicini. Nel programma:

> u2 = step corrente
>
> u1 = step precedente

```c
do iy = 2, ny - 1
    do ix = 2, nx - 1
        u2(ix, iy) = u1(ix, iy)
            + cx * (u1(ix+1,iy) + u1(ix-1,iy) - 2.*u1(ix,iy))
            + cy * (u1(ix,iy+1) + u1(ix,iy-1) - 2.*u1(ix,iy))
    end do
end do
```

### Soluzione parallela (num. 1)

Usiamo il modello **SPMD** e il **parallelismo dei dati**. L'intero array viene **partizionato** e distribuito come sottoarray all'attività. Ogni attività ha una parte dell'intero array. Determiniamo le dipendenze dei dati:

- elementi interni che appartengono a compiti indipendenti da altri compiti;
- gli elementi di confine dipendono dagli elementi di dati dei vicini, quindi è necessario comunicare...

Il **processo master** invia i dati iniziali a uno slave, controlla la convergenza e raccoglie i risultati. I **processi slave** calcolano la soluzione, indicando quando e dove necessario, con i processi vicini.

![[eqCaloreSol1.PNG]]

![[eqCaloreCod1.PNG]]

### Soluzione parallela (num. 2): comunicazione sovrapposta con calcolo

Nella soluzione precedente si ipotizza l'utilizzo del **blocco della comunicazione** per gli slave. Block Comunication attende il "completamento" del processo di comunicazione prima di eseguire l'istruzione successiva.

Nella soluzione precedente, i processi adiacenti **PRIMA** di comunicare i dettagli del confine, **POI** aggiornano la loro parte dell'array.

I **tempi di comunicazione** possono essere drasticamente ridotti (complicando però il codice!) Attraverso l'uso di comunicazioni non bloccanti. *Le comunicazioni non bloccanti consentono l'esecuzione del calcolo MENTRE la comunicazione è in corso.*

In questa seconda soluzione, ogni processo aggiorna la parte interna del proprio array mentre è in atto la comunicazione della scheda, aggiornando la sua porzione di confine DOPO che la comunicazione è terminata.

![[eqCaloreCod2.PNG]]

```
