## Ingegneria dei Requisiti

È il processo di definizione dei servizi che il cliente richiede da un sistema e i vincoli in base ai quali opera e si sviluppa.

I requisiti sono le descrizioni dei servizi di sistema e dei vincoli generati durante il processo di ingegneria dei requisiti.

## Che cos'è un requisito?

È una affermazione di un servizio o di un vincolo di sistema ad alto livello (astratto, lingua parlata) o basso livello (specifiche funzionali matematiche).

Questo è inevitabile in quanto i requisiti possono svolgere una duplice funzione:

- può essere la base di un'offerta per un contratto, aperto alle interpretazioni;
- può essere la base per il contratto stesso, deve essere definito in dettaglio;
- entrambe queste affermazioni possono essere chiamate requisiti.

## Tipi di requisiti

Andiamo a suddividere i requisiti in due obiettivi:

- **requisiti dell'utente:** dichiarazioni in linguaggio naturale più diagrammi dei servizi forniti dal sistema e dei suoi vincoli operativi ed è scritto per i clienti;
- **requisiti del sistema:** un documento strutturato che fornisce descrizioni dettagliate delle funzioni, dei servizi e dei vincoli operativi del sistema. Definisce cosa dovrebbe essere implementato e può essere parte di un contratto tra cliente e appaltatore.

## Definizioni e specifiche

- **Definizione dei requisiti dell'utente** 
    1. Il software deve fornire un mezzo per rappresentare e accedere a file esterni creati da altri strumenti;
- **Specifica dei requisiti di sistema** 
    1. L'utente dovrebbe essere dotato di strutture per definire il tipo di file esterni;
    2. Ogni tipo di file esterno può avere uno strumento associato che può essere applicato al file;
    3. Ogni tipo di file esterno può essere rappresentato come un'icona specifica sul display dell'utente;
    4. Dovrebbero essere fornite le funzionalità per l'icona che rappresenta un tipo di file esterno da definire dall'utente;
    5. Quando un utente seleziona un'icona che rappresenta un file esterno, l'effetto di tale selezione è applicare lo strumento associato al tipo di file esterno al file rappresentato dall'icona selezionata.

## Requisiti lettori

I due tipi di requisiti hanno una diversa platea di persone che ne usufruiranno:

![[requisitiLettori.png]]

## Requisiti funzionali e non funzionali

C'è un altro modo per suddividere i requisiti, in modo ortogonale:

- **richieste funzionali:** descrivere funzionalità o servizi di sistema (cosa dovrebbe fornire il sistema, come il sistema dovrebbe reagire a particolari input e come dovrebbe comportarsi il sistema in particolari situazioni);
- **requisiti non funzionali:** esprimono vincoli sui servizi o sulle funzioni (per esempio. vincoli temporali, vincoli sul processo di sviluppo, standard, ecc.);
- **requisiti del dominio:** provengono dal dominio dell'applicazione, riflettere le caratteristiche di quel dominio.

Un esempio di requisiti funzionali sono alcuni requisiti di sistema di LIBSYS.

> *L'utente deve essere in grado di eseguire ricerche in tutto l'insieme iniziale di banche dati o selezionarne un sottoinsieme.*
> 
> *Il sistema fornisce visualizzatori appropriati per consentire all'utente di leggere i documenti nell'archivio documenti.*
> 
> *Ad ogni ordine sarà assegnato un identificativo univoco (ORDER_ID) che l'utente potrà copiare nell'area di archiviazione permanente dell'account.*

## Imprecisione dei requisiti

**I problemi sorgono quando i requisiti non sono dichiarati con precisione.**

I requisiti ambigui possono essere interpretati in modi diversi da sviluppatori e utenti.

Consideriamo il termine "visualizzatori appropriati" nell'esempio precedente: per l'interpretazione dell'utente, si può intendere visualizzatore per scopi speciali per ogni diverso tipo di documento mentre, per l'interpretazione dello sviluppatore, si fornisce un visualizzatore di testo che mostri il contenuto del documento.

## Completezza e coerenza dei requisiti

Con **completezza** dovrebbe includere le descrizioni di tutte le strutture richieste. Con **coerenza** non dovrebbero esserci conflitti o contraddizioni nelle descrizioni delle strutture del sistema.

