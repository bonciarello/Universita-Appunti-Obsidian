---
aliases: [sicurezza]
tags: [aws-clf-002-cloud-practitioner-essentials]
---
## Prevenzione di accessi non autorizzati

L’**utente root** è il primo utente creato automaticamente quando si apre un account AWS. Ha il controllo totale su tutte le risorse e può eseguire qualunque azione, dalla creazione di istanze EC2 fino all’eliminazione di interi [[Database|database]]. Proprio per questo motivo è considerato un’identità estremamente potente e potenzialmente rischiosa. AWS raccomanda di proteggerlo con una password complessa e con l’autenticazione a più fattori (MFA), ma anche di non usarlo per le operazioni quotidiane. L’utente root dovrebbe essere riservato solo a compiti amministrativi critici, come la gestione dei pagamenti o la modifica delle impostazioni di sicurezza globali.

**AWS Identity and Access Management (IAM)** è il servizio che consente di gestire in modo sicuro utenti, gruppi e autorizzazioni all’interno di un account AWS. Grazie a IAM, è possibile creare identità individuali (utenti IAM) e assegnare a ciascuna solo i permessi strettamente necessari per svolgere determinate operazioni.

Le **policy IAM** sono documenti in formato JSON che definiscono in modo esplicito le autorizzazioni di un utente, gruppo o ruolo. Ogni policy contiene istruzioni che indicano tre elementi fondamentali:

*   **Effect (Effetto)**: indica se l’azione è consentita (Allow) o negata (Deny).
*   **Action (Azione)**: specifica le chiamate API AWS permesse (es. `s3:ListBucket`).
*   **Resource (Risorsa)**: identifica l’oggetto su cui l’azione può essere eseguita (es. un bucket S3 specifico).

I **gruppi IAM** servono a semplificare la gestione delle autorizzazioni per più utenti contemporaneamente. Invece di applicare una policy individualmente a ciascun utente, è possibile associare la policy a un gruppo, e tutti i membri erediteranno automaticamente le stesse autorizzazioni. Questo approccio riduce la complessità amministrativa e garantisce coerenza nei permessi assegnati.

I **ruoli IAM** rappresentano identità temporanee dotate di permessi specifici, ma senza credenziali permanenti come password o chiavi di accesso. A differenza degli utenti, i ruoli vengono assunti da persone, applicazioni o servizi AWS per accedere temporaneamente a determinate risorse. Questo li rende ideali per scenari in cui non è pratico o sicuro distribuire credenziali fisse, come nei processi automatizzati o nei servizi che interagiscono tra loro. Una volta terminato l’uso del ruolo, le autorizzazioni vengono revocate automaticamente, riducendo il rischio di abuso.

La **federazione delle identità** permette agli utenti di un’organizzazione di accedere ad AWS utilizzando le proprie credenziali aziendali. In questo modo, non è necessario creare un utente IAM per ciascuna persona. IAM gestisce i permessi attraverso ruoli federati, che vengono assunti dagli utenti quando accedono. Ciò semplifica l’amministrazione e migliora la sicurezza, centralizzando la gestione delle identità.

Il **Centro identità IAM (IAM Identity Center)** è un servizio AWS che consente di configurare e gestire in modo centralizzato l’accesso degli utenti, introducendo un sistema di Single Sign-On (SSO). Gli utenti possono così accedere a più account o applicazioni AWS utilizzando un’unica identità, senza dover ricordare più password.

**AWS Secrets Manager** offre un metodo sicuro per gestire, ruotare e recuperare credenziali del [[Database|database]], chiavi API e altri segreti durante l'intero ciclo di vita. In questo modo, le applicazioni, i servizi e le risorse IT restano al sicuro.

**AWS Systems Manager** fornisce una visualizzazione centralizzata dei nodi negli account e nelle regioni dell'organizzazione e in ambienti multi-cloud e ibridi. Con questo servizio puoi accedere rapidamente alle informazioni sui nodi, come i dettagli su ID e [[Sistemi Operativi|sistemi operativi]], e automatizzare le modifiche al registro, la gestione degli utenti e l'applicazione di patch di sicurezza.

## Protezione di [[Reti|reti]] e applicazioni

Un **attacco DoS (Denial of Service)** consiste nel sovraccaricare un’applicazione web con un volume eccessivo di traffico di rete. L’obiettivo è rendere il servizio indisponibile agli utenti legittimi, saturando le risorse del server fino a impedirgli di rispondere alle richieste reali.

Un **attacco DDoS (Distributed Denial of Service)** è una versione più potente e distribuita di un attacco DoS. Invece di provenire da una singola macchina, il traffico malevolo arriva da centinaia o migliaia di computer infetti, chiamati bot, che fanno parte di una botnet. Questa rete distribuita rende gli attacchi DDoS molto più difficili da bloccare, perché le richieste sembrano provenire da molteplici sorgenti legittime.

