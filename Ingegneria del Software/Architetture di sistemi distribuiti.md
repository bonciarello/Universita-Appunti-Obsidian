---
aliases: [ASD, Architetture sistemi distribuiti]
tags: [ingegneria-del-software]
---
Sarebbe avere la capacità di dividere i compiti in una maniera furba. Ci sono diversi tipi di applicazioni distribuite:

- sistemi personali non distribuiti e progettati per essere eseguiti su un personal computer o una workstation;
- sistemi embedded che girano su un singolo processore o su un gruppo integrato di processori;
- sistemi distribuiti in cui il software di sistema viene eseguito su un gruppo integrato di processori cooperanti collegati da una rete.

Praticamente tutti i grandi sistemi basati su computer sono ora sistemi distribuiti.

## Sistemi distribuiti

L'elaborazione delle informazioni è distribuita su più computer anziché confinata a un'unica macchina.

In sintesi, sempre più si sta abbandonando un sistema centralizzato, questo perché il volume dei dati sta crescendo ad un livello tale che conviene un lavoro cooperativo, distribuito.

L'[[Ingegneria del Software|ingegneria del software]] distribuito è quindi molto importante per i sistemi informatici aziendali.

Le prime osservazioni che facciamo su un sistema distribuito è quello che *condivide hardware e software*, sono *sistemi aperti* (hardware prodotti da diversi fornitori), *gestisce problemi di concorrenza*, *scalabilità* (avere la capacità di ingrandire le caratteristiche del sistema per affrontare grandi quantità crescenti) e *tolleranza al guasto*.

Abbiamo una serie di svantaggi: se io devo andare a distribuire l'applicazione, avremo *problemi di complessità* (nel programmare applicazione compatibili per più dispositivi), *problemi di [[Sicurezza|sicurezza]]* (dovremmo creare una protezione per ogni sistema), *difficili da gestire* e potremmo avere **problemi imprevedibili**.

Se noi guardiamo al mondo prima del cloud, c'erano due modi per creare un infrastruttura:

- **architetture client-server:** servizi distribuiti che vengono richiamati dai client. I server che forniscono servizi vengono trattati in modo diverso dai client che utilizzano i servizi;
- **architetture a oggetti distribuiti:** nessuna distinzione tra client e server. Qualsiasi oggetto nel sistema può fornire e utilizzare servizi da altri oggetti.

## Architetture multiprocessori

Modello di sistema distribuito più semplice. Anche le semplici macchine multiprocessori, in un qualche modo sono distribuite.

Sistema composto da più processi che possono (ma non devono) essere eseguiti su processori differenti.

La distribuzione del processo al responsabile può essere preordinata o può essere sotto il controllo di un responsabile.

## Architetture client-server

L'applicazione è modellata come un insieme di servizi forniti dai server e un insieme di client che utilizzano questi servizi.

I client conoscono i server, ma i server non devono necessariamente conoscere i client.

Client e server sono processi logici e la mappatura dei processori ai processi non è necessariamente 1:1.

## [[Architettura]] dell'applicazione a più livelli

Anche nell'ambito delle architetture distribuite, possiamo suddividerle in strati:

- **strato della presentazione:** *riguarda la presentazione dei risultati* di un calcolo agli utenti del sistema e la raccolta degli input degli utenti;
- **strato applicativo (web-server):** preoccupato di fornire funzionalità specifiche della applicazione, ad esempio in un sistema bancario, funzioni bancarie come apertura conto, chiusura conto, ecc.;
- **strato della gestione dati ([[Database|database]]):** si occupa della gestione dei [[Database|database]] di sistema.

*Strato applicativo* serve per comunicare con gli altri due strati.

## Thin e fat client

Quando parliamo di applicazione di questo genere, le applicazioni si dividono in base al carico in due modi:

- **modello thin-client:** tutta l'elaborazione dell'applicazione e la gestione dei dati vengono eseguite sul server. Il client è semplicemente responsabile dell'esecuzione del software di presentazione (per esempio app per smartwatch, praticamente si visualizzano solo le informazioni);
- **modello fat-client:** il server è responsabile solo della gestione dei dati. Il software sul client implementa la logica dell'applicazione e le interazioni con l'utente del sistema (per esempio Facebook, Google Docs, YouTube, l'applicazione fa tutto il lavoro).

### Modello thin-client

Utilizzato quando i sistemi legacy vengono migrati alle architetture client-server: il sistema legacy funge da server a sé stante con un'interfaccia grafica implementata su un client.

Uno dei principali svantaggi è che pone un pesante carico di elaborazione sia sul server che sulla rete.

### Modello fat-client

Una maggiore elaborazione viene delegata al client poiché l'elaborazione della applicazione viene eseguita localmente.

Particolarmente adatto per i nuovi sistemi C/S in cui le capacità del sistema client sono note in anticipo.

Più complesso di un modello thin client soprattutto per la gestione. Le nuove versioni della applicazione devono essere installate su tutti i client.

## Architetture a tre livelli

In un'[[Architettura|architettura]] a tre livelli, ciascuno dei livelli dell'[[Architettura|architettura]] della applicazione può essere eseguito su un processore separato.

Consente prestazioni migliori rispetto a un approccio thin client ed è più semplice da gestire rispetto a un approccio fat client.

Un'[[Architettura|architettura]] più scalabile: all'aumentare delle richieste, è possibile aggiungere server aggiuntivi.

## Architetture a oggetti distribuiti

*Non c'è distinzione* nelle architetture di oggetti distribuiti *tra client e server*.

*Ogni entità distribuibile* è un oggetto che *fornisce servizi* ad altri oggetti e riceve servizi da altri oggetti.

*La comunicazione degli oggetti avviene tramite un sistema middleware* chiamato broker di richieste di oggetti.

Tuttavia, le architetture di oggetti distribuiti sono **più complesse da progettare rispetto ai sistemi C/S**.

In sintesi, oggetti diversi in macchine diverse comunicano tra di loro tramite un middleware.

I vantaggi sono:

- consente al progettista del sistema di ritardare le decisioni su dove e come fornire i servizi;
- è un'[[Architettura|architettura]] di sistema molto aperta che consente di aggiungere nuove risorse secondo necessità;
- il sistema è flessibile e scalabile;
- è possibile riconfigurare il sistema in modo dinamico con gli oggetti che migrano attraverso la rete secondo necessità.

## CORBA

CORBA è uno standard internazionale per un *Object Request Broker - middleware* per gestire le comunicazioni tra oggetti distribuiti.

Il middleware per il calcolo distribuito è richiesto a 2 livelli:

- **a livello di comunicazione logica**, il middleware consente agli oggetti su computer diversi di scambiare dati e controllare informazioni;
- **a livello di componente**, il middleware fornisce una base per lo sviluppo di componenti compatibili. Sono stati definiti gli standard dei componenti CORBA.

## Architetture peer-to-peer

I sistemi peer to peer (p2p) sono sistemi decentralizzati in cui i calcoli possono essere eseguiti da qualsiasi nodo della rete. Peer-to-peer ha client e server in un corpo unico, cioè una macchina può essere server ma può essere anche client.

L'intero sistema è progettato per sfruttare la potenza di calcolo e l'archiviazione di un gran numero di computer in rete.

La maggior parte dei sistemi p2p sono stati sistemi personali, ma c'è un crescente utilizzo aziendale di questa tecnologia.

Abbiamo due tipi di [[Architettura|architettura]] peer-to-peer:

- [[Architettura|architettura]] p2p decentralizzata: 
  ![[decentralizzato.png]]

- [[Architettura|architettura]] p2p semi-centralizzata: 
  ![[semicentralizzato.png]]

## Architetture orientate ai servizi

Basato sulla nozione di servizi forniti esternamente (servizi web), **un servizio web è un approccio standard per rendere disponibile e accessibile un componente riutilizzabile attraverso il web**: un servizio di dichiarazione dei redditi potrebbe fornire supporto agli utenti per compilare i moduli fiscali e inviarli alle autorità fiscali.

## Standard dei servizi

I servizi si basano su standard concordati basati su XML, quindi possono essere forniti su qualsiasi piattaforma e scritti in qualsiasi linguaggio di programmazione.

Gli standard chiave sono:

- SOAP - Simple Object Access Protocol;
- WSDL - Web Services Description Language;
- UDDI - Universal Description, Discovery and Integration.

Ultimamente lo standard più utilizzato e conosciuto è il REST.