In pratica, è impossibile produrre un documento dei requisiti completo e coerente.

## Requisiti non funzionali

Questi definiscono le proprietà e i vincoli del sistema come per esempio affidabilità, tempi di risposta e requisiti di archiviazione.

I vincoli sono la capacità del dispositivo I/O, le rappresentazioni del sistema, ecc.

I requisiti di processo possono anche essere specificati imponendo un particolare sistema CASE, linguaggio di programmazione o metodo di sviluppo.

**I requisiti non funzionali possono essere più critici dei requisiti funzionali:** se questi non vengono rispettati, il sistema è inutile.

Abbiamo una classificazione per requisiti non funzionali:

- **requisiti del prodotto:** il prodotto deve comportarsi in un modo particolare (velocità di esecuzione, affidabilità, ecc.);
- **requisiti organizzativi:** conseguenza di politiche e procedure organizzative (es. standard di processo utilizzati, requisiti di implementazione, ecc.). Un esempio reale è quella di *Smart Campus*, ogni week-end i professori devono inserire le prenotazioni per le lezioni anche se ogni settimana l'orario è lo stesso;
- **requisiti esterni:** derivano da fattori esterni al sistema e al suo processo di sviluppo (ad esempio requisiti di interoperabilità, requisiti legislativi, ecc.). Un esempio reale è un *e-commerce* quando deve interagire con siti esterni per il pagamento.

![[nonFunzionali.png]]

## Obiettivi e requisiti

**Non confondere un obiettivo del sistema con i requisiti di sistema:** un *obiettivo* è un'intenzione generale dell'utente come la facilità d'uso, un *requisito* è un'affermazione che utilizza una misura che può essere verificata oggettivamente.

Gli obiettivi sono utili per gli sviluppatori in quanto trasmettono le intenzioni degli utenti del sistema.

Facciamo degli esempi:
- con un *obiettivo di sistema*, il sistema dovrebbe essere facile da usare da parte di controllori esperti e dovrebbe essere organizzato in modo tale da ridurre al minimo gli errori dell'utente;
- con un *requisito non funzionale verificabile*, i controllori esperti devono essere in grado di utilizzare tutte le funzioni del sistema dopo un totale di due ore di formazione. Dopo questa formazione, il numero medio di errori commessi da utenti esperti non deve superare due al giorno.

## Misure dei requisiti

| **Proprietà** | **Misurazione** |
| :--- | :--- |
| *Velocità* | Transazioni elaborate/secondo<br>Tempo di risposta utente/evento<br>Tempo di aggiornamento dello schermo |
| *Dimensione* | M byte<br>Numero di chip ROM |
| *Facilità d'uso* | Tempo di allenamento<br>Numero di frame di aiuto |
| *Affidabilità* | Tempo medio per il fallimento<br>Probabilità di indisponibilità<br>Tasso di occorrenza del guasto<br>Disponibilità |
| *Robustezza* | È ora di riavviare dopo un errore<br>Percentuale di eventi che causano guasti<br>Probabilità di danneggiamento dei dati in caso di errore |
| *Portabilità* | Percentuale di dichiarazioni dipendenti dall'obiettivo<br>Numero di sistemi target |

## Interazione dei requisiti

I conflitti tra diversi requisiti non funzionali sono comuni nei sistemi complessi.

Per esempio, guardiamo il sistema dei veicoli spaziali:

- per ridurre al minimo il peso, il numero di chip separati nel sistema dovrebbe essere ridotto al minimo;
- per ridurre al minimo il consumo di energia, dovrebbero essere utilizzati chip di potenza inferiore;
- tuttavia, l'utilizzo di chip a bassa potenza può comportare l'utilizzo di più chip.

## Requisiti del dominio

Deriva dal dominio dell'applicazione e descrivere le caratteristiche e le caratteristiche del sistema che riflettono il dominio.

**Se i requisiti del dominio non sono soddisfatti, il sistema potrebbe non funzionare.**

I problemi dei requisiti del dominio sono:

- *comprensibilità:* i requisiti sono espressi nella lingua del dominio applicativo, questo spesso non è compreso dagli ingegneri del software che sviluppano il sistema.
- *implicità:* gli specialisti del dominio comprendono l'area così bene che non pensano di rendere espliciti i requisiti del dominio.

