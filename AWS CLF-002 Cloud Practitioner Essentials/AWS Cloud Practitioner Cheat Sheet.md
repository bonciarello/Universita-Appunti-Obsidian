---
aliases: [ACPCS]
tags: [aws-clf-002-cloud-practitioner-essentials]
---
## [[Introduzione ad Amazon Web Services]]
-   **Cloud Computing**: Distribuzione di risorse IT on-demand tramite Internet.
-   **Modelli di distribuzione**:
    -   **Cloud-based**: Applicazioni interamente nel cloud.
    -   **On-premises**: Gestione locale delle risorse.
    -   **Ibrida**: Connessione tra cloud e infrastruttura on-premises.
-   **Vantaggi**: Riduzione costi (spesa variabile, no investimenti iniziali), scalabilità, flessibilità, economie di scala, velocità di innovazione, presenza globale.

## [[Infrastruttura Globale e Affidabilità]]
-   **Regioni**: Aree geografiche isolate per alta disponibilità e bassa latenza. Scelta basata su: Governance, Latenza, Disponibilità servizi, Costo.
-   **Availability Zone (AZ)**: Data center fisicamente separati all'interno di una regione, con alimentazione e rete ridondanti. Si raccomanda di distribuire applicazioni su almeno due AZ.
-   **Edge Location (Punti di presenza)**: Punti distribuiti globalmente per avvicinare contenuti agli utenti finali con bassa latenza.
    -   **Amazon CloudFront**: CDN che distribuisce contenuti tramite Edge Location.
    -   **Amazon Route 53**: Servizio DNS che indirizza gli utenti verso la risorsa più vicina/performante.
-   **AWS Outposts**: Infrastruttura AWS fisica all'interno del data center del cliente per esigenze on-premises.

### Provisioning risorse AWS
-   **API (Application Programming Interface)**: Ogni azione in AWS corrisponde a una chiamata API.
-   **Interazione con AWS**:
    -   **AWS Management Console**: Interfaccia grafica browser-based.
    -   **AWS Command Line Interface (CLI)**: Gestione risorse da terminale, automazione script.
    -   **AWS Software Development Kits (SDK)**: Librerie per integrare servizi AWS nelle applicazioni con diversi linguaggi.
-   **Strumenti di gestione e automazione**:
    -   **AWS Elastic Beanstalk**: Distribuisce applicazioni gestendo automaticamente l'infrastruttura sottostante (EC2, load balancer, scaling).
    -   **AWS CloudFormation**: Infrastructure as Code (IaC) per definire l'infrastruttura tramite template (JSON/YAML).
    -   **Amazon Lightsail**: Servizio semplificato con VPS, archiviazione e rete.
    -   **Infrastructure as Code (IaC)**: Crea ambienti identici e automatizza il provisioning.

## [[Calcolo nel Cloud]]
-   **Tipi di istanze EC2**: Offrono diverse combinazioni di CPU, memoria, archiviazione, rete.
    -   Generico, ottimizzato per calcolo, ottimizzato per memoria, calcolo accelerato (GPU), ottimizzato per archiviazione.
-   **Prezzi EC2**:
    -   **On-demand**: Pagamento per l'uso effettivo, senza impegni.
    -   **Saving Plan**: Sconti per impegno di utilizzo costante (1 o 3 anni).
    -   **Istanze riservate**: Sconti per acquisto anticipato (1 o 3 anni). Standard (fissi) o modificabili (flessibili).
    -   **Istanze Spot**: Capacità EC2 inutilizzata con sconti fino al 90%, ideali per carichi flessibili (possono essere interrotte con 2 minuti di preavviso).
    -   **Host dedicati**: Intero server fisico per uso esclusivo (licenze, [[Sicurezza|sicurezza]]).
    -   **Istanze dedicate**: Istanze su hardware dedicato all'account.
-   **Dimensionamento EC2**:
    -   **Amazon EC2 Auto Scaling**: Aggiunge/rimuove istanze automaticamente in base alla domanda.
        -   Scalabilità dinamica (risposta alla domanda), scalabilità predittiva (basata su previsioni).
    -   **Dimensionare verticalmente**: Aggiungere più potenza a una macchina esistente.
    -   **Dimensionare orizzontalmente**: Aggiungere nuove macchine e distribuire il carico.
    -   **Parametri Auto Scaling**: Capacità minima, desiderata, massima.
