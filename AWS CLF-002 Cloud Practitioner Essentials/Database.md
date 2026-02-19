---
aliases: [DB, Databasa, Basi di Dati, DBMS]
tags: [aws-clf-002-cloud-practitioner-essentials]
---
## Introduzione ai database

Il **modello di responsabilità condivisa di AWS** raggruppa i servizi in tre categorie in base al concetto di proprietà delle attività amministrative. I servizi possono appartenere a queste categorie:

*   **Completamente gestiti**: AWS gestisce quasi tutte le attività operative come provisioning, dimensionamento, applicazione di patch, backup, ottimizzazione delle prestazioni e patch di [[Sicurezza|sicurezza]]. AWS fornisce anche funzionalità di monitoraggio e metriche integrate. Con questa tipologia di servizi, i clienti sono unicamente responsabili della progettazione delle strutture di dati e della gestione del controllo degli accessi.
*   **Gestiti**: AWS gestisce attività di routine come backup, applicazione di patch e provisioning dell'hardware, mentre i clienti sono responsabili delle decisioni in merito a configurazione del database, ottimizzazione delle query e ottimizzazione delle prestazioni.
*   **Non gestiti**: i clienti sono responsabili di installazione, configurazione, applicazione di patch, attività di manutenzione, [[Sicurezza|sicurezza]] del database, backup, configurazione della disponibilità elevata e ottimizzazione delle prestazioni.

## Servizi di database relazionali

Un **database relazionale** è un sistema che consente di archiviare e collegare i dati tra tabelle diverse utilizzando relazioni logiche basate su chiavi comuni. Questo modello è ideale per applicazioni che gestiscono dati transazionali e strutturati, come ordini, clienti o inventari.

Le aziende che desiderano spostare i propri database nel cloud senza modifiche strutturali possono adottare un approccio chiamato **rehosting** o “lift and shift”: spostano il database esistente su istanze Amazon EC2, mantenendo il pieno controllo su configurazioni come sistema operativo, memoria, storage e CPU e questo approccio consente di sfruttare la scalabilità del cloud AWS, ma senza rinunciare alla familiarità del proprio ambiente. Il processo può essere semplificato grazie a **AWS Database Migration Service (DMS)**, che automatizza la copia e la sincronizzazione dei dati con tempi di inattività ridotti.

**Amazon RDS (Relational Database Service)** è un servizio gestito che permette di eseguire e amministrare database relazionali nel cloud AWS in modo semplice e scalabile. Supporta diversi motori, tra cui [[MySQL]], PostgreSQL, MariaDB, Oracle e SQL Server. Il vantaggio principale è che AWS si occupa delle attività operative più complesse, come patch di [[Sicurezza|sicurezza]], backup automatici, replica, failover e alta disponibilità. Questo permette alle aziende di concentrarsi sulle proprie applicazioni e sui dati, anziché sulla manutenzione del sistema.

**Amazon Aurora** è un motore di database relazionale completamente gestito, compatibile con [[MySQL]] e PostgreSQL, progettato per offrire prestazioni fino a cinque volte superiori rispetto ai database standard. A differenza di un’istanza RDS tradizionale, Aurora è basato su un'[[Architettura|architettura]] distribuita, che consente di creare fino a 15 repliche in diverse zone di disponibilità per garantire alta disponibilità e tolleranza ai guasti. Supporta inoltre backup continui con ripristino point-in-time fino a 35 giorni, offrendo un livello di resilienza e automazione superiore.

Aurora combina la velocità e affidabilità dei database commerciali con la flessibilità e il costo contenuto delle soluzioni open source. È altamente scalabile, automatizza il failover e la replica dei dati e garantisce backup costanti senza interruzioni. Grazie a queste caratteristiche, Aurora è particolarmente adatta per applicazioni mission-critical, come piattaforme di e-commerce, sistemi bancari o gestionali, dove la disponibilità continua e le prestazioni elevate sono essenziali.

## Servizi di database NoSQL

