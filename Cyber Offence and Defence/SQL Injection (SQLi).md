L'SQL injection (SQLi) è una vulnerabilità della sicurezza web che consente a un aggressore di interferire con le query che un'applicazione effettua sul suo database. Ciò può consentire a un aggressore di visualizzare dati che normalmente non è in grado di recuperare. Ciò potrebbe includere dati che appartengono ad altri utenti o qualsiasi altro dato a cui l'applicazione può accedere. In molti casi, un aggressore può modificare o eliminare questi dati, causando modifiche persistenti al contenuto o al comportamento dell'applicazione.

In alcune situazioni, un aggressore può intensificare un attacco di SQLi per compromettere il server sottostante o altre infrastrutture back-end. Può anche consentire loro di eseguire attacchi denial-of-service.

Nell’immagine di esempio, oltre alla categoria scelta, unirà la tabella `users` prendendo username e password (quindi rubando dati a cui non potrebbe accedere).

**SQLi è in declino:** basti guardare la OWASP Top Ten per affermare che la priorità di questa tipologia di attacco è diminuita dal 2017 al 2021.

In qualsiasi caso, la maggior parte dei framework web ha meccanismi di protezione integrati *ancora comuni e solitamente critici*.

Possiamo classificare SQLi come di seguito:

*   **classic SQLi:** un attaccante manipola direttamente una query SQL inserendo codice SQL malevolo in un modulo web vulnerabile o in un parametro URL. L’obiettivo è ingannare l’applicazione affinché esegua query SQL non desiderate, permettendo l’accesso non autorizzato ai dati, la modifica o persino la cancellazione delle informazioni;
```sql
' UNION SELECT username, password FROM users--`
    `SELECT name, description FROM products WHERE category = 'Gifts' UNION SELECT username, password FROM users--
```
*   **blind SQLi:** l’attaccante non riceve feedback diretto dal database riguardo al successo o al fallimento della query iniettata. Invece, l’attaccante formula domande al database a cui si può rispondere con vero o falso e osserva come l’applicazione risponde (ad esempio, il comportamento della pagina o i tempi di risposta) per dedurre informazioni in modo indiretto. È più difficile da sfruttare, ma molto pericoloso;
*   **first-order SQLi:** avviene quando l’input malevolo viene eseguito immediatamente dall’applicazione in un singolo ciclo di request-response. Il payload dell’attaccante viene utilizzato direttamente nella costruzione della query SQL, consentendogli di manipolare immediatamente il database;
*   **second-order SQLi:** l’input malevolo viene memorizzato dall’applicazione (ad esempio, in un database) e viene eseguito successivamente in un contesto diverso. L’attacco non avviene immediatamente, ma viene attivato quando i dati memorizzati vengono recuperati e utilizzati in una futura query SQL;
*   **in-band:** si verifica quando l’attaccante inietta il payload malevolo e riceve i risultati della query attraverso lo stesso canale di comunicazione (ad esempio, la pagina web). È il tipo di SQLi più comune e facile da sfruttare, poiché l’attaccante può vedere direttamente i risultati.
*   **out-of-band:** l’attaccante inietta codice SQL ma riceve i risultati attraverso un canale diverso (come una richiesta HTTP separata o una risposta via email). Questo tipo di attacco è meno comune e viene utilizzato quando i metodi “in-band” non sono praticabili o il database non risponde direttamente.

## Qual è l'impatto di un attacco SQLi riuscito?

Un attacco SQLi riuscito può causare l'accesso non autorizzato a dati sensibili, come:

*   password;
*   dettagli della carta di credito;
*   informazioni personali dell'utente.

Gli attacchi di SQLi sono stati utilizzati in molte violazioni di dati di alto profilo nel corso degli anni. Questi hanno causato danni alla reputazione e sanzioni normative. In alcuni casi, un aggressore può ottenere una backdoor persistente nei sistemi di un'organizzazione, portando a una compromissione a lungo termine che può passare inosservata per un periodo prolungato.

