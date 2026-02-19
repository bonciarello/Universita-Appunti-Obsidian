---
aliases: [Apprendimento Automatico, ML]
tags: [aws-clf-002-cloud-practitioner-essentials]
---
## Introduzione all'intelligenza artificiale e al [[Machine Learning|machine learning]]

L'**intelligenza artificiale** è un campo vasto incentrato sullo sviluppo di sistemi informatici intelligenti in grado di svolgere compiti simili a quelli umani.

Il **[[Machine Learning|machine learning]]** è un tipo di intelligenza artificiale per addestrare le macchine a eseguire attività complesse senza istruzioni esplicite. L'addestramento per il [[Machine Learning|machine learning]] trova i pattern nascosti in vaste quantità di dati cronologici per produrre un modello di ML. Questo modello di [[Machine Learning|machine learning]] può quindi essere applicato a nuovi dati per generare previsioni o prendere decisioni in base ai pattern appresi.

### AI/ML e soluzioni di AWS

Lo stack AI/ML AWS è costituito dai tre livelli di soluzioni seguenti:

*   **Servizi di intelligenza artificiale (AI services)**: modelli predefiniti già addestrati per eseguire funzioni specifiche.
*   **Servizi di [[Machine Learning|machine learning]] (ML services)**: approccio più personalizzato con Amazon SageMaker AI in cui puoi creare, addestrare e distribuire i tuoi modelli di ML con un'infrastruttura completamente gestita.
*   **Framework e infrastruttura di [[Machine Learning|machine learning]] (ML frameworks and infrastructures)**: approccio completamente personalizzato alla creazione di modelli utilizzando chip dedicati che si integrano con i framework di ML più diffusi.

I **servizi di intelligenza artificiale AWS (AI services)** forniscono modelli già addestrati, pronti per essere integrati nelle applicazioni senza necessità di competenze avanzate di [[Machine Learning|machine learning]]. Questi strumenti semplificano l’adozione dell’AI in casi d’uso concreti, come l’elaborazione del linguaggio, il riconoscimento visivo e la personalizzazione delle esperienze utente. Alcuni dei servizi AWS di questo livello sono:

*   **Amazon Comprehend**: analizza testi per estrarre informazioni e sentimenti.
*   **Amazon Polly**: trasforma testi in voce naturale.
*   **Amazon Transcribe**: converte l’audio in testo.
*   **Amazon Translate**: gestisce traduzioni automatiche multilingue.
*   **Amazon Rekognition**: riconosce volti e oggetti in immagini e video.
*   **Amazon Textract**: estrae testo da documenti.
*   **Amazon Kendra**: consente ricerche intelligenti in grandi archivi di contenuti.
*   **Amazon Lex**: crea chatbot vocali e testuali realistici.
*   **Amazon Personalize**: genera suggerimenti personalizzati per gli utenti.

Questi servizi offrono facilità d’uso e tempi rapidi di implementazione, ma limitano la personalizzazione, poiché si basano su modelli predefiniti che non sempre rispondono a esigenze altamente specifiche.

I **servizi di [[Machine Learning|machine learning]] (ML services)** sono pensati per chi desidera maggiore controllo sui modelli di [[Machine Learning|machine learning]] senza gestire direttamente l’infrastruttura. **Amazon SageMaker AI** consente di creare, addestrare e distribuire modelli personalizzati in modo semplice e scalabile ed offre un ambiente integrato per sperimentare, monitorare e ottimizzare i flussi di lavoro di [[Machine Learning|machine learning]], con la possibilità di utilizzare anche modelli preaddestrati. I principali vantaggi di SageMaker sono la gestione automatizzata delle risorse, la ripetibilità dei processi e la possibilità di scegliere tra diversi strumenti e framework. Tuttavia, richiede una conoscenza tecnica superiore rispetto ai servizi di AI predefiniti e può risultare più costoso in progetti di lunga durata o con grandi volumi di dati.

Il terzo livello è destinato alle organizzazioni che necessitano del massimo controllo e personalizzazione. Qui si lavora direttamente con framework come TensorFlow, PyTorch o Apache MXNet, sfruttando l’infrastruttura AWS — ad esempio Amazon EC2, EMR o ECS — per addestrare modelli complessi.