## Requisiti e design

In linea di principio, i requisiti dovrebbero indicare cosa dovrebbe fare il sistema e il progetto dovrebbe descrivere come lo fa.

In pratica, **requisiti e design sono inseparabili**:

- un'architettura di sistema può essere progettata per strutturare i requisiti;
- il sistema può interagire con altri sistemi che generano requisiti di progettazione;
- l'uso di un design specifico può essere un requisito di dominio.

## Requisiti dell'utente

**I requisiti funzionali e non funzionali dovrebbero essere comprensibili dagli utenti del sistema** (che non hanno conoscenze tecniche dettagliate).

I requisiti dell'utente sono definiti utilizzando il *linguaggio naturale e tabelle e diagrammi* in quanto comprensibili a tutti gli utenti.

## Problemi con il linguaggio naturale

Abbiamo diversi problemi con il linguaggio naturale tra cui:

- **mancanza di chiarezza:** la precisione è difficile senza rendere difficile la lettura del documento;
- **confusione dei requisiti:** i requisiti funzionali e non funzionali tendono a essere confusi;
- **fusione dei requisiti:** diversi requisiti possono essere espressi insieme;
- **ambiguità:** i lettori e gli autori del requisito devono interpretare le stesse parole nello stesso modo. Il linguaggio naturale è naturalmente ambiguo, quindi è molto difficile;
- **flessibilità eccessiva:** la stessa cosa può essere detta in diversi modi nella specifica;
- **mancanza di modularizzazione:** le strutture dei linguaggi naturali sono inadeguate ai requisiti di sistema della struttura.

## Linee guida per i requisiti di scrittura

- inventa un formato standard e utilizzalo per tutte le esigenze;
- usa la lingua in modo coerente;
- utilizzare l'evidenziazione del testo per identificare le parti chiave del requisito;
- **evitare l'uso del gergo informatico**.

## Requisiti di sistema

Specifiche più dettagliate delle funzioni, dei servizi e dei vincoli del sistema rispetto ai requisiti dell'utente.

Sono destinati ad essere una base per la progettazione del sistema.

Possono essere incorporati nel contratto di sistema.

I requisiti di sistema possono essere definiti o illustrati utilizzando modelli di sistema.

## Specifiche del linguaggio strutturato

La libertà dell'autore dei requisiti è limitata da un modello predefinito per i requisiti. **Tutti i requisiti sono scritti in modo standard.** La terminologia utilizzata nella descrizione può essere limitata. Il *vantaggio* è che si mantiene al massimo l'espressività. Il *limite* è che un certo grado di uniformità è imposto alla specifica.

Abbiamo vari tipi di specifiche:

- **specifiche basate su moduli:** 
    - definizione della funzione o entità;
    - descrizione degli input e della loro provenienza;
    - descrizione delle uscite e dove vanno;
    - indicazione di altri soggetti richiesti;
    - condizioni pre e post (se appropriato);
    - gli effetti collaterali (se presenti) della funzione.
- **specifica tabellare:** 
    - usato per integrare il linguaggio naturale;
    - particolarmente utile quando si devono definire una serie di possibili linee d'azione alternative.
- **modelli grafici:** 
    - mostra come cambia lo stato;
    - descrivere una sequenza di azioni.

## Diagramma di sequenza di prelievo ATM

![[atm.png]]

## Processi di ingegneria dei requisiti

Variano ampiamente a seconda del dominio dell'applicazione, le persone coinvolte e l'organizzazione che sviluppa i requisiti.

Le attività generiche comuni a tutti i processi sono:

- elicitazione sui requisiti;
- analisi dei requisiti;
- validazione dei requisiti;
- gestione dei requisiti.

![[processiRequisiti.png]]

## Studi di fattibilità

**Uno studio di fattibilità decide se il sistema proposto è utile o meno.**

Un breve studio mirato che verifica se il sistema contribuisce agli obiettivi organizzativi, se il sistema può essere progettato utilizzando la tecnologia attuale e rispettando il budget e se il sistema può essere integrato con altri sistemi utilizzati.

## Elicitazione e analisi