## Come rilevare le vulnerabilità di SQLi?

È possibile rilevare manualmente l'SQLi utilizzando un set sistematico di test su ogni punto di ingresso nell'applicazione. Per fare ciò, in genere si invia:

*   il carattere di virgoletta singola `'` e si cercano errori o altre anomalie;
*   una sintassi specifica di SQL che valuta il valore di base (originale) del punto di ingresso e un valore diverso e si cercano differenze sistematiche nelle risposte dell'applicazione;
*   condizioni booleane come `OR 1=1` e `OR 1=2` e si cercano differenze nelle risposte dell'applicazione.
*   payload progettati per innescare ritardi temporali quando eseguiti all'interno di una query SQL e si cercano differenze nel tempo impiegato per rispondere;
*   payload OAST progettati per innescare un'interazione di rete fuori banda quando eseguiti all'interno di una query SQL e si monitorano le interazioni risultanti.

La maggior parte delle vulnerabilità delle SQLi si verifica all'interno della clausola `WHERE` di una query `SELECT`. La maggior parte dei tester esperti ha familiarità con questo tipo di SQLi.

Tuttavia, le vulnerabilità delle SQLi possono verificarsi in qualsiasi posizione all'interno della query e all'interno di diversi tipi di query. Alcune altre posizioni comuni in cui si verifica la SQLi sono:

*   nelle istruzioni `UPDATE`, all'interno dei valori aggiornati o della clausola `WHERE`;
*   nelle istruzioni `INSERT`, all'interno dei valori inseriti;
*   nelle istruzioni `SELECT`, all'interno del nome della tabella o della colonna;
*   nelle istruzioni `SELECT`, all'interno della clausola `ORDER BY`.

Esistono numerose vulnerabilità, attacchi e tecniche di SQLi che si verificano in situazioni diverse. Alcuni esempi comuni di SQLi includono:

*   **recupero di dati nascosti** in cui è possibile modificare una query SQL per restituire risultati aggiuntivi;
*   **sovvertimento della logica dell'applicazione** in cui è possibile modificare una query per interferire con la logica dell'applicazione;
*   **attacchi UNION** in cui è possibile recuperare dati da diverse tabelle di database;
*   **blind SQLi** in cui i risultati di una query controllata non vengono restituiti nelle risposte dell'applicazione.

## Sovvertimento della logica dell'applicazione

Immagina un'applicazione che consente agli utenti di effettuare l'accesso con un nome utente e una password. Se un utente invia il nome utente `wiener` e la password `bluecheese`, l'applicazione controlla le credenziali eseguendo la seguente query SQL:

```sql
SELECT * FROM users WHERE username = 'wiener' AND password = 'bluecheese'
```

Se la query restituisce i dettagli di un utente, l'accesso ha esito positivo. In caso contrario, viene rifiutato.

In questo caso, un aggressore può effettuare l'accesso come qualsiasi utente senza dover immettere una password. Può farlo utilizzando la sequenza di commenti SQL `--` per rimuovere il controllo della password dalla clausola `WHERE` della query. Ad esempio, l'invio del nome utente `administrator'--` e di una password vuota genera la seguente query:

```sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
```

Questa query restituisce l'utente il cui username è administrator e consente all'aggressore di effettuare correttamente l'accesso come tale utente.

*(APPRENTICE: SQL injection vulnerability allowing login bypass)*

## Recupero di dati nascosti

Immagina un'applicazione di shopping che mostra prodotti in diverse categorie. Quando l'utente clicca sulla categoria Gifts, il suo browser richiede l'URL:

`https://insecure-website.com/products?category=Gifts`

Ciò fa sì che l'applicazione esegua una query SQL per recuperare i dettagli dei prodotti rilevanti dal database:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Questa query SQL chiede al database di restituire:

*   tutti i dettagli (*)
*   dalla tabella dei prodotti
*   dove la `category` è `Gifts`
*   e `released` è 1.

