## Tipi di istanze EC2
Le istanze EC2 sono molto convenienti perché costruire server fisici significa impegnare un investimento rigido e costi di gestione molto importanti. Quando configuri un'istanza EC2, puoi scegliere il sistema operativo con Amazon Machine Image (AMI) (tra Windows, Linux e altri sistemi operativi), di quali software abbiamo bisogno (tra applicazioni interne, web app, db o altri servizi di terze parti) e la dimensione di archiviazione.

I tipi di istanze EC2 offrono diverse combinazioni di CPU, memoria, archiviazione, capacità di rete e altro. Per distinguere questi diversi tipi di combinazioni, avremo:
*   **istanze per l'uso generico**: forniscono un buon equilibrio tra calcolo, memoria, risorse di rete che possono essere utilizzate per vari carichi di lavoro, come i servizi web;
*   **istanze ottimizzate per il calcolo**: ideali per attività ad alta intensità di calcolo, come servizi di gioco, calcolo ad alta prestazione, o HPC (High Performance Computing), e anche il modeling scientifico;
*   **istanze ottimizzate per la memoria**: ottime per le attività che richiedono molta memoria, come ad esempio i database ad alte prestazioni;
*   **istanze con calcolo accelerato**: ideali per l'elaborazione grafica, 3D rendering, computer vision, machine learning e altro ancora, e questo perché hanno una scheda grafica GPU;
*   **istanze ottimizzate per l'archiviazione**: utili per carichi di lavoro che richiedono prestazioni elevate per dati memorizzati localmente, come ad esempio il data warehousing.

## Prezzi EC2
Per le istanze EC2, esistono diverse opzioni di fatturazione:
*   **istanze on-demand**: ideali per carichi di lavoro irregolari a breve termine che non possono essere interrotti. Non si applicano costi anticipati o contratti minimi. Le istanze vengono eseguite continuativamente fino a quando non vengono arrestate e si paga solo per il tempo di calcolo utilizzato;
*   **istanze Saving Plan**: offrono prezzi bassi sulle istanze EC2 in cambio di un impegno di quantità costante di utilizzo per un periodo di 1 o 3 anni;
*   **istanze riservate**: rappresentano uno sconto di fatturazione applicato all'utilizzo di istanze on-demand nel tuo account, con l'opzione di acquisto per un periodo di 1 o 3 anni. Al termine del periodo di un'istanza riservata, puoi continuare a utilizzare l'istanza Amazon EC2 senza interruzioni ma ti verranno addebitate le tariffe on-demand fino a quando non viene terminata un'istanza o acquistata una nuova. Sono disponibili due tipi di istanze riservate:
    *   **istanze riservate standard**: ideali se conosci il tipo e la dimensione dell'istanza EC2 di cui hai bisogno per le applicazioni a utilizzo costante e continuato e la regione AWS nella quale pianifichi di eseguirle;
    *   **istanze riservate modificabili**: ideali se hai necessità di eseguire le istanze EC2 in zone di disponibilità differenti o in diversi tipi di istanze;
*   **istanze Spot**: ideali per carichi di lavoro con orari di inizio e fine flessibili o che resistono alle interruzioni, richiedendo capacità di calcolo di Amazon EC2 non utilizzata, risparmiando fino al 90% sul prezzo on-demand. Il problema con queste istanze è che AWS può recuperarle in qualsiasi momento, dando un preavviso di 2 minuti per completare il lavoro e salvare lo stato;
*   **host dedicati**: prenota un intero server fisico per l'uso esclusivo. Questa opzione offre il pieno controllo ed è ideale per carichi di lavoro con rigide esigenze di sicurezza o licenza;
*   **istanze dedicate**: paga per le istanze eseguite su hardware dedicato esclusivamente al tuo account. Questa opzione fornisce l'isolamento dagli altri clienti AWS.

