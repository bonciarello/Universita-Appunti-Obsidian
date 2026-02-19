---
aliases: [DB, Basi di Dati, DBMS]
tags: [aws-clf-002-cloud-practitioner-essentials]
---
## Archiviazione dell'istanza e Amazon Elastic Block Store (Amazon EBS)

Le istanze EC2, come qualsiasi server, devono poter accedere a CPU, memoria, rete e soprattutto spazio di archiviazione. L’archiviazione serve per salvare dati, file, [[Database|database]] o configurazioni che le applicazioni utilizzano durante l’esecuzione. In AWS, questo spazio può essere fornito da diversi tipi di dischi virtuali, a seconda delle esigenze di prestazioni e persistenza.

L’**archiviazione a livello di blocco** memorizza i dati in blocchi separati, come avviene su un disco rigido. Quando un file viene modificato, vengono aggiornati solo i blocchi interessati e non l’intero file. Questo la rende molto efficiente per applicazioni che gestiscono grandi volumi di dati, come [[Database|database]] o [[File System|file system]] aziendali, dove le modifiche sono frequenti e localizzate.

Un **Instance Store** è un tipo di archiviazione temporanea fisicamente collegata all’host che esegue l’istanza EC2. Funziona come un normale disco locale, ma i dati vengono eliminati quando l’istanza viene arrestata o terminata, perché l’istanza può essere riavviata su un altro host. È utile solo per dati temporanei o facilmente ricreabili, come file temporanei o cache.

L’archiviazione dell’Instance Store è volatile: tutti i dati vengono persi quando l’istanza EC2 viene interrotta o terminata. Questo rende rischioso usarla per dati critici come [[Database|database]] o configurazioni permanenti, poiché non persisterebbero al riavvio dell’istanza.

**Amazon EBS (Elastic Block Store)** è un servizio di archiviazione persistente per EC2. Consente di creare volumi virtuali che possono essere collegati alle istanze EC2. I dati su EBS rimangono salvati anche se l’istanza viene fermata o riavviata. È possibile scegliere dimensione, tipo di volume e prestazioni in base alle esigenze applicative.

Gli **snapshot di EBS** sono backup incrementali dei volumi EBS. Permettono di salvare una copia dei dati in un determinato momento e di ripristinarli in caso di guasto o perdita. Creare snapshot regolari è una best practice di [[Sicurezza|sicurezza]] e resilienza, poiché consente di proteggere i dati da errori, danni o cancellazioni accidentali.

**Amazon Data Lifecycle Manager (DLM)** è un servizio che consente di automatizzare la gestione degli snapshot EBS, cioè i backup incrementali dei volumi di archiviazione collegati alle istanze EC2. Con DLM puoi creare, conservare ed eliminare snapshot in modo automatico, senza doverli gestire manualmente. Il vantaggio principale è la riduzione del lavoro manuale e del rischio di errore nella gestione dei backup. DLM permette di impostare policy che definiscono quando creare uno snapshot, per quanto tempo conservarlo e quando eliminarlo. Questo garantisce una gestione coerente, efficiente e sicura dei dati.

## Amazon Simple Storage Service (Amazon S3)

**Amazon S3 (Simple Storage Service)** è un servizio di archiviazione di oggetti che consente di salvare e recuperare quantità praticamente illimitate di dati nel cloud. È utilizzato per conservare file di ogni tipo e garantisce alta disponibilità, durabilità e scalabilità. È ideale per aziende che devono gestire grandi volumi di dati in modo sicuro e accessibile da qualsiasi parte del mondo.

I dati in Amazon S3 sono memorizzati sotto forma di oggetti all’interno di contenitori chiamati **bucket**. Ogni oggetto può avere fino a 5 terabyte di dimensione e include i dati veri e propri più i relativi metadati. Puoi pensare ai bucket come a “cartelle” e agli oggetti come ai “file” contenuti al loro interno.

Il **versioning** permette di mantenere più versioni di uno stesso oggetto nel tempo ed è utile in Amazon S3 per proteggersi da cancellazioni o sovrascritture accidentali: se un file viene eliminato o modificato per errore, puoi sempre ripristinare una versione precedente.

Amazon S3 offre diverse classi di archiviazione per bilanciare costi, prestazioni e frequenza di accesso ai dati:

*   **S3 Standard**: per dati a cui si accede frequentemente, con elevata disponibilità e replicazione in almeno tre zone di disponibilità.
*   **S3 Standard-IA (Infrequent Access)**: per dati consultati raramente, ma che devono rimanere facilmente accessibili.
*   **S3 One Zone-IA**: simile a Standard-IA ma archivia i dati in una sola zona di disponibilità, con costi più bassi.
*   **S3 Intelligent-Tiering**: sposta automaticamente gli oggetti tra livelli di accesso frequente e infrequente in base all’utilizzo.
*   **S3 Glacier Instant Retrieval**: archiviazione economica per dati a lungo termine con recupero immediato.
*   **S3 Glacier Flexible Retrieval**: per archiviazione a lungo termine con recupero in pochi minuti o ore.
*   **S3 Glacier Deep Archive**: la classe più economica, pensata per conservare dati a cui si accede raramente (una o due volte l’anno).
*   **S3 Outposts**: per archiviazione locale su infrastrutture AWS Outposts, utile in contesti ibridi o on-premises.