Un **database NoSQL**, detto anche non relazionale, organizza i dati in modo diverso rispetto ai database relazionali. Invece di utilizzare tabelle con righe e colonne collegate da relazioni, i database NoSQL memorizzano i dati tramite coppie chiave-valore. Ogni chiave identifica in modo univoco un insieme di attributi, che possono variare da un elemento all’altro. Questo rende i database NoSQL molto flessibili, perché consentono di aggiungere o rimuovere attributi in qualsiasi momento senza modificare la struttura generale del database.

**Amazon DynamoDB** è un servizio di database NoSQL completamente gestito che utilizza una struttura basata su documenti e coppie chiave-valore. È progettato per offrire prestazioni elevate e prevedibili con un’elevata scalabilità. DynamoDB è una soluzione ideale per applicazioni che necessitano di risposte rapide, alta disponibilità e capacità di crescere automaticamente insieme ai volumi di dati, senza richiedere interventi di gestione manuale da parte dell’utente.

Amazon DynamoDB offre **scalabilità automatica con capacità assegnata**: questo significa che il servizio aumenta o riduce automaticamente le risorse in base al carico effettivo. Gli utenti possono impostare gli obiettivi di throughput desiderati, e DynamoDB provvede ad adattare la capacità per mantenere prestazioni costanti. Non ci sono limiti pratici alla dimensione delle tabelle o alla quantità di dati archiviati, quindi il servizio può crescere insieme alle esigenze dell’applicazione, garantendo continuità e affidabilità.

Amazon DynamoDB è progettato per offrire **tempi di risposta inferiori a 10 millisecondi**, anche in presenza di grandi volumi di dati o richieste simultanee. Per ottenere questo risultato, il servizio distribuisce automaticamente i dati su più server e unità SSD, assicurando velocità costante e assenza di colli di bottiglia. Questa [[Architettura|architettura]] rende DynamoDB perfetto per applicazioni che richiedono accessi ai dati in tempo reale.

Amazon DynamoDB assicura una **disponibilità del 99,999%**, replicando automaticamente i dati in tre strutture distinte all’interno di ogni regione AWS. In aggiunta, mantiene copie ridondanti in altre regioni per garantire tolleranza ai guasti e protezione contro la perdita dei dati. In caso di problemi hardware o disastri locali, le repliche consentono di mantenere il servizio operativo senza interruzioni.

Amazon DynamoDB integra **funzionalità complete di crittografia**, sia per i dati in transito (mentre viaggiano tra client e server) che per quelli inattivi (memorizzati nei dischi). Tutti i dati vengono crittografati automaticamente prima di essere salvati nel sistema. Gli utenti possono anche scegliere tra diversi tipi di chiavi di crittografia, inclusi quelli gestiti da AWS o chiavi proprie, per avere un controllo più preciso sulla [[Sicurezza|sicurezza]] dei dati sensibili.

## Servizi di caching in memoria

I database relazionali, come Amazon RDS, possono subire rallentamenti quando devono gestire grandi volumi di traffico in lettura o query complesse su set di dati molto estesi. Ad esempio, un sito di e-commerce con migliaia di utenti che visualizzano gli stessi prodotti genera numerose richieste ripetute per gli stessi dati. Questo tipo di carico intensivo può saturare le risorse del database, aumentando la latenza e riducendo le prestazioni complessive dell’applicazione.

Il **caching** è un livello di archiviazione ad alta velocità che archivia temporaneamente i dati a cui si accede di frequente nella memoria principale di un computer o RAM. In questo modo, le applicazioni possono recuperare rapidamente i dati senza dover eseguire ogni volta query complesse sul database. Il risultato è un accesso quasi istantaneo alle informazioni, tempi di risposta molto più brevi e minore pressione sul sistema back-end.

Gli strumenti più diffusi per il caching dei dati includono **Redis OSS, Valkey e Memcached**. Queste tecnologie consentono di memorizzare i dati direttamente in memoria, offrendo tempi di accesso estremamente rapidi. Su AWS, la soluzione gestita per utilizzare questi motori è **Amazon ElastiCache**, che integra e semplifica l’uso del caching senza dover gestire manualmente l’infrastruttura.

