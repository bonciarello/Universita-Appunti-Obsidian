Le vulnerabilità di caricamento file si verificano quando un server web consente agli utenti di caricare file sul suo file system senza convalidare sufficientemente elementi come nome, tipo, contenuto o dimensione. Non riuscire a far rispettare correttamente le restrizioni su questi potrebbe significare che anche una funzione di caricamento immagini di base può essere utilizzata per caricare file arbitrari e potenzialmente pericolosi. Ciò potrebbe persino includere file di script lato server che consentono l'esecuzione di codice remoto.

In alcuni casi, l'atto di caricare il file è di per sé sufficiente a causare danni. Altri attacchi possono comportare una richiesta HTTP di follow-up per il file, in genere per innescarne l'esecuzione da parte del server.
### Come nascono le vulnerabilità nel caricamento dei file?
Considerati i pericoli abbastanza evidenti, è raro che i siti web in natura non abbiano alcuna restrizione su quali file gli utenti possono caricare. Più comunemente, gli sviluppatori implementano quella che ritengono essere una convalida robusta che è intrinsecamente difettosa o può essere facilmente aggirata.

Ad esempio, potrebbero tentare di mettere in blacklist tipi di file pericolosi, ma non tenere conto delle discrepanze di analisi quando controllano le estensioni dei file. Come con qualsiasi blacklist, è anche facile omettere accidentalmente tipi di file più oscuri che potrebbero comunque essere pericolosi.

In altri casi, il sito web potrebbe tentare di controllare il tipo di file verificandone le proprietà che potrebbero essere facilmente manipolate da un aggressore.

In ultima analisi, anche misure di convalida robuste potrebbero essere applicate in modo incoerente all'intera rete di host e directory che compongono il sito web, dando origine a discrepanze che possono essere sfruttate.
### Qual è l'impatto delle vulnerabilità nel caricamento dei file?
L'impatto delle vulnerabilità di caricamento file dipende generalmente da due fattori chiave:
- quale aspetto del file il sito web non riesce a convalidare correttamente, che si tratti di dimensioni, tipo, contenuti e così via;
- quali restrizioni vengono imposte al file una volta caricato correttamente.

Nel peggiore dei casi, il tipo di file non viene convalidato correttamente e la configurazione del server consente l'esecuzione di determinati tipi di file (come `.php` e `.jsp`) come codice. In questo caso, un aggressore potrebbe potenzialmente caricare un file di codice lato server che funziona come una web shell, garantendogli di fatto il pieno controllo sul server.

Se il nome del file non venisse convalidato correttamente, ciò potrebbe consentire a un aggressore di sovrascrivere file critici semplicemente caricando un file con lo stesso nome. Se il server fosse anche vulnerabile al directory traversal, ciò potrebbe significare che gli aggressori sono persino in grado di caricare file in posizioni impreviste.

Non assicurarsi che la dimensione del file rientri nelle soglie previste potrebbe anche consentire una forma di attacco denial-of-service (DoS), tramite il quale l'aggressore riempie lo spazio disponibile sul disco.
### In che modo i server web gestiscono le richieste di file statici?
Prima di esaminare come sfruttare le vulnerabilità di caricamento file, è importante avere una conoscenza di base di come i server gestiscono le richieste di file statici.

Storicamente, i siti web erano costituiti quasi interamente da file statici che venivano forniti agli utenti quando richiesti. Di conseguenza, il percorso di ogni richiesta poteva essere mappato 1:1 con la gerarchia di directory e file sul file system del server. Oggigiorno, i siti web sono sempre più dinamici e il percorso di una richiesta spesso non ha alcuna relazione diretta con il file system. Tuttavia, i server web gestiscono ancora le richieste di alcuni file statici, inclusi fogli di stile, immagini e così via.