## Dimensionamento EC2
La scalabilità è uno dei tanti vantaggi di AWS ed implica iniziare solo con le risorse necessarie e progettare l'architettura in modo da rispondere automaticamente alle mutevoli esigenze aumentando o diminuendo le risorse. Di conseguenza, paghi solo le risorse che utilizzi.
Amazon EC2 Auto Scaling permette di avviare automaticamente il processo di dimensionamento: in questo modo, è possibile mantenere una maggiore disponibilità delle applicazioni. Questo strumento consente di aggiungere o rimuovere automaticamente istanze Amazon EC2 in risposta alle mutevoli richieste delle applicazioni.

Abbiamo due approcci:
*   **scalabilità dinamica**: si risponde all'evoluzione della domanda;
*   **scalabilità predittiva**: si pianifica automaticamente l'utilizzo del numero corretto di istanze Amazon EC2 in base alla domanda prevista.

Amazon EC2 Auto Scaling permette di gestire la richiesta in crescita ridimensionando:
*   **dimensionare verticalmente**: aggiungere più potenza alle macchine in esecuzione;
*   **dimensionare orizzontalmente**: aggiungere nuove macchine al sistema e distribuire il carico tra di esse.

Al momento della creazione di un gruppo Amazon EC2 Auto Scaling, si impostano alcuni parametri:
*   **capacità minima**: numero di istanze Amazon EC2 che vengono avviate immediatamente dopo la creazione del gruppo Auto Scaling;
*   **capacità desiderata**: numero di istanze Amazon EC2 che vuoi mantenere in esecuzione all’interno di un gruppo Auto Scaling. Se non specifichi un valore, il sistema imposta automaticamente la capacità desiderata pari alla capacità minima del gruppo Auto Scaling;
*   **capacità massima**: limite superiore di istanze Amazon EC2 che un gruppo Auto Scaling può avviare.

## Elastic Load Balancing (ELB)
Un bilanciatore del carico è un'applicazione che accetta le richieste e le indirizza verso le istanze in modo da bilanciare il carico per ogni istanza.

In AWS, il lavoro pesante indifferenziato di bilanciamento del carico viene affrontato dall’Elastic Load Balancing (ELB): viene eseguito a livello regionale piuttosto che su una singola istanza EC2, il servizio è automaticamente altamente disponibile, senza ulteriori sforzi da parte dello sviluppatore.

ELB è automaticamente scalabile. Quando la flotta di istanze EC2 si dimensiona e vengono aggiunte delle istanze, il servizio di dimensionamento automatico comunica al servizio ELB che è pronto a gestire il traffico. Quando la flotta di istanze EC2 si dimensiona in riduzione, ELB prima ferma tutto il traffico e attende il completamento delle richieste, una volta eseguita questa operazione, il motore di dimensionamento automatico può terminare le istanze non richieste.

ELB centralizza la gestione del traffico backend permettendo alle istanze frontend di comunicare tramite un unico URL. In questo modo, non devono conoscere il numero o l’identità delle istanze backend. ELB distribuisce automaticamente le richieste verso l’istanza meno carica e, quando una nuova istanza backend diventa disponibile, basta segnalarlo a ELB senza aggiornare le istanze frontend.

## Messaggistica e accodamento
Un'architettura strettamente accoppiata dipende dal fatto che tutti i componenti siano sempre disponibili e sincronizzati. Se anche solo uno di essi smette di funzionare o rallenta (come il barista che va in pausa), l’intero processo si blocca e gli altri componenti non riescono a svolgere il loro lavoro. Questo rende il sistema fragile e difficile da scalare.

Un’architettura disaccoppiata, più affidabile rispetto all’architettura strettamente accoppiata, è un sistema in cui i componenti non dipendono direttamente l’uno dall’altro: comunicano tramite code o servizi intermedi, così eventuali errori o ritardi di un componente non bloccano gli altri, garantendo maggiore affidabilità e scalabilità.

Nello specifico, la coda agisce come un buffer che riceve i messaggi e li conserva fino a quando il componente che deve elaborarli è pronto. In questo modo, il mittente (ad esempio il cassiere) non deve aspettare che il destinatario (il barista) sia disponibile. I messaggi non vengono persi e possono essere elaborati in modo asincrono, riducendo i colli di bottiglia e aumentando l’efficienza del sistema.