La restrizione `released = 1` viene utilizzata per nascondere i prodotti che non sono stati rilasciati. Potremmo supporre che per i prodotti non rilasciati, `released = 0`.

L'applicazione non implementa alcuna difesa contro gli attacchi di SQLi. Ciò significa che un aggressore può costruire il seguente attacco, ad esempio:

`https://insecure-website.com/products?category=Gifts'--`

Il risultato è la query SQL:

```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

In modo cruciale, nota che `--` è un indicatore di commento in SQL. Ciò significa che il resto della query viene interpretato come un commento, rimuovendolo di fatto. In questo esempio, ciò significa che la query non include più `AND released = 1`. Di conseguenza, vengono visualizzati tutti i prodotti, inclusi quelli che non sono ancora stati rilasciati.

Puoi usare un attacco simile per far sì che l'applicazione visualizzi tutti i prodotti in qualsiasi categoria, incluse le categorie di cui non è a conoscenza:

`https://insecure-website.com/products?category=Gifts'+OR+1=1--`

Il risultato è la query SQL:

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```

La query modificata restituisce tutti gli elementi in cui la categoria è Gifts oppure 1 è uguale a 1. Poiché `1=1` è sempre vero, la query restituisce tutti gli elementi.

**Attenzione!** Fai attenzione quando inserisci la condizione `OR 1=1` in una query SQL. Anche se sembra innocua nel contesto in cui la stai inserendo, è comune che le applicazioni utilizzino dati da una singola richiesta in più query diverse. Se la tua condizione raggiunge un'istruzione `UPDATE` o `DELETE`, ad esempio, può causare una perdita accidentale di dati.

*(APPRENTICE: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data)*

## Esaminando il database

Alcune funzionalità principali del linguaggio SQL sono implementate nello stesso modo su piattaforme di database popolari, e molti modi di rilevare e sfruttare le vulnerabilità di iniezione SQL funzionano in modo identico su diversi tipi di database.

Tuttavia, ci sono anche molte differenze tra database comuni. Ciò significa che alcune tecniche per rilevare e sfruttare l'SQLi funzionano in modo diverso su piattaforme diverse. Ad esempio:

*   sintassi per la concatenazione di stringhe;
*   commenti;
*   query in batch (o impilate);
*   API specifiche della piattaforma;
*   messaggi di errore.

Dopo aver identificato una vulnerabilità di SQLi, spesso è utile ottenere informazioni sul database. Queste informazioni possono aiutarti a sfruttare la vulnerabilità.

Puoi interrogare i dettagli della versione per il database. Metodi diversi funzionano per tipi di database diversi. Ciò significa che se trovi un metodo particolare che funziona, puoi dedurre il tipo di database. Ad esempio, su Oracle puoi eseguire:

```sql
SELECT * FROM v$version
```

Puoi anche identificare quali tabelle di database esistono e le colonne che contengono. Ad esempio, sulla maggior parte dei database puoi eseguire la seguente query per elencare le tabelle:

```sql
SELECT * FROM information_schema.tables
```

Per altre informazioni, leggere la pagina a questo link.

## Attacchi UNION

Quando un'applicazione è vulnerabile alle SQLi e i risultati della query vengono restituiti nelle risposte dell'applicazione, puoi usare la parola chiave `UNION` per recuperare dati da altre tabelle nel database. Questo è comunemente noto come **attacco UNION di SQLi**.

La parola chiave `UNION` ti consente di eseguire una o più query `SELECT` aggiuntive e di aggiungere i risultati alla query originale. Ad esempio:

```sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

Questa query SQL restituisce un singolo set di risultati con due colonne, contenente i valori delle colonne a e b nella `table1` e delle colonne c e d nella `table2`.

Per far funzionare una query UNION, devono essere soddisfatti due requisiti chiave:

*   le singole query devono restituire lo stesso numero di colonne;
*   i tipi di dati in ogni colonna devono essere compatibili tra le singole query.

