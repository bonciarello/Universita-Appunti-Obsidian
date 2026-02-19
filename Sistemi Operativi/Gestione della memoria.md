---
aliases: [Gestione memoria, GM]
tags: [sistemi-operativi]
---
## Gestione della memoria virtuale

Ogni processo come abbiamo già detto non può andare ad intaccare la porzione di memoria di un altro processo pertanto avviene un processo di virtualizzazione della RAM che permette di isolare i vari processi. Per comprendere meglio la gestione di quest'ultima facciamo riferimento, con un linguaggio simil *C++*, alle seguenti supposizioni:
- **spazio degli indirizzi reali:** ad esempio,
```cpp
virtualByte mem[M];
```
dove *M* sta ad indicare la memoria RAM che il computer possiede e può contenere *512MB*, *1GB*, ..., *16 \* 2^30 - 1 bit*. Può essere diviso in pagine nel seguente modo:
```cpp
byte P[X][4096]
```
dove *X* dipende dalla memoria e 4096 sta ad indicare i *KB* della pagina (ogni pagina contiene *4096KB*);
- **spazio degli indirizzi virtuali:** ad esempio,
```cpp
virtualByte memPn[N];
```
dove *N* è la grandezza dei registri (quindi in architetture a 64bit, significa che ci sono registri fino a *2^64*) e può contenere *4GB* per gli Intel x32. Può essere diviso in pagine nel seguente modo:
```cpp
byte Pn[1MB][4096]
```

![](virtualPhysicalMemory.png)

Ogni processo quindi viene illuso di avere *4GB* ma in realtà tutti gli indirizzi che possiede sono "virtuali". Pertanto viene applicato un meccanismo di conversione da indirizzo virtuale ad indirizzo fisico: nel dettaglio andiamo ad introdurre una tabella delle **Page Entries** ed una per ogni processo. Un esempio d'implementazione è il seguente:
```cpp
typedef pagina byte[4096];
pagina P[X];

class PageEntry {
	bool inMemoria;
	bool acceduto;
	bool scritto;
	long posizione;
	long posizioneSuDisco;
	bool leggibile;
	bool scrivibile;
	bool eseguibile;
}

class process {
    ....
    PageEntry pageDirectory[1MB];
    virtualByte mem(4GB, pageDirectory);
}
```
Attenzione, *X* dipende dalla memoria RAM reale e *P[X]* è un array che non è tutto materializzato all'interno nella RAM ma bensì è smistato nella memoria di massa.

Ad ogni processo viene assegnata una tabella detta **Page Entries** che tiene conto se una determinata pagina è in RAM, ed eventualmente dove, oppure sul disco. Possiamo vederlo come un array di *1MB* che tiene conto dello stato di ogni pagina caricata su quel processo.

Esiste inoltre un cosiddetto file di **SWAP** che non è altro che un punto di appoggio in cui vengono memorizzate porzioni di RAM (il *long posizione* fa riferimento a questo file).

```cpp
byte& virtualByte::operator[] (long indirizzo) {
 	pagina = indirizzo >> 12;
 	offset = indirizzo % 4096;
 	PageEntry pe = pageDirectory[pagina];
 	if (!pe.inMemoria) {
	 	// page fault.
		pe.posizione = trovaPaginaLibera();
   	    loadPage(pe.posizioneSuDisco); // swap in
   	    pe.scritto = false; // pagina fresca presa da disco
	}
	pe.acceduto = true;
	if (operazione di Scrittura)
		 pe.scritto = true;
	return P[pe.posizione][offset];
}
```

Facendo riferimento a quest'ultimo codice, possiamo vedere che se una pagina PE non è in memoria RAM (**page fault → non è nel cosidetto working set**), il sistema operativo deve procedere con un operazione di **Swap in** che consiste nell'andare in memoria (grazie alla variabile *long posizioneSuDisco*) prendere un blocco di pagine e caricarlo in RAM. Il processo inverso è lo **Swap out** che permette di ripulire da pagine non attualmente utilizzate la RAM.

## Swap in e Swap out

Ci sono dei thread ad altissima priorità che si occupano di *caricare in anticipo le pagine che si prevede siano usate* (**Swap in**) e di *eliminare le pagine non usate* (**Swap out**).

### Swap out nel dettaglio

È un semplice algoritmo i cui le pagine stanno in una lista FIFO. Quando una pagina p è acceduta, viene settato il bit *p.acceduto* a 1, e viene messa in testa alla FIFO.

Un thread *pulitore* elimina periodicamente, dalla fine della coda (swap out), quelle pagine *p* in cui trova *p.acceduto == 0* e pone *p.acceduto = 0* per tutte le altre pagine. Le pagine accedute periodicamente tendono ad evitare di essere "swapped out".

Il *pulitore* è tanto più aggressivo tanto più c'è meno memoria. Un esempio di codice di swap out è il seguente:
```cpp
void swapout(PageEntry p) {
	if (p.scritto)
        p.posizioneSuDisco = scriviSuDisco(p.posizione);
    p.inMemoria = false;
}
```

## Trashing

Una macchina performante deve possedere abbastanza RAM, il rischio che si corre è quello del trashing. Il processore è perennemente in uso per ricercare pagine che vengono costantemente eliminate dalla RAM (swap out).

![](trashing.png)

## Protezione

Generalmente i processi hanno due porzioni di RAM: **User Space** e **System Space** (c'è il codice del sistema operativo e la cosiddetta *Memoria Shared*).

È fisicamente impossibile che un processo acceda alla memoria di un altro. Tuttavia i processi condividono tutti lo stesso System Space. Pertanto si può sfruttare quest'ultimo per poter condividere porzioni di RAM fisica tra più processi partendo da indirizzi virtuali differenti (si sfruttano le page entries).

![](protezione.png)

## Mobile OSes

Nei [[Sistemi Operativi|sistemi operativi]] dei dispositivi mobili viene utilizzato Linux per diverse ragioni:
- scheduler efficiente;
- kernel semplice ed intuitivo;
- non offre meccanismi di swap out ma viene usato il modulo OOM Manager (Out of Memory Manager) attraverso cui un processo viene killato per poter liberare memoria indipendentemente se questa si stia utilizzando o meno.

## VMMAP

Possiamo distinguere tre diverse barre: **commited** (memoria effettivamente impegnata dal processo), **private bytes** e **working set** (la parte di Memoria RAM che è realmente utilizzata nel computer. La differenza tra *committed* e *working set* risiede all'interno del disco).

Ogni barra si caratterizza di diverse partizioni tra cui possiamo distinguere:
- **Image:** rappresenta il file *.EXE*;
- **Mapped File:** un file che generalmente risiede nel disco viene mappato nella ram per velocizzare il processo di acquisizione;
- **Shareable:** codice che viene ripetuto in più processi e quindi condiviso;
- **Heap:** rappresenta l'esito di tutte le "new" effettuate;
- **Managed Heap**;
- **Stack:** porzione che viene utilizzata per allocare le variabili locali;
- **Private data:** dati che non devono essere condivisi dagli altri processi;
- **Page Table**;
