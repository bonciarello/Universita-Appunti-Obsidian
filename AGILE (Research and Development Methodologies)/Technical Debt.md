Con questo termine ci riferiamo all'insieme di scorciatoie o agli aspetti negativi che infettano il nostro codice, come ad esempio:
- **design inadatto (cattivo):** un design che una volta aveva senso ma non ha più;
- **difetti:** problemi noti nel software per i quali non abbiamo ancora investito tempo nella rimozione;
- **copertura dei test insufficiente:** aree in cui sappiamo che dovremmo eseguire più test, ma non lo facciamo;
- **test manuali eccessivi:** test manuali quando dovremmo davvero avere test automatizzati;
- **scarsa integrazione e gestione dei rilasci:** esecuzione di queste attività in modo dispendioso in termini di tempo e soggetto a errori;
- **mancanza di esperienza con la piattaforma:** ad esempio applicazioni scritte in COBOL ma non abbiamo molti programmatori esperti;

e molti altri, perché il termine debito tecnico oggi è davvero usato come *segnaposto per un problema multidimensionale*.

Talvolta, contrarre questi **debiti** è inevitabile: inevitabili debiti tecnici maturano indipendentemente dalle misure preventive che adottiamo. Il debito tecnico ingenuo deriva dall'immaturità dei membri del team, dell'organizzazione e/o dei processi. Il debito strategico è qualcosa che potremmo scegliere di assumere quando i benefici derivanti dall'accumulo del debito superano sostanzialmente il costo del debito. Ci sono numerosi punti:
- *pressione per rispettare una scadenza:* provoca debito tecnico sia strategico che ingenuo;
- *tentativo di accelerare la velocità:* al team che esegue il lavoro viene detto di accelerare la propria velocità per raggiungere la data di rilascio desiderata;
- *mito, meno test possono accelerare la velocità:* il mito è che il test sia un sovraccarico aggiuntivo. Ridurre i test aumenterà il debito e ci farà rallentare;
- *il debito si accumula rapidamente in aggiunta al debito tecnico esistente:* il debito tecnico inizia a crescere, iniziano a manifestarsi conseguenze economicamente dannose.

Non sempre contrarre un debito gioca a nostro svantaggio, anzi questo può avere un utilizzo **strategico**. Basti pensare all'esigenza di accelerare determinate parti dello sviluppo per raggiungere degli obiettivi immediati.  

Ciononostante ci sono alcune ripercussioni:
- il debito cresce in maniera imprevedibile e non lineare. Si arriva ad un punto, comunemente chiamato **"tipping point"**, in cui anche la modifica più insignificante potrebbe compromettere tutto. Questo comporta un innalzamento del rischio di business;
- aumento dei tempi di consegna, naturalmente è necessario "ripagare" questi debiti e automaticamente si va ad intaccare lo scheduling previsto;
- aumento dei bug e di difetti del codice con conseguenti problemi di manutenibilità;
- aumento dei costi di sviluppo e di supporto;
- atrofizzazione del prodotto e diminuzione della prevedibilità: per risolvere i problemi è necessario diminuire il numero di nuove feature rilasciate. Oltreché avere numerosi debiti aumenta il margine di incertezza;
- diminuzione dell'interesse del customer, diminuzione delle performance, e frustrazione universale: andando a rallentare l'intera catena di lavoro gli stessi componenti del team oltre che il customer risultano scontenti e non motivati a portare avanti il progetto.

## Far fronte al debito
Per far fronte al debito contratto ci sono un insieme di strategie, tra cui:
- *utilizzare le good-practice:* sfruttare il test driven development, refactoring (*regola del boy-scout*, cioè miglioro il codice ogni volta che ci metto mano). Migliorare la definizione di **completato**;
- *cercare di darne una rappresentazione tangibile:* generalmente si realizza andando a realizzare un *Tecnical Debt Backlog* associato al product backlog.

Inoltre non tutto il debito contratto deve essere necessariamente ripagato. Per esempio, nello sviluppo dei prototipi o di prodotti che hanno vita breve, è inutile andare a pagarne i debiti poiché sono stati concepiti per essere *"usa e getta"*.  

Sarebbe opportuno inoltre: 
- limitare il numero di grandi investimenti e quindi andare a pagare il debito in maniera incrementale;
- cercare di focalizzarsi sui debiti che comportano un alto rischio o un alto interesse;
- ripagare il debito durante lo sviluppo di feature di valore per il customer.

I vantaggi del rimborso durante l'esecuzione del lavoro prezioso del cliente sono:
- allinea il lavoro di riduzione del debito con il lavoro prezioso per il cliente a cui il proprietario del prodotto può dare la giusta priorità;
- rende chiaro a tutti i membri del team di sviluppo che la riduzione del debito tecnico è una responsabilità condivisa e non qualcosa da rinviare e delegare a qualcuno o qualche altro team per ripulire;
- rafforza le capacità tecniche di prevenzione e rimozione dei debiti perché tutti possono esercitarle in ogni momento;
- ci aiuta a identificare le aree ad alto interesse su cui concentrare il servizio tecnico del debito;
- evita lo spreco di ripagare il debito tecnico in aree in cui davvero non è necessario.