Per eseguire un attacco UNION di SQLi, assicurati che il tuo attacco soddisfi questi due requisiti. Ciò comporta normalmente la scoperta di:

*   quante colonne vengono restituite dalla query originale;
*   quali colonne restituite dalla query originale sono di un tipo di dati adatto a contenere i risultati della query iniettata.

### Determinazione del numero di colonne necessarie

Quando esegui un attacco UNION di SQLi, ci sono due metodi efficaci per determinare quante colonne vengono restituite dalla query originale.

**Il primo metodo** prevede l'iniezione di una serie di clausole `ORDER BY` e l'incremento dell'indice di colonna specificato finché non si verifica un errore. Ad esempio, se il punto di iniezione è una stringa tra virgolette all'interno della clausola `WHERE` della query originale, dovresti inviare:

```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
etc.
```

Questa serie di payload modifica la query originale per ordinare i risultati in base a colonne diverse nel set di risultati. La colonna in una clausola `ORDER BY` può essere specificata dal suo indice; quindi, non è necessario conoscere i nomi di alcuna colonna. Quando l'indice di colonna specificato supera il numero di colonne effettive nel set di risultati, il database restituisce un errore, come:

`The ORDER BY position number 3 is out of range of the number of items in the select list.`

L'applicazione potrebbe effettivamente restituire l'errore del database nella sua risposta HTTP, ma potrebbe anche emettere una risposta di errore generica. In altri casi, potrebbe semplicemente non restituire alcun risultato. In entrambi i casi, finché riesci a rilevare qualche differenza nella risposta, puoi dedurre quante colonne vengono restituite dalla query.

**Il secondo metodo** prevede l'invio di una serie di payload `UNION SELECT` che specificano un numero diverso di valori nulli:

```sql
' UNION SELECT NULL --
' UNION SELECT NULL, NULL --
' UNION SELECT NULL , NULL, NULL --
etc.
```

Se il numero di valori nulli non corrisponde al numero di colonne, il database restituisce un errore, ad esempio:

`All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.`

Utilizziamo `NULL` come valori restituiti dalla query `SELECT` iniettata perché i tipi di dati in ogni colonna devono essere compatibili tra le query originali e quelle iniettate. `NULL` è convertibile in ogni tipo di dati comune, quindi massimizza la possibilità che il payload abbia successo quando il conteggio delle colonne è corretto.

Come con la tecnica `ORDER BY`, l'applicazione potrebbe effettivamente restituire l'errore del database nella sua risposta HTTP, ma potrebbe restituire un errore generico o semplicemente non restituire alcun risultato.
Quando il numero di valori nulli corrisponde al numero di colonne, il database restituisce una riga aggiuntiva nel set di risultati, contenente valori nulli in ogni colonna. L'effetto sulla risposta HTTP dipende dal codice dell'applicazione. Se sei fortunato, vedrai del contenuto aggiuntivo all'interno della risposta, come una riga extra su una tabella HTML. Altrimenti, i valori nulli potrebbero innescare un errore diverso, come una `NullPointerException`. Nel caso peggiore, la risposta potrebbe apparire uguale a una risposta causata da un numero errato di valori nulli. Ciò renderebbe questo metodo inefficace.

*(PRACTITIONER: SQL injection UNION attack, determining the number of columns returned by the query)*

### Trovare colonne con un tipo di dati utile

Un attacco UNION di SQLi consente di recuperare i risultati da una query iniettata. I dati interessanti che si desidera recuperare sono normalmente in formato stringa. Ciò significa che è necessario trovare una o più colonne nei risultati della query originale il cui tipo di dati è, o è compatibile con, dati stringa.

Dopo aver determinato il numero di colonne richieste, è possibile sondare ogni colonna per verificare se può contenere dati stringa. È possibile inviare una serie di payload `UNION SELECT` che inseriscono un valore stringa in ogni colonna a turno. Ad esempio, se la query restituisce quattro colonne, si invierà:

```sql
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```

Se il tipo di dati della colonna non è compatibile con i dati stringa, la query inserita causerà un errore del database, ad esempio:

`Conversion failed when converting the varchar value 'a' to data type int.`

Se non si verifica alcun errore e la risposta dell'applicazione contiene del contenuto aggiuntivo, tra cui il valore stringa inserito, la colonna pertinente è adatta per il recupero dei dati stringa.

*(PRACTITIONER: SQL injection UNION attack, finding a column containing text)*

### Utilizzo di un attacco UNION di SQLi per recuperare dati interessanti

Una volta determinato il numero di colonne restituite dalla query originale e individuate quali colonne possono contenere dati stringa, si è in grado di recuperare dati interessanti.

Supponiamo che:

*   la query originale restituisca due colonne, entrambe in grado di contenere dati stringa;
*   il punto di iniezione è una stringa tra virgolette all'interno della clausola `WHERE`;
*   il database contiene una tabella denominata `utenti` con le colonne `username` e `password`.

In questo esempio, è possibile recuperare il contenuto della tabella `users` inviando l'input:

```sql
' UNION SELECT username, password FROM users--
```

Per eseguire questo attacco, devi sapere che esiste una tabella chiamata `users` con due colonne chiamate `username` e `password`. Senza queste informazioni, dovresti indovinare i nomi delle tabelle e delle colonne. Tutti i database moderni forniscono modi per esaminare la struttura del database e determinare quali tabelle e colonne contengono.

*(PRACTITIONER: SQL injection UNION attack, retrieving data from other tables)*

### Recupero di più valori all'interno di una singola colonna

In alcuni casi la query nell'esempio precedente potrebbe restituire solo una singola colonna.
Puoi recuperare più valori insieme all'interno di questa singola colonna concatenando i valori insieme. Puoi includere un separatore per permetterti di distinguere i valori combinati. Ad esempio, su Oracle potresti inviare l'input:

```sql
' UNION SELECT username || '~' || password FROM users--
```

Questo usa la sequenza double-pipe `||` che è un operatore di concatenazione di stringhe su Oracle. La query iniettata concatena insieme i valori dei campi `username` e `password`, separati dal carattere `~`.

I risultati della query contengono tutti gli username e le password, ad esempio:

```text
...
administrator~s3cure
wiener~peter
carlos~montoya
...
```

Diversi database utilizzano sintassi diverse per eseguire la concatenazione delle stringhe. Per altre informazioni, leggere la pagina a questo link.

*(PRACTITIONER: SQL injection UNION attack, retrieving multiple values in a single column)*

## Blind SQLi

La **blind SQLi** si verifica quando un'applicazione è vulnerabile all'SQLi, ma le sue risposte HTTP non contengono i risultati della query SQL pertinente o i dettagli di eventuali errori del database.

Molte tecniche come gli attacchi UNION non sono efficaci con le vulnerabilità della blind SQLi. Questo perché si basano sulla possibilità di vedere i risultati della query iniettata all'interno delle risposte dell'applicazione. È ancora possibile sfruttare la blind SQLi per accedere a dati non autorizzati, ma devono essere utilizzate tecniche diverse.

### Sfruttare la blind SQLi attivando risposte condizionali

Considera un'applicazione che utilizza cookie di tracciamento per raccogliere dati analitici sull'utilizzo. Le richieste all'applicazione includono un'intestazione cookie come questa:

`Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4`

Quando viene elaborata una richiesta contenente un cookie `TrackingId`, l'applicazione utilizza una query SQL per determinare se si tratta di un utente noto:

```sql
SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

Questa query è vulnerabile alla SQLi, ma i risultati della query non vengono restituiti all'utente. Tuttavia, l'applicazione si comporta in modo diverso a seconda che la query restituisca dati. Se invii un `TrackingId` riconosciuto, la query restituisce dati e ricevi un messaggio di "Welcome back" nella risposta.

Questo comportamento è sufficiente per sfruttare la vulnerabilità della blind SQLi. Puoi recuperare informazioni attivando risposte diverse in modo condizionale, a seconda di una condizione iniettata.

Per capire come funziona questo exploit, supponiamo che vengano inviate due richieste contenenti i seguenti valori di cookie `TrackingId` a turno:

*   `…xyz' AND '1'='1` fa sì che la query restituisca risultati, perché la condizione `AND '1'='1` iniettata è vera. Di conseguenza, viene visualizzato il messaggio "Welcome back";
*   `…xyz' AND '1'='2` fa sì che la query non restituisca alcun risultato, perché la condizione iniettata è falsa. Il messaggio "Welcome back" non viene visualizzato.

Ciò ci consente di determinare la risposta a qualsiasi singola condizione iniettata ed estrarre i dati un pezzo alla volta.

Ad esempio, supponiamo che ci sia una tabella chiamata `Users` con le colonne `Username` e `Password` e un utente chiamato `Administrator`. Puoi determinare la password per questo utente inviando una serie di input per testare la password un carattere alla volta.

Per fare ciò, inizia con il seguente input:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm
```

Questo non restituisce il messaggio "Welcome back", indicando che la condizione iniettata è falsa e quindi il primo carattere della password non è maggiore di `t`.

Alla fine, inviamo il seguente input, che restituisce il messaggio "Welcome back", confermando così che il primo carattere della password è `s`:

```sql
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

Possiamo continuare questo processo per determinare sistematicamente la password completa per l'utente Administrator.

*(PRACTITIONER: Blind SQL injection with conditional responses)*

### Sfruttare la blind SQLi innescando errori condizionali

Alcune applicazioni eseguono query SQL ma il loro comportamento non cambia, indipendentemente dal fatto che la query restituisca dati. La tecnica nella sezione precedente non funzionerà, perché l'iniezione di diverse condizioni booleane non fa alcuna differenza nelle risposte dell'applicazione.

Spesso è possibile indurre l'applicazione a restituire una risposta diversa a seconda che si verifichi un errore SQL. È possibile modificare la query in modo che causi un errore del database solo se la condizione è vera.
Molto spesso, un errore non gestito generato dal database causa una differenza nella risposta dell'applicazione, come un messaggio di errore. Ciò consente di dedurre la verità della condizione iniettata.

Per vedere come funziona, supponiamo che vengano inviate due richieste contenenti i seguenti valori del cookie `TrackingId` a turno:

```sql
xyz' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a
xyz' AND (SELECT CASE WHEN (1=1) THEN 1/0 ELSE 'a' END)='a
```

Questi input utilizzano la parola chiave `CASE` per testare una condizione e restituire un'espressione diversa a seconda che l'espressione sia vera o meno:

*   con il primo input, l'espressione `CASE` viene valutata come `'a'`, che non causa alcun errore.
*   con il secondo input, viene valutata come `1/0`, che causa un errore di divisione per zero.

Se l'errore causa una differenza nella risposta HTTP dell'applicazione, puoi usarlo per determinare se la condizione iniettata è vera.

Usando questa tecnica, puoi recuperare i dati testando un carattere alla volta:

```sql
xyz' AND (SELECT CASE WHEN (Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') THEN 1/0 ELSE 'a' END FROM Users)='a
```

*(PRACTITIONER: Blind SQL injection with conditional errors)*

### Sfruttare la blind SQLi innescando ritardi temporali

Se l'applicazione rileva errori del database quando viene eseguita la query SQL e li gestisce correttamente, non ci sarà alcuna differenza nella risposta dell'applicazione. Ciò significa che la tecnica precedente per indurre errori condizionali non funzionerà.