Il processo per gestire questi file statici è ancora in gran parte lo stesso. A un certo punto, il server analizza il percorso nella richiesta per identificare l'estensione del file. Quindi lo utilizza per determinare il tipo di file richiesto, in genere confrontandolo con un elenco di mappature preconfigurate tra estensioni e tipi MIME. Ciò che accade dopo dipende dal tipo di file e dalla configurazione del server:
- se questo tipo di file non è eseguibile, come un'immagine o una pagina HTML statica, il server potrebbe semplicemente inviare il contenuto del file al client in una response HTTP.
- se il tipo di file è eseguibile, come un file PHP, e il server è configurato per eseguire file di questo tipo, assegnerà variabili in base alle intestazioni e ai parametri nella richiesta HTTP prima di eseguire lo script. L'output risultante potrebbe quindi essere inviato al client in una response HTTP.
- se il tipo di file è eseguibile, ma il server non è configurato per eseguire file di questo tipo, in genere risponderà con un errore. Tuttavia, in alcuni casi, il contenuto del file potrebbe comunque essere fornito al client come testo normale. Tali configurazioni errate possono occasionalmente essere sfruttate per far trapelare il codice sorgente e altre informazioni sensibili. Puoi vedere un esempio di ciò nei nostri materiali didattici sulla divulgazione delle informazioni.

**Consiglio:** l'intestazione di risposta `Content-Type` può fornire indizi sul tipo di file che il server pensa di aver servito. Se questa intestazione non è stata impostata esplicitamente dal codice dell'applicazione, normalmente contiene il risultato della mappatura estensione file/tipo MIME.
## Sfruttare i caricamenti di file senza restrizioni per distribuire una web shell
Da una prospettiva di sicurezza, lo scenario peggiore possibile è quando un sito web consente di caricare script lato server, come file PHP, Java o Python, ed è anche configurato per eseguirli come codice. Ciò rende banale creare la propria web shell sul server.

Una web shell è uno script dannoso che consente a un aggressore di eseguire comandi arbitrari su un server web remoto semplicemente inviando richieste HTTP all'endpoint corretto.

Se riesci a caricare con successo una web shell, hai effettivamente il controllo completo sul server. Ciò significa che puoi leggere e scrivere file arbitrari, esfiltrare dati sensibili, persino usare il server per indirizzare attacchi sia contro l'infrastruttura interna che contro altri server esterni alla rete. Ad esempio, il seguente one-liner PHP potrebbe essere usato per leggere file arbitrari dal file system del server:

`<?php echo file_get_contents('/path/to/target/file'); ?>`

Una volta caricato, l'invio di una richiesta per questo file dannoso restituirà il contenuto del file di destinazione nella risposta (APPRENTICE: Remote code execution via web shell upload).

Una web shell più versatile potrebbe avere un aspetto simile a questo:

`<?php echo system($_GET['command']); ?>`

Questo script consente di passare un comando di sistema arbitrario tramite un parametro di query come segue:

`GET /example/exploit.php?command=id HTTP/1.1`
## Sfruttare la convalida difettosa dei caricamenti di file
In natura, è improbabile che tu trovi un sito web che non abbia protezione contro gli attacchi di caricamento file. Ma solo perché le difese sono in atto, ciò non significa che siano robuste. A volte puoi ancora sfruttare i difetti di questi meccanismi per ottenere una web shell per l'esecuzione di codice remoto.
### Convalida del tipo di file difettosa
Quando si inviano moduli HTML, il browser in genere invia i dati forniti in una richiesta POST con il tipo di contenuto `application/x-www-form-url-encoded`. Questo va bene per inviare testo semplice come il tuo nome o indirizzo. Tuttavia, non è adatto per inviare grandi quantità di dati binari, come un intero file immagine o un documento PDF. In questo caso, è preferibile il tipo di contenuto `multipart/form-data`.

Considera un modulo contenente campi per caricare un'immagine, fornire una descrizione e immettere il tuo nome utente. L'invio di un modulo del genere potrebbe comportare una richiesta simile a questa:

```http
POST /images HTTP/1.1
Host: normal-website.com
Content-Length: 12345
Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="image"; filename="example.jpg"
Content-Type: image/jpeg

[...binary content of example.jpg...]
---------------------------012345678901234567890123456
Content-Disposition: form-data; name="description"

This is an interesting description of my image.
---------------------------012345678901234567890123456
Content-Disposition: form-data; name="username"

wiener
---------------------------012345678901234567890123456--
```

Come puoi vedere, il corpo del messaggio è suddiviso in parti separate per ogni input del modulo. Ogni parte contiene un'intestazione `Content-Disposition`, che fornisce alcune informazioni di base sul campo di input a cui si riferisce. Queste singole parti possono anche contenere la propria intestazione `Content-Type`, che indica al server il tipo MIME dei dati inviati tramite questo input.

Un modo in cui i siti web possono tentare di convalidare i caricamenti di file è controllare che questa intestazione `Content-Type` specifica dell'input corrisponda a un tipo MIME previsto. Se il server si aspettasse solo file di immagini, ad esempio, potrebbe consentire solo tipi come `image/jpeg` e `image/png`. Possono sorgere problemi quando il valore di questa intestazione è implicitamente ritenuto attendibile dal server. Se non viene eseguita alcuna ulteriore convalida per controllare se il contenuto del file corrisponde effettivamente al presunto tipo MIME, questa difesa può essere facilmente aggirata utilizzando strumenti (APPRENTICE: Web shell upload via Content-Type restriction bypass).
### Impedire l'esecuzione di file in directory accessibili all'utente
Sebbene sia chiaramente meglio impedire che tipi di file pericolosi vengano caricati in primo luogo, la seconda linea di difesa è impedire al server di eseguire qualsiasi script che passi attraverso la rete.

Come precauzione, i server in genere eseguono solo script il cui tipo MIME sono stati esplicitamente configurati per l'esecuzione. Altrimenti, potrebbero semplicemente restituire un qualche tipo di messaggio di errore o, in alcuni casi, servire il contenuto del file come testo normale:

```http
GET /static/exploit.php?command=id HTTP/1.1
Host: normal-website.com

HTTP/1.1 200 OK
Content-Type: text/plain
Content-Length: 39

<?php echo system($_GET['command']); ?>
```

Questo comportamento è potenzialmente interessante di per sé, in quanto potrebbe fornire un modo per far trapelare il codice sorgente, ma annulla qualsiasi tentativo di creare una web shell.

Questo tipo di configurazione spesso differisce tra le directory. Una directory in cui vengono caricati file forniti dall'utente avrà probabilmente controlli molto più severi rispetto ad altre posizioni sul file system che si presume siano fuori dalla portata degli utenti finali. Se riesci a trovare un modo per caricare uno script in una directory diversa che non dovrebbe contenere file forniti dall'utente, il server potrebbe eseguire lo script dopo tutto (PRACTITIONER: Web shell upload via path traversal).

**Consiglio:** i server web utilizzano spesso il campo `filename` nelle richieste `multipart/form-data` per determinare il nome e la posizione in cui salvare il file.

Dovresti anche notare che anche se puoi inviare tutte le tue richieste allo stesso nome di dominio, questo spesso punta a un server proxy inverso di qualche tipo, come un bilanciatore di carico. Le tue richieste saranno spesso gestite da server aggiuntivi dietro le quinte, che potrebbero anche essere configurati in modo diverso.
### Blacklist insufficiente dei tipi di file pericolosi
Uno dei modi più ovvi per impedire agli utenti di caricare script dannosi è quello di mettere in blacklist estensioni di file potenzialmente pericolose come `.php`. La pratica della blacklist è intrinsecamente imperfetta, in quanto è difficile bloccare in modo esplicito ogni possibile estensione di file che potrebbe essere utilizzata per eseguire codice. Tali blacklist possono talvolta essere aggirate utilizzando estensioni di file alternative meno note che potrebbero comunque essere eseguibili, come `.php5`, `.shtml` e così via.

