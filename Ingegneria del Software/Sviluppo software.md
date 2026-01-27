Abbiamo vari tipi di sviluppo software:

- Sviluppo rapido del software (RAD);
- Ingegneria del software basata sul riutilizzo;
- Ingegneria del software basata su componenti.

## Sviluppo rapido del software

**Sviluppo rapido perché molto spesso cambiano le esigenze velocemente, anche più veloce dello sviluppo.**

Le aziende potrebbero essere disposte ad accettare software di qualità inferiore se è possibile fornire rapidamente le funzionalità essenziali.

A causa dell'ambiente in evoluzione, è spesso impossibile arrivare a un insieme stabile e coerente di requisiti di sistema.

Un modello di sviluppo a cascata non è pratico e la specifica e la consegna iterative sono l'unico modo per distribuire rapidamente il software.

Le caratteristiche principali dei processi RAD:

- i processi di specifica, progettazione e implementazione sono concorrenti;
- non ci sono specifiche dettagliate e la documentazione di progettazione è ridotta al minimo;
- il sistema è sviluppato in una serie di incrementi. Gli utenti finali valutano ogni incremento e fanno proposte per incrementi successivi;
- le interfacce utente di sistema sono generalmente sviluppate utilizzando un sistema di sviluppo interattivo.

Un esempio di processo di sviluppo iterativo:

![[iterativeDevelopment.png]]

### Sviluppo incrementale

I vantaggi dello sviluppo incrementale sono:

- **Consegna accelerata dei servizi ai clienti:** ogni incremento offre al cliente la funzionalità con la massima priorità;
- **Coinvolgimento dell'utente con il sistema:** gli utenti devono essere coinvolti nello sviluppo, il che significa che è più probabile che il sistema soddisfi i loro requisiti e che gli utenti siano più impegnati nel sistema.

Abbiamo anche degli svantaggi:

- **Problemi di gestione:** i progressi possono essere difficili da giudicare e problemi difficili da trovare perché non c'è documentazione per dimostrare cosa è stato fatto;
- **Problemi contrattuali:** il contratto normale può includere una specifica; senza una specificazione, devono essere utilizzate diverse forme di contratto;
- **Problemi di convalida:** senza una specifica, contro cosa viene testato il sistema?
- **Problemi di manutenzione:** il cambiamento continuo tende a corrompere la struttura del software, rendendo più costoso il cambiamento e l'evoluzione per soddisfare i nuovi requisiti.

![[sviluppoIncrementale.png]]

### Sviluppo incrementale e prototipazione usa e getta

L'obiettivo dello **sviluppo incrementale** è fornire un sistema funzionante agli utenti finali. Lo sviluppo inizia con quei requisiti che sono meglio compresi.

L'obiettivo della **prototipazione usa e getta** è convalidare o derivare i requisiti di sistema. Il processo di prototipazione inizia con quei requisiti che sono poco compresi.

### Prototipazione software

Un prototipo è una versione iniziale di un sistema utilizzato per dimostrare concetti e provare opzioni di progettazione.

Un prototipo può essere utilizzato in:

- il processo di ingegneria dei requisiti per aiutare con l'elicitazione e la convalida dei requisiti;
- nei processi di progettazione per esplorare le opzioni e sviluppare un design dell'interfaccia utente;
- nel processo di test per eseguire test back-to-back.

### Prototipi usa e getta

I prototipi dovrebbero essere scartati dopo lo sviluppo in quanto non sono una buona base per un sistema di produzione:

- potrebbe essere impossibile mettere a punto il sistema per soddisfare requisiti non funzionali;
- i prototipi sono normalmente privi di documentazione;
- la struttura del prototipo viene solitamente degradata a causa di rapidi cambiamenti;
- il prototipo probabilmente non soddisferà i normali standard di qualità organizzativa.

### Ambienti RAD

I RAD sono progettati per sviluppare applicazioni aziendali ad alta intensità di dati e si basano sulla programmazione e sulla presentazione di informazioni da un database. 

Alcuni tools sono: linguaggio di programmazione del database, generatore di interfaccia, collegamenti ad applicazioni per ufficio e generatori di rapporti.

![[RAD.png]]

### Riutilizzo COTS

Altro metodo per sviluppo rapido sono i COTS. Un approccio efficace allo sviluppo rapido consiste nel configurare e collegare i sistemi esistenti pronti all'uso.

Ad esempio, un sistema di gestione dei requisiti potrebbe essere costruito utilizzando:

- un database per memorizzare i requisiti;
- un elaboratore di testi per acquisire i requisiti e formattare i report;
- un foglio elettronico per la gestione della tracciabilità.

### Metodi agili

L'insoddisfazione per le spese generali coinvolte nei metodi di progettazione ha portato alla creazione di metodi agili. Questi metodi:

- si concentrano sul codice piuttosto che sul design;
- si basano su un approccio iterativo allo sviluppo del software;
- sono destinati a fornire rapidamente software funzionante ea farlo evolvere rapidamente per soddisfare le mutevoli esigenze.

I metodi agili sono probabilmente **più adatti a sistemi aziendali di piccole/medie dimensioni** o prodotti per PC.

I principi dei metodi agili:

