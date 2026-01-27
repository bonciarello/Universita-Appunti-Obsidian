È un insieme strutturato di attività necessarie per sviluppare un sistema software che riguardano *specifiche*, *design*, *validazione* ed *evoluzione*.

Un **modello di processo software** è una rappresentazione astratta di un processo. Presenta una descrizione di un processo da una prospettiva particolare.

## Modelli di processo software generici

- **Modello a cascata:** fasi separate e distinte di specifica e sviluppo;
- **Sviluppo evolutivo:** le specifiche, lo sviluppo e la convalida sono intercalati;
- **Ingegneria del Software basata sui componenti:** il sistema è assemblato da componenti esistenti.

*Esistono molte varianti di questi modelli*, ad es. sviluppo formale in cui viene utilizzato un processo a cascata ma la specifica è una specifica formale che viene raffinata attraverso diverse fasi fino a un design implementabile.

### Modello a cascata

Le fasi del modello a cascata sono:

![[waterfall.png]]

- analisi e definizione dei requisiti;
- progettazione di sistemi e software;
- implementazione e test di unità;
- integrazione e test del sistema;
- funzionamento e manutenzione.

Il principale svantaggio è la difficoltà di accogliere il cambiamento dopo che il processo è in corso: una fase deve essere completata prima di passare alla fase successiva.

I problemi di questo modello:

- la suddivisione inflessibile del progetto in fasi distinte rende difficile rispondere alle mutevoli esigenze dei clienti;
- è appropriato solo quando i requisiti sono ben compresi e le modifiche saranno abbastanza limitate durante il processo di progettazione.
- pochi sistemi aziendali hanno requisiti stabili;
- il modello a cascata viene utilizzato principalmente per grandi progetti di ingegneria dei sistemi in cui un sistema viene sviluppato in diversi siti.

### Sviluppo evolutivo

- **Sviluppo esplorativo:** l'obiettivo è lavorare con i clienti e sviluppare un sistema finale partendo da una specifica iniziale. Inizia con requisiti ben compresi e aggiungi nuove funzionalità come proposto dal cliente;
- **Prototipazione usa e getta:** l'obiettivo è comprendere i requisiti di sistema. Dovrebbe iniziare con requisiti poco compresi per chiarire cosa è veramente necessario.

![[evolutionary.png]]

I principali problemi di questo modello sono:

- mancanza di visibilità del processo;
- i sistemi sono spesso poco strutturati;
- potrebbero essere richieste competenze speciali (ad esempio nelle lingue per la prototipazione rapida).

Viene applicato per:

- per sistemi interattivi di piccole o medie dimensioni;
- per parti di sistemi di grandi dimensioni (es. l'interfaccia utente);
- per sistemi di breve durata.

### Ingegneria del Software basata sui componenti

Basato sul riutilizzo sistematico in cui i sistemi sono integrati da componenti esistenti o sistemi COTS (Commercial-off-the-shelf).

Le fasi del processo sono:
- analisi dei componenti;
- modifica dei requisiti;
- progettazione del sistema con riutilizzo;
- sviluppo e integrazione.

Questo approccio sta diventando sempre più utilizzato man mano che sono emersi gli standard dei componenti.

## Iterazione del processo

I requisiti di sistema si evolvono SEMPRE nel corso di un progetto. L'iterazione può essere applicata a qualsiasi modello di processo generico. Due approcci (correlati): *consegna incrementale* e *sviluppo a spirale*.

## Sviluppo a spirale

Il processo è rappresentato come una spirale piuttosto che come una sequenza di attività con backtracking.

Ciascun ciclo della spirale rappresenta una fase del processo.

Nessuna fase fissa come specifica o progettazione: i loop nella spirale vengono scelti in base a ciò che è richiesto.

I rischi sono esplicitamente valutati e risolti durante tutto il processo.

## Sviluppo incrementale

Lo sviluppo e la consegna sono suddivisi in incrementi: ogni incremento fornisce parte della funzionalità richiesta.

I requisiti degli utenti hanno la priorità e i requisiti con priorità più alta sono inclusi nei primi incrementi.

Una volta avviato lo sviluppo di un incremento, i requisiti vengono congelati anche se i requisiti per gli incrementi successivi possono continuare a evolversi.

![[incrementale.png]]

I vantaggi di questo modello sono:

- il valore del cliente può essere fornito con ogni incremento in modo che la funzionalità del sistema sia disponibile prima;
- i primi incrementi fungono da prototipo per aiutare a sollecitare i requisiti per gli incrementi successivi;
- minor rischio di fallimento complessivo del progetto;
- i servizi di sistema con la priorità più alta tendono a ricevere il maggior numero di test.

## Attività di processo

### Specifiche del software

È il processo per stabilire quali servizi sono richiesti dei vincoli al funzionamento e allo sviluppo del sistema. Il processo di ingegneria dei requisiti è composto da:

- studio di fattibilità;
- elicitazione e analisi dei requisiti;
- specifica dei requisiti;
- convalida dei requisiti.

![[specificheSoftware.png]]

### Progettazione e implementazione del software

È il processo di conversione delle specifiche di sistema in un sistema eseguibile.

Con questo processo, si deve progettare una struttura software che realizzi la specifica e tradurre questa struttura in un programma eseguibile.

Le attività di progettazione e realizzazione sono strettamente correlate e possono essere interconnesse.

Le attività del processo di progettazione:

- progettazione architettonica;
- specifica astratta;
- progettazione dell'interfaccia;
- progettazione dei componenti;
- progettazione della struttura dei dati;
- progettazione di algoritmi;

![[progettazione.png]]

### Convalida del software

La verifica e la convalida *hanno lo scopo di dimostrare che un sistema è conforme alle sue specifiche e soddisfa i requisiti del cliente del sistema*.

Implica il controllo e la revisione dei processi e dei test di sistema.

Il test del sistema implica l'esecuzione del sistema con casi di test derivati dalla specifica dei dati reali che devono essere elaborati dal sistema.

### Evoluzione del software

**Il software è intrinsecamente flessibile e può cambiare.**

I requisiti cambiano e il software deve evolversi e cambiare.

Sebbene vi sia stata una demarcazione tra sviluppo ed evoluzione (manutenzione), ciò è sempre più irrilevante poiché *sempre meno sistemi sono completamente nuovi*.

![[evoluzioneSoftware.png]]

## Metodi strutturati

Sono approcci sistematici allo sviluppo di un software design. Il progetto è solitamente documentato come un insieme di modelli grafici.
I modelli possibili sono:

- modello a oggetti;
- modello di sequenza;
- modello di transizione di stato;
- modello strutturale;
- modello a flusso di dati.

## Fasi di test

- *Test di componenti o unità:* i singoli componenti vengono testati in modo indipendente oppure possono essere funzioni o oggetti o raggruppamenti coerenti di queste entità;
- *Test del sistema:* test del sistema nel suo complesso. La verifica delle proprietà emergenti è particolarmente importante;
- *Test di accettazione:* test con i dati del cliente per verificare che il sistema soddisfi le esigenze del cliente.

## Computer-aided software engineering (CASE)

CASE è un software per supportare i processi di sviluppo e di evoluzione del software. Si occupa dell'automazione delle attività come:

- editor grafici per lo sviluppo di modelli di sistema;
- dizionario dati per la gestione delle entità progettuali;
- generatore di interfaccia utente grafica per la costruzione dell'interfaccia utente;
- debugger per supportare la ricerca dei guasti del programma;
- traduttori automatici per generare nuove versioni di un programma.

## Integrazioni CASE

- **Tools:** supporta le singole attività di processo come il controllo della coerenza del design, la modifica del testo, ecc;
- **Workbenches:** supporta una fase di processo come la specifica o la progettazione, normalmente include una serie di strumenti integrati;
- **Ambienti:** supporta tutto o una parte sostanziale di un intero processo software. Normalmente includono diversi banchi di lavoro integrati.

![[integrazioni.png]]