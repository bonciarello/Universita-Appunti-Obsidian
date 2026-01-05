## Infrastruttura globale di AWS

Le regioni sono gruppi di data center distribuiti in tutto il mondo. AWS le ha progettate per garantire ridondanza, resilienza e prossimità ai clienti, così che le aziende possano eseguire applicazioni vicino agli utenti senza dover gestire da sole i costi elevati di costruzione e manutenzione di data center duplicati, né gestire hardware, sicurezza, elettricità e personale.

I quattro fattori principali per scegliere una regione AWS sono:

*   **Governance e requisiti legali**: in alcuni casi i dati devono rimanere in un Paese specifico (es. dati bancari italiani in Italia).
*   **Latenza**: scegliere una regione vicina ai clienti riduce i tempi di risposta e migliora le prestazioni.
*   **Disponibilità dei servizi**: non tutti i servizi AWS sono disponibili ovunque, quindi può essere necessario scegliere una regione che supporta ciò che serve.
*   **Costo**: i prezzi variano da regione a regione, come avviene con beni e servizi nei diversi Paesi.

La latenza nella scelta di una regione è importante perché la distanza fisica tra il cliente e il server influisce sul tempo di risposta. Se i clienti si trovano in Italia ma l’applicazione gira in una regione negli Stati Uniti, il tempo di comunicazione sarà più alto, causando rallentamenti nell’esperienza utente.

AWS fornisce la **Pricing Calculator**, uno strumento online che consente di simulare configurazioni (es. EC2 in diverse regioni) e confrontare i prezzi. Questo permette alle aziende di stimare in anticipo le spese e scegliere la regione più conveniente per i propri servizi.

Un **Availability Zone (AZ, zona di disponibilità)** è un data center, o un gruppo di data center, all’interno di una regione AWS. Ogni AZ è progettata con alimentazione, rete e connettività ridondanti, così da essere isolata da eventuali guasti locali. Quando avvii un’istanza EC2, questa gira su un server fisico situato in una specifica AZ della regione scelta.

Le AZ sono separate da decine di chilometri proprio per evitare che un disastro naturale o un guasto su larga scala possa rendere indisponibile l’intera infrastruttura. Sono comunque abbastanza vicine da garantire latenze molto basse (pochi millisecondi), così che i servizi possano funzionare in sincronia tra più zone.

Se un’applicazione gira in una sola Availability Zone e questa diventa indisponibile, l’applicazione smette di funzionare perché tutta la sua capacità è concentrata in un unico punto. È per questo che AWS raccomanda di distribuire le applicazioni su almeno due AZ, così che, anche in caso di disastro, il servizio rimanga disponibile.

Molti servizi regionali, come **Elastic Load Balancer (ELB)**, operano automaticamente su più Availability Zone senza richiedere configurazioni aggiuntive. Questo significa che sono già intrinsecamente altamente disponibili, bilanciando il traffico tra istanze distribuite su diverse zone.

## Posizioni edge

Una **posizione edge (comunemente nota come Edge Location)** è un punto di presenza AWS distribuito in tutto il mondo che serve per avvicinare contenuti e servizi agli utenti finali.

Grazie alle Edge Location, i dati vengono consegnati più velocemente e con minore latenza, indipendentemente da dove si trovino i clienti. Se i clienti di un'azienda si trovano lontani dalla regione dove sono ospitati i dati, le richieste impiegano più tempo e l’esperienza utente peggiora. Le Edge Location consentono di avere copie cache dei contenuti più vicino agli utenti, riducendo la latenza e migliorando la velocità di accesso, anche quando i clienti sono distribuiti in diverse parti del mondo.

**Amazon CloudFront** è la rete CDN (Content Delivery Network) di AWS e permette di distribuire dati, applicazioni, video e API agli utenti con bassa latenza e alta velocità di trasferimento. Utilizza le Edge Location per fornire i contenuti in modo rapido e vicino agli utenti finali.

**Amazon Route 53** è il servizio DNS di AWS e funziona insieme alle Edge Location per indirizzare gli utenti verso la posizione web corretta. In questo modo, i clienti vengono instradati verso la risorsa più vicina o più performante, riducendo la latenza e migliorando l’affidabilità delle connessioni.