-   **Elastic Load Balancing (ELB)**: Bilancia il carico del traffico tra le istanze EC2. Scalabile e ad alta disponibilità.

### Messaggistica e accodamento (architetture disaccoppiate)
-   **Amazon Simple Queue Service (SQS)**: Servizio di coda di messaggi gestito per comunicazioni asincrone tra applicazioni.
-   **Amazon Simple Notification Service (SNS)**: Servizio di messaggistica publish-subscribe per inviare messaggi a più sottoscrittori (altri servizi, email, SMS).

### Servizi di calcolo aggiuntivi
-   **AWS Lambda**: Servizio "serverless" per eseguire codice in risposta a [[Eventi|eventi]], senza gestire server. Ideale per elaborazioni brevi.
-   **Container (ECS/EKS)**:
    -   **Amazon ECS (Elastic Container Service)**: Servizio di orchestrazione di container con strumenti AWS.
    -   **Amazon EKS (Elastic Kubernetes Service)**: Servizio di orchestrazione Kubernetes gestito.
    -   **AWS Fargate**: Piattaforma serverless per container (ECS o EKS), non richiede la gestione delle istanze EC2 sottostanti.
    -   **Amazon ECR**: Repository per immagini Docker.
-   **AWS Batch**: Gestisce carichi di lavoro in batch su larga scala.

## [[Archiviazione e Database]]

### Archiviazione dell'istanza e Amazon EBS
-   **Archiviazione a livello di blocco**: Memorizza i dati in blocchi (efficiente per DB/[[File System|file system]]).
-   **Instance Store**: Archiviazione temporanea, volatile, fisicamente collegata all'istanza EC2. Dati persi all'arresto/terminazione.
-   **Amazon EBS (Elastic Block Store)**: Archiviazione persistente per EC2 (volumi virtuali). I dati rimangono anche se l'istanza viene fermata/riavviata.
    -   **Snapshot di EBS**: Backup incrementali dei volumi EBS, per ripristino.
    -   **Amazon Data Lifecycle Manager (DLM)**: Automatizza la gestione degli snapshot EBS.

### Amazon Simple Storage Service (Amazon S3)
-   **Amazon S3 (Simple Storage Service)**: Servizio di archiviazione di oggetti scalabile, duraturo e altamente disponibile.
    -   **Bucket**: Contenitori per gli oggetti.
    -   **Oggetti**: Dati veri e propri più metadati (fino a 5 TB).
    -   **Versioning**: Mantiene più versioni di un oggetto per protezione da cancellazioni/sovrascritture accidentali.
-   **Classi di archiviazione S3**:
    -   **S3 Standard**: Accesso frequente, elevata disponibilità (3+ AZ).
    -   **S3 Standard-IA (Infrequent Access)**: Accesso raro, facile accesso.
    -   **S3 One Zone-IA**: Accesso raro, una sola AZ, costi più bassi.
    -   **S3 Intelligent-Tiering**: Sposta automaticamente gli oggetti tra livelli in base all'utilizzo.
    -   **S3 Glacier Instant Retrieval**: Archiviazione economica, recupero immediato.
    -   **S3 Glacier Flexible Retrieval**: Archiviazione a lungo termine, recupero in minuti/ore.
    -   **S3 Glacier Deep Archive**: Più economica, accesso rarissimo (1-2 volte/anno).
    -   **S3 Outposts**: Archiviazione locale su infrastruttura AWS Outposts.
-   **Lifecycle policy**: Automatizzano lo spostamento dei dati tra le classi di archiviazione per ottimizzare i costi.
-   **Durabilità**: 11 nove (99.999999999%), replicato in più AZ.
-   **Hosting statico di siti web**: S3 può ospitare siti [[HTML]], CSS, JS senza server backend.

