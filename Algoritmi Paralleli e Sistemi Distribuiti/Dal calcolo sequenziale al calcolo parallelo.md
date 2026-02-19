---
aliases: [calcolo sequenziale calcolo parallelo, CSCP]
tags: [algoritmi-paralleli-e-sistemi-distribuiti]
---
## Calcolo sequenziale

Risolve un problema mediante un algoritmo le cui istruzioni vengono eseguite in sequenza. Il modello computazionale è caratterizzato da un unico processore quindi abbiamo a che fare con la macchina di Von Newmann.

Per esempio, supponiamo di voler fare la somma di 8 numeri. Per eseguire la somma abbiamo bisogno di **7 somme**, oltre a un'allocazione iniziale *(sum = i [0])*:

![[calcoloSequenziale.PNG]]

## Calcolo parallelo

Risolve lo stesso problema da un algoritmo le cui istruzioni vengono eseguite in modalità parallela. Il modello computazionale che fornisce più processori e meccanismi di cooperazione correlati.

In questo caso, le istruzioni che compongono questo algoritmo possono essere eseguite in parallelo, cioè che possono essere eseguite nello stesso istante o in istanti differenti. Sicuramente abbiamo a che fare con un modello computazionale dove abbiamo più processori e quindi abbiamo il problema di come far cooperare questi processori, condividendo le variabili.

Per esempio, supponiamo di eseguire la somma di otto numeri con 4 CPU, ciascuna delle quali esegue la somma di due variabili. Successivamente, 2 CPU eseguono la somma dei due risultati precedenti e quindi la CPU 1 per la somma finale dei due risultati ottenuti. **Richiede **3 passaggi** invece dei 7 precedenti (log2 n)**:

![[calcoloParallelo.PNG]]

In questo esempio, nello step 2 e 3 c'è una perdita di tempo per comunicare le nuove informazioni, però posso subito mettere in moto in parallelo le CPU che mi serviranno per continuare il calcolo. Oltretutto, molte CPU svolgono pochissime operazioni nell'algoritmo: questo è un problema per il calcolo parallelo.

Quindi, quando sviluppo un calcolo parallelo, dobbiamo essere in grado di sfruttare ogni componente a disposizione.

## Problemi importanti nel calcolo parallelo
- **Partizionamento di attività / programmi**, cioè come si suddivide una singola attività tra i processori in modo che ogni processore esegua la stessa quantità di lavoro e tutti i processori lavorino insieme per completare l'attività;
- **Partizionamento dei dati**, cioè come si suddivide i dati in modo uniforme tra i processori in modo tale da ridurre al minimo l'interazione con il processore.
- **Comunicazione / arbitrato**, cioè come permettiamo la comunicazione tra processori diversi e come arbitriamo i conflitti relativi alla comunicazione.

## Concetti di calcolo parallelo - Esempi
Quando si esegue un'attività, alcune attività secondarie dipendono l'una dall'altra, mentre altre no. Facciamo l'esempio di preparare la cena, nello specifico:
- preparazione dell'insalata indipendente dalla cottura delle lasagne;
- le lasagne devono essere assemblate prima della cottura;
- aggiustare la tavola.

Allo stesso modo, nel risolvere problemi scientifici, alcuni compiti sono indipendenti l'uno dall'altro. Se applichiamo il ragionamento del calcolo sequenziale e parallelo all'esempio, possiamo notare:
- **Task seriali:** fare la salsa, assemblare le lasagne, cuocere le lasagne, lavare la lattuga, tagliare le verdure, montare l'insalata, aggiustare la tavola;
- **Task parallele:** cucinare la lasagna, preparare l'insalata, aggiustare la tavola;

Facciamo un altro esempio: supponiamo di voler realizzare un puzzle da 5000 pezzi. Il tempo necessario per una persona affinché completi il puzzle è di *n* ore. Come possiamo ridurre il tempo necessario al completamento?

