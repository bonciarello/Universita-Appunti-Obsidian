---
aliases: [DNS]
tags: [cyber-offence-and-defence]
---
## Serializzazione e deserializzazione

La serializzazione è il processo di conversione di strutture dati complesse, come oggetti e i loro campi, in un formato "più piatto" che può essere inviato e ricevuto come flusso sequenziale di byte. La serializzazione dei dati semplifica notevolmente:
- scrivere dati complessi nella memoria interprocesso, in un file o in un [[Database|database]]
- inviare dati complessi, ad esempio, tramite una rete, tra diversi componenti di un'applicazione o in una chiamata API

Fondamentalmente, quando si serializza un oggetto, anche il suo stato viene reso persistente. In altre parole, gli attributi dell'oggetto vengono preservati, insieme ai loro valori assegnati.

La deserializzazione è il processo di ripristino di questo flusso di byte in una replica completamente funzionale dell'oggetto originale, nello stato esatto in cui era quando è stato serializzato. La logica del sito web può quindi interagire con questo oggetto deserializzato, proprio come farebbe con qualsiasi altro oggetto.

Molti linguaggi di programmazione offrono supporto nativo per la serializzazione. Il modo esatto in cui gli oggetti vengono serializzati dipende dal linguaggio. Alcuni linguaggi serializzano gli oggetti in formati binari, mentre altri utilizzano formati di stringa diversi, con vari gradi di leggibilità umana. Nota che tutti gli attributi dell'oggetto originale sono archiviati nel flusso di dati serializzati, inclusi tutti i campi privati. Per impedire che un campo venga serializzato, deve essere contrassegnato esplicitamente come "transient" nella dichiarazione della classe.

Tieni presente che quando lavori con diversi linguaggi di programmazione, la serializzazione può essere definita marshalling (Ruby) o pickling (Python). Questi termini sono sinonimi di "serializzazione" in questo contesto.
## Definizione di deserializzazione non sicura
La deserializzazione non sicura si verifica quando i dati controllabili dall'utente vengono deserializzati da un sito web. Ciò consente potenzialmente a un aggressore di manipolare oggetti serializzati per passare dati dannosi nel codice dell'applicazione.

È persino possibile sostituire un oggetto serializzato con un oggetto di una classe completamente diversa. In modo allarmante, gli oggetti di qualsiasi classe disponibile sul sito web verranno deserializzati e istanziati, indipendentemente dalla classe prevista. Per questo motivo, la deserializzazione non sicura è talvolta nota come vulnerabilità "object injection".

Un oggetto di una classe imprevista potrebbe causare un'eccezione. A questo punto, tuttavia, il danno potrebbe essere già stato fatto. Molti attacchi basati sulla deserializzazione vengono completati prima che la deserializzazione sia terminata. Ciò significa che il processo di deserializzazione stesso può avviare un attacco, anche se la funzionalità del sito web non interagisce direttamente con l'oggetto dannoso. Per questo motivo, anche i siti web la cui logica si basa su linguaggi fortemente tipizzati possono essere vulnerabili a queste tecniche.

**Come nascono le vulnerabilità di deserializzazione non sicura?** La deserializzazione non sicura si verifica quando dati controllati dagli utenti vengono deserializzati, esponendo i sistemi a potenziali vulnerabilità. Gli sviluppatori spesso sottovalutano il rischio, pensando che controlli aggiuntivi o l’uso di formati binari siano sufficienti. Tuttavia, questi approcci sono inefficaci perché:
- **controlli post-deserializzazione:** agire dopo la deserializzazione è spesso troppo tardi per fermare attacchi;
- **complessità delle dipendenze:** le molteplici librerie e classi usate dai siti moderni ampliano la superficie di attacco, rendendo difficile prevedere e bloccare l’abuso di metodi o concatenazioni inattese.

In conclusione, si può sostenere che non è possibile deserializzare in modo sicuro l'input non attendibile.

**Qual è l'impatto della deserializzazione non sicura?** L'impatto della deserializzazione non sicura può essere molto grave perché fornisce un punto di ingresso a una superficie di attacco enormemente aumentata. Consente a un aggressore di riutilizzare il codice applicativo esistente in modi dannosi, con conseguenti numerose altre vulnerabilità, spesso l'esecuzione di codice remoto. Anche nei casi in cui l'esecuzione di codice remoto non è possibile, la deserializzazione non sicura può portare a un'escalation di privilegi, accesso arbitrario ai file e attacchi di negazione del servizio.
### Come identificare la deserializzazione non sicura
Identificare la deserializzazione non sicura è relativamente semplice, indipendentemente dal fatto che si stia eseguendo un test whitebox o blackbox.