- **coinvolgimento del cliente:** il cliente dovrebbe essere strettamente coinvolto durante tutto il processo di sviluppo. Il loro ruolo è fornire e dare priorità ai nuovi requisiti di sistema e valutare le iterazioni del sistema;
- **consegna incrementale:** il software viene sviluppato ad incrementi con il cliente che specifica i requisiti da includere in ogni incremento;
- **le persone non elaborano:** le competenze del team di sviluppo dovrebbero essere riconosciute e sfruttate. Il team dovrebbe essere lasciato a sviluppare i propri modi di lavorare senza processi prescrittivi;
- **abbraccia il cambiamento:** aspettati che i requisiti di sistema cambino e progettano il sistema in modo che possa adattarsi a queste modifiche;
- **mantieni la semplicità:** puntare sulla semplicità sia nel software in fase di sviluppo che nel processo di sviluppo utilizzato. Ove possibile, lavora attivamente per eliminare la complessità dal sistema.

Abbiamo anche dei problemi con i metodi agili:

- può essere difficile mantenere l'interesse dei clienti coinvolti nel processo;
- i membri del team possono non essere adatti all'intenso coinvolgimento che caratterizza i metodi agili;
- dare priorità ai cambiamenti può essere difficile quando ci sono più parti interessate;
- mantenere la semplicità richiede un lavoro extra;
- i contratti possono essere un problema come con altri approcci allo sviluppo iterativo.

### Extreme Programming (XP)

Forse il metodo agile più conosciuto e più utilizzato. Extreme Programming (XP) adotta un approccio "estremo" allo sviluppo iterativo:

- le nuove versioni possono essere create più volte al giorno;
- gli incrementi vengono consegnati ai clienti ogni 2 settimane;
- tutti i test devono essere eseguiti per ogni build e la build viene accettata solo se i test vengono eseguiti correttamente.

Abbiamo vari tipi di pratiche per quanto riguarda l'XP:

- **pianificazione incrementale:** i requisiti sono registrati sulle Story Card e le Storie da includere in una pubblicazione sono determinate dal tempo a disposizione e dalla relativa priorità. Gli sviluppatori suddividono queste storie in "Compiti" di sviluppo;
- **piccole uscite:** l'insieme minimo di funzionalità utili che fornisce valore aziendale viene sviluppato per primo. I rilasci del sistema sono frequenti e aggiungono funzionalità in modo incrementale al primo rilascio;
- **design semplice:** viene eseguita una progettazione sufficiente per soddisfare i requisiti attuali e non di più;
- **primo test di sviluppo:** un framework di unit test automatizzato viene utilizzato per scrivere test per una nuova funzionalità prima che tale funzionalità venga implementata;
- **refactoring:** ci si aspetta che tutti gli sviluppatori effettuino il refactoring del codice in modo continuo non appena vengono rilevati possibili miglioramenti del codice. Ciò mantiene il codice semplice e gestibile;
- **programmazione in coppia:** gli sviluppatori lavorano in coppia, controllando il lavoro degli altri e fornendo il supporto per fare sempre un buon lavoro;
- **proprietà collettiva:** le coppie di sviluppatori lavorano su tutte le aree del sistema, in modo che non si sviluppino isole di competenza e tutti gli sviluppatori possiedano tutto il codice. Chiunque può cambiare qualsiasi cosa;
- **integrazione continua:** non appena il lavoro su un'attività è completo, viene integrato nell'intero sistema. Dopo tale integrazione, tutti i test unitari nel sistema devono superare;
- **ritmo sostenibile:** grandi quantità di straordinario non sono considerate accettabili poiché l'effetto netto è spesso quello di ridurre la qualità del codice e la produttività a medio termine;
- **cliente in loco:** un rappresentante dell'utente finale del sistema (il Cliente) dovrebbe essere disponibile a tempo pieno per l'utilizzo del team XP. In un processo di programmazione estremo, il cliente è un membro del team di sviluppo ed è responsabile di portare i requisiti di sistema al team per l'implementazione.

Ecco il ciclo di rilascio di XP:

![[releaseXP.png]]

### Scenari dei requisiti

In XP, i requisiti degli utenti sono espressi come scenari o storie utente scritto su carte e suddividere in compiti di attuazione.

**Il cliente sceglie le storie da inserire nella prossima release** in base alle proprie priorità e alle previsioni di pianificazione.

### Test in XP

Abbiamo vari tipi di test:

- sviluppo test-first.
- sviluppo di test incrementali da scenari.
- coinvolgimento dell'utente nello sviluppo e nella convalida dei test.
- test harness automatizzati vengono utilizzati per eseguire tutti i test dei componenti ogni volta che viene creata una nuova versione.

### Sviluppo test-first

Scrivere test prima che il codice chiarisca i requisiti da implementare.

I test vengono scritti poiché i programmi possono essere eseguiti automaticamente.

Tutti i test precedenti e nuovi vengono eseguiti automaticamente quando viene aggiunta una nuova funzionalità, verificando così che la nuova funzionalità non abbia introdotto errori.

### Programmazione in coppia

In XP, i programmatori lavorano in coppia, seduti insieme per sviluppare il codice.

Questo aiuta a sviluppare la proprietà comune del codice e diffonde la conoscenza all'interno del team.

Serve come processo di revisione informale poiché ogni riga di codice viene esaminata da più di 1 persona.

Incoraggia il refactoring poiché l'intero team può trarne vantaggio.

Le misurazioni suggeriscono che la produttività dello sviluppo con la programmazione in coppia è simile a quella di due persone che lavorano indipendentemente.
