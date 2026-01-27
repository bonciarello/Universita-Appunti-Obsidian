## Interfaccia utente

Dovrebbe essere progettato per corrispondere a competenze, esperienza e aspettative dei suoi utenti previsti.

Gli utenti spesso giudicano un sistema dalla sua interfaccia piuttosto che dalla sua funzionalità.

Un'interfaccia mal progettata può indurre un utente a commettere errori catastrofici.

Il design scadente dell'interfaccia utente è il motivo per cui così tanti sistemi software non vengono mai utilizzati.

I fattori umani nel design dell'interfaccia sono:

- **Memoria a breve termine limitata:** le persone possono ricordare istantaneamente circa 7 elementi di informazione. Se presenti più di questo, sono più soggetti a commettere errori;
- **Le persone commettono errori:** quando le persone commettono errori e i sistemi vanno male, allarmi e messaggi inappropriati possono aumentare lo stress e quindi la probabilità di ulteriori errori;
- **Le persone sono diverse:** le persone hanno un'ampia gamma di capacità fisiche. I designer non dovrebbero limitarsi a progettare per le proprie capacità;
- **Le persone hanno preferenze di interazione diverse:** ad alcuni piacciono le immagini, ad altri il testo.

## Principi di progettazione dell'interfaccia utente

Prendiamo in considerazione le esigenze, l'esperienza e le capacità degli utenti del sistema.

**Bisogna essere consapevoli dei limiti fisici e mentali delle persone** (ad es. memoria a breve termine limitata) e bisogna riconoscere che le persone commettono errori.

Notiamo che non tutti i principi sono applicabili a tutti i progetti.

Altri principi sono:

- **Familiarità dell'utente:** l'interfaccia dovrebbe utilizzare termini e concetti tratti dall'esperienza delle persone che utilizzeranno maggiormente il sistema;
- **Consistenza:** l'interfaccia dovrebbe essere coerente in quanto, ove possibile, operazioni comparabili dovrebbero essere attivate allo stesso modo;
- **Sorpresa minima:** gli utenti non dovrebbero mai essere sorpresi dal comportamento di un sistema;
- **Recuperabilità:** l'interfaccia dovrebbe includere meccanismi per consentire agli utenti di recuperare dagli errori;
- **Guida per l'utente:** l'interfaccia dovrebbe fornire un feedback significativo quando si verificano errori e fornire servizi di assistenza all'utente sensibili al contesto;
- **Diversità degli utenti:** l'interfaccia dovrebbe fornire adeguate strutture di interazione per i diversi tipi di utenti del sistema.

## Problemi di progettazione nelle interfacce utente

Due problemi devono essere affrontati nella progettazione di sistemi interattivi:

- Come devono essere fornite le informazioni dell'utente al sistema informatico?
- Come devono essere presentate all'utente le informazioni provenienti dal sistema informatico?

## Stili di interazione

- **Manipolazione diretta:** i vantaggi sono che è ha interazione veloce e intuitiva ed è facile da imparare, gli svantaggi sono che potrebbe essere difficile da implementare ed è adatto solo dove c'è una metafora visiva per compiti e oggetti (esempi di applicazione sono videogiochi e sistemi CAD);
- **Selezione del menu:** i vantaggi sono che evita l'errore dell'utente ed è necessaria una piccola digitazione, gli svantaggi sono che è lento per utenti esperti e può diventare complesso se molte opzioni di menu (esempi di applicazioni sono la maggior parte dei sistemi generici);
- **Compilazione modulo:** i vantaggi sono che l'inserimento dati è semplice, facile da imparare e verificabile, gli svantaggi sono che occupa molto spazio sullo schermo e causa problemi in cui le opzioni utente non corrispondono ai campi del modulo.(esempi di applicazioni sono );
- **Linguaggio naturale:** i vantaggi sono che è accessibile agli utenti occasionali ed è facilmente esteso, gli svantaggi sono che richiede più digitazione e i sistemi di comprensione del linguaggio naturale sono inaffidabili (esempi di applicazioni sono sistemi di recupero delle informazioni).

## Interfacce utente multiple

È comodo che vengono fornite diverse tipologie di interfaccia per le stesse funzionalità. L'esempio tipo è quello della shell e delle finestre, entrambe vengono fornite dal sistema operativo:

![[multipleUserInterfaces.png]]

## Presentazione informativa

La presentazione delle informazioni riguarda la presentazione delle informazioni agli utenti. Abbiamo due tipi:

- Presentazione diretta (ad es. testo in un elaboratore di testi);
- Presentazione indiretta (ad es. in qualche forma grafica).

L'**approccio Model-View-Controller** è un modo per supportare più presentazioni di dati.

Abbiamo due tipi d'informazioni:

- **Informazioni statiche:** inizializzato all'inizio di una sessione. Non cambia durante la sessione e può essere numerico o testuale;
- **Informazioni dinamiche:** le modifiche durante una sessione e le modifiche devono essere comunicate all'utente del sistema. Può essere numerico o testuale.

Quando dobbiamo mostrare le informazioni, dobbiamo rispettare certi fattori:

- L'utente è interessato a informazioni precise o a relazioni di dati?
- Quanto velocemente cambiano i valori delle informazioni? La modifica deve essere indicata immediatamente?
- L'utente deve intraprendere qualche azione in risposta a un cambiamento?
- Esiste un'interfaccia di manipolazione diretta?
- Le informazioni sono testuali o numeriche? I valori relativi sono importanti?

Possiamo mostrare presentazioni informative alternative come, per esempio:

![[presentazioneInfoAlternative.png]]

**Presentazione analogica o digitale?** La presentazione digitale è compatta, occupa poco spazio sullo schermo, possono essere comunicati valori precisi. La presentazione analogica è più facile ottenere un'impressione “a colpo d'occhio” di un valore, possibilità di visualizzare i valori relativi e più facile vedere valori di dati eccezionali.

Qui, di seguito, un esempio di pattern MVC:

![[MVC.png]]

## Visualizzazione dei dati

Sono un insieme di tecniche per visualizzare grandi quantità di informazioni. La visualizzazione può rivelare le relazioni tra entità e tendenze nei dati. Le possibili visualizzazioni dei dati sono:

- informazioni meteorologiche raccolte da una serie di fonti;
- lo stato di una rete telefonica come insieme di nodi collegati;
- impianto chimico visualizzato mostrando pressioni e temperature in un insieme collegato di serbatoi e tubazioni;
- un modello di una molecola visualizzato in 3 dimensioni;
- pagine Web visualizzate come albero iperbolico.

## Display a colori

Il colore aggiunge una dimensione extra a un'interfaccia e può aiutare l'utente a comprendere strutture informative complesse.

Il colore può essere utilizzato per evidenziare eventi eccezionali.

Gli errori comuni nell'uso del colore nella progettazione dell'interfaccia includono:

- l'uso del colore per comunicare il significato;
- l'uso eccessivo del colore nel display.

Le linee guida per l'uso del colore sono:

- limitare il numero di colori utilizzati ed essere prudenti nel loro utilizzo;
- usa cambio colore per mostrare un cambiamento nello stato del sistema;
- utilizzare la codifica a colori per supportare l'attività che gli utenti stanno cercando di eseguire;
- utilizzare la codifica a colori in modo ponderato e coerente;
- fare attenzione agli abbinamenti di colori.

## Messaggio di errore

La progettazione dei messaggi di errore è di fondamentale importanza. **Messaggi di errore scadenti possono significare che un utente rifiuta piuttosto che accettare un sistema.**

I messaggi devono essere educati, concisi, coerenti e costruttivi.

Il **background e l'esperienza degli utenti** dovrebbero essere il **fattore determinante** nella progettazione del messaggio.

I fattori di progettazione nella formulazione del messaggio sono:

- **Contesto:** ove possibile, i messaggi generati dal sistema dovrebbero riflettere il contesto dell'utente corrente. Per quanto possibile, il sistema dovrebbe essere consapevole di ciò che l'utente sta facendo e dovrebbe generare messaggi rilevanti per la sua attività corrente;
- **Esperienza:** man mano che gli utenti acquisiscono familiarità con un sistema, diventano irritati da messaggi lunghi e "significativi". Tuttavia, i principianti hanno difficoltà a comprendere brevi e concise affermazioni di un problema. Dovresti fornire entrambi i tipi di messaggio e consentire all'utente di controllare la concisione del messaggio;
- **Livello di abilità:** i messaggi devono essere personalizzati in base alle capacità dell'utente e alla sua esperienza. I messaggi per le diverse classi di utenti possono essere espressi in modi diversi a seconda della terminologia familiare al lettore;
- **Stile:** i messaggi dovrebbero essere positivi piuttosto che negativi. Dovrebbero usare la modalità di indirizzo attiva piuttosto che passiva. Non dovrebbero mai essere insultanti o cercare di essere divertenti;
- **Cultura:** ove possibile, il progettista dei messaggi dovrebbe conoscere la cultura del paese in cui viene venduto il sistema. Ci sono differenze culturali distinte tra Europa, Asia e America. Un messaggio adatto per una cultura potrebbe essere inaccettabile in un'altra.