Durante l'audit, dovresti esaminare tutti i dati passati al sito web e cercare di identificare qualsiasi cosa che assomigli a dati serializzati. I dati serializzati possono essere identificati relativamente facilmente se si conosce il formato utilizzato dai diversi linguaggi.

PHP utilizza un formato stringa per lo più leggibile dall'uomo, con lettere che rappresentano il tipo di dati e numeri che rappresentano la lunghezza di ogni voce. Ad esempio, considera un oggetto `User` con gli attributi:

`$user->name = "carlos";`
`$user->isLoggedIn = true;`

Quando serializzato, questo oggetto potrebbe apparire simile a questo:

`O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}`

Questo può essere interpretato come segue:
- `O:4:"User"`: un oggetto con il nome di classe di 4 caratteri "User";
- `2`: l'oggetto ha 2 attributi;
- `s:4:"name"`: la chiave del primo attributo è la stringa di 4 caratteri "name";
- `s:6:"carlos"`: il valore del primo attributo è la stringa di 6 caratteri "carlos";
- `s:10:"isLoggedIn"`: la chiave del secondo attributo è la stringa di 10 caratteri "isLoggedIn";
- `b:1`: il valore del secondo attributo è il valore booleano true.

I metodi nativi per la serializzazione PHP sono `serialize()` e `unserialize()`. Se hai accesso al codice sorgente, dovresti iniziare cercando `unserialize()` in qualsiasi punto del codice e indagando ulteriormente.
## Sfruttamento delle vulnerabilità di deserializzazione non sicura
### Manipolazione di oggetti serializzati
Sfruttare alcune vulnerabilità di deserializzazione può essere semplice come modificare un attributo in un oggetto serializzato. Man mano che lo stato dell'oggetto viene reso persistente, puoi studiare i dati serializzati per identify e modificare valori di attributo interessanti. Puoi quindi passare l'oggetto dannoso nel sito web tramite il suo processo di deserializzazione. Questo è il passaggio iniziale per un exploit di deserializzazione di base.

In generale, ci sono due approcci che puoi adottare quando manipoli oggetti serializzati. Puoi modificare l'oggetto direttamente nella sua forma di flusso di byte oppure puoi scrivere un breve script nel linguaggio corrispondente per creare e serializzare tu stesso il nuovo oggetto. Quest'ultimo approccio è spesso più semplice quando si lavora con formati di serializzazione binari.

**Modifica degli attributi degli oggetti** Quando si manomettono i dati, finché l'aggressore conserva un oggetto serializzato valido, il processo di deserializzazione creerà un oggetto lato server con i valori degli attributi modificati.

Come semplice esempio, considera un sito web che utilizza un oggetto `User` serializzato per archiviare i dati sulla sessione di un utente in un cookie. Se un aggressore individuasse questo oggetto serializzato in una richiesta HTTP, potrebbe decodificarlo per trovare il seguente flusso di byte:

`O:4:"User":2:{s:8:"username";s:6:"carlos";s:7:"isAdmin";b:0;}`

L'attributo `isAdmin` è un ovvio punto di interesse. Un aggressore potrebbe semplicemente modificare il valore booleano dell'attributo in 1 (vero), ricodificare l'oggetto e sovrascrivere il proprio cookie corrente con questo valore modificato. In isolamento, questo non ha alcun effetto. Tuttavia, supponiamo che il sito web utilizzi questo cookie per verificare se l'utente corrente ha accesso a determinate funzionalità amministrative:

```php
$user = unserialize($_COOKIE);
if ($user->isAdmin === true) {
// consenti l'accesso all'interfaccia di amministrazione
}
```

Questo codice vulnerabile istanzia un oggetto `User` in base ai dati del cookie, incluso l'attributo `isAdmin` modificato dall'attaccante. In nessun momento viene verificata l'autenticità dell'oggetto serializzato. Questi dati vengono quindi passati nell'istruzione condizionale e, in questo caso, consentirebbero una facile escalation dei privilegi.

Questo semplice scenario non è comune in natura. Tuttavia, la modifica di un valore di attributo, in questo modo, dimostra il primo passo verso l'accesso all'enorme quantità di superficie di attacco esposta dalla deserializzazione non sicura.

**Modifica dei tipi di dati** La logica basata su PHP è particolarmente vulnerabile a questo tipo di manipolazione a causa del comportamento del suo operatore di confronto approssimativo (`==`) quando si confrontano tipi di dati diversi. Ad esempio, se si esegue un confronto approssimativo tra un intero e una stringa, PHP tenterà di convertire la stringa in un intero, il che significa che `5 == "5"` viene valutato come vero.

