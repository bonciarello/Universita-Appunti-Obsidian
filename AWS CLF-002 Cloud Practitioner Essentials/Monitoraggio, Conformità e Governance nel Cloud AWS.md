## Importanza del monitoraggio

Il **monitoraggio delle risorse cloud** è importante. Permette di osservare e analizzare continuamente l'attività del sistema, il traffico di rete e gli eventi di sicurezza per rilevare potenziali minacce o anomalie. Il monitoraggio e l'osservabilità sono componenti essenziali per garantire la sicurezza, la disponibilità, l'affidabilità e le prestazioni dei carichi di lavoro e dei dati nel cloud.

Il monitoraggio viene eseguito utilizzando appositi strumenti in tempo reale, dashboard e raccolta e analisi dei log. Nelle lezioni seguenti individuerai diversi strumenti di monitoraggio e a che cosa servono.

### Amazon CloudWatch

**Amazon CloudWatch** è un servizio di monitoraggio che fornisce visibilità in tempo reale sulle risorse AWS, sulle applicazioni e sui sistemi on-premises collegati. Ti consente di osservare le prestazioni, l’utilizzo delle risorse e lo stato operativo delle infrastrutture per garantire un funzionamento efficiente e affidabile.

CloudWatch offre diverse funzionalità integrate che lavorano insieme per migliorare il monitoraggio e l’automazione:

*   **Metriche CloudWatch**: raccolgono e memorizzano dati sulle prestazioni e sull’utilizzo di servizi e risorse AWS.
*   **Allarmi CloudWatch**: consentono di impostare soglie per le metriche e attivare automaticamente notifiche o modifiche alle risorse.
*   **Dashboard CloudWatch**: permettono di creare viste personalizzate per monitorare più risorse in un’unica interfaccia.
*   **Log CloudWatch**: centralizzano i log provenienti da sistemi, applicazioni e servizi AWS per semplificare l’analisi e la diagnostica.

CloudWatch consente di monitorare e analizzare le risorse in modo efficiente grazie a una visualizzazione integrata e all’automazione dei processi. Favorisce un approccio proattivo alla gestione, aiutando a identificare problemi prima che incidono sulle prestazioni e a ottimizzare l’uso delle risorse in base ai dati raccolti.

CloudWatch è particolarmente utile per monitorare infrastrutture AWS e risolvere problemi operativi. Ad esempio, può essere impiegato per rilevare picchi di utilizzo anomali, analizzare le cause di errori applicativi o automatizzare azioni correttive quando vengono superate determinate soglie di performance o disponibilità.

### AWS CloudTrail

In un ambiente cloud ibrido, le organizzazioni devono poter sapere chi ha effettuato modifiche, quando e su quali risorse, sia nel cloud che on-premises. Questo è essenziale per risolvere problemi operativi, garantire la conformità e mantenere un elevato livello di sicurezza.

**AWS CloudTrail** è un servizio che tiene traccia dell’attività degli utenti e dell’utilizzo delle API in AWS, negli ambienti on-premises e persino con altri provider di servizi cloud. Registra ogni chiamata API effettuata, con informazioni su chi ha effettuato l’azione, quando è avvenuta e quali risorse sono state coinvolte.

CloudTrail offre numerosi vantaggi chiave:

*   **Verifica e conformità**: fornisce prove documentate delle attività per soddisfare gli audit e le normative di sicurezza.
*   **Monitoraggio della sicurezza**: consente di identificare azioni sospette o non autorizzare sugli account e sulle risorse.
*   **Risoluzione dei problemi**: aiuta a diagnosticare eventi operativi analizzando chi ha eseguito determinate operazioni e in quali circostanze.

CloudTrail è ideale per:

*   **Conformità e audit**: garantisce la tracciabilità completa delle azioni per soddisfare requisiti normativi come PCI-DSS o HIPAA.
*   **Individuazione di incidenti di sicurezza**: aiuta a rilevare e investigare attività sospette.
*   **Risoluzione dei problemi operativi**: permette di capire cosa è successo e perché, ricostruendo la sequenza degli eventi.