In questa situazione, è spesso possibile sfruttare la vulnerabilità della blind SQLi attivando ritardi temporali a seconda che una condizione iniettata sia vera o falsa. Poiché le query SQL vengono normalmente elaborate in modo sincrono dall'applicazione, ritardare l'esecuzione di una query SQL ritarda anche la risposta HTTP.
Ciò consente di determinare la verità della condizione iniettata in base al tempo impiegato per ricevere la risposta HTTP.

Le tecniche per attivare un ritardo temporale sono specifiche del tipo di database utilizzato. Ad esempio, su Microsoft SQL Server, è possibile utilizzare quanto segue per testare una condizione e attivare un ritardo a seconda che l'espressione sia vera:

*   `'; IF (1=2) WAITFOR DELAY '0:0:10'--` non innesca un ritardo, perché la condizione `1=2` è falsa;
*   `'; IF (1=1) WAITFOR DELAY '0:0:10'--` innesca un ritardo di 10 secondi, perché la condizione `1=1` è vera.

Utilizzando questa tecnica, possiamo recuperare i dati testando un carattere alla volta:

```sql
'; IF (SELECT COUNT(Username) FROM Users WHERE Username = 'Administrator' AND SUBSTRING(Password, 1, 1) > 'm') = 1 WAITFOR DELAY '0:0:{delay}'--
```

*(PRACTITIONER: Blind SQL injection with time delayss)*
*(PRACTITIONER: Blind SQL injection with time delays and information retrieval)*

### Sfruttare la blind SQLi mediante tecniche out-of-band (OAST)

Un'applicazione potrebbe eseguire la stessa query SQL dell'esempio precedente, ma in modo asincrono. L'applicazione continua a elaborare la richiesta dell'utente nel thread originale e utilizza un altro thread per eseguire una query SQL utilizzando il cookie di tracciamento. La query è ancora vulnerabile all'iniezione SQL, ma nessuna delle tecniche descritte finora funzionerà. La risposta dell'applicazione non dipende dal fatto che la query restituisca dati, che si verifichi un errore del database o dal tempo impiegato per eseguire la query.

In questa situazione, è spesso possibile sfruttare la vulnerabilità della blind SQLi attivando interazioni **out-of-band** con un sistema che controlli. Queste possono essere attivate in base a una condizione iniettata per dedurre informazioni un pezzo alla volta. Ancora più utile, i dati possono essere filtrati direttamente all'interno dell'interazione di rete.

A questo scopo possono essere utilizzati vari protocolli di rete, ma in genere il più efficace è DNS (domain name service). Molte reti di produzione consentono l'uscita libera delle query DNS, perché sono essenziali per il normale funzionamento dei sistemi di produzione.

*(PRACTITIONER: Blind SQL injection with out-of-band data exfiltration)*

## Come prevenire gli attacchi SQLi

È possibile prevenire la maggior parte delle istanze di SQLi utilizzando **query parametrizzate** anziché la concatenazione di stringhe all'interno della query. Queste query parametrizzate sono note anche come **istruzioni preparate**.

Puoi usare query parametrizzate per qualsiasi situazione in cui l'input non attendibile appare come dati all'interno della query, inclusa la clausola `WHERE` e i valori in un'istruzione `INSERT` o `UPDATE`. Non possono essere usate per gestire l'input non attendibile in altre parti della query, come nomi di tabelle o colonne o la clausola `ORDER BY`. La funzionalità dell'applicazione che inserisce dati non attendibili in queste parti della query deve adottare un approccio diverso, come:

*   inserimento nella whitelist dei valori di input consentiti.
*   utilizzo di una logica diversa per fornire il comportamento richiesto.

Affinché una query parametrizzata sia efficace nel prevenire l'SQLi, la stringa usata nella query deve sempre essere una costante hard-coded. Non deve mai contenere dati variabili da nessuna origine. Non essere tentato di decidere caso per caso se un elemento di dati è attendibile e continua a usare la concatenazione di stringhe all'interno della query per i casi considerati sicuri. È facile commettere errori sulla possibile origine dei dati o che le modifiche in altro codice contaminino i dati attendibili.