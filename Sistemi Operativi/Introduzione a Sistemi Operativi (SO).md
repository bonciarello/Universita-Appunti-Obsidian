---
aliases: [SO, OS, Introduzione a Sistemi Operativi]
tags: [sistemi-operativi]
---
## Programma del modulo

Il programma del modulo di SO prevede:
- **Teoria dei sistemi operaativi:** gestione dei file, dei processi e della memoria;
- **Linguaggi di scripting:** Perl;
- **Programmazione multithreading** (Python);
- **Case studies:** Linux, Windows, [[Sistemi Operativi]] Mobili.

## Testi suggeriti e materiale

- Materiale disponibile sul sito del corso;
- *Python 3 concurrency*;
- *Learning Perl*, ed. O'Reilly;
- *Operating Systems*, Deitel & Deitel;
- *Operating Systems Concepts (8 o 9 edizione)*, Addison-Wesley. **(IMPORTANTE)**

## Visione astratta di un SO

![](visioneAstrattaSO.png)

Con sistema di elaborazione si intende una CPU, una memoria RAM, degli input/output e, opzionalmente, una memoria di massa. Esempi di sistemi di elaborazione sono Chroomecast, luci intelligenti, i computer stessi, ecc.

Immaginiamo di avere solo hardware, la prima cosa è installare un SO: immaginiamo non esistono SO, il problema è creare un qualcosa che permetti all'hardware di funzionare. Insomma lo dovevi programmare a mano, da solo.

L'SO è un livello di astrazione rispetto all'hardware (astrae rispetto all'hardware sotto): definisce i servizi che deve offrire l'hardware e, ai giorni d'oggi, solo il costruttore implementa l'SO.

## Visione di dettaglio

![](visioneDettaglio.png)

Nel dettaglio:
- **Users:** si appoggiano e utilizzano le applicazioni, ma non solo umani. Possono comunicare le macchine tra di loro;
- **Applications:** parliamo dei software che si possono installare sullo strato dell'Operating System;
- *Operating System:* troviamo la maggiore interazione tra strato Hardware e strato Application;
- *Hardware:* dove possiamo trovare l'*HAL (Hardware abstraction layer)*, la RAM e la CPU (può essere anche più di una)

L'HAL si può dividere in 4 macrocategorie cioè: *memorie di massa*, *dispositivi di rete*, *dispositivi di input e output*. I nuovi SO forniscono delle piattaforme che unificano proprio queste categorie: esempio l'Oculus contiene alcune delle categorie citate sopra come la parte input/output.

La RAM e la CPU sono collegate ad una parte dell'SO cioè, rispettivamente, *memory manager* e *process scheduler*: quest'ultimo si occupa di turnificare il nostro processore.

Quando si parla di accounting in SO, non si parla di permessi ma di logging (cioè registrare un operazione fatta dall'utente). In Android è tutto multiutente, cioè si crea un account utente per ogni applicazione.

## Cos'è un SO?

Un SO è una raccolta di software predefiniti con lo scopo primario di gestire le risorse di un calcolatore. Nel dettaglio, con un SO si può:
- *generalizzare l'uso delle periferiche tramite un interfaccia comune* con funzioni di libreria comuni per le più grandi categorie di periferiche;
- *gestire la memoria primaria (RAM)* con allocazione e protezione;
- *gestire la memoria secondaria (dischi fissi, ecc.)* con mappatura sul dispositivo fisico, organizzazione e [[Gestione degli errori|gestione degli errori]];
- **gestire i processi e i thread** con scheduling e accesso condiviso alle risorse;
- *gestire gli [[Eventi|eventi]] e l'[[Interfaccia utente|interfaccia utente]]* con cattura e smistamento degli [[Eventi|eventi]] ai processi;
- *gestire i diritti di accesso e la presenza di più utenti* con accesso multiplo, quote e diritti di accesso;
- *gestire la rete* con applicazioni distribuite, condivisione di risorse, socket;

Nella costruzione di un SO, la tecnica della [[Programmazione ad oggetti|programmazione ad oggetti]] è molto più lenta rispetto alle funzioni pure. Si utilizza, quindi, il *linguaggio C* nudo e crudo.

## Funzioni di libreria

Mettiamo a confronti due funzioni, uno per Windows ed uno per Linux, che svolgono lo stesso compito di sistema cioè creare ed aprire un qualsiasi file. Di seguito le implementazioni precedentemente citate:

![](funzioneLibreria.png)

Se cominciamo ad analizzare le due funzioni, ci accorgiamo che l'implementazione per Windows risulta molto più macchinosa perchè il meccanismo su cui gira Windows si basa sui registri, meccanismo completamente diverso rispetto a Linux.

Nella implementazione per Linux, notiamo l'utilizzo di variabili semplici e con la restituzione di un valore di successo o insuccesso. Tra le due implementazioni, la più intuitiva e ottimizzata risulta l'implementazione per Linux.

## Meet Strace e Procmon

Meet Strace e Procmon sono due strumenti interessanti (il primo per Linux, il secondo per Windows), utili per il debugging, cattura e registra tutte le API che vengono chiamate. Un esempio d'interfaccia grafica di Procmon è la seguente:

![](procmon.png)

Attenzione quando si avvia questo programma! È molto pesante per il computer reggere e monitorare questa lista. Per avviare Meet Strace si utilizza nel terminale: *strace find*.

## Storia degli SO

![](storia.png)

**Linux** è diviso in distro (distribuzioni) in base alle funzionalità. Android gira su kernel Linux: questo ramo è importante perchè nel mercato mondiale è abbastanza diffuso per leggerezza e facilità per il programmatore. Gratuito per uso personale ma a livello professionale è a pagamento. Il fratello di Linux è FreeBSD e Solaris: su questo ramo si basano Mac OS e iOS.

**Windows** nasce dall'antico MS-DOS: si divide in due rami che sono Windows 3 e Windows NT. C'è stato anche un esperimento di IBM chiamata OS/2. Interfaccia economica ma meno user-friendly rispetto a Mac OS. Linux come interfaccia grafica è anni luce dietro.