### Amazon Elastic [[File System]] (Amazon EFS)
-   **Amazon EFS (Elastic [[File System]])**: [[File System|File system]] gestito, scalabile, condiviso tra più istanze EC2 (come un [[File System|file system]] di rete). Autoscalante.
-   **Differenza EFS vs EBS**: EFS è a livello di file, per più istanze, anche in diverse AZ/regioni. EBS è a livello di blocco, per singola istanza.
-   **Classi di archiviazione EFS**:
    -   **EFS Standard / Standard-IA**: Elevata disponibilità/durabilità (più AZ).
    -   **EFS One Zone / One Zone-IA**: Una sola AZ, costi più bassi, meno ridondanza.
    -   **EFS Archive**: Più economica, per dati a cui si accede raramente.
-   **Lifecycle policy EFS**: Spostano file tra classi IA/Archive (es. dopo 30 o 90 giorni di inutilizzo).
-   **Amazon FSx**: [[File System|File system]] completamente gestiti per specifici carichi di lavoro (es. Windows File Server, Lustre).

### Gateway di archiviazione AWS e AWS Elastic Disaster Recovery
-   **AWS Storage Gateway**: Servizio di archiviazione ibrida che collega l'on-premises al cloud AWS.
    -   **File Gateway**: Archivia file in S3 con compatibilità NFS/SMB.
    -   **Volume Gateway**: Volumi a blocchi locali backuppati come snapshot EBS nel cloud.
    -   **Tape Gateway**: Emula un sistema di nastri virtuali per backup nel cloud.
-   **AWS Elastic Disaster Recovery (DRS)**: Replica continua carichi di lavoro critici (fisici/virtuali) verso AWS per minimizzare tempi di inattività e perdita dati.

## [[Database]]

### Introduzione ai [[Database|database]]
-   **Modello di responsabilità condivisa AWS**:
    -   **Completamente gestiti**: AWS gestisce quasi tutto (provisioning, scaling, patching, backup). Cliente gestisce design dati e accessi.
    -   **Gestiti**: AWS gestisce routine (backup, patching). Cliente gestisce configurazione, query, ottimizzazione.
    -   **Non gestiti**: Cliente gestisce tutto (installazione, configurazione, patch, [[Sicurezza|sicurezza]], backup, HA, ottimizzazione).

### Servizi di [[Database|database]] relazionali
-   **[[Database]] relazionale**: Archivia e collega dati tra tabelle diverse (transazionali, strutturati).
-   **Rehosting (lift and shift)**: Migrare [[Database|database]] esistenti su istanze EC2 senza modifiche strutturali.
-   **Amazon RDS (Relational [[Database]] Service)**: Servizio gestito per [[Database|database]] relazionali ([[MySQL]], PostgreSQL, MariaDB, Oracle, SQL Server). AWS gestisce patch, backup, replica, failover.
-   **Amazon Aurora**: Motore relazionale completamente gestito, compatibile con [[MySQL]]/PostgreSQL, 5x più veloce, [[Architettura|architettura]] distribuita, fino a 15 repliche, backup continui.

### Servizi di [[Database|database]] NoSQL
-   **[[Database]] NoSQL**: Organizza i dati in modo non relazionale (es. chiave-valore, documenti). Flessibile per dati non strutturati/semi-strutturati.
-   **Amazon DynamoDB**: Servizio [[Database|database]] NoSQL completamente gestito (documenti, chiave-valore). Alte prestazioni, scalabilità automatica, tempi di risposta <10ms, disponibilità 99.999%, crittografia completa.

### Servizi di caching in memoria
-   **Caching**: Archiviazione ad alta velocità per dati a cui si accede di frequente (RAM). Riduce latenza, alleggerisce DB.
-   **Amazon ElastiCache**: Servizio gestito di cache in memoria (Redis OSS, Memcached). Latenza in microsecondi, scala automaticamente.

### Servizi di [[Database|database]] aggiuntivi
-   **Amazon DocumentDB**: [[Database]] completamente gestito compatibile con MongoDB (documenti JSON).
-   **AWS Backup**: Centralizza e automatizza la gestione dei backup AWS e on-premises.
-   **Amazon Neptune**: [[Database]] a grafo completamente gestito per dati fortemente connessi.

## [[IA-Machine Learning e Analisi dei Dati]]