A volte chiamato elicitazione dei requisiti o scoperta dei requisiti. Qui è inevitabile l'interazione con i clienti.

**Coinvolge il personale tecnico che lavora con i clienti per informarsi sul dominio dell'applicazione**, sui servizi che il sistema dovrebbe fornire e sui vincoli operativi del sistema.

Può coinvolgere utenti finali, manager, ingegneri coinvolti nella manutenzione, esperti di dominio, sindacati, ecc. Questi sono chiamati **stakeholder**.

I problemi dell'analisi dei requisiti possono essere:

- gli stakeholder non sanno cosa vogliono veramente;
- le parti interessate esprimono i requisiti nei propri termini;
- diverse parti interessate possono avere requisiti in conflitto;
- fattori organizzativi e politici possono influenzare i requisiti di sistema;
- i requisiti cambiano durante il processo di analisi. Possono emergere nuovi portatori di interessi e l'ambiente aziendale può cambiare.

## Attività di processo

- *scoperta dei requisiti:* interagisce con le parti interessate per scoprire le loro esigenze. In questa fase vengono rilevati anche i requisiti di dominio;
- *classificazione e organizzazione dei requisiti:* raggruppa i requisiti correlati e li organizza in cluster coerenti;
- *priorità e negoziazione:* si dà priorità ai requisiti e risolvere i conflitti tra requisiti;
- *documentazione dei requisiti:* i requisiti vengono documentati e inseriti nel ciclo successivo della spirale.

## Punti di vista

**I punti di vista sono un modo per strutturare i requisiti per rappresentare le prospettive dei diversi stakeholder**: gli stakeholder possono essere classificati sotto diversi punti di vista.

Questa analisi multi-prospettiva è importante: non esiste un unico modo corretto per analizzare i requisiti di sistema.

Abbiamo vari tipi di punti di vista:

- *punti di vista dell'interattore:* persone o altri sistemi che interagiscono direttamente con il sistema (per esempio, in un bancomat, il database del cliente e del conto sono interagenti);
- *punti di vista indiretti:* stakeholder che non utilizzano il sistema da soli ma che influenzano i requisiti (per esempio, in un ATM, il personale di gestione e di sicurezza sono punti di vista indiretti);
- *punti di vista del dominio:* caratteristiche del dominio e vincoli che influenzano i requisiti (per esempio, in un ATM, potrebbero essere gli standard per le comunicazioni interbancarie).

## Intervista

Nelle interviste formali o informali, il team RE pone domande agli stakeholder sul sistema che usano e sul sistema da sviluppare.

Ci sono due tipi di interviste:

- colloqui chiusi in cui si risponde a una serie predefinita di domande;
- interviste aperte in cui non esiste un'agenda predefinita e vengono esplorate una serie di questioni con le parti interessate.

## Fattori sociali e organizzativi

I sistemi software sono utilizzati in un contesto sociale e organizzativo. Questo può influenzare o addirittura dominare i requisiti di sistema.

**I fattori sociali e organizzativi non sono un unico punto di vista, ma influenzano tutti i punti di vista.**

I buoni analisti devono essere sensibili a questi fattori, ma attualmente non esiste un modo sistematico per affrontare la loro analisi.

## Scenari d'interazione

Gli scenari sono esempi reali di come un sistema può essere utilizzato. Dovrebbero includere:

- una descrizione della situazione di partenza;
- una descrizione del normale flusso degli eventi;
- una descrizione di cosa può andare storto;
- informazioni su altre attività concomitanti;
- una descrizione dello stato al termine dello scenario.

## Casi d'uso

I casi d'uso sono una tecnica basata su scenari in UML che identifica gli attori in un'interazione e che descrivono l'interazione stessa.

Una serie di casi d'uso dovrebbe descrivere tutte le possibili interazioni con il sistema.

I diagrammi di sequenza possono essere utilizzati per aggiungere dettagli ai casi d'uso mostrando la sequenza di elaborazione degli eventi nel sistema.

## Modellazione del sistema

La modellazione del sistema aiuta l'analista a comprendere la funzionalità del sistema e i modelli vengono utilizzati per comunicare con i clienti.

Diversi modelli presentano il sistema da diverse prospettive:

- *prospettiva esterna* che mostra il contesto o l'ambiente del sistema;
- *prospettiva comportamentale* che mostra il comportamento del sistema; 
- *prospettiva strutturale* che mostra il sistema o l'architettura dei dati.

Abbiamo vari tipi di modello:

- *modello di elaborazione dei dati* che mostra come i dati vengono elaborati nelle diverse fasi;
- *modello di composizione* che mostra come le entità sono composte da altre entità;
- *modello architettonico* che mostra i principali sottosistemi;
- *modello di classificazione* che mostra come le entità hanno caratteristiche comuni;
- *modello stimolo/risposta* che mostra la reazione del sistema agli eventi.

## Modelli comportamentali

**I modelli comportamentali vengono utilizzati per descrivere il comportamento complessivo di un sistema.**

Due tipi di modello comportamentale sono:

- *modelli di elaborazione dei dati* che mostrano come i dati vengono elaborati mentre si spostano attraverso il sistema;
- *modelli di macchine a stati* che mostrano la risposta dei sistemi agli eventi.

Questi modelli mostrano prospettive diverse, quindi entrambi sono necessari per descrivere il comportamento del sistema.

## Diagrammi di flusso dei dati

I DFD modellano il sistema da una prospettiva funzionale.

Tracciare e documentare come i dati associati a un processo sono utili per sviluppare una comprensione generale del sistema.

**I diagrammi di flusso dei dati possono essere utilizzati anche per mostrare lo scambio di dati tra un sistema e altri sistemi nel suo ambiente.**

## Modelli di macchine a stati

**Questi modelli modellano il comportamento del sistema in risposta a eventi esterni e interni.**

**Mostrano le risposte del sistema agli stimoli**, quindi sono spesso usati per modellare i sistemi in tempo reale.

I modelli di macchine a stati mostrano gli stati del sistema come nodi e gli eventi come archi tra questi nodi. Quando si verifica un evento, il sistema si sposta da uno stato all'altro.

## Modelli di dati semantici

**Utilizzato per descrivere la struttura logica dei dati elaborati dal sistema.**

Un *modello entità-relazione-attributo* definisce le entità nel sistema, le relazioni tra queste entità e gli attributi dell'entità

Ampiamente utilizzato nella progettazione di database. Può essere facilmente implementato utilizzando database relazionali.

Nessuna notazione specifica fornita in UML ma è possibile utilizzare oggetti e associazioni.

## Dizionari dati

**I dizionari dati sono elenchi di tutti i nomi utilizzati nei modelli di sistema.** Sono incluse anche le descrizioni delle entità, delle relazioni e degli attributi.

I vantaggi sono che supporta la gestione dei nomi ed evita la duplicazione ed è un deposito di conoscenze organizzative che collegano analisi, progettazione e implementazione.

Molti ambienti di lavoro CASE supportano i dizionari di dati.

## Modelli di oggetti

**I modelli di oggetti descrivono il sistema in termini di classi di oggetti e le loro associazioni.**

Una classe di oggetti è un'astrazione su un insieme di oggetti con attributi comuni dei servizi (operazioni) forniti da ciascun oggetto.

Possono essere prodotti vari modelli di oggetti:

- modelli ereditari;
- modelli di aggregazione;
- modelli di interazione.

L'UML è una rappresentazione standard ideata dagli sviluppatori di metodi di analisi e progettazione orientati agli oggetti ampiamente utilizzati.

È diventato uno standard efficace per la modellazione orientata agli oggetti.

## Metodi strutturati

**I metodi strutturati incorporano la modellazione del sistema come parte intrinseca del metodo.**

I metodi definiscono un insieme di modelli, un processo per derivare questi modelli e regole e linee guida che dovrebbero applicarsi ai modelli.

Gli strumenti CASE supportano la modellazione del sistema come parte di un metodo strutturato.

## Punti deboli del metodo

- Non modellano requisiti di sistema non funzionali;
- Di solito non includono informazioni sull'adeguatezza di un metodo per un determinato problema;
- Potrebbe produrre troppa documentazione;
- I modelli di sistema a volte sono troppo dettagliati e difficili da capire per gli utenti.

## Convalida dei requisiti

**È la preoccupazione di dimostrare che i requisiti definiscono il sistema che il cliente vuole veramente.**