Insolitamente, questo funziona anche per qualsiasi stringa alfanumerica che inizia con un numero. In questo caso, PHP convertirà effettivamente l'intera stringa in un valore intero in base al numero iniziale. Il resto della stringa viene ignorato completamente. Pertanto, `5 == "5 di qualcosa"` è in pratica trattato come `5 == 5`.

Questo diventa ancora più strano quando si confronta una stringa con l'intero 0:

`0 == "Stringa di esempio" // vero`

Perché? Perché non c'è alcun numero, ovvero 0 numeri nella stringa. PHP tratta questa stringa intera come l'intero 0.

Considera un caso in cui questo operatore di confronto libero viene utilizzato insieme a dati controllabili dall'utente da un oggetto deserializzato. Ciò potrebbe potenzialmente causare pericolosi difetti logici.

```php
$login = unserialize($_COOKIE)
if ($login['password'] == $password) {
// accedi correttamente
}
```

Supponiamo che un aggressore abbia modificato l'attributo password in modo che contenesse l'intero `0` anziché la stringa prevista. Finché la password memorizzata non inizia con un numero, la condizione restituirà sempre true, abilitando un bypass di autenticazione. Nota che ciò è possibile solo perché la deserializzazione preserva il tipo di dati. Se il codice recuperasse la password direttamente dalla richiesta, lo `0` verrebbe convertito in una stringa e la condizione verrebbe valutata come false.

Tieni presente che quando modifichi i tipi di dati in qualsiasi formato di oggetto serializzato, è importante ricordare di aggiornare anche le etichette di tipo e gli indicatori di lunghezza nei dati serializzati. In caso contrario, l'oggetto serializzato verrà danneggiato e non verrà deserializzato.
### Utilizzo delle funzionalità dell'applicazione
Oltre a controllare semplicemente i valori degli attributi, la funzionalità di un sito web potrebbe anche eseguire operazioni pericolose sui dati di un oggetto deserializzato. In questo caso, puoi utilizzare la deserializzazione non sicura per passare dati inaspettati e sfruttare la funzionalità correlata per causare danni.

Ad esempio, come parte della funzionalità "Delete user" di un sito web, l'immagine del profilo dell'utente viene eliminata accedendo al percorso del file nell'attributo `$user->image_location`. Se questo `$user` è stato creato da un oggetto serializzato, un aggressore potrebbe sfruttarlo passando un oggetto modificato con `image_location` impostato su un percorso di file arbitrario. L'eliminazione del proprio account utente eliminerebbe quindi anche questo file arbitrario.
### Magic methods
I magic methods sono metodi speciali che vengono invocati automaticamente al verificarsi di determinati [[Eventi|eventi]], senza bisogno di richiamarli esplicitamente. Questi metodi, comuni nella programmazione orientata agli oggetti, sono spesso identificati da doppie underscore nel nome (ad esempio, `__construct()` in PHP o `__init__()` in Python).

I magic method possono essere personalizzati dagli sviluppatori per eseguire codice specifico in risposta a [[Eventi|eventi]], come l’istanziazione di un oggetto (`__construct()` in PHP) o il caricamento di dati durante la deserializzazione (`__wakeup()` in PHP e `readObject()` in Java). In Java, i metodi come `readObject()` permettono alle classi di controllare come i campi vengono deserializzati.

I rischi potenziali sono:
- **manipolazione dati:** se i magic methods gestiscono dati controllabili dall’utente (ad esempio, provenienti da oggetti deserializzati), possono diventare vettori di attacco;
- **invocazioni automatiche:** alcuni linguaggi, come PHP e Java, attivano magic methods automaticamente durante la deserializzazione. Ciò consente agli attaccanti di eseguire codice arbitrario inserendo dati malformati nei processi di deserializzazione.

Attenzione particolare deve essere prestata alle classi che includono magic methods legati alla deserializzazione. Questi metodi possono fungere da punto di ingresso per exploit più sofisticati, consentendo agli attaccanti di manipolare i dati prima che l’oggetto sia completamente deserializzato. Questo scenario è alla base di molte vulnerabilità basate sulla deserializzazione.
### Iniezione di oggetti arbitrari
È occasionalmente possibile sfruttare la deserializzazione non sicura semplicemente modificando l'oggetto fornito dal sito web. Tuttavia, l'iniezione di tipi di oggetto arbitrari può aprire molte più possibilità.

Nella programmazione orientata agli oggetti, i metodi disponibili per un oggetto sono determinati dalla sua classe. Pertanto, se un aggressore può manipolare la classe di oggetto che viene passata come dati serializzati, può influenzare il codice che viene eseguito dopo e persino durante la deserializzazione.