### Introduzione all'intelligenza artificiale e al [[Machine Learning|machine learning]]
-   **Intelligenza artificiale (AI)**: Sistemi informatici che svolgono compiti simili a quelli umani.
-   **[[Machine Learning]] (ML)**: Sottocampo dell'AI per addestrare macchine a trovare pattern e fare previsioni (modello di ML).
-   **Stack AI/ML AWS**:
    -   **Servizi di intelligenza artificiale (AI Services)**: Modelli predefiniti, già addestrati (es. Comprehend, Polly, Transcribe, Translate, Rekognition, Textract, Kendra, Lex, Personalize).
    -   **Servizi di [[Machine Learning|machine learning]] (ML Services)**: Approccio personalizzato con **Amazon SageMaker AI** (crea, addestra, distribuisce modelli personalizzati).
    -   **Framework e infrastruttura di [[Machine Learning|machine learning]]**: Controllo massimo con framework (TensorFlow, PyTorch) su infrastruttura AWS (EC2, EMR, ECS).

### Introduzione all'IA generativa su AWS
-   **[[Deep Learning]]**: Sottocampo del ML che usa [[Reti|reti]] neurali artificiali per problemi complessi (immagini, linguaggio).
-   **Intelligenza Artificiale Generativa (IA Generativa)**: Crea nuovi contenuti (testo, immagini) basata su **Modelli di Fondazione (FM)**.
-   **Modelli di Fondazione (FM)**: Modelli ML preaddestrati su grandi quantità di dati, adattabili a molteplici compiti. **Large Language Models (LLM)** sono un tipo di FM.
-   **Servizi AWS per IA Generativa**:
    -   **Amazon SageMaker JumpStart**: Hub ML con FM e soluzioni predefinite per distribuzione rapida e personalizzazione.
    -   **Amazon Bedrock**: Servizio gestito con accesso a FM di Amazon e terze parti. Permette adattamento privato e sicuro con propri dati via API.
    -   **Amazon Q**: Assistente intelligente personalizzabile (Q Business, Q Developer) integrato con repository aziendali.

### Introduzione all'analisi dei dati
-   **Dati**: Base per IA/ML (puliti, accurati, accessibili).
-   **Analisi tradizionale dei dati**: Esamina dati passati per spiegare [[Eventi|eventi]] e trovare trend.
-   **Data Lake**: Archivio centralizzato per tutti i tipi di dati (strutturati, semi-strutturati, non strutturati). **Amazon S3** è spesso usato come Data Lake.
-   **Processi ETL/ELT**:
    -   **ETL (Extract, Transform, Load)**: Estrai, Trasforma, Carica (nel data warehouse).
    -   **ELT (Extract, Load, Transform)**: Estrai, Carica, Trasforma (nel sistema di destinazione).
-   **Zero-ETL**: Elimina passaggi complessi di ETL, dati già in formato compatibile.
-   **Pipeline di dati**: Flussi automatizzati per acquisire, elaborare e distribuire dati.

### Pipeline di dati su AWS
-   **Fasi della pipeline**: Acquisizione, Archiviazione, Trasformazione e Catalogazione, Analisi e Visualizzazione.
-   **Acquisizione dati**:
    -   **In tempo reale (streaming)**: **Amazon Kinesis** (raccoglie, elabora, analizza flussi dati in tempo reale).
    -   **In batch**: **Amazon Data Firehose** (soluzione ETL gestita per acquisizione dati quasi in tempo reale verso S3, Redshift, OpenSearch).
-   **Catalogazione dati**:
    -   **AWS Glue Data Catalog**: Repository centralizzato per metadati dei dati (inventario, descrizione).
-   **Pulizia e Trasformazione dati**:
    -   **AWS Glue**: Servizio ETL completamente gestito (interfacce visive, pianificazione job, supporta vari formati).
    -   **Amazon EMR (Elastic MapReduce)**: Piattaforma per big data su larga scala (Spark, Hadoop, Hive).
-   **Analisi e Query**:
    -   **Amazon Athena**: Servizio serverless per query SQL su dati in S3, senza spostarli.
    -   **Amazon Redshift**: Data warehouse completamente gestito per query complesse su grandi set di dati.