**AWS Outposts** è una soluzione che porta l’infrastruttura AWS direttamente all’interno dei data center dei clienti: più precisamente, si tratta di una “mini-regione” completamente gestita da AWS, installata fisicamente nell’edificio dell’azienda. È utile in casi particolari in cui i dati o i servizi devono rimanere on-premise per motivi legali, tecnici o di performance, pur mantenendo tutte le funzionalità dell’ambiente AWS.

In conclusione, i tre concetti chiave dell’infrastruttura globale di AWS sono:

*   **Regioni**: aree geografiche isolate che offrono i servizi AWS.
*   **Availability Zone (zone di disponibilità)**: data center fisicamente separati all’interno di ogni regione, che garantiscono resilienza e alta disponibilità.
*   **Edge Location (posizioni edge)**: punti distribuiti globalmente che eseguono servizi come CloudFront e Route 53 per avvicinare contenuti e applicazioni agli utenti in qualsiasi parte del mondo.

## Provisioning risorse AWS

In AWS tutto avviene tramite API (Application Programming Interface): ogni azione fatta in AWS corrisponde a una chiamata API. Questo approccio fornisce un modo standardizzato e programmabile per gestire risorse e servizi, permettendo automazione, scalabilità e riduzione degli errori manuali.

I tre modi principali per interagire con AWS sono:

*   **AWS Management Console**: interfaccia grafica basata su browser che permette di gestire risorse AWS in modo visivo e intuitivo. È utile soprattutto per chi inizia ad usare AWS, per creare ambienti di test, monitorare le risorse, visualizzare le fatture e per chi non ha competenze tecniche avanzate.
*   **AWS Command Line Interface (CLI)**: consente di interagire con AWS dal terminale eseguendo comandi testuali e permette di automatizzare attività ripetitive tramite script, riduce il rischio di errori manuali e rende le operazioni ripetibili e veloci, particolarmente utile in ambienti di produzione.
*   **AWS Software Development Kits (SDK)**: librerie che permettono di interagire con AWS tramite diversi linguaggi di programmazione (Python, Java, JavaScript, ecc.). Consentono di integrare direttamente i servizi cloud nelle applicazioni, semplificando lo sviluppo di soluzioni cloud-native automatizzate e personalizzate.

Gli strumenti di gestione che permettono di automatizzare il provisioning e la configurazione delle risorse AWS sono:

*   **AWS Elastic Beanstalk**: servizio che consente di distribuire applicazioni fornendo solo il codice e le configurazioni necessarie. Si occupa automaticamente di creare e gestire risorse come EC2, load balancer e scaling, evitando all’utente di doverle configurare manualmente. In questo modo ci si può concentrare sullo sviluppo dell’applicazione piuttosto che sull’infrastruttura.
*   **AWS CloudFormation**: strumento di Infrastructure as Code (IaC) che permette di definire infrastrutture AWS tramite file testuali in JSON o YAML, chiamati template. Con un template si descrive cosa si vuole ottenere e CloudFormation si occupa di creare e configurare automaticamente tutte le risorse necessarie, riducendo errori manuali e aumentando la ripetibilità.
*   **Amazon Lightsail**: servizio semplificato con server privati virtuali (VPS), archiviazione e rete.

Con **Infrastructure as Code (IaC)** intendiamo la possibilità di creare ambienti identici in più regioni o account AWS, automatizzando completamente il processo di provisioning. Questo riduce il rischio di errori, garantisce coerenza tra ambienti di sviluppo e produzione e facilita la gestione di infrastrutture complesse.

La differenza tra gli strumenti base (Console, CLI, SDK) e quelli avanzati (Elastic Beanstalk, CloudFormation) è che i primi permettono di gestire direttamente le risorse, rispettivamente in modo manuale (console), da terminale con comandi e script (CLI), o tramite codice (SDK) mentre le seconde sono strumenti di livello più alto che automatizzano e orchestrano il provisioning, riducendo la complessità e i rischi di errore. AWS Elastic Beanstalk è focalizzato sulle applicazioni, mentre AWS CloudFormation permette di definire l’intera infrastruttura come codice.