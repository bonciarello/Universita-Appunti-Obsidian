## Definizione: le sezioni di un contratto

Il seguente schema mostra una descizione delle sezioni di un contratto:

- **Operazione:** nome e parametri dell'operazione;
- **Riferimenti:** casi d'uso in cui può verificarsi questa operazione;
- **Pre-condizioni:** ipotesi significative sullo stato del sistema o degli oggetti nel Modello del Dominio prima dell'esecuzione dell'operazione. Si tratta di ipotesi non banali, che dovrebbero essere comunicate al lettore;
- **Post-condizioni:** è la sezione più importante. Lo stato degli oggetti nel Modello di Dominio dopo il completamento dell'operazione.

## Definizione: che cos'è un'operazione di sistema

I contratti delle operazioni possono essere definiti per le **operazioni di sistema**, ovvero operazioni che il sistema, considerato come un componente a scatola nera, offre nella sua interfaccia pubblica. Le operazioni di sistema possono essere identificare mentre si abbozzano gli SSD, come nella figura successiva,. Per essere più precisi, gli SSD mostrano **eventi di sistema**, ovvero eventi o messaggi di I/O relativi al sistema. Un evento di sistema di input implica che il sistema contenga un'operazione di sistema per gestire quell'evento, così come un messaggio OO (che è un tipo di evento o segnale) viene gestito da un metodo OO (che è un tipo di operazione).

![[contrattiOperazioni1.png]]

L'intero insieme delle operazioni di sistema, tra tutti i casi d'uso, definisce l'**interfaccia di sistema** pubblica, considerando il sistema come un singolo componente o una singola classe. In UML, il sistema nel suo insieme può essere rappresentato come un unico oggetto di una classe denominata, per esempio, *System*.

## Definizione: post-condizioni

Si noti che ciascuna delle post-condizioni nell'esempio *enterItem* comprende una classificazione di aiuto per l'apprendimento come *creazione di istanza* o *associazione formata*. Ecco un punto importante.

> **Definizione:** le **post-condizioni** descrivono i cambiamenti nello stato degli oggetti nel modello di dominio. I cambiamenti di stato nel modello di dominio comprendono le istanze create, le associazioni formate o rotte e gli attributi modificati.

***Le post-condizioni non sono azioni da eseguire nel corso dell'operazione;*** si tratta piuttosto di *osservazioni* (rilevazioni) sugli oggetti del modello di dominio che risultano avere al termine dell'operazione.

Per riassumere, le post-condizioni rientrano nelle seguenti categorie:

- Creazione o cancellazione di istanza;
- Cambiamento del valore di un attributo;
- Associazioni (per essere precisi, *collegamenti*, in UML) formate o spezzate.

Come esempio di associazione spezzata, si consideri un'operazione che consente l'eliminazione di una riga di vendita per un articolo. La post-condizione potrebbe essere: "È stata spezzata l'associazione tra la *SalesLineItem* selezionata e la *Sale* corrente". In altri domini, le associazioni vengono spezzate quando viene estinto un mutuo o quando qualcuno revoca la propria iscrizione a qualche società.

Le post-condizioni di cancellazione (eliminazione) di istanza sono le più rare, poiché solitamente non si ha interesse nell'attuare in modo esplicito la distruzione di qualcosa ne mondo reale. Per esempio: in molti paesi dopo che una persona ha dichiarato il fallimento e sono trascorsi sette o dieci anni, tutta la documentazione relativa al fallimento deve essere distrutta per legge. Si noti che questo è un punto di vista concettuale, non di implementazione. Non si tratta di istruzioni su come liberare la memoria di un computer occupata da oggetti software.

### Le post-condizioni sono correlate al modello di dominio

Queste post-condizioni sono espresse nel contesto degli oggetti del Modello di Dominio. Quali istanze si possono creare? Quelle del Modello di Dominio. Quali associazioni di possono formare? Quelle del Modello di Dominio, e così via.

## Linea guida: come creare e scrivere contratti

Per creare i contratti si proceda come segue:

1. Identificare le operazioni di sistema dagli SSD;
2. Creare un contratto per le operazioni di sistema complesse o i cui effetti sono probabilmente sottili, o che non sono chiare dai casi d'uso;
3. Per descrivere le post-condizioni si utilizzino le seguenti categorie: 
    - creazione o cancellazione di istanza;
    - modifica di attributo;
    - associazione formata o spezzata.

### Scrivere i contratti

- Come accennato, le post-condizioni vanno scritte in una forma dichiarativa, con una forma verbale al passato e passiva (*è stato...*) per sottolineare l'*osservazione* di un cambiamento, e non un progetto di come lo si otterrà. Per esempio: 
    - (consigliato) **È stata** creata una *SalesLineItem*;
    - (peggiore) Creare una *SalesLineItem*;
- Ci si ricordi di stabilire le necessarie associazioni tra oggetti esistenti o con quelli appena creati. Per esempio, quando si verifica l'operazione *enterItem*, non è sufficiente che sia stata creata una nuova istanza di *SalesLineItem*. Dopo che l'operazione è stata completata, deve essere anche vero che l'istanza appena creata di *SalesLineItem* è stata associata con una *Sale*; pertanto: 
    - La *SalesLineItem* è stata associata con la *Sale* corrente (associazione formata).

### L'errore più comune

Il problema più comune è dimenticarsi di includere la *formazione di associazioni*. In particolare, quando sono create nuove istanze, è molto probabile che debbano essere stabilite delle associazioni con diversi oggetti. Non bisogna dimenticarlo!

## Esempi di contratto

### Contratto CO1: makeNewSale

- **Operazione:** makeNewSale();
- **Riferimenti:** casi d'uso: Process Sale;
- **Pre-condizioni:** nessuna;
- **Post-condizioni:** 
    - è stata creata un'istanza s di Sale (creazione di istanza);
    - s è stata associata con Register (associazione formata);
    - gli attributi di s sono stati inizializzati (modifica di attributi).

Si noti la descrizione vaga nell'ultima post-condizione. Se è compatibile, può essere accettata. 
In un progetto, tutte queste particolari post-condizioni sono talmente ovvie nel caso d'uso che probabilmente non verrebbe scritto il contratto per *makeNewSale*.
Si rammenti uno dei principi guida di un processo sano e UP: mantenere la massima leggerezza, evitare tutti gli elaborati a meno che non aggiungano realmente valore.

### Contratto CO2: enterItem

- **Operazione:** enterItem(itemID: ItemID, quantity: integer);
- **Riferimenti:** casi d'uso: Process Sale;
- **Pre-condizioni:** è in corso una vendita;
- **Post-condizioni:** 
    - è stata creata un'istanza sli di SalesLineItem (creazione di istanza);
    - sli è stata associata con la Sale (vendita) corrente (associazione formata);
    - sli.quantity è diventata quantity (modifica di attributo);
    - sli è stata associata con una ProductDescription, in base alla corrispondenza con itemID (associazione formata).

### Contratto CO3: endSale

- **Operazione:** endSale();
- **Riferimenti:** casi d'uso: Process Sale;
- **Pre-condizioni:** è in corso una vendita;
- **Post-condizioni:** 
    - Sale.isComplete è diventato true (modifica di attributo);