-   **Visualizzazione e analisi interattiva**:
    -   **Amazon QuickSight**: Business intelligence unificata e dashboard interattive (anche con Amazon Q).
    -   **Amazon OpenSearch**: Ricerca, monitoraggio e analisi in tempo reale di log e metriche.

## [[Reti]]

### Connettività ad AWS
-   **Amazon VPC (Virtual Private Cloud)**: Rete privata virtuale in AWS, con controllo su indirizzi IP e risorse.
-   **Sottoreti**: Suddivisione della VPC, per isolare risorse pubbliche/private.
-   **Gateway Internet**: Connessione tra VPC e Internet, per risorse pubbliche.
-   **Virtual Private Gateway**: Collega VPC a rete privata esterna (es. data center) tramite VPN.
-   **AWS Direct Connect**: Connessione privata dedicata tra data center e VPC (maggiore velocità, minore latenza, più [[Sicurezza|sicurezza]]).
-   **AWS PrivateLink**: Connette VPC privatamente a servizi AWS come se fossero nel tuo VPC.
-   **VPN sito-a-sito AWS**: Connessione sicura tra data center/filiali e cloud AWS.
-   **VPN Client di AWS**: Servizio VPN per personale remoto, autenticazione avanzata, accesso elastico.

### Sottoreti e liste di controllo accessi di rete
-   **Network ACL (Access Control List)**: Firewall virtuale a livello di sottorete. Stateless (ogni pacchetto valutato singolarmente).
-   **Security Group**: Firewall virtuale a livello di istanza (es. EC2). Stateful (ricorda decisioni, autorizza risposte).

### [[Reti]] globali
-   **DNS (Domain Name System)**: Traduce nomi di dominio (es. www.miosito.it) in indirizzi IP.
-   **Amazon Route 53**: Servizio DNS di AWS. Supporta:
    -   Routing basato sulla latenza.
    -   Geolocalizzazione DNS.
    -   Geoproximity.
    -   Weighted round robin.
    -   Registrazione domini.

## [[Sicurezza]]

### Prevenzione di accessi non autorizzati
-   **Utente root**: Primo utente, controllo totale. Usare solo per compiti critici, proteggere con MFA.
-   **AWS Identity and Access Management (IAM)**: Gestisce utenti, gruppi, ruoli e autorizzazioni.
    -   **Policy IAM**: Documenti JSON che definiscono permessi (Effect, Action, Resource).
    -   **Gruppi IAM**: Semplificano gestione permessi per più utenti.
    -   **Ruoli IAM**: Identità temporanee con permessi specifici, senza credenziali permanenti.
    -   **Federazione delle identità**: Accedere ad AWS con credenziali aziendali.
    -   **Centro identità IAM (IAM Identity Center)**: SSO centralizzato per account/applicazioni AWS.
-   **AWS Secrets Manager**: Gestisce, ruota e recupera credenziali del [[Database|database]], chiavi API e altri segreti.
-   **AWS Systems Manager**: Visualizzazione centralizzata dei nodi, automazione modifiche, gestione patch.

### Protezione di [[Reti|reti]] e applicazioni
-   **Attacco DoS (Denial of Service)**: Sovraccarica applicazione web.
-   **Attacco DDoS (Distributed Denial of Service)**: Versione distribuita da botnet.
-   **Protezioni AWS**: [[Architettura]] globale resistente, gruppi di [[Sicurezza|sicurezza]], ELB distribuisce traffico.
-   **AWS Shield**: Protegge da attacchi DDoS.
    -   **Standard**: Incluso gratuitamente, difende da attacchi comuni.
    -   **Advanced**: A pagamento, diagnostica dettagliata, mitigazione avanzata, supporto tecnico.
-   **AWS WAF (Web Application Firewall)**: Protegge applicazioni web monitorando richieste HTTP/HTTPS con Web ACL (regole di blocco).

### Protezione dei dati
-   **Crittografia**: Essenziale per riservatezza dati.
    -   **Dati inattivi**: Dati fermi ([[Database|database]], S3, EBS).
    -   **Dati in transito**: Dati tra sistemi (SSL/TLS).
