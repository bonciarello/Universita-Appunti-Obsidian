---
aliases: [FS]
tags: [sistemi-operativi]
---
I file system nascono dall'esigenza di gestire la periferica più importante, la memoria di massa. I **file system** sono un insieme di funzionalità per astrarre i dati grezzi presenti in memoria di massa e interpretare questi ultimi in termini di files e cartelle. Abbiamo vari livelli tra le applicazioni e le memorie di massa e sono le seguenti:

![](applicazioniToMemory.png)

## Livello PHY

### Hard disk meccanico

Un hard disk meccanico è ancora nelle nostre vite perché abbastanza economico. Ovviamente gli hard disk meccanico sono migliori ma comunque per utilizzi massicci sono ancora costosi.

Se devo andare in una porzione del disco, devo arrivare in un **piattello** composto da una certa quantità di **tracce** composte da **settori**, che sono zone di disco composti da *512 byte*. Avremo delle coordinate composte proprio da questi fattori precedentemente elencati che si trovano all'interno del *cluster*, un altro settore del *piattello*.

Avremo dei parametri prestazionali per valutare il nostro hard disk meccanico e sono i seguenti:
- **latenza di rotazione (in *ms*):** tempo che ci mettono i dati richiesti ad arrivare sotto la testina (in media *1/(RPM/60\*2)*);
- **seek time (in *ms*):** tempo che ci mettono le testine ad arrivare su un dato cilindro (dipende da fattori costruttivi);
- **tempo di trasmissione (in bit/sec):** tempo che ci mettono tutti i dati richiesti (sequenze di bit adiacenti) ad essere letti cioè *RPM/60 \* bit per cilindro*.

![](memorizzazioneMagnetica.png)

### Solid State Disk (SSD)

Quando parliamo di SSD (hard disk a stato solido) parliamo di robe elettroniche, non più meccaniche. Abbiamo seek time inesistenti e vince sulle operazioni di lettura/scrittura e sulle operazioni casuali.

## Livello elettronico (SATA, SAS, SCSI...)

Possiamo dare comandi di basso livello ad un hard disk cioè comandi di lettura (*READ C/H/S*) e scrittura (*WRITE C/H/S*).

Si tratta di comandi di basso livello che consentono di leggere/scrivere 1 o più settori per volta. Su questo livello di astrazione non esiste il concetto di file, directory, ecc.

Il piano di numerazione interno dei settori è composto da 3 coordinate: **cilindro (Cylinder)**, **testina (Head)**, **settore (Sector)** dette anche **coordinate CHS**.

I comandi vengono impartiti in due modalità: **tecnica mista DMA/interrupt** e **traduzione automatica da LBA (numerazione logica da 0 a N) a CHS**.

### DMA (Direct Memory Access)

DMA consente l'accesso diretto alla memoria da parte del disco con intervento minimo della CPU. Abbiamo alcuni vantaggi:
- i dati sono trasferiti direttamente dal dispositivo alla memoria e viceversa;
- nel frattempo il processore è libero;
- c'è un chip I/O che gestisce il trasferimento al posto della CPU: la CPU viene notificata quando il trasferimento termina;
- enorme miglioramento della performance per sistemi multitasking con molti accessi al disco.

### Notifiche alla CPU

Gli [[Eventi|eventi]] hardware possono essere catturati e gestiti tramite gli interrupt: ad esempio, possono essere generati da dei processi a causa di una divisione per zero oppure a causa di un tentativo di accesso a un area di memoria protetta ma anche, possono nascere da [[Eventi|eventi]] esterni, ad esempio un interrupt è generato se il mouse viene mosso o se qualcuno preme un tasto sulla tastiera.

La scomoda alternativa è il *polling* continuo in cui il processore controlla continuamente tutti i dispositivi.

## Livello driver

![](blockDeviceDriver.png)

In basso abbiamo delle memorie di massa (USB, hard disk, ecc), leggermente sopra abbiamo i suoi driver (insieme di procedure software scritte in assembly che permette al SO di manipolare la periferica) e ancora più in su abbiamo l'**I/O scheduler**.

Quest'ultimo permette la turnificazione delle richieste di input/output che arrivano dagli strati superiori (possiamo immaginarla quasi come una coda FIFO anche se in realtà anche qui ci sono delle regole specifiche per migliorare le performance).

Il **blocco più alto (VFS)** indica il **virtual file system**, cioè una libreria che unifica il modo di vedere le memorie di massa indipendentemente da come esse siano realizzate. Possiamo immaginarla come una grande *facade* che traduce le cartelle e i file in coordinate geometriche per l'accesso reale ai dati. Inoltre il VFS sfrutta meccanismi di **disk caches**.

## Livello file system

Un **file system** è responsabile della gestione dei file, della loro integrità e delle modalità di accesso. Sfruttano una numerazione lineare dei settori (*0 → N*). Un blocco di settori consecutivi nel sistema di numerazione lineare prende il nome di **partizione**. Ciascuna di essa viene formattata con un certo file system a scelta (*FAT32*, *FAT16*, *NTFS*, ecc).