Gli **eventi CloudTrail** registrano in modo dettagliato tutte le azioni compiute nell’account AWS come chiamate API, operazioni nella console o azioni eseguite da servizi AWS. CloudTrail conserva una cronologia degli eventi di gestione degli ultimi 90 giorni, consultabile gratuitamente nella console. Gli utenti possono anche ricercare e scaricare i dati per analisi più approfondite.

I **log CloudTrail** vengono generati dagli eventi e archiviati come file nel bucket Amazon S3. Questi log sono conservati in modo sicuro e immutabile, così da poter essere utilizzati come prova di conformità con normative come PCI (Payment Card Industry) e HIPAA (Healthcare Insurance Portability and Accountability Act). Centralizzare i log in S3 garantisce una tracciabilità a lungo termine e una facile integrazione con strumenti di analisi o SIEM.

**CloudTrail Insights** analizza automaticamente i pattern di comportamento normali nell’uso delle API AWS (come volumi di chiamate o tassi di errore). Quando rileva anomalie o deviazioni significative da questi modelli, genera eventi “Insights” che segnalano attività insolite o potenzialmente dannose. Puoi abilitare CloudTrail Insights per individuare comportamenti anomali, come un picco improvviso di chiamate API o tentativi ripetuti di accesso non riusciti, migliorando così il rilevamento delle minacce e la risposta agli incidenti.

## Conformità

La **conformità** significa assicurarsi che dati, risorse e processi rispettino tutte le normative legali, gli standard di settore e le policy interne relative alla sicurezza e alla protezione dei dati. In AWS, la conformità è parte integrante della governance aziendale: garantisce che l’organizzazione utilizzi il cloud in modo sicuro, etico e verificabile, riducendo i rischi di violazioni o sanzioni.

AWS fornisce numerosi strumenti e controlli per aiutarti a raggiungere e mantenere la conformità. In particolare:

*   Offre controlli di sicurezza aggiornati e utilizzati internamente da AWS stessa.
*   Fornisce convalide di terze parti per migliaia di requisiti globali (ISO, SOC, PCI, HIPAA, ecc.).
*   Semplifica e automatizza la gestione della conformità, riducendo la complessità manuale.
*   Consente l’accesso on-demand a report di conformità tramite servizi dedicati.

**AWS Artifact** è un servizio gratuito che fornisce accesso on-demand ai report AWS di sicurezza e conformità e ad alcuni accordi online selezionati. È una piattaforma centrale che consente di gestire la conformità in modo trasparente, eliminando la necessità di richiedere manualmente i documenti a AWS.

AWS Artifact offre due categorie principali:

*   **Accordi di AWS Artifact**: consentono alle organizzazioni di esaminare, accettare e gestire accordi legali con AWS, relativi all’utilizzo di dati sensibili o regolamentati.
*   **Report di AWS Artifact**: forniscono documenti di conformità verificati da revisori di terze parti. Questi report attestano che AWS ha superato verifiche indipendenti relative a standard di sicurezza e privacy a livello globale, regionale e settoriale (es. ISO 27001, SOC 2, PCI DSS, ecc.).

AWS mette a disposizione diversi portali e documenti ufficiali per aiutarti a comprendere e gestire la conformità:

*   **Portale per la conformità AWS**: raccoglie risorse, testimonianze dei clienti e guide su governance e verifica.
*   **Programmi per la conformità AWS**: elenca i principali programmi normativi e gli standard supportati.
*   **Whitepaper e guide di sicurezza**: includono risposte alle domande più frequenti, panoramiche sui rischi e checklist per la verifica.
*   **Risorse formative**: contengono best practice, guide operative e strumenti pratici per la conformità quotidiana.

### Verifica della conformità delle risorse AWS

La maggior parte delle aziende adotta linee guida di configurazione specifiche per garantire sicurezza, efficienza e conformità normativa. Tuttavia, poiché le risorse AWS possono essere create e modificate rapidamente, è essenziale disporre di strumenti che permettano di valutare e monitorare automaticamente le configurazioni, verificando che rispettino le policy aziendali.

**AWS Config** è un servizio che consente di valutare, verificare e monitorare continuamente le configurazioni delle risorse AWS. Tiene traccia delle modifiche e consente di confrontare le configurazioni correnti con uno stato desiderato (compliance baseline).