I **framework e infrastruttura di [[Machine Learning|machine learning]] (ML frameworks and infrastructures)** sono destinati alle organizzazioni che necessitano del massimo controllo e personalizzazione. Qui si lavora direttamente con framework come TensorFlow, PyTorch o Apache MXNet, sfruttando l’infrastruttura AWS per addestrare modelli complessi. Questo approccio è ideale per team esperti che sviluppano soluzioni su misura con requisiti tecnici o prestazionali molto specifici. Offre la massima flessibilità e potenza di calcolo, ma comporta anche una maggiore complessità operativa e una gestione più attenta dei costi e delle risorse.

## Introduzione all'IA generativa su AWS

Il **[[Deep Learning|deep learning]]** è un sottoinsieme del [[Machine Learning|machine learning]] che utilizza [[Reti|reti]] neurali artificiali per modellare e risolvere problemi complessi. Mentre il [[Machine Learning|machine learning]] tradizionale si basa su modelli più semplici e spesso richiede l’intervento umano per l’estrazione delle caratteristiche dai dati, il [[Deep Learning|deep learning]] è in grado di apprendere automaticamente rappresentazioni complesse dai dati grezzi. Queste [[Reti|reti]] neurali imitano il funzionamento del cervello umano, utilizzando strati di neuroni artificiali che elaborano le informazioni in modo gerarchico ed è in grado di affrontare problemi complessi come il riconoscimento delle immagini, la traduzione automatica e la comprensione del linguaggio naturale.

Sebbene i concetti teorici alla base del [[Deep Learning|deep learning]] esistono da decenni, la sua applicazione pratica è diventata possibile solo di recente grazie ai progressi dell’hardware. In particolare, l’avvento delle GPU potenti e accessibili, insieme alla disponibilità di grandi quantità di dati digitali, ha permesso di addestrare [[Reti|reti]] neurali complesse in tempi ragionevoli. In passato, la mancanza di capacità di calcolo e di dataset sufficientemente grandi rendeva impossibile realizzare modelli di [[Deep Learning|deep learning]] efficaci.

Una **rete neurale artificiale** è un insieme di nodi chiamati neuroni artificiali, ispirati al cervello umano. Ogni neurone riceve uno o più input, li elabora tramite una funzione matematica e produce un output che viene trasmesso al livello successivo. Le [[Reti|reti]] neurali sono organizzate in strati: uno strato di input, uno o più strati nascosti e uno strato di output. Durante l’addestramento, la rete regola i pesi delle connessioni tra neuroni per ridurre l’errore nelle previsioni. Questo processo consente alla rete di “imparare” dai dati e di migliorare progressivamente le proprie prestazioni.

L’**intelligenza artificiale generativa (IA generativa)** è una branca del [[Deep Learning|deep learning]] che si concentra sulla creazione di nuovi contenuti, come testo, immagini, musica o codice, invece di limitarsi ad analizzare o classificare i dati esistenti. Questi sistemi si basano su **modelli di fondazione (Foundation Models, FM)** di dimensioni enormi, addestrati su vastissime raccolte di dati.

I **modelli di fondazione (FM)** sono modelli di [[Machine Learning|machine learning]] di grandi dimensioni, preaddestrati su enormi quantità di dati e progettati per essere adattabili a molteplici compiti. Un tipo molto diffuso di FM sono i **Large Language Models (LLM)**, che vengono addestrati su grandi collezioni di testi per apprendere il funzionamento del linguaggio umano. A differenza dei modelli tradizionali, addestrati per un solo compito, i LLM possono essere personalizzati per svolgere diverse attività: rispondere a domande, riassumere testi, tradurre, scrivere codice o generare dialoghi.

AWS offre diversi servizi pensati per facilitare la creazione, la gestione e la distribuzione di soluzioni di IA generativa e [[Deep Learning|deep learning]]:

*   **Amazon SageMaker JumpStart**: è un hub di [[Machine Learning|machine learning]] che include modelli di fondazione e soluzioni predefinite. Con pochi clic, puoi distribuire modelli preaddestrati e personalizzarli con i tuoi dati per rispondere alle esigenze specifiche della tua azienda. Alcuni casi d’uso sono: distribuzioni rapide di modelli di [[Machine Learning|machine learning]], soluzioni personalizzate ed ottimizzate e esperimenti e prototipi di ML.
*   **Amazon Bedrock**: è un servizio completamente gestito che fornisce l’accesso a una vasta gamma di modelli di fondazione di Amazon e di altre aziende leader nel settore dell’IA. Bedrock consente di adattare i modelli in modo privato e sicuro, utilizzando i propri dati, e di integrare tramite un’unica API senza dover gestire l’infrastruttura sottostante. Alcuni casi d’uso sono: IA generativa di livello aziendale, generazione di contenuti multimediali e IA conversazionale avanzata.
*   **Amazon Q**: è un assistente intelligente personalizzabile che si integra con i repository informativi dell’azienda per fornire risposte contestualizzate, suggerimenti e azioni automatiche. È disponibile in due varianti principali: **Amazon Q Business**, pensato per l’ambiente aziendale, e **Amazon Q Developer**, dedicato agli sviluppatori.

I servizi AWS, come SageMaker JumpStart, Bedrock e Amazon Q, rendono l’IA generativa accessibile e personalizzabile anche per le aziende che non dispongono di grandi competenze interne in intelligenza artificiale. Offrendo modelli preaddestrati, infrastruttura gestita e API unificate, AWS consente alle organizzazioni di accelerare lo sviluppo di soluzioni intelligenti, migliorare l’efficienza operativa e innovare più rapidamente, riducendo i costi e la complessità tecnica.

## Introduzione all'analisi dei dati

I **dati** sono la base di ogni tecnologia di intelligenza artificiale (IA) e [[Machine Learning|machine learning]] (ML). Senza dati puliti, accurati e accessibili, i modelli di IA e ML non possono essere addestrati o fornire risultati affidabili. I dati permettono agli algoritmi di individuare schemi, tendenze e correlazioni, rendendo possibile la previsione di comportamenti futuri o l’automazione di decisioni complesse.

L’**analisi tradizionale dei dati** si concentra sull’esame dei dati passati per spiegare [[Eventi|eventi]] già avvenuti e individuare tendenze storiche, spesso utilizzando strumenti statistici e visualizzazioni. L’analisi tradizionale dei dati continua a essere fondamentale in diversi ambiti come la finanza e credito, sanità e assicurazioni. L’IA e il [[Machine Learning|machine learning]], invece, si focalizzano sul prevedere [[Eventi|eventi]] futuri o sull’automazione di decisioni tramite modelli predittivi. Tuttavia, entrambe le metodologie richiedono dati puliti e accessibili.

Un **data lake** è un archivio centralizzato in cui un’organizzazione può memorizzare tutti i propri dati, indipendentemente dal formato o dalla fonte. Può contenere dati strutturati (come tabelle), semi-strutturati (come file JSON) e non strutturati (come immagini, video o log). I data lake sono importanti perché riuniscono i dati sparsi in un unico luogo, rendendoli accessibili per analisi, [[Machine Learning|machine learning]] o business intelligence. In AWS, Amazon S3 è spesso utilizzato come data lake, grazie alla sua scalabilità, [[Sicurezza|sicurezza]] e integrazione con numerosi altri servizi AWS.

I processi **ETL (Extract, Transform, Load)** ed **ELT (Extract, Load, Transform)** servono per integrare e preparare dati provenienti da sistemi diversi. Nello specifico:

*   Nell’approccio **ETL**, i dati vengono estratti dalle sorgenti, trasformati in un formato coerente e poi caricati nel sistema di destinazione, come un data warehouse.
*   Nell’approccio **ELT**, i dati vengono estratti e caricati prima, e la trasformazione avviene dopo, all’interno del sistema di destinazione stesso.

La scelta tra ETL ed ELT dipende dall’infrastruttura e dai volumi di dati. Ad esempio, ELT è spesso preferito nei contesti cloud moderni, dove la potenza di calcolo può essere sfruttata per trasformazioni più complesse.

Un processo **Zero-ETL** elimina la necessità di passaggi complessi di estrazione, trasformazione e caricamento, poiché i dati si trovano già in un formato e una posizione compatibili con i sistemi di analisi o destinazione. Questo approccio riduce la latenza, semplifica l’[[Architettura|architettura]] e accelera l’accesso ai dati.