**Amazon ElastiCache** è un servizio completamente gestito che fornisce una cache in memoria ad alte prestazioni compatibile con Redis OSS e Memcached. Riduce la latenza delle applicazioni offrendo letture di dati in microsecondi e alleggerendo il carico sui database back-end. ElastiCache memorizza i dati temporaneamente nella memoria del sistema e li restituisce rapidamente alle applicazioni. Inoltre, il servizio è flessibile: si adatta automaticamente alla domanda e offre anche una modalità serverless, che si espande o riduce dinamicamente in base al traffico.

Amazon ElastiCache migliora le prestazioni delle applicazioni riducendo drasticamente i tempi di accesso ai dati e la latenza. Ma non solo: permette anche di ottimizzare i costi, poiché il caching riduce il numero di query dirette al database, consentendo di utilizzare istanze più piccole ed economiche. Inoltre, essendo un servizio completamente gestito da AWS, elimina la complessità operativa legata all’installazione, alla manutenzione e al monitoraggio dell’infrastruttura.

In un’[[Architettura|architettura]] classica, **Amazon EC2** ospita i server applicativi, **Amazon RDS** funge da database relazionale, e **Amazon ElastiCache** si posiziona tra i due come livello di cache. Quando un utente invia una richiesta:

1.  L’applicazione controlla prima se i dati sono disponibili in ElastiCache.
2.  Se sì, li restituisce immediatamente all’utente (cache hit).
3.  Se no, li recupera da RDS, li memorizza nella cache e poi li invia all’utente (cache miss).

Questo approccio riduce il numero di query verso RDS e garantisce prestazioni costanti e tempi di risposta molto bassi.

Amazon ElastiCache offre latenza in microsecondi, riduce il carico sui database relazionali e scala automaticamente in base alle esigenze. Inoltre, migliora la stabilità complessiva dell’applicazione, prevenendo colli di bottiglia dovuti a picchi di traffico. Combinando prestazioni elevate, scalabilità e semplicità operativa, rappresenta una soluzione ideale per chi desidera ottimizzare l’esperienza utente e ridurre i costi di infrastruttura.

## Servizi di database aggiuntivi

**Amazon DocumentDB** è un database completamente gestito compatibile con MongoDB, pensato per gestire dati semistrutturati come documenti JSON. È ideale per applicazioni che richiedono schemi flessibili e aggiornamenti frequenti, come sistemi di gestione dei contenuti o cataloghi di prodotti. Offre compatibilità totale con gli strumenti MongoDB, garantendo alte prestazioni e scalabilità automatica dello storage, oltre a funzioni di backup e [[Sicurezza|sicurezza]] integrate. Il suo principale limite è il costo, generalmente più elevato rispetto a soluzioni autogestite, e una minore flessibilità nella personalizzazione del motore rispetto a MongoDB nativo.

**AWS Backup** è un servizio che centralizza e automatizza la gestione dei backup di risorse AWS e on-premises, semplificando la protezione dei dati e riducendo il carico operativo. È usato per creare policy di backup coerenti, supportare il ripristino di emergenza e rispettare requisiti di conformità. I principali vantaggi sono la gestione unificata e la possibilità di replicare automaticamente i backup in più regioni, garantendo [[Sicurezza|sicurezza]] e continuità operativa. Tuttavia, i costi possono aumentare con grandi volumi di dati e il servizio offre meno controllo rispetto a soluzioni di backup personalizzate.

**Amazon Neptune** è un database a grafo completamente gestito, progettato per gestire dati fortemente connessi, come quelli dei social network o dei sistemi di raccomandazione. Consente di analizzare rapidamente relazioni complesse e di eseguire query a bassa latenza su grandi quantità di dati interconnessi. È apprezzato per le sue alte prestazioni, la scalabilità automatica e l’affidabilità grazie a backup e failover gestiti. Tuttavia, può risultare costoso e più complesso da utilizzare rispetto ai database tradizionali, poiché richiede familiarità con i modelli e i linguaggi a grafo.