Aggiungiamo una persona al procedimento del completamento del puzzle: sicuramente dimezzeremo il tempo necessario al completamento, ma avremo problemi nella comunicazione (per esempio, a me serve un pezzo che ha l'altra persona) e nella contesa delle risorse.

## Calcolo parallelo in poche parole
In generale, la descrizione delle risorse con cui stiamo gestendo il nostro programma parallelo possono essere:
- un singolo computer con più processori;
- un certo numero di computer collegati da una rete;
- una combinazione di entrambi.

Un programma viene svolto in parallelo solamente quando:
- viene suddiviso in pezzi di lavoro discreti che possono essere risolti simultaneamente;
- si esegue più istruzioni di un programma in qualsiasi momento;
- si risolve in meno tempo con più risorse del computer che con una singola risorsa di calcolo.

## Programmazione parallela
Un programma sequenziale può essere diviso in due tipi di sezione:
- **sezione prettamente sequenziale** (che non può essere eseguita in parallelo);
- **sezione prettamente parallelizzabile**.

I metodi per la suddivisione delle sezioni parallelizzabili sono fondamentali per ottenere prestazioni elevate.

È fondamentale che le sezioni che sono prettamente sequenziali siano piccole.

Lo scopo di una **buona parallelizzazione** è:
- mantenere tutti i processori (equamente) occupati (**bilanciamento del carico**);
- ridurre al minimo la comunicazione tra processori (**parallelismo a grana grossa**);
- limitare la **replica** del calcolo al minimo necessario.

Per valutare la bontà di una macchina parallela in termine di prestazioni, si utilizza la metrica del **flop/s** che indica le operazioni floating point per secondo.

## CPU multi-core: perché?
Il passaggio da 1 core a più core è dovuto al fatto che era diventato difficile raffreddare le macchine soprattutto per i computer portatili, ecc.

Altro problema è che, aumentando le prestazioni, e quindi i cicli di clock, ho un problema di bottleneck, cioè si ha un accesso contemporaneo a un dato esistente.

Un vecchio esempio ci dimostra quello che abbiamo spiegato:

![[multiCore.PNG]]

## Perché usare il calcolo parallelo?
Uno dei motivi è, ovviamente, velocizzare i calcoli ma ci sono altri motivi più nobili come l'utilizzo di risorse non locali come per esempio:
- *SETI@home* è un applicazione che serve per ricercare segnali extraterrestri dove abbiamo all'incirca 1.000.000 di computer che contemporaneamente vanno ad analizzare i segnali dallo spazio che riceve e, messi in parallelo, si riesce a raggiungere 1 exaflop;
- *folding@home* che ha circa 340.000 computer erogando 4.2 petaflop.

I limiti per quanto riguarda il calcolo seriale sono:
- velocità di trasmissione;
- limiti alla miniaturizzazione;
- limitazioni economiche;
- le attuali architetture dei computer fanno sempre più affidamento sul parallelismo a livello hardware per migliorare le prestazioni tra cui:
    - unità di esecuzione multiple;
    - istruzioni pipeline;
    - multi-core.

## Unità di misura in HPC
L'unità di misura principale nel calcolo parallelo è il flops che fa parte delle due unità dell'High Performance Computing (HPC) di cui fanno parte:
- *flops:* operazioni di floating point;
- *flops/s:* operazioni di floating point per secondo.

Le dimensioni tipiche sono milioni, miliardi, trilioni...

![[HPC.PNG]]

La macchina attualmente più veloce (pubblica) è di circa 500 Pflops/s di picco.

## Perché abbiamo bisogno di computer potenti?

Storicamente, il calcolo parallelo è stato considerato *"la fascia alta dell'informatica"* ed è stato utilizzato per modellare difficili problemi scientifici e ingegneristici riscontrati nel mondo reale mondo.

Nella conoscenza di questi computer scopriamo il terzo pilastro della scienza.

## Simulazione, il terzo pilastro della scienza
Fino all'avvento delle macchine parallele, i due paradigmi tradizionali della scienza e dell'ingegneria erano:
1. Fai Teoria e Design "sulla carta" (*teoria*)
2. Esegui o crea un esperimento di sistema (*esperimento*)
3. Utilizzare sistemi informatici ad alte prestazioni per simulare fenomeni, basati su leggi fisiche e metodi numerici efficienti (*simulazione*)

Prima del paradigma della simulazione, le limitazioni degli esperimenti erano:
- *troppo difficile* (per esempio, costruire di gallerie del vento);
- *troppo costoso* (per esempio, costruire un aeroplano usa e getta);
- *troppo lento* (per esempio, attendere l'evoluzione del clima, ecc.);
- *troppo pericoloso* (per esempio, armi nucleari, progettazione di nuovi farmaci, ecc.);

Facciamo un esempio di alcune sfide di calcolo (non importante da ricordare). Nel campo scientifico:
- modellazione climatica globale, previsioni meteorologiche;
- modellazione astrofisica;
- nella biologia, analisi del genoma e ripiegamento delle proteine (progettazione di farmaci);
- nella medicina, fisiologia, neuroscienze, modellazione cardiaca.

Nel campo ingegneristico:
- design dell'aeroplano;
- simulazione d'incidenti;
- progettazione di semiconduttori;
- modellazione sismica e strutturale.

Nel campo del business:
- modellazione finanziaria ed economica;
- elaborazione transazioni, servizi web e motori di ricerca.

Nel campo della difesa, le più grandi sfide riguardano le armi nucleari (ASCI) e crittografia.

## Problema e calcolo della modellazione climatica globale
Giusto due parole su questo problema, per capire la complessità di questi tipi di problemi.

Il problema è calcolare la temperatura, la pressione, l'umidità e la velocità del vento partendo dalla latitudine, dalla longitudine, dall'altezza del suolo e dal tempo (ora).

Si utilizza un modello atmosferico che si basa sull'equazioni di Navier-Stokes, equazioni della dinamica dei fluidi: in realtà si va a discretizzare il nostro dominio in tante celle, dove all'interno di ogni cella vado a implementare un approssimazione dell'equazione di Navier-Stokes.

Un pezzo sta modellando il flusso del fluido nell'atmosfera, si risolve l'equazioni di Navier-Stokes e si necessita di circa 100 flop per punto della griglia per calcolare 1 minuto di predizioni.

I requisiti computazionali sono:
- Per abbinare in tempo reale, servono 5 x 1011 flop in 60 secondi = 8 Gflop/s;
- Previsione meteo (7 giorni in 24 ore) → 56 Gflop/s;
- Previsione climatica (50 anni in 30 giorni) → 4,8 Tflop/s;
- Da utilizzare nelle negoziazioni politiche (50 anni in 12 ore) → 288 Tflop/s.

Per raddoppiare la risoluzione della griglia, il calcolo va da 8x a 16x. I modelli all'avanguardia richiedono l'integrazione di atmosfera, oceano, ghiaccio marino, modelli terrestri, più, possibilmente, il ciclo del carbonio, geochimica e altro. I modelli attuali sono più grossolani di questo.

## Supercomputer

I supercomputer sono i dispositivi informatici più *potenti* disponibili in un determinato periodo di tempo: si utilizza il termine *potente* nel senso di velocità di esecuzione, capacità di memoria e precisione.

## GPGPU e CUDA

**GPGPU (General Purpose Programing on GPUs)** è l'utilizzo di unità di elaborazione grafica (GPU) per scopi diversi dai tradizionali processi di creazione tridimensionale.

Le GPU sono processori **multi-core ad alte prestazioni**. Le prime soluzioni programmabili risalgono al 2006, in precedenza erano dedicate solo allo sviluppo di grafica e videogiochi.

Le GPU sono ora considerate processori paralleli con interfacce di programmazione generiche con supporto per linguaggi di programmazione come **CUDA-C** (nVidia) o **Stream** (ATI).

L'**OpenCL** standard è un framework per la scrittura di programmi che vengono eseguiti su **piattaforme eterogenee** costituite da unità di elaborazione centrale (CPU) e unità di elaborazione grafica (GPU).

## Fattore di accelerazione

![[fattoreAcc1.png]]

dove *ts* è il tempo di esecuzione in un singolo processore e *tp* è il tempo di esecuzione in un multiprocessore. *S(n)* aumenta la velocità utilizzando il multiprocessore. L'algoritmo sottostante per l'implementazione parallela potrebbe essere (ed è solitamente) diverso.

Il fattore di accelerazione può anche essere lanciato in termini di passaggi computazionali:

![[fattoreAcc2.png]]

La velocità massima è (di solito) n con n processori (aumento della velocità lineare).

## Legge di Amdahl (velocità massima)

![[leggeAmdahl1.png]]

Il fattore di accelerazione è dato da:

![[leggeAmdahl2.png]]

Questa equazione è conosciuta come legge di Amdahl.

Così, per n → $+\infty$:

![[leggeAmdahl3.png]]

Ad esempio, se la frazione seriale è f = 5 % (molto plausibile!), la velocità massima è 20!

La **frazione seriale** rappresenta quella "frazione" di codice che non può essere parallelizzata (es. I / O, sezioni critiche, ecc.).

## Legge di Moore

Tornando al discorso del non aumento dei gigahertz delle CPU e, quindi, alla nascita dei dual-core (che poi portarono all'implementazione dei multi-core).

Il tutto nasce da *Gordon Moore* che individuò una legge empirica, basata sull'osservazione che la densità del numero dei transistor su un chip semiconduttore (CPU) raddoppiava ogni 18 mesi.

Questa osservazione portò successivamente alla **legge di Moore** secondo cui, quando si fece questa osservazione, i microprocessori sarebbero diventati più piccoli, più densi e più potenti.

![[leggeMoore.PNG]]

Altra cosa, questa legge vale in altri campi, cioè non solo nel campo in cui nasce ma vale anche per i flops (come figura di sopra).