I costi di errore dei requisiti sono elevati, quindi la convalida è molto importante: la correzione di un errore dei requisiti dopo la consegna può costare fino a 100 volte il costo della correzione di un errore di implementazione.

Le tecniche di convalida dei requisiti sono:

- *revisioni dei requisiti:* analisi manuale sistematica dei requisiti;
- *prototipazione:* utilizzo di un modello eseguibile del sistema per verificare i requisiti;
- *generazione di casi di test:* sviluppo di test per i requisiti per verificare la testabilità.

## Verifica dei requisiti

- **Validità:** il sistema fornisce le funzioni che meglio supportano le esigenze del cliente?
- **Coerenza:** ci sono conflitti di requisiti?
- **Completezza:** sono incluse tutte le funzioni richieste dal cliente?
- **Realismo:** i requisiti possono essere implementati in base al budget e alla tecnologia disponibili?
- **Verificabilità:** è possibile verificare i requisiti?

## Gestione dei requisiti

**La gestione dei requisiti è il processo di gestione dei requisiti mutevoli durante il processo di ingegneria dei requisiti e lo sviluppo del sistema.**

I requisiti sono inevitabilmente incompleti e incoerenti. Nuovi requisiti emergono durante il processo man mano che le esigenze aziendali cambiano e viene sviluppata una migliore comprensione del sistema: punti di vista diversi hanno esigenze diverse e spesso contraddittorie.

## Tracciabilità

**La tracciabilità riguarda le relazioni tra i requisiti, le loro fonti e la progettazione del sistema:**

- *Tracciabilità alla fonte:* collegamenti dai requisiti alle parti interessate che hanno proposto questi requisiti;
- *Tracciabilità dei requisiti:* collegamenti tra requisiti dipendenti;
- *Tracciabilità del progetto:* collegamenti dai requisiti alla progettazione;

## Supporto strumento CASE

- *Archiviazione dei requisiti:* i requisiti devono essere gestiti in un archivio dati sicuro e gestito;
- *Gestione del cambiamento:* il processo di gestione del cambiamento è un processo del flusso di lavoro le cui fasi possono essere definite e il flusso di informazioni tra queste fasi è parzialmente automatizzato;
- *Gestione della tracciabilità:* recupero automatizzato dei collegamenti tra i requisiti.

## Documento dei requisiti

**Il documento dei requisiti è la dichiarazione ufficiale di ciò che è richiesto agli sviluppatori di sistema.**

Dovrebbe includere sia una definizione dei requisiti utente che una specifica dei requisiti di sistema.

*NON è il documento di progettazione.* Per quanto possibile, dovrebbe stabilire COSA dovrebbe fare il sistema piuttosto che COME dovrebbe farlo.

## Standard dei requisiti IEEE

Definisce una struttura generica per un documento dei requisiti che deve essere istanziata per ogni sistema specifico.

## Struttura del documento dei requisiti

- Prefazione
- Introduzione
- Glossario
- Definizione dei requisiti utente
- Architettura del sistema
- Specifica dei requisiti di sistema
- Modelli di sistema
- Evoluzione del sistema
- Appendici
- Indice

## Metodi formali

**È basato sulla rappresentazione matematica e sull'analisi del software.** I metodi formali non sono stati ampiamente utilizzati perché:

- altre tecniche di ingegneria del software hanno avuto successo;
- cambiamenti di mercato;
- non adatto a specificare e analizzare le interfacce utente e l'interazione dell'utente;
- i metodi formali sono ancora difficili da scalare fino a sistemi di grandi dimensioni.

I principali vantaggi dei metodi formali sono la riduzione del numero di guasti nei sistemi.

La principale area di applicabilità è nell'ingegneria dei sistemi critici: sono convenienti perché devono essere evitati costi elevati di guasto del sistema.

Di seguito, i costi di sviluppo con specifica formale:

![[specificaFormale.png]]

## Tecniche di specifica

- **Specifica algebrica:** il sistema è specificato nei termini delle sue operazioni e delle loro relazioni;
- **Specifiche basate sul modello:** il sistema è specificato in termini di un modello di stato costruito utilizzando costrutti matematici come insiemi e sequenze. Le operazioni sono definite dalle modifiche allo stato del sistema.