-   **Servizi con crittografia integrata**: Amazon S3, EBS, DynamoDB.
-   **Servizio AWS di [[Gestione delle Chiavi|gestione delle chiavi]] (KMS)**: Crea, gestisce, controlla chiavi crittografiche.
-   **Amazon Macie**: ML e analisi per individuare e proteggere dati sensibili in S3 (PII).
-   **AWS Certificate Manager (ACM)**: Gestisce certificati SSL/TLS per crittografia dati in transito.

### Rilevamento e risposta agli incidenti di [[Sicurezza|sicurezza]]
-   **Amazon Inspector**: Valutazioni di [[Sicurezza|sicurezza]] automatiche (EC2, container, Lambda), identifica vulnerabilità.
-   **Amazon GuardDuty**: Rilevamento intelligente delle minacce monitorando flussi metadati/attività di rete.
-   **Amazon Detective**: Indaga su incidenti di [[Sicurezza|sicurezza]], mostra relazioni tra risorse/utenti.
-   **Centrale di [[Sicurezza|sicurezza]] AWS (AWS Security Hub)**: Aggrega risultati di [[Sicurezza|sicurezza]] da vari servizi, visione completa dello stato di [[Sicurezza|sicurezza]].

## [[Monitoraggio, Conformità e Governance nel Cloud AWS]]

### Importanza del monitoraggio
-   **Monitoraggio**: Osservare e analizzare attività del sistema, traffico, [[Eventi|eventi]] di [[Sicurezza|sicurezza]].
-   **Amazon CloudWatch**: Servizio di monitoraggio in tempo reale su risorse AWS e applicazioni.
    -   **Metriche CloudWatch**: Raccoglie dati sulle prestazioni.
    -   **Allarmi CloudWatch**: Notifiche/azioni automatiche su soglie metriche.
    -   **Dashboard CloudWatch**: [[Viste]] personalizzate.
    -   **Log CloudWatch**: Centralizza i log per analisi.
-   **AWS CloudTrail**: Traccia attività utenti e utilizzo API (chi, cosa, quando, su quali risorse).
    -   **[[Eventi]] CloudTrail**: Registrano azioni (API, console). Cronologia 90 giorni gratuita.
    -   **Log CloudTrail**: File archiviati in S3 per conformità e audit.
    -   **CloudTrail Insights**: Analizza pattern di comportamento, segnala anomalie.

### Conformità
-   **Conformità**: Rispetto di normative, standard e policy interne.
-   **AWS Artifact**: Accesso on-demand a report AWS di [[Sicurezza|sicurezza]] e conformità (accordi, report ISO, SOC, PCI, HIPAA).
-   **Verifica conformità risorse AWS**:
    -   **AWS Config**: Valuta, verifica e monitora continuamente configurazioni risorse AWS rispetto a regole.
    -   **Gestione audit AWS**: Verifica automaticamente utilizzo risorse per valutazione rischio e conformità, raccoglie prove.

### AWS Organizations
-   **AWS Organizations**: Gestisce centralmente più account AWS.
    -   **Vantaggi**: Scalabilità rapida, gestione policy centralizzata, ottimizzazione costi (fatturazione consolidata), controllo [[Sicurezza|sicurezza]].
    -   **Concetti chiave**: Organizzazione, Elemento root, Unità organizzativa (UO), Account membro, Service Control Policy (SCP) per limitare/consentire azioni.

### Governance
-   **AWS Control Tower**: Applica e gestisce regole di governance per [[Sicurezza|sicurezza]], operazioni e conformità su larga scala.
-   **Catalogo dei servizi AWS**: Crea, condivide e organizza risorse AWS approvate in un catalogo personalizzato.
-   **Strumento AWS di gestione delle licenze**: Semplifica gestione/monitoraggio licenze software, supporta BYOL.
-   **AWS Health Dashboard**: Visualizza informazioni sull'integrità specifiche dell'account e aggiornamenti [[Eventi|eventi]].
-   **AWS Trusted Advisor**: Valuta ambiente AWS ([[Sicurezza|sicurezza]], prestazioni, costi, resilienza) rispetto a best practice, fornisce raccomandazioni.
-   **Sistema di analisi degli accessi IAM**: Analizza permessi, individua eccessi di privilegi, suggerisce ottimizzazioni.