I file system sono composti da codice API (permettono di gestire le strutture dati e forniscono funzionalità di gestione dei file e cartelle) e strutture dati (sono contenute in speciali settori della partizione, ad esempio ci può essere una tabella dei cluster liberi).

Le caratteristiche dei file system sono:
- **indipendenti dal dispositivo**;
- **funzionalità di backup/recovery** per contrastare le possibili perdite di dati;
- **compressione e crittografia**;
- **meccanismi di file locking**;
- **associamento di attributi** come di diritti di accesso, data/ora creazione/modifica .

Un **files** rappresenta un gruppo di cluster a cui è associato un nome e che contiene dati correlati. Una **directory** è un raccoglitore che raggruppa più file.

Ogni file system raggruppa, per comodità, i settori in cluster (da *8k = 16 settori*). Ogni file ha un piano di numerazione interno e un organizzazione dipendente dal suo formato. Non è detto che un file sia memorizzato in cluster consecutivi.

### WinHex

WinHex permette di vedere in profondità la memoria di massa, vedendo la posizione dei file nel primo settore oppure visualizzando i metadati anche di file precedentemente eliminati. Poossiamo recuperare l'indirizzo del cluster del file e la sua concatenazione.

In FAT32 il file eliminato viene indicato con l'esadecimale E5: ovviamente molta roba potrebbe sparire ma potrebbe rimanere traccia del file.

### Frammentazione

Purtroppo non sempre i nostri file vengono memorizzati in maniera contigua all'interno della memoria: questo causa un rallentamento generale delle performance di lettura/scrittura sequenziale. Questo problema purtroppo non può essere risolto ma può essere solo mitigato tramite meccanismi di deframmentazione cioè programmi che riorganizzano i file all'interno della nostra memoria in maniera tale da evitare il più possibile la frammentazione. Questo problema esiste soprattutto nei dischi meccanici, mentre negli SSD i tempi di accesso sono nettamente superiori. I [[Sistemi Operativi|sistemi operativi]] moderni hanno meccanismi di deframmentazione in background che ne migliorano le performance dei dischi.

Nel FAT32 abbiamo una cosiddetta **Fat Table** in cui si tiene conto dei cluster occupati e di quelli liberi: possiamo immaginare questa tabella come se fosse una lista doppiamente concatenata. Questo file system purtroppo **non è transazionale** cioè l'insieme delle operazioni da eseguire deve essere atomico, pertanto non ci devono essere interruzioni di alcun tipo. La conseguenza sarebbe avere un disallineamento tra la fat table e il file stesso. Ad oggi altri file system permettono di fare un UNDO delle operazioni qualora non ci fosse la giusta corrispondenza (operazione di **JOURNALINE**).

In Linux non c'è bisogno della deframmentazione, ma in parte: questo grazie agli \*in cui i file vengono organizzati sotto forma di alberi e non più di liste concatenate.

Per sapere livello deframmentazione in Linux si utilizza *sudo fsck -fn /dev/sdb* oppure *sudo fsck -fn /dev/sda*. In Windows, per verificare se un file o directory è deframmentato, si utilizza *config -a <file/directory>*.

### File locking

I file system si regolano su locking per la gestione dell'apertura di un file da diverse applicazioni. In Windows, abbiamo la possibilità di aprire un file in varie modalità (*SHARED_READ*, *SHARED_WRITE*, *SHARED_DELETE*) e abbiamo la possibilità di mettere lock su porzioni di file. In Linux, il locking non è previsto (assenza di lock esplicito) ma potrebbero presentarsi delle race condition: possiamo montare file system con lock obbligatorio.

### FAT32

Un esempio di file system è il FAT32. Quando apro un file (quindi avvio il comando), si prende il path del file, si a prendere la directory dov'è il file e si prende l'indirizzo del cluster del file.

![](FAT32.png)

### NTFS

NTFS è uno delle parti qualitativamente migliore di Windows. Ha tante cose da dire: è robusto, remapping automatico dei cluster, compressione dei file (non si parla di zippare), crittografia (difficile da decrittografare anche per le autorità), diritti di accesso sofisticati, transazionale (sempre consistente) e utilizza la MFT (master file table, sfrutta essa per capire in che posizione stanno i file).

### Mounting

Un ulteriore differenza tra Linux e Windows è la differente gestione di più periferiche di memorizzazione all'interno del pc: in Windows ogni periferica è identificata da una lettera (C; D; ecc) mentre in Linux ciò non avviene. L'operazione di mount combina più file system in un unica gerarchia e assegna una cartella, chiamata **mount point** nel file system nativo: al mount point viene "agganciata" la radice del file system "montato". Le **mount tables** memorizzano le informazioni sui punti di mount: la tabella è usata dal SO per capire che formato hanno i dati e che dispositivo deve essere montato per ciascun mount point.

![](mounting.png)