I server in genere non eseguono file a meno che non siano stati configurati per farlo. Ad esempio, prima che un server Apache esegua i file PHP richiesti da un client, gli sviluppatori potrebbero dover aggiungere le seguenti direttive al loro file `/etc/apache2/apache2.conf`:

```apache
LoadModule php_module /usr/lib/apache2/modules/libphp.so
AddType application/x-httpd-php .php
```

Molti server consentono inoltre agli sviluppatori di creare file di configurazione speciali all'interno di directory individuali per sovrascrivere o aggiungere una o più impostazioni globali. I server Apache, ad esempio, caricheranno una configurazione specifica per la directory da un file denominato `.htaccess`, se presente.

Allo stesso modo, gli sviluppatori possono creare una configurazione specifica per la directory sui server IIS utilizzando un file `web.config`. Ciò potrebbe includere direttive come la seguente, che in questo caso consente di fornire file JSON agli utenti:

```xml
<staticContent>
 <mimeMap fileExtension=".json" mimeType="application/json" />
</staticContent>
```

I server web utilizzano questi tipi di file di configurazione quando sono presenti, ma normalmente non ti è consentito accedervi tramite richieste HTTP. Tuttavia, potresti occasionalmente trovare server che non riescono a impedirti di caricare il tuo file di configurazione dannoso. In questo caso, anche se l'estensione file di cui hai bisogno è nella blacklist, potresti riuscire a ingannare il server in modo che esegua il mapping di un'estensione file personalizzata e arbitraria su un tipo MIME eseguibile (PRACTITIONER: Web shell upload via extension blacklist bypass).
## Sfruttare le vulnerabilità di caricamento file senza esecuzione di codice remoto
Siamo stati in grado di caricare script lato server per l'esecuzione di codice remoto. Questa è la conseguenza più grave di una funzione di caricamento file non sicura, ma queste vulnerabilità possono comunque essere sfruttate in altri modi.
### Caricamento di script dannosi lato client
Sebbene tu possa non essere in grado di eseguire script sul server, potresti comunque essere in grado di caricare script per attacchi lato client. Ad esempio, se riesci a caricare file HTML o immagini SVG, puoi potenzialmente utilizzare i tag `<script>` per creare payload XSS archiviati.

Se il file caricato appare quindi su una pagina visitata da altri utenti, il loro browser eseguirà lo script quando tenterà di eseguire il rendering della pagina. Nota che a causa delle restrizioni della same-origin policy, questo tipo di attacchi funzionerà solo se il file caricato viene servito dalla stessa origine su cui lo carichi.
### Sfruttamento delle vulnerabilità nell'analisi dei file caricati
Se il file caricato sembra essere sia archiviato che servito in modo sicuro, l'ultima risorsa è provare a sfruttare vulnerabilità specifiche per l'analisi o l'elaborazione di diversi formati di file. Ad esempio, se sai che il server analizza file basati su XML, come file Microsoft Office `.doc` o `.xls`, questo potrebbe essere un potenziale vettore per attacchi di iniezione XXE.
## Come prevenire le vulnerabilità di caricamento dei file
Consentire agli utenti di caricare file è una prassi comune e non deve essere pericoloso, purché si prendano le giuste precauzioni. In generale, il modo più efficace per proteggere i propri siti Web da queste vulnerabilità è implementare tutte le seguenti pratiche:
- controllare l'estensione del file rispetto a una whitelist di estensioni consentite anziché a una blacklist di estensioni vietate. È molto più facile indovinare quali estensioni si desidera consentire piuttosto che quali un aggressore potrebbe provare a caricare;
- assicurarsi che il nome del file non contenga sottostringhe che potrebbero essere interpretate come una directory o una sequenza di attraversamento (`../`);
- rinominare i file caricati per evitare collisioni che potrebbero causar la sovrascrittura dei file esistenti;
- non caricare file sul file system permanente del server finché non sono stati completamente convalidati;
- per quanto possibile, utilizzare un framework consolidato per la preelaborazione dei caricamenti di file anziché tentare di scrivere i propri meccanismi di convalida.