## [[Migrazione al Cloud AWS]]

### Introduzione alla migrazione
-   **Fasi di migrazione**:
    -   **Valutazione**: Comprendere punto di partenza, idoneità cloud, business case.
    -   **Mobilitazione**: Definire piano di migrazione, risolvere idoneità, framework operativo, strategie (rehosting, refactoring, replatforming).
    -   **Migrazione e modernizzazione**: Migrare e testare applicazioni, ottimizzare per il cloud.

### Framework di adozione del cloud AWS (AWS CAF)
-   Linee guida, strumenti e best practice per l'adozione del cloud.
-   **Sei prospettive**: Azienda, Persone, Governance, Piattaforma, [[Sicurezza]], Operazioni.

### Sette strategie di migrazione
-   **Rehosting (Lift and Shift)**: Spostare senza modifiche significative.
-   **Rilocazione**: Migrare VM/container on-premises direttamente in AWS.
-   **Ridefinizione della piattaforma (Lift, Tinker and Shift)**: Piccole ottimizzazioni durante la migrazione (es. RDS).
-   **Rifattorizzazione / riprogettazione architetturale**: Modificare codice/struttura per migliorare funzionalità/scalabilità.
-   **Riacquisto (Drop and Shop)**: Sostituire con nuove soluzioni cloud/SaaS.
-   **Mantenimento**: Lasciare applicazioni nell'ambiente originale.
-   **Ritiro**: Disattivare applicazioni non più utilizzate.

### Servizi e strumenti per la migrazione
-   **Migration Evaluator**: Analizza ambiente IT on-premises, stima costi/benefici del cloud.
-   **Servizio di individuazione delle applicazioni**: Raccoglie informazioni su inventario e connessioni server on-premises.
-   **Hub di migrazione**: Centro di controllo unificato per pianificazione ed esecuzione migrazione.
-   **Application Migration Service**: Migra e migliora applicazioni on-premises/cloud, minimizza downtime.

### Migrazioni dei [[Database|database]]
-   **AWS [[Database]] Migration Service (DMS)**: Trasferisce [[Database|database]]/data warehouse in modo rapido, sicuro, con tempi di inattività minimi. Supporta migrazioni omogenee ed eterogenee.
-   **AWS Schema Conversion Tool (SCT)**: Automatizza conversione schemi [[Database|database]] per migrazioni eterogenee.

### Trasferimento di dati
-   **Online**:
    -   **AWS DataSync**: Automatizza e accelera spostamento grandi volumi di dati tra on-premises e AWS (S3).
    -   **AWS Transfer Family**: Servizio gestito per trasferire file (FTP, SFTP, FTPS) direttamente con S3 o EFS.
    -   **AWS Direct Connect**: Connessione privata dedicata tra data center e AWS.
-   **Offline**:
    -   **AWS Snowball Edge Storage Optimized**: Dispositivi fisici per spostare petabyte di dati offline.

## [[Prezzi e Supporto]]

### Concetti relativi ai prezzi in AWS
-   **Pagamento in base al consumo**: Paga solo per le risorse effettivamente usate.
-   **Risparmi in base alla durata**: Prezzi vantaggiosi per impegni di spesa costanti (es. Saving Plans).
-   **Prezzi decrescenti con l’aumentare dell’utilizzo**: Costo per unità diminuisce all'aumentare del volume.
-   **Fattori che influenzano i costi**: Calcolo (tempo di esecuzione), Archiviazione (spazio, frequenza accesso), Trasferimento dati in uscita.

### Servizi AWS per prezzi e fatturazione
-   **AWS Organizations**: Fatturazione consolidata per più account.
-   **Dashboard Gestione costi e fatturazione AWS**: Visualizza spese, utilizzo, previsioni.
-   **Budget AWS**: Definisce limiti di spesa, riceve notifiche.
-   **AWS Cost Explorer**: Analisi avanzata delle spese, trend, ottimizzazione.
-   **Calcolatore dei prezzi AWS**: Stima anticipata dei costi per configurazioni cloud.