I metodi di deserializzazione in genere non controllano cosa stanno deserializzando. Ciò significa che puoi passare oggetti di qualsiasi classe serializzabile disponibile sul sito web e l'oggetto verrà deserializzato. Ciò consente effettivamente a un aggressore di creare istanze di classi arbitrarie. Il fatto che questo oggetto non sia della classe prevista non ha importanza. Il tipo di oggetto inaspettato potrebbe causare un'eccezione nella logica dell'applicazione, ma l'oggetto dannoso sarà già stato istanziato a quel punto.

Se un aggressore ha accesso al codice sorgente, può studiare tutte le classi disponibili in dettaglio. Per costruire un semplice exploit, cercherebbero classi contenenti magic methods di deserializzazione; quindi, controllererebbero se qualcuno di essi esegue operazioni pericolose su dati controllabili. L'attaccante può quindi passare un oggetto serializzato di questa classe per utilizzare il suo metodo magico per un exploit.
### Gadget chain
L’individuazione manuale delle gadget chain può essere complessa e quasi impossibile senza l’accesso al codice sorgente. Tuttavia, esistono strumenti che offrono gadget chain già scoperte e sfruttate su altri siti. Questi strumenti permettono di identificare e sfruttare vulnerabilità di deserializzazione insicure con facilità, grazie all’ampio utilizzo di librerie contenenti gadget chain sfruttabili.

**Lavorare con gadget chain pre-costruite** Identificare manualmente le gadget chain può essere un processo piuttosto arduo, ed è quasi impossibile senza l'accesso al codice sorgente. Fortunatamente, ci sono alcune opzioni per lavorare con gadget chain pre-costruite che puoi provare prima.

Ci sono diversi strumenti disponibili che forniscono una gamma di catene pre-scoperte che sono state sfruttate con successo su altri siti web. Anche se non hai accesso al codice sorgente, puoi usare questi strumenti sia per identificare che per sfruttare vulnerabilità di deserializzazione non sicure con relativamente poco sforzo. Questo approccio è reso possibile dall'uso diffuso di librerie che contengono gadget chain sfruttabili. Ad esempio, se una gadget chain nella libreria **Apache Commons Collections** di Java può essere sfruttata su un sito web, qualsiasi altro sito web che implementa questa libreria potrebbe essere sfruttabile usando la stessa catena.

La maggior parte dei linguaggi che soffrono frequentemente di vulnerabilità di deserializzazione non sicura hanno strumenti proof-of-concept equivalenti. Ad esempio, per i siti basati su PHP puoi usare **PHP Generic Gadget Chains (PHPGGC)**.

**Nota:** è importante notare che la vulnerabilità è la deserializzazione di dati controllabili dall'utente, non la mera presenza di una gadget chain nel codice del sito web o in una qualsiasi delle sue librerie. La gadget chain è solo un mezzo per manipolare il flusso di dati dannosi una volta che sono stati iniettati. Ciò si applica anche a varie vulnerabilità di corruzione della memoria che si basano sulla deserializzazione di dati non attendibili. In altre parole, un sito web potrebbe comunque essere vulnerabile anche se in qualche modo riuscisse a collegare ogni possibile gadget chain.
## Come prevenire le vulnerabilità di deserializzazione non sicure
In generale, la deserializzazione dell'input utente dovrebbe essere evitata a meno che non sia assolutamente necessaria. L'elevata gravità degli exploit che potenzialmente consente e la difficoltà di proteggersi da essi superano i vantaggi in molti casi.

Se hai bisogno di deserializzare dati da fonti non attendibili, incorpora misure robuste per assicurarti che i dati non siano stati manomessi. Ad esempio, potresti implementare una firma digitale per verificare l'integrità dei dati. Tuttavia, ricorda che tutti i controlli devono essere eseguiti prima di iniziare il processo di deserializzazione. Altrimenti, sono di scarsa utilità.

Se possibile, dovresti evitare del tutto di utilizzare funzionalità di deserializzazione generiche. I dati serializzati da questi metodi contengono tutti gli attributi dell'oggetto originale, inclusi i campi privati che potenzialmente contengono informazioni sensibili. Invece, potresti creare i tuoi metodi di serializzazione specifici per classe in modo da poter almeno controllare quali campi sono esposti.

Infine, ricorda che la vulnerabilità è la deserializzazione dell'input utente, non la presenza di gadget chain che gestiscono successivamente i dati. Non affidarti al tentativo di eliminare le gadget chain che identifichi durante i test. È poco pratico provare a tapparle tutte a causa della rete di dipendenze tra librerie che quasi certamente esistono sul tuo sito web. In qualsiasi momento, anche gli exploit di corruzione della memoria documentati pubblicamente sono un fattore, il che significa che la tua applicazione potrebbe essere vulnerabile in ogni caso.