I vantaggi di AWS Config sono:

*   Valutazione automatica delle configurazioni rispetto alle regole aziendali.
*   Gestione delle modifiche: monitora quando e come una risorsa viene modificata.
*   Facilitazione della risoluzione dei problemi: puoi individuare rapidamente la causa di una configurazione errata.
*   Correzione semplificata: puoi integrare AWS Config con automazioni (es. AWS Systems Manager o Lambda) per riportare le risorse in stato conforme.

**Gestione audit AWS** è un servizio che verifica automaticamente e continuamente l’utilizzo delle risorse AWS per facilitare la valutazione del rischio e della conformità. Aiuta le organizzazioni a raccogliere prove, gestire i dati di verifica e semplificare gli audit interni ed esterni.

I vantaggi di Gestione audit AWS sono:

*   Raccolta automatica delle prove (log, metriche, configurazioni, ecc.) senza intervento manuale.
*   Collaborazione semplificata tra team di sicurezza, conformità e audit.
*   Maggiore integrità delle verifiche grazie a un accesso di sola lettura ai dati raccolti.
*   Riduzione del tempo e dei costi necessari per preparare o condurre audit di conformità.

## AWS Organizations

Quando un’azienda cresce, tende a suddividere le risorse AWS tra diversi team, progetti o reparti. Questa separazione consente di gestire in modo più sicuro i permessi, isolare i costi, applicare policy specifiche per area o funzione. Tuttavia, più account significano anche maggiore complessità nella gestione centralizzata di sicurezza, costi e governance.

**AWS Organizations** è un servizio che permette di gestire centralmente più account AWS all’interno di un’unica struttura aziendale. Consente di applicare policy di governance, automatizzare la creazione di nuovi account e controllare l’accesso ai servizi AWS in modo uniforme.

I vantaggi principali di AWS Organizations?

*   **Scalabilità rapida**: puoi creare nuovi account AWS in modo programmatico per team o progetti.
*   **Gestione centralizzata delle policy**: tramite le Service Control Policies (SCP) puoi definire quali servizi o azioni sono consentiti in ciascun account.
*   **Ottimizzazione dei costi**: puoi gestire la fatturazione consolidata, analizzando e ottimizzando le spese complessive.
*   **Maggiore controllo e sicurezza**: puoi assegnare permessi e risorse condivise in modo coerente tra gli account.

Si utilizza AWS Organizations per automatizzare la creazione di account per nuovi reparti, team o clienti, gestire in modo centralizzato le policy di sicurezza e accesso, controllare l’utilizzo dei servizi AWS per evitare errori o violazioni di policy e condividere risorse comuni, come VPC, Route53 o licenze, tra più account.

I concetti chiave di AWS Organizations sono:

*   **Organizzazione (Organization)**: è l’insieme di tutti gli account AWS gestiti centralmente.
*   **Elemento root**: è il livello più alto della gerarchia; tutti gli account e le unità organizzative (UO) si trovano al suo interno.
*   **Unità organizzativa (UO)**: è un gruppo logico di account. Le UO possono essere annidate, creando una struttura ad albero (es. per reparti o funzioni aziendali).
*   **Account membro**: è un account AWS gestito all’interno dell’organizzazione (può avere policy specifiche).
*   **Service Control Policy (SCP)**: è un tipo di policy utilizzato per limitare o consentire l’accesso a determinati servizi o azioni AWS negli account dell’organizzazione.

Quando pianifichi la tua organizzazione, considera i requisiti di sicurezza, governance e conformità di ogni reparto, raggruppa i team o i progetti con obiettivi simili in unità organizzative (UO) e applica policy mirate a ogni livello (root, UO, account).

## Governance

Man mano che le aziende crescono e si espandono nel cloud, la gestione di più account, risorse e licenze software può diventare complessa. AWS offre diversi servizi pensati per semplificare la governance e mantenere il controllo sull’ambiente aziendale. Tra questi, tre sono particolarmente utili: AWS Control Tower, il Catalogo dei servizi AWS e lo Strumento AWS di gestione delle licenze.