### Piani di supporto AWS
-   **Basic**: Gratuito, documentazione, forum, Trusted Advisor (limitato), Personal Health Dashboard.
-   **Developer**: Contatto diretto assistenza, tempi di risposta 24h/12h.
-   **Business**: Tutte le funzionalità precedenti, Trusted Advisor completo, accesso telefonico, tempi di risposta 4h/1h, Infrastructure Event Management.
-   **Enterprise (On-Ramp/Enterprise)**: Livello proattivo, tempi di risposta ridotti, Technical Account Manager (TAM).

### Marketplace AWS e partner AWS
-   **Marketplace AWS**: Catalogo digitale di soluzioni software di vendor terzi, integrate con AWS.
-   **Rete dei partner AWS (APN)**: Comunità globale di aziende/professionisti che creano soluzioni con AWS.

### Ottimizzazione dei costi
-   **Dimensionamento ottimale EC2**: Adattare risorse, usare Compute Optimizer.
-   **Istanze Spot**: Sconti fino al 90% per carichi flessibili.
-   **Auto Scaling**: Ridimensiona dinamicamente in base alla domanda.
-   **Pulizia account**: Eliminare risorse inutilizzate.
-   **Ottimizzazione RDS**: Dimensionamento corretto, repliche di lettura, caching con ElastiCache.
-   **Gestione classi S3**: S3 Intelligent-Tiering, compressione file, Lifecycle policy.
-   **Costi trasferimento dati**: Ridurre traffico tra AZ/Internet, usare endpoint VPC.

## [[Soluzioni Well-Architected]]

### Servizi AWS specializzati
-   **Sviluppo**:
    -   **AWS CodeBuild**: Compilazione e test automatici.
    -   **AWS CodePipeline**: Automatizza cicli di rilascio CI/CD.
    -   **AWS X-Ray**: Analizza comportamento applicazioni, identifica colli di bottiglia.
    -   **AWS AppSync**: API GraphQL centralizzate.
    -   **AWS Amplify**: Semplifica creazione app full-stack.
-   **Applicazioni aziendali**:
    -   **Amazon Connect**: Contact center scalabili con AI.
    -   **Amazon SES (Simple Email Service)**: Invio email affidabili e ad alto volume.
-   **End-user computing**:
    -   **Amazon AppStream 2.0**: Trasmette applicazioni dal cloud a dispositivi.
    -   **Amazon WorkSpaces**: Desktop virtuali accessibili ovunque.
    -   **Amazon WorkSpaces Secure Browser**: Accesso sicuro a web/SaaS.
-   **IoT**:
    -   **AWS IoT Core**: Connette dispositivi fisici e applicazioni cloud, gestisce autenticazione, comunicazione, elaborazione dati.

### Framework AWS Well-Architected
-   Definisce caratteristiche per progettare soluzioni cloud robuste, efficienti, sostenibili.
-   **Sei pilastri**:
    -   **Eccellenza operativa**: Gestione quotidiana, monitoraggio, automazione, miglioramento continuo.
    -   **[[Sicurezza]]**: Protezione dati/sistemi (privilegio minimo, crittografia, integrità).
    -   **Affidabilità**: Recupero errori, scalabilità, funzionamento coerente.
    -   **Efficienza delle prestazioni**: Uso risorse adatte, adattamento ai cambiamenti.
    -   **Ottimizzazione dei costi**: Uso intelligente risorse, contenimento spese.
    -   **Sostenibilità**: Riduzione consumo energetico e impatto ambientale.
-   **Strumento AWS Well-Architected**: Supporto gratuito per valutare carichi di lavoro secondo i pilastri, identificare rischi, piani di miglioramento.

### [[Casi d'uso]] specializzati
-   **Architetture Serverless**: Creazione di sistemi flessibili e scalabili con servizi gestiti.
    -   **Back-end serverless**: API Gateway -> Lambda -> DynamoDB -> X-Ray.
    -   **Siti web statici in Amazon S3** con logiche dinamiche (es. modulo contatto con API Gateway -> Lambda -> SES).
    -   **Amazon Connect** integrato con Lambda e CloudFront per assistenza clienti avanzata.