Le **Lifecycle policy** consentono di automatizzare lo spostamento dei dati tra diverse classi di archiviazione nel tempo. Ad esempio, un oggetto può rimanere per 90 giorni in S3 Standard, poi passare a S3 Infrequent Access, e infine dopo 120 giorni essere trasferito in Glacier. Questo permette di ottimizzare i costi di archiviazione in base all’età e alla frequenza d’uso dei dati.

Amazon S3 è progettato per una durabilità dell’**11 nove (99.999999999%)** e un’elevata disponibilità. I dati vengono replicati automaticamente in più zone di disponibilità (almeno tre per S3 Standard), assicurando protezione da guasti hardware, perdite di dati o disastri locali.

Amazon S3 può essere utilizzato per l’**hosting statico di siti web**, ovvero pagine [[HTML]], CSS, [[JavaScript]] e media che non richiedono un server backend. Basta caricare i file in un bucket, abilitare l’hosting web statico e ottenere un URL pubblico del sito. È una soluzione semplice, economica e scalabile per blog o pagine aziendali.

## Amazon Elastic [[File System]] (Amazon EFS)

**Amazon EFS (Elastic [[File System]])** è un servizio di [[File System|file system]] gestito e scalabile offerto da AWS. Permette di archiviare e condividere dati tra più istanze EC2 in modo simultaneo, come se fosse un [[File System|file system]] di rete tradizionale. È ideale per scenari in cui più server o applicazioni devono accedere contemporaneamente agli stessi file, ad esempio per analisi dati, contenuti multimediali o applicazioni condivise.

EFS è un servizio autoscalante: quando aggiungi nuovi file, lo spazio si espande automaticamente; se elimini dati, si riduce. Inoltre, include Lifecycle policy che spostano automaticamente i dati meno utilizzati verso classi di archiviazione più economiche, mantenendo così i costi ottimizzati. Queste policy possono essere personalizzate in base ai modelli di accesso ai dati.

La differenza tra Amazon EBS e Amazon EFS è che la prima fornisce archiviazione a livello di blocco collegata a una singola istanza EC2 mentre la seconda fornisce archiviazione a livello di file, accessibile da più istanze EC2 contemporaneamente, anche in zone o regioni diverse. EFS è la scelta migliore quando più server devono accedere agli stessi dati in contemporanea, come nei casi di applicazioni distribuite su più istanze EC2, ambienti di sviluppo o contenuti condivisi, data analytics o [[Machine Learning|machine learning]] su dataset comuni ed archiviazione condivisa in cluster o microservizi. EBS, invece, è più adatto a un singolo server o [[Database|database]] che richiedono alte prestazioni locali.

Amazon EFS offre diverse classi di archiviazione progettate per bilanciare costi, disponibilità e frequenza di accesso ai dati:

*   **EFS Standard e EFS Standard-IA (Infrequent Access)**: offrono elevata disponibilità e durabilità, con dati replicati su più zone di disponibilità. Sono ideali per applicazioni che richiedono accesso rapido e resilienza elevata.
*   **EFS One Zone e EFS One Zone-IA**: memorizzano i dati in una sola zona di disponibilità, riducendo i costi di archiviazione rispetto alle classi Standard, ma con un livello di ridondanza inferiore.
*   **EFS Archive**: è la classe più economica, ottimizzata per dati a cui si accede raramente (pochi accessi all’anno) e che non necessitano di latenze molto basse.

Amazon EFS consente di impostare Lifecycle policy per spostare automaticamente i file tra le diverse classi di archiviazione in base al comportamento di accesso. In questo modo, i dati vengono mantenuti nella classe più economica possibile, senza interventi manuali. Possiamo distinguere tre policy:

*   **Policy di transizione a IA**: sposta automaticamente i file che non vengono utilizzati da 30 giorni dalla classe Standard a Standard-IA o One Zone-IA. Questo riduce i costi di archiviazione per i dati usati raramente, mantenendo comunque tempi di accesso rapidi.
*   **Policy di transizione a EFS Archive**: sposta i file che non vengono utilizzati da 90 giorni nella classe di archiviazione EFS Archive, ottimizzata per dati a lunghissimo termine e accessi molto rari. È la soluzione ideale per archiviazione storica o conservazione a lungo termine.
*   **Policy di transizione a Standard**: serve per riportare i file dalle classi IA o Archive di nuovo alla classe Standard nel momento in cui vengono riacceduti. Tuttavia, per impostazione predefinita, i file non vengono spostati automaticamente: anche dopo l’accesso, restano nella classe IA o Archive, a meno che non si configuri diversamente.