## Errore utente

Supponiamo che un'infermiera scriva male il nome di un paziente di cui sta cercando di recuperare i dati.

![[userError1.png]]

Ecco un esempio di design del messaggio **cattivo** e **buono** per l'utente:

![[userError2.png]]

## Processo di progettazione dell'interfaccia utente

La progettazione dell'interfaccia utente è un processo iterativo che coinvolge stretti collegamenti tra utenti e designer.

Le 3 attività principali in questo processo sono:

- **analisi dell'utente:** capire cosa faranno gli utenti con il sistema;
- **prototipazione del sistema:** sviluppare una serie di prototipi per l'esperimento;
- **valutazione dell'interfaccia:** sperimenta questi prototipi con gli utenti.

![[designProcess.png]]

## Analisi dell'utente

Se non capisci cosa vogliono fare gli utenti con un sistema, non hai prospettive realistiche di progettare un'interfaccia efficace.

Le analisi degli utenti devono essere descritte in termini comprensibili agli utenti e ad altri designer.

Gli scenari in cui si descrivono episodi tipici di utilizzo sono un modo per descrivere queste analisi.

## Tecniche di analisi

Abbiamo 3 tipi di tecniche:

- **analisi del compito:** modella i passaggi coinvolti nel completamento di un'attività;
- **interviste e questionari:** chiede agli utenti il lavoro che svolgono;
- **etnografia:** osserva l'utente al lavoro.

## Intervista

**Progetta interviste semi-strutturate basate su domande aperte.**

Gli utenti possono quindi fornire informazioni che ritengono essenziali; non solo informazioni che hai pensato di raccogliere.

Le interviste di gruppo o i focus group consentono agli utenti di discutere tra loro di ciò che fanno.

## Etnografia

**Coinvolge un osservatore esterno che osserva gli utenti al lavoro e li interroga in modo non programmato sul loro lavoro.**

Prezioso perché molte attività dell'utente sono intuitive e le trovano molto difficili da descrivere e spiegare.

Aiuta anche a comprendere il ruolo delle influenze sociali e organizzative sul lavoro.

## Prototipazione dell'interfaccia utente

Lo scopo della prototipazione è consentire agli utenti di **acquisire esperienza diretta con l'interfaccia**.

Senza tale esperienza diretta, è impossibile giudicare l'usabilità di un'interfaccia.

La prototipazione può essere un processo in due fasi:

- all'inizio del processo, possono essere utilizzati prototipi di carta; elaborare scenari utilizzando schizzi dell'interfaccia;
- il design viene poi perfezionato e vengono poi sviluppati prototipi automatizzati sempre più sofisticati.

## Tecniche di prototipazione

Abbiamo varie tecniche di prototipazione:

- **Prototipazione basata su script:** sviluppare una serie di script e schermate utilizzando uno strumento come Macromedia Director. Quando l'utente interagisce con questi, lo schermo passa alla visualizzazione successiva;
- **Programmazione visiva:** utilizzare un linguaggio progettato per uno sviluppo rapido come Visual Basic;
- **Prototipazione basata su Internet:** utilizzare un browser Web e gli script associati.

## Valutazione dell'interfaccia utente

La valutazione completa è molto costosa e poco pratica per la maggior parte dei sistemi.

Semplici tecniche di valutazione sono:

- questionari per il feedback degli utenti;
- registrazione video dell'utilizzo del sistema e successiva valutazione del nastro;
- strumentazione di codice per raccogliere informazioni sull'uso della struttura e sugli errori dell'utente;
- la fornitura di codice nel software per raccogliere il feedback degli utenti online.

## Attributi di usabilità

Abbiamo degli attributi fondamentali per quanto riguarda l'usabilità:

- **Apprendibilità:** quanto tempo impiega un nuovo utente per diventare produttivo con il sistema?
- **Velocità di funzionamento:** in che misura la risposta del sistema corrisponde alla pratica lavorativa dell'utente?
- **Robustezza:** quanto è tollerante il sistema di errore dell'utente?
- **Recuperabilità:** quanto è bravo il sistema a recuperare dagli errori dell'utente?
- **Adattabilità:** quanto è legato il sistema a un unico modello di lavoro?