Le **pipeline di dati** sono flussi automatizzati che gestiscono il trasferimento e la trasformazione dei dati da una sorgente a una destinazione. Si comportano come una catena di montaggio in cui i dati vengono acquisiti, elaborati e resi utilizzabili in modo continuo e ripetibile. Le pipeline permettono di ridurre gli errori manuali, migliorare la coerenza e garantire che le informazioni siano sempre aggiornate. Su AWS, servizi come AWS Glue, Amazon Kinesis e Amazon EMR aiutano a costruire e mantenere pipeline scalabili e affidabili.

I servizi AWS sono progettati per collaborare in modo sinergico. Ad esempio, i dati non elaborati possono essere archiviati in Amazon S3 come data lake. Da lì, AWS Glue può eseguire processi ETL per prepararli, Amazon Redshift può gestire analisi complesse e Amazon QuickSight può visualizzare i risultati per il team di business intelligence. Allo stesso tempo, gli stessi dati possono essere utilizzati da Amazon SageMaker per addestrare modelli di [[Machine Learning|machine learning]].

Centralizzare e standardizzare i dati è essenziale per garantire che tutte le analisi e i modelli predittivi si basino su informazioni coerenti, aggiornate e affidabili. Dati duplicati, disomogenei o incompleti possono portare a errori, inefficienze e decisioni sbagliate. Riunendo i dati in un data lake e utilizzando processi ETL ben progettati, le aziende possono ottenere una visione unificata delle proprie informazioni e utilizzarle in modo più efficace, sia per analisi tradizionali che per progetti di IA e [[Machine Learning|machine learning]].

## Pipeline di dati su AWS

Una **pipeline di dati** è un insieme di processi automatizzati che consente di acquisire, catalogare, trasformare e distribuire i dati dalle loro origini alle destinazioni finali. Automatizzando queste fasi, le pipeline riducono le attività manuali e il rischio di errori, garantendo che i dati arrivino nel formato giusto e nel posto giusto, pronti per l’analisi o per l’uso in modelli di intelligenza artificiale.

Una pipeline di dati tipica si compone di quattro fasi principali, assicurandosi che i dati passino in modo ordinato dallo stato grezzo a quello utile:

*   **Acquisizione**: i dati vengono raccolti da fonti come applicazioni, [[Database|database]] o sensori.
*   **Archiviazione**: i dati vengono salvati in un sistema centrale, come un data lake o un data warehouse.
*   **Trasformazione e catalogazione**: i dati vengono puliti, strutturati e descritti con metadati per renderli comprensibili e riutilizzabili.
*   **Analisi e visualizzazione**: i dati pronti vengono interrogati o visualizzati tramite strumenti analitici e di business intelligence.

Su AWS, Amazon S3 è spesso usato come data lake, mentre Amazon Redshift è la soluzione di riferimento per i data warehouse ad alte prestazioni.

L’acquisizione dei dati consiste nel trasferire i dati dalle origini (come applicazioni, sensori o [[Database|database]]) verso un sistema di archiviazione centrale. Esistono due principali modalità:

*   **In tempo reale (streaming)**: i dati vengono acquisiti continuamente, con latenza minima, ideale per casi come finanza, gaming o IoT.
*   **In batch**: i dati vengono raccolti e inviati a intervalli regolari, utile per sistemi che tollerano un certo ritardo.

AWS offre servizi per entrambe le modalità:

*   **Amazon Kinesis** è un servizio progettato per l’acquisizione di dati in tempo reale. Permette di raccogliere, elaborare e analizzare flussi di dati con latenza minima, consentendo a più applicazioni di accedere simultaneamente agli stessi dati. È ideale per casi d’uso in cui la velocità è essenziale, come il monitoraggio del mercato azionario, le analisi di [[Sicurezza|sicurezza]] o il tracking dei click utente su siti web.
*   **Amazon Data Firehose** è una soluzione ETL per l’acquisizione di dati quasi in tempo reale, completamente gestita, che raccoglie i dati da una fonte e li consegna direttamente a una destinazione come Amazon S3, Redshift o OpenSearch. A differenza di Kinesis Streams, Firehose non richiede la gestione dell’infrastruttura né la scrittura di codice per elaborare i flussi: tutto avviene in automatico. È ideale per aziende che vogliono raccogliere e archiviare rapidamente dati provenienti da dispositivi, app o sensori, ad esempio in una rete di smart home.