**Amazon FSx** è un servizio di Amazon Web Services (AWS) che fornisce [[File System|file system]] completamente gestiti e ad alte prestazioni, compatibili con i protocolli e le applicazioni più comuni. In pratica, permette di creare e utilizzare [[File System|file system]] in cloud come se fossero locali, senza doversi occupare di gestione hardware, backup o scalabilità. Esistono diverse versioni di FSx, ad esempio per Windows File Server, Lustre o NetApp ONTAP, pensate per scenari specifici come applicazioni Windows, carichi HPC o ambienti enterprise.

## Gateway di archiviazione AWS e AWS Elastic Disaster Recovery

**AWS Storage Gateway** è un servizio di archiviazione ibrida che collega l’infrastruttura on-premises al cloud AWS. Funziona come un ponte che consente alle organizzazioni di accedere a uno spazio di archiviazione nel cloud praticamente illimitato, mantenendo al tempo stesso i propri sistemi locali. È particolarmente utile per chi vuole sfruttare i vantaggi del cloud, come backup automatici, archiviazione a lungo termine e ridondanza, senza dover modificare radicalmente l’infrastruttura esistente.

AWS Storage Gateway è disponibile in tre varianti, ciascuna pensata per scenari diversi:

*   **File Gateway (S3 File Gateway)**: permette di archiviare e accedere ai file direttamente in Amazon S3, mantenendo compatibilità con i protocolli di [[File System|file system]] comuni come NFS e SMB. È ideale per condividere file o centralizzare dati accessibili da più servizi AWS.
*   **Volume Gateway**: fornisce volumi a blocchi locali utilizzabili dalle applicazioni on-premises. Tutti i dati vengono automaticamente backuppati nel cloud come snapshot Amazon EBS, garantendo durabilità e facilità di ripristino.
*   **Tape Gateway (Virtual Tape Library)**: emula un sistema di nastri virtuali, pensato per aziende che usano ancora backup su nastro fisico. Consente di migrare facilmente i dati di backup tradizionali nel cloud AWS, eliminando la gestione fisica dei nastri.

AWS Storage Gateway offre diversi vantaggi pratici:

*   **Backup e ripristino semplificati**: permette di mantenere copie sicure dei dati nel cloud, garantendo un piano di disaster recovery immediato.
*   **Archiviazione scalabile**: consente di spostare nel cloud i dati meno utilizzati, liberando spazio locale e riducendo i costi.
*   **Integrazione fluida**: non richiede modifiche significative ai flussi di lavoro esistenti, quindi può essere integrato facilmente in ambienti ibridi.
*   **Affidabilità e [[Sicurezza|sicurezza]] AWS**: tutti i dati vengono protetti con crittografia e snapshot automatici, assicurando alta durabilità e conformità.

AWS Storage Gateway è ideale per organizzazioni che vogliono iniziare a migrare gradualmente verso il cloud o potenziare la propria infrastruttura on-premises. Alcuni casi d’uso tipici sono:

*   **Backup e ripristino di emergenza (Disaster Recovery)**: mantenendo copie dei dati critici nel cloud, le aziende possono ripristinare rapidamente i sistemi in caso di guasto locale.
*   **Archiviazione a lungo termine**: permette di archiviare dati a basso tasso di accesso in modo economico su Amazon S3 o Glacier.
*   **Migrazione dei nastri fisici**: con Tape Gateway, le aziende che usavano nastri di backup possono spostare tutto nel cloud, mantenendo gli stessi strumenti e processi.

Il grande valore di AWS Storage Gateway è che consente di ottenere i benefici del cloud senza abbandonare l’infrastruttura locale. Le aziende possono continuare a lavorare come sempre, ma con l’aggiunta di un livello di [[Sicurezza|sicurezza]], flessibilità e scalabilità tipico di AWS. In sostanza, è una porta d’ingresso verso il cloud, perfetta per chi vuole iniziare a migrare in modo graduale e senza rischi.

**AWS Elastic Disaster Recovery (DRS)** è un servizio che consente di replicare in modo continuo i carichi di lavoro critici, sia da server fisici che virtuali, verso AWS. Il suo obiettivo è garantire la massima disponibilità operativa durante interruzioni o disastri, riducendo al minimo i tempi di inattività e la perdita di dati. In caso di emergenza, il servizio permette di avviare rapidamente le istanze di ripristino nel cloud, mantenendo la continuità dei servizi aziendali.

AWS Elastic Disaster Recovery replica continuamente i dati a livello di blocco dai server locali o da altri ambienti verso AWS. Questo significa che ogni modifica ai dati viene catturata quasi in tempo reale, riducendo drasticamente la finestra di possibile perdita. In caso di disastro, i sistemi possono essere riavviati nel cloud utilizzando le copie aggiornate, garantendo un ripristino rapido e affidabile.

A differenza dei classici sistemi di disaster recovery che richiedono un data center secondario sempre attivo e costoso da mantenere, AWS Elastic Disaster Recovery elimina questi oneri. I dati vengono replicati nel cloud AWS, riducendo i costi operativi e infrastrutturali. Inoltre, grazie alla scalabilità del cloud, le risorse di ripristino vengono avviate solo quando servono, garantendo una soluzione efficiente e conveniente.