**AWS Control Tower** consente di applicare e gestire regole di governance per la sicurezza, le operazioni e la conformità su larga scala. Attraverso controlli preconfigurati, il servizio aiuta a configurare rapidamente ambienti con più account, fornendo automazione e governance integrata. È una soluzione che semplifica la creazione e l’amministrazione di nuovi account AWS, garantendo che rispettino le best practice di sicurezza e conformità. In questo modo le organizzazioni possono distribuire applicazioni in modo più rapido e sicuro, mantenendo un controllo centralizzato.

Il **Catalogo dei servizi AWS** permette di creare, condividere e organizzare risorse AWS approvate in un catalogo personalizzato. Questo approccio riduce i tempi di provisioning e aiuta i team a distribuire in autonomia servizi e risorse predefinite, senza rinunciare alla governance. È utile per garantire che le nuove distribuzioni rispettino le configurazioni e le policy aziendali, mantenendo coerenza e sicurezza in tutti gli account. Grazie al catalogo, le aziende possono accelerare la creazione di ambienti standardizzati, come pipeline di integrazione continua e distribuzione continua, migliorando l’efficienza complessiva.

Infine, lo **Strumento AWS di gestione delle licenze** semplifica la gestione e il monitoraggio delle licenze software, aiutando le aziende a ottimizzare i costi e a ridurre il rischio di mancata conformità. Supporta il modello Bring Your Own License, che consente di utilizzare le licenze già acquistate da fornitori come Microsoft all’interno dei servizi AWS. Questo approccio garantisce maggiore flessibilità e consente di risparmiare evitando l’acquisto di nuove licenze. Il servizio offre inoltre strumenti di automazione per la distribuzione e l’attivazione dei diritti software, migliorando la visibilità e il controllo sulle licenze utilizzate.

### AWS Health

La **Dashboard AWS Health** serve a visualizzare informazioni sull'integrità specifiche dell'account e ottenere aggiornamenti sugli eventi in AWS Health. Puoi anche utilizzare AWS Health a livello di programmazione utilizzando l'API AWS Health, disponibile con Supporto AWS Premium.

Il vantaggio è che la Dashboard AWS Health fornisce informazioni preziose come origine dati per eventi e modifiche. Offre indicazioni tempestive e di utilità pratica per risolvere i problemi. Inoltre, aiuta a gestire l'integrità dei servizi ed è integrata e automatizzata per l'utilizzo su larga scala.

### AWS Trusted Advisor

Per garantire un ambiente cloud sicuro, performante e ottimizzato, è importante valutare continuamente la configurazione e l’utilizzo delle risorse AWS. La semplice implementazione iniziale non basta: la gestione e l’ottimizzazione devono essere processi costanti. In questo contesto, AWS offre strumenti che aiutano a monitorare, analizzare e migliorare costantemente l’ambiente, come AWS Trusted Advisor e il Sistema di analisi degli accessi IAM.

**AWS Trusted Advisor** è un servizio che consente di valutare continuamente l’ambiente AWS in base alle best practice in diverse aree, come sicurezza, prestazioni, ottimizzazione dei costi e resilienza. Fornisce controlli e raccomandazioni che aiutano a identificare potenziali problemi e a migliorare l’efficienza complessiva dell’infrastruttura. Tutti i piani di supporto AWS includono l’accesso a una selezione di controlli Trusted Advisor, mentre i piani più avanzati, come Business e Enterprise, offrono centinaia di controlli aggiuntivi. Questo servizio è particolarmente utile per mantenere allineato l’ambiente cloud alle best practice AWS, classificare in ordine di priorità le azioni da intraprendere e ottimizzare l’utilizzo delle risorse su larga scala.

Tuttavia, anche con Trusted Advisor, la gestione delle autorizzazioni degli utenti rimane una parte critica della sicurezza. È qui che entra in gioco il **Sistema di analisi degli accessi IAM**, un servizio progettato per aiutare a impostare, verificare e perfezionare le autorizzazioni in AWS Identity and Access Management. Questo strumento analizza in modo dettagliato chi ha accesso a cosa, individuando eventuali eccessi di privilegi e suggerendo come ridurli. Consente di verificare la corrispondenza tra le policy di accesso e gli standard di sicurezza aziendali, promuovendo il principio del minimo privilegio.