La **catalogazione dei dati** serve a creare un inventario centralizzato che descrive dove si trovano i dati e quali caratteristiche possiedono, tramite metadati (come formato, origine o ultima modifica). AWS offre questa funzionalità tramite **AWS Glue Data Catalog**, un repository centralizzato che permette di indicizzare, cercare e gestire i set di dati in modo efficiente. Proprio come le informazioni in una foto (data, luogo, formato), i metadati rendono i dati aziendali facili da scoprire e da utilizzare da diversi team o applicazioni.

Per pulire e trasformare i dati, AWS mette a disposizione **AWS Glue**, un servizio ETL completamente gestito che automatizza i processi di estrazione, trasformazione e caricamento. AWS Glue offre interfacce visive per creare job ETL senza codice, pianificazione automatica dei job per aggiornare i dati periodicamente e supporto per diversi formati (JSON, Parquet, CSV, ecc.). In contesti più complessi o con grandi volumi di dati, le aziende possono utilizzare Amazon EMR, che permette di elaborare i dati con framework come Apache Spark o Hadoop.

**Amazon EMR (Elastic MapReduce)**, invece, è una piattaforma più flessibile e potente per l’elaborazione su larga scala di big data, che utilizza framework open source come Spark, Hadoop e Hive. Glue è la scelta giusta per processi ETL standard e gestiti, mentre EMR è perfetto per analisi personalizzate, workflow complessi e ambienti di data engineering avanzati.

Con **Amazon Athena**, un servizio serverless e completamente gestito, è possibile eseguire query SQL direttamente sui dati archiviati in Amazon S3, senza doverli spostare in un [[Database|database]]. Athena supporta origini dati relazionali, non relazionali e personalizzate, e può anche connettersi a fonti esterne. È ideale per analisi esplorative rapide e ad hoc, poiché elimina la necessità di configurare infrastrutture o cluster.

**Amazon Redshift** è un data warehouse completamente gestito progettato per eseguire query complesse su set di dati molto grandi e per gestire carichi di lavoro analitici intensivi. A differenza di Athena, Redshift memorizza i dati in modo ottimizzato per le prestazioni e permette di eseguire analisi continue e multidimensionali con latenze ridotte. È la scelta ideale per ambienti di business intelligence, reportistica aziendale e analisi storiche su larga scala.

Due strumenti principali supportano la visualizzazione e l’analisi interattiva:

*   **Amazon QuickSight**: offre business intelligence unificata e dashboard interattive. Permette anche di utilizzare il linguaggio naturale tramite Amazon Q in QuickSight, così da porre domande dirette ai dati.
*   **Amazon OpenSearch**: è ottimo per la ricerca, il monitoraggio e l’analisi in tempo reale di log e metriche operative. È spesso usato per osservabilità, analisi dei log e ricerche testuali su larga scala.

## Analisi dei dati e intelligenza artificiale/[[Machine Learning|machine learning]]

Un’azienda di e-commerce utilizza una pipeline di dati automatizzata su AWS per mantenere aggiornato il proprio modello di [[Machine Learning|machine learning]].

I dati dei clienti vengono salvati in **Amazon DynamoDB** e inviati a **Amazon Kinesis Data Streams**, che li inoltra a **Amazon Data Firehose**. Firehose invoca una funzione **AWS Lambda** per trasformare i dati da JSON a CSV e li archivia nel data lake su **Amazon S3**. Successivamente, **AWS Glue Data Catalog** organizza e descrive i dati rendendoli disponibili per le query. I data scientist utilizzano **Amazon Athena** per analizzare i dati direttamente in S3 tramite SQL standard. Infine, **Amazon SageMaker** accede agli stessi dati per addestrare ed aggiornare automaticamente i modelli di [[Machine Learning|machine learning]].

La pipeline è completamente gestita, scalabile e programmabile, riducendo gli errori manuali e ottimizzando il flusso dei dati. Grazie a questa automazione, l’azienda può reagire in tempo reale ai comportamenti dei clienti e migliorare continuamente le proprie previsioni.