AWS include protezioni automatiche integrate contro attacchi di forza bruta e DDoS a livello di rete. La sua [[Architettura|architettura]] globale, distribuita su più regioni, zone di disponibilità e posizioni edge, è progettata per resistere a grandi volumi di traffico. Inoltre, AWS può assorbire e distribuire il carico in modo efficiente, rendendo estremamente difficile per un aggressore sovraccaricare il sistema.

I **gruppi di sicurezza** agiscono come firewall virtuali a livello di istanza o servizio. Consentono solo traffico in entrata considerato legittimo e bloccano tutte le richieste non autorizzate. Operando a livello di rete AWS, impediscono che attacchi di massa riescano a colpire direttamente le risorse interne, sfruttando la grande capacità della regione per neutralizzare il traffico malevolo.

**Elastic Load Balancing (ELB)** distribuisce automaticamente il traffico in ingresso tra più server o istanze. Questo evita che un singolo server venga sovraccaricato da troppe richieste. Oltre a migliorare le prestazioni e l’affidabilità, ELB aiuta anche a mitigare attacchi DoS o DDoS, smistando il carico e riducendo i punti di vulnerabilità.

Le **regioni AWS** possiedono un’enorme capacità di rete e infrastruttura, distribuita su più data center e zone di disponibilità. Questa ampiezza rende estremamente difficile e costoso per un aggressore generare abbastanza traffico da provocare un sovraccarico. La resilienza e la scalabilità intrinseche dell’[[Architettura|architettura]] AWS rappresentano una prima linea di difesa contro gli attacchi DDoS su larga scala.

**AWS Shield** è un servizio gestito che protegge automaticamente le risorse AWS dagli attacchi DDoS. Avremo:

*   **AWS Shield Standard**: è incluso gratuitamente e difende contro gli attacchi più comuni, monitorando il traffico in tempo reale e bloccando automaticamente quello sospetto.
*   **AWS Shield Advanced**: è un servizio a pagamento che offre diagnostica dettagliata, mitigazione avanzata e supporto tecnico dedicato in caso di attacco. Si integra perfettamente con altri servizi come Amazon CloudFront, Route 53 ed Elastic Load Balancing, e può essere combinato con AWS WAF per regole personalizzate.

**AWS WAF (Web Application Firewall)** protegge le applicazioni web monitorando tutte le richieste HTTP e HTTPS in ingresso. Quando riceve una richiesta, la confronta con le Web ACL (Access Control List), che contengono regole predefinite su quali indirizzi IP o pattern di traffico bloccare. Se la richiesta proviene da un IP sospetto o corrisponde a una regola di blocco, viene negata immediatamente. Solo le richieste considerate legittime vengono inoltrate all’applicazione, garantendo un livello di protezione aggiuntivo contro attacchi come SQL injection, XSS o DDoS applicativi.

## Protezione dei dati

La **crittografia** è essenziale per garantire che i dati rimangano riservati e protetti, anche in caso di accessi non autorizzati o incidenti di sicurezza. In AWS, gran parte delle operazioni e dei servizi si basa sull’elaborazione e sull’archiviazione dei dati: questi possono includere informazioni personali dei clienti, dati finanziari, proprietà intellettuale e altri contenuti sensibili.

Crittografando i dati, si rende illeggibile qualsiasi informazione a chi non possiede la chiave corretta. Solo gli utenti o i sistemi autorizzati possono decifrare i dati e accedervi. In questo modo, la crittografia mantiene la fiducia dei clienti, garantisce la conformità alle normative (come GDPR o HIPAA) e protegge il corretto funzionamento delle applicazioni aziendali.

La crittografia funziona come un sistema di chiave e serratura. Quando si applica la crittografia, i dati originali vengono trasformati in una sequenza di caratteri casuali, apparentemente privi di significato, chiamata testo cifrato. Per tornare ai dati leggibili (testo in chiaro), è necessaria una chiave di decrittografia. Solo chi possiede la chiave corretta può “aprire” i dati e visualizzarli.

AWS distingue due categorie principali di crittografia:

*   **Crittografia dei dati inattivi**: si applica ai dati fermi in un archivio, ad esempio in un [[Database|database]], in un bucket Amazon S3 o in un volume EBS. Serve a proteggere le informazioni da accessi non autorizzati o furti di supporti fisici. In AWS, questa crittografia può essere automatizzata e gestita tramite servizi come KMS o integrata direttamente nei sistemi di archiviazione.
*   **Crittografia dei dati in transito**: protegge i dati mentre viaggiano tra sistemi diversi (es. tra un [[Database|database]] e un’applicazione o tra un client e un server). Si basa su protocolli di rete sicuri come SSL/TLS, che stabiliscono connessioni crittografate, evitando che le informazioni vengano intercettate o modificate durante la trasmissione.

Molti servizi AWS includono la crittografia integrata come impostazione standard o opzionale:

*   **Amazon S3**: consente di crittografare automaticamente gli oggetti archiviati nei bucket, sia con chiavi gestite da AWS (SSE-S3) sia con chiavi personalizzate gestite da KMS (SSE-KMS).
*   **Amazon EBS (Elastic Block Store)**: permette di cifrare i volumi di archiviazione a blocchi e gli snapshot, garantendo che i dati siano protetti sia a riposo sia durante il trasferimento tra istanze.
*   **Amazon DynamoDB**: supporta la crittografia dei dati a riposo, proteggendo le tabelle e i backup senza necessità di configurazione manuale da parte dell’utente.

Il **Servizio AWS di [[Gestione delle Chiavi|gestione delle chiavi]] (KMS)** consente di creare, gestire e controllare le chiavi crittografiche usate per proteggere i dati. Con KMS puoi generare chiavi uniche per i tuoi dati, definire chi (utenti o ruoli IAM) può utilizzarle e monitorare ogni utilizzo tramite AWS CloudTrail. Le chiavi create con KMS non lasciano mai il servizio: restano protette all’interno dell’infrastruttura AWS. È anche possibile disattivarle temporaneamente, ad esempio per sospendere l’accesso a determinati dati.

**Amazon Macie** è un servizio che utilizza [[Machine Learning|machine learning]] (ML) e analisi automatica per individuare e proteggere dati sensibili archiviati in Amazon S3. Macie può riconoscere automaticamente informazioni personali (PII), credenziali o dati finanziari, segnalare potenziali esposizioni o configurazioni rischiose e fornire report per la conformità e la sicurezza. È uno strumento particolarmente utile per le aziende che devono rispettare normative severe (come GDPR o PCI DSS) e vogliono valutare la propria postura di sicurezza in modo continuo e automatizzato.

**AWS Certificate Manager (ACM)** gestisce i certificati SSL/TLS, che sono fondamentali per la crittografia dei dati in transito. Con ACM puoi richiedere, distribuire e rinnovare automaticamente certificati SSL/TLS, applicarli a servizi AWS come Elastic Load Balancing, CloudFront o API Gateway e utilizzarli anche per risorse on-premises collegate al cloud. I certificati SSL/TLS stabiliscono connessioni cifrate tra due sistemi (ad esempio, browser e server), garantendo che i dati trasmessi non possano essere intercettati o alterati. In questo modo ACM semplifica la gestione della sicurezza di rete e riduce gli errori di configurazione.

AWS adotta un approccio a più livelli per la protezione dei dati:

*   Servizi di base come S3, EBS e DynamoDB integrano la crittografia dei dati inattivi.
*   KMS gestisce le chiavi crittografiche in modo centralizzato e sicuro.
*   Macie monitora i dati sensibili e rileva possibili esposizioni.
*   ACM protegge la trasmissione dei dati attraverso certificati SSL/TLS.

Questi strumenti lavorano in sinergia per garantire che i dati siano protetti in ogni fase del loro ciclo di vita.

## Rilevamento e risposta agli incidenti di sicurezza

I servizi di rilevamento e risposta in AWS consentono di individuare e gestire gli incidenti di sicurezza che possono verificarsi all’interno dell’infrastruttura cloud. Oltre alla prevenzione, questi strumenti forniscono monitoraggio continuo, analisi automatica delle minacce e meccanismi di risposta per migliorare la postura di sicurezza complessiva.

**Amazon Inspector** è un servizio che esegue valutazioni di sicurezza automatiche su risorse come istanze Amazon EC2, container e funzioni Lambda. Analizza le applicazioni per identificare vulnerabilità, configurazioni errate o software non aggiornati, e fornisce risultati classificati per livello di gravità insieme a raccomandazioni dettagliate su come correggere ogni problema. I risultati possono essere consultati nella console o recuperati tramite API, consentendo un’integrazione diretta con altri strumenti di sicurezza.

**Amazon GuardDuty** offre un rilevamento intelligente delle minacce monitorando costantemente i flussi di metadati dell’account AWS e l’attività di rete. Utilizza fonti di intelligence su IP dannosi, modelli di apprendimento automatico e analisi delle anomalie per individuare comportamenti sospetti. I risultati, consultabili nella console, includono descrizioni dettagliate e azioni consigliate per la mitigazione. Inoltre, è possibile configurare funzioni AWS Lambda per rispondere automaticamente alle minacce individuate.

**Amazon Detective** aiuta a investigare più a fondo sugli incidenti di sicurezza dopo che sono stati rilevati da servizi come GuardDuty o Inspector. Fornisce visualizzazioni interattive che mostrano le relazioni e le attività tra risorse e utenti nel tempo, aiutando a comprendere la causa principale di una minaccia. Le analisi vengono presentate in un’unica interfaccia centralizzata, semplificando l’indagine e la risoluzione.

La **Centrale di sicurezza AWS (AWS Security Hub)** raccoglie e aggrega i risultati di sicurezza provenienti da diversi servizi AWS e da partner esterni in un’unica interfaccia. In questo modo, offre una visione completa dello stato di sicurezza e conformità dell’ambiente cloud. Organizza i risultati in “approfondimenti” utili e consente di automatizzare la correzione dei problemi più comuni, riducendo i tempi di risposta agli incidenti.