Quando si progettano applicazioni su AWS, è possibile adottare una delle due architetture descritte che soddisfano funzioni diverse. Due servizi facilitano l'integrazione delle applicazioni:
*   **Amazon Simple Queue Service (Amazon SQS)**: servizio gestito che permette di inviare, memorizzare e ricevere messaggi in coda tra applicazioni o componenti software. Garantisce che i messaggi non vadano persi e che siano conservati fino alla loro elaborazione. È scalabile, sicuro e non richiede di gestire server o infrastrutture. In pratica, funziona come la bacheca degli ordini nella caffetteria, dove ogni richiesta rimane registrata finché non viene preparata;
*   **Amazon Simple Notification Service (Amazon SNS)**: servizio di messaggistica basato sul modello publish-subscribe. Permette di inviare un messaggio a un “topic”, che viene automaticamente distribuito a tutti i sottoscrittori di quel topic. Può essere usato sia per integrare altri servizi (come SQS o Lambda), sia per inviare notifiche direttamente agli utenti finali tramite SMS, e-mail o notifiche push. È utile quando lo stesso messaggio deve raggiungere più destinatari contemporaneamente.

## Servizi di calcolo aggiuntivi
Le istanze EC2 sono macchine virtuali eseguite su AWS. Sono molto flessibili, affidabili e scalabili, quindi adatte a vari scenari: da un semplice server web fino a cluster di calcolo ad alte prestazioni. Con EC2 l’utente mantiene il controllo sul sistema operativo e sull’ambiente, ma deve occuparsi della gestione quotidiana, come patch di sicurezza, aggiornamenti software e dimensionamento delle istanze. È comunque più semplice che gestire un’infrastruttura on-premise, ma richiede comunque responsabilità operative.

Per questo viene in aiuto il calcolo “serverless” con cui indichiamo che lo sviluppatore non gestisce né vede l’infrastruttura sottostante: provisioning, scalabilità e alta disponibilità sono responsabilità di AWS. L’utente si concentra solo sul codice e sull’applicazione. Un esempio è AWS Lambda, che esegue funzioni in risposta a trigger e gestisce automaticamente scalabilità e disponibilità: che ci sia una o mille richieste, le funzioni vengono eseguite senza intervento manuale. È ideale per elaborazioni brevi (fino a 15 minuti), come backend web o elaborazioni di eventi. I componenti chiave di AWS Lambda sono la funzione, i trigger e i runtime.

Un container è un pacchetto che include applicazione, dipendenze e configurazioni, e gira in isolamento sopra un host (solitamente un’istanza EC2). I container permettono portabilità e coerenza tra ambienti. Su AWS i container non si gestiscono singolarmente, ma in cluster distribuiti. Per orchestrare i container esistono strumenti complessi, ma AWS offre due servizi gestiti: Amazon ECS (Amazon Elastic Container Service), che semplifica l’orchestrazione con strumenti AWS, ed Amazon EKS (Amazon Elastic Kubernetes Service), che fornisce orchestrazione Kubernetes.

AWS Fargate è una piattaforma serverless per l’esecuzione di container con ECS o EKS: non è necessario gestire le istanze EC2 sottostanti, né preoccuparsi del sistema operativo: l’utente definisce solo i container e le risorse necessarie. Conviene usarlo quando non serve controllo diretto sul sistema operativo, ma si vuole beneficiare della semplicità e della scalabilità automatica offerte dal modello serverless.

Come scegliere tra EC2, Lambda, ECS/EKS e Fargate?
*   **EC2**: se serve il controllo completo del sistema operativo e dell’infrastruttura (Linux o Windows), adatto a workload tradizionali;
*   **Lambda**: se servono funzioni brevi e reattive a eventi, senza gestire server o scalabilità;
*   **ECS/EKS su EC2**: se si vogliono gestire container mantenendo comunque il controllo sulle istanze sottostanti;
*   **ECR**: archivia, gestisce e distribuisce immagini di container conformi alla Open Container Initiative (OCI);
*   **Fargate**: se si vogliono eseguire container in modo serverless, delegando completamente ad AWS la gestione dell’infrastruttura;
*   **Batch AWS**: progettato per carichi di lavoro in batch su larga scala e può gestire e ridimensionare automaticamente le risorse di calcolo per le code di processi.
