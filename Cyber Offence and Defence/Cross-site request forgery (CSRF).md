Cross-site request forgery (noto anche come CSRF) è una vulnerabilità della sicurezza web che consente a un aggressore di indurre gli utenti a eseguire azioni che non intendono eseguire. Consente a un aggressore di aggirare in parte la same origin policy, che è progettata per impedire a siti web diversi di interferire tra loro.
### Come funziona il CSRF?
Affinché un attacco CSRF sia possibile, devono essere presenti tre condizioni chiave:
- **un'azione pertinente:** c’è un’azione nell’applicazione che l’aggressore ha interesse a provocare. Questa potrebbe essere un'azione privilegiata (come la modifica delle autorizzazioni per altri utenti) o qualsiasi azione su dati specifici dell'utente (come la modifica della password dell'utente stesso);
- **gestione delle sessioni basata sui cookie:** l'esecuzione dell'azione comporta l'emissione di una o più richieste HTTP e l'applicazione si basa esclusivamente sui cookie di sessione per identificare l'utente che ha effettuato le richieste. Non esiste nessun altro meccanismo per tracciare le sessioni o convalidare le richieste dell'utente;
- **nessun parametro di richiesta imprevedibile:** le richieste che eseguono l'azione non contengono parametri i cui valori l'aggressore non può determinare o indovinare. Ad esempio, quando si fa in modo che un utente cambi la propria password, la funzione non è vulnerabile se un aggressore ha bisogno di conoscere il valore della password esistente.

Ad esempio, supponiamo che un'applicazione contenga una funzione che consente all'utente di modificare l'indirizzo e-mail sul proprio account. Quando un utente esegue questa azione, effettua una richiesta HTTP come la seguente:
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE

email=wiener@normal-user.com
```

Ciò soddisfa le condizioni richieste per CSRF:
- l'azione di modifica dell'indirizzo e-mail sull'account di un utente è di interesse per un aggressore. In seguito a questa azione, l'aggressore sarà in genere in grado di attivare una reimpostazione della password e assumere il pieno controllo dell'account dell'utente;
- l'applicazione utilizza un cookie di sessione per identificare quale utente ha emesso la richiesta. Non ci sono altri token o meccanismi in atto per tracciare le sessioni utente;
- l'aggressore può facilmente determinare i valori dei parametri di richiesta necessari per eseguire l'azione.

Con queste condizioni in atto, l'aggressore può creare una pagina web contenente il seguente codice HTML:
```html
<html>
 <body>
 <form action="https://vulnerable-website.com/email/change" method="POST">
 <input type="hidden" name="email" value="pwned@evil-user.net" />
 </form>
 <script>
 document.forms[0].submit();
 </script>
 </body>
</html>
```

Se un utente vittima visita la pagina web dell'attaccante, accadrà quanto segue:
- la pagina dell'attaccante attiverà una richiesta HTTP al sito web vulnerabile;
- se l'utente ha effettuato l'accesso al sito web vulnerabile, il suo browser includerà automaticamente il suo cookie di sessione nella richiesta (supponendo che non vengano utilizzati cookie SameSite);
- il sito web vulnerabile elaborerà la richiesta nel modo normale, la tratterà come se fosse stata effettuata dall'utente vittima e cambierà il suo indirizzo e-mail.

**Nota:** sebbene CSRF venga normalmente descritto in relazione alla gestione delle sessioni basata sui cookie, si verifica anche in altri contesti in cui l'applicazione aggiunge automaticamente alcune credenziali utente alle richieste, come l'autenticazione HTTP Basic e l'autenticazione basata su certificato.

**Qual è l'impatto di un attacco CSRF?** In un attacco CSRF riuscito, l'aggressore fa sì che l'utente vittima esegua un'azione involontaria. Ad esempio, potrebbe essere la modifica dell'indirizzo e-mail del proprio account, la modifica della password o l'esecuzione di un trasferimento di fondi. A seconda della natura dell'azione, l'aggressore potrebbe essere in grado di ottenere il pieno controllo dell'account dell'utente. Se l'utente compromesso avesse un ruolo privilegiato all'interno dell'applicazione, l'aggressore potrebbe essere in grado di assumere il pieno controllo di tutti i dati e le funzionalità dell'applicazione.
### Come distribuire un exploit CSRF
I meccanismi di distribuzione per gli attacchi cross-site request forgery sono essenzialmente gli stessi dei Reflected XSS. In genere, l'aggressore inserirà l'HTML dannoso su un sito web che controlla e quindi indurrà le vittime a visitare tale sito web. Ciò potrebbe essere fatto fornendo all'utente un collegamento al sito web, tramite un'e-mail o un messaggio sui social media. Oppure, se l'attacco viene inserito in un sito web popolare (ad esempio, in un commento dell'utente), potrebbe semplicemente attendere che gli utenti visitino il sito web.

Nota che alcuni semplici exploit CSRF utilizzano il metodo GET e possono essere completamente autonomi con un singolo URL sul sito web vulnerabile. In questa situazione, l'aggressore potrebbe non aver bisogno di utilizzare un sito esterno e può fornire direttamente alle vittime un URL dannoso sul dominio vulnerabile.
Nell'esempio precedente, se la richiesta di modifica dell'indirizzo e-mail può essere eseguita con il metodo GET, un attacco autonomo apparirebbe così:
`<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">`
## Qual è la differenza tra XSS e CSRF?
Cross-site scripting (o XSS) consente a un aggressore di eseguire JavaScript arbitrario all'interno del browser di un utente vittima. Cross-site request forgery (o CSRF) consente a un aggressore di indurre un utente vittima a eseguire azioni che non intende.

Le conseguenze delle vulnerabilità XSS sono generalmente più gravi rispetto alle vulnerabilità CSRF:
- CSRF spesso si applica solo a un sottoinsieme di azioni che un utente è in grado di eseguire. Molte applicazioni implementano difese CSRF in generale, ma trascurano una o due azioni che rimangono esposte. Al contrario, un exploit XSS riuscito può normalmente indurre un utente a eseguire qualsiasi azione che l'utente è in grado di eseguire, indipendentemente dalla funzionalità in cui si verifica la vulnerabilità.
- CSRF può essere descritta come una vulnerabilità "unidirezionale", in quanto mentre un aggressore può indurre la vittima a inviare una richiesta HTTP, non può recuperare la risposta da tale richiesta. Al contrario, XSS è "bidirezionale", nel senso che lo script iniettato dall'aggressore può inviare richieste arbitrarie, leggere le risposte ed esfiltrare dati in un dominio esterno scelto dall'aggressore.
### Token CSRF possono prevenire gli attacchi XSS?
Alcuni attacchi XSS possono effettivamente essere prevenuti tramite l'uso efficace dei token CSRF. Si consideri una semplice vulnerabilità Reflected XSS che può essere sfruttata in modo banale in questo modo:
`https://insecure-website.com/status?message=<script>/*+Bad+stuff+here...+*/</script>`

Supponiamo ora che la funzione vulnerabile includa un token CSRF:
`https://insecure-website.com/status?csrf-token=CIwNZNlR4XbisJF39I8yWnWX9wX4WFoz&message=<script>/*+Bad+stuff+here...+*/</script>`

Supponendo che il server convalidi correttamente il token CSRF e rifiuti le richieste senza un token valido, allora il token impedisce lo sfruttamento della vulnerabilità XSS. L'indizio qui è nel nome: "cross-site scripting", almeno nella sua forma riflessa, implica una richiesta cross-site. Impedendo a un aggressore di falsificare una richiesta cross-site, l'applicazione impedisce lo sfruttamento banale della vulnerabilità XSS.

Qui sorgono alcune importanti avvertenze:
- se una vulnerabilità Reflected XSS esiste in qualsiasi altro punto del sito all'interno di una funzione che non è protetta da un token CSRF, allora tale XSS può essere sfruttata nel modo normale;
- se una vulnerabilità XSS sfruttabile esiste in qualsiasi punto di un sito, allora la vulnerabilità può essere sfruttata per far sì che un utente vittima esegua azioni anche se tali azioni sono protette da token CSRF. In questa situazione, lo script dell'aggressore può richiedere la pagina pertinente per ottenere un token CSRF valido e quindi utilizzare il token per eseguire l'azione protetta;
- i token CSRF non proteggono dalle vulnerabilità Stored XSS. Se una pagina protetta da un token CSRF è anche il punto di output per una vulnerabilità Stored XSS, allora tale vulnerabilità XSS può essere sfruttata nel solito modo e il payload XSS verrà eseguito quando un utente visita la pagina.
## Esempio di attacco CSRF
Immagina di essere su Twitter, dove hai appena effettuato l’accesso. Il tuo browser ha memorizzato un cookie di sessione che identifica la tua utenza. Questo cookie, invisibile a te, viene inviato ogni volta che fai qualcosa sulla piattaforma, ad esempio inviare un tweet.
Ora, supponiamo che esista un modulo HTML standard per pubblicare tweet: un campo di testo, un pulsante “Invia”, e dietro le quinte, una richiesta POST all’endpoint che pubblica il contenuto.
A questo punto, entra in gioco l’attacco CSRF. Un sito web malevolo potrebbe creare una pagina con un modulo che assomiglia a quello di Twitter, ma con un dettaglio ingannevole. Per esempio, il pulsante potrebbe dire “Clicca qui per vincere un milione”, ma in realtà, cliccandoci, invii un tweet come “Segui Mario”. Il tuo browser, ignaro della trappola, include automaticamente il cookie di sessione nella richiesta, credendo che stia interagendo con il sito legittimo. Twitter non ha modo di distinguere se la richiesta proviene da te o da un sito esterno.
Non serve nemmeno un clic per compiere l’attacco. Un iframe nascosto o uno script possono automatizzare l’invio del modulo appena visiti la pagina malevola. Questo significa che semplicemente aprendo un link, potresti involontariamente pubblicare contenuti o compiere azioni non desiderate sul tuo account.
## Difese comuni contro il CSRF
Per prevenire questo tipo di attacchi, le applicazioni web devono adottare misure come i token CSRF, che aggiungono un ulteriore livello di verifica per distinguere le richieste legittime da quelle sospette. Inoltre, limitare il contesto d’uso dei cookie con impostazioni come SameSite e verificare l’origine delle richieste possono aiutare a contrastare queste tecniche.

Al giorno d'oggi, trovare e sfruttare con successo le vulnerabilità CSRF spesso implica l'aggiramento delle misure anti-CSRF implementate dal sito web di destinazione, dal browser della vittima o da entrambi. Le difese più comuni che incontrerai sono le seguenti:
- **token CSRF:** un token CSRF è un valore univoco, segreto e imprevedibile generato dall'applicazione lato server e condiviso con il client. Quando si tenta di eseguire un'azione sensibile, come l'invio di un modulo, il client deve includere il token CSRF corretto nella richiesta. Ciò rende molto difficile per un aggressore creare una richiesta valida per conto della vittima;
- **cookie SameSite:** SameSite è un meccanismo di sicurezza del browser che determina quando i cookie di un sito web sono inclusi nelle richieste provenienti da altri siti web. Poiché le richieste per eseguire azioni sensibili in genere richiedono un cookie di sessione autenticato, le opportune restrizioni SameSite possono impedire a un aggressore di attivare azioni sensibili tra siti. Dal 2021, Chrome applica le restrizioni Lax SameSite per impostazione predefinita. Poiché questo è lo standard proposto, ci aspettiamo che altri browser principali adottino questo comportamento in futuro;
- **validazione basata su referer:** alcune applicazioni utilizzano l'intestazione HTTP Referer per tentare di difendersi dagli attacchi CSRF, normalmente verificando che la richiesta provenga dal dominio dell'applicazione stessa. Questo è generalmente meno efficace della convalida del token CSRF.
### Bypassare la convalida del token CSRF
Un token CSRF è un valore univoco, segreto e imprevedibile generato dall'applicazione lato server e condiviso con il client. Quando si invia una richiesta per eseguire un'azione sensibile, come l'invio di un modulo, il client deve includere il token CSRF corretto. In caso contrario, il server rifiuterà di eseguire l'azione richiesta.
Un modo comune per condividere i token CSRF con il client è includerli come parametro nascosto in un modulo HTML, ad esempio:

```html
<form name="change-email-form" action="/my-account/change-email" method="POST">
 <label>Email</label>
 <input required type="email" name="email" value="example@normal-website.com">
 <input required type="hidden" name="csrf" value="50FaWgdOhi9M9wyna8taR1k3ODOR8d6u">
 <button class='button' type='submit'> Update email </button>
</form>
```

L'invio di questo modulo genera la seguente richiesta:
```http
POST /my-account/change-email HTTP/1.1
Host: normal-website.com
Content-Length: 70
Content-Type: application/x-www-form-urlencoded

csrf=50FaWgdOhi9M9wyna8taR1k3ODOR8d6u&email=example@normal-website.com
```

Se implementati correttamente, i token CSRF aiutano a proteggere dagli attacchi CSRF rendendo difficile per un aggressore costruire una richiesta valida per conto della vittima. Poiché l'aggressore non ha modo di prevedere il valore corretto per il token CSRF, non sarà in grado di includerlo nella richiesta dannosa.

**Nota:** i token CSRF non devono essere inviati come parametri nascosti in una richiesta POST. Alcune applicazioni inseriscono i token CSRF nelle intestazioni HTTP, ad esempio. Il modo in cui vengono trasmessi i token ha un impatto significativo sulla sicurezza di un meccanismo nel suo complesso. Per ulteriori informazioni, vedere Come prevenire le vulnerabilità CSRF.

Le vulnerabilità CSRF in genere sorgono a causa di una convalida difettosa dei token CSRF. Alcuni dei problemi più comuni che consentono agli aggressori di aggirare queste difese sono:
- **validazione del token CSRF dipendente dal metodo di richiesta:** alcune applicazioni convalidano correttamente il token quando la richiesta utilizza il metodo POST ma saltano la convalida quando viene utilizzato il metodo GET. In questa situazione, l'attaccante può passare al metodo GET per bypassare la convalida e inviare un attacco CSRF:
```http
GET /email/change?email=pwned@evil-user.net HTTP/1.1
Host: vulnerable-website.com
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm
```
- **validazione del token CSRF dipendente dalla presenza del token:** alcune applicazioni convalidano correttamente il token quando è presente, ma saltano la convalida se il token viene omesso. In questa situazione, l'attaccante può rimuovere l'intero parametro contenente il token (non solo il suo valore) per bypassare la convalida e inviare un attacco CSRF:
```http
POST /email/change HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 25
Cookie: session=2yQIDcpia41WrATfjPqvm9tOkDvkMvLm

email=pwned@evil-user.net
```
- **token CSRF non è legato alla sessione utente:** alcune applicazioni non verificano se il token appartiene alla stessa sessione dell’utente che invia la richiesta. Invece, l'applicazione mantiene un pool globale di token che ha emesso e accetta qualsiasi token che appare in questo pool. In questa situazione, l'aggressore può accedere all'applicazione utilizzando il proprio account, ottenere un token valido e quindi fornire tale token all'utente vittima nel suo attacco CSRF.
### Bypassare le restrizioni sui cookie SameSite
SameSite è un meccanismo di sicurezza del browser che determina quando i cookie di un sito web sono inclusi nelle richieste provenienti da altri siti web. Le restrizioni dei cookie SameSite forniscono una protezione parziale contro una varietà di attacchi cross-site, tra cui CSRF, perdite cross-site e alcuni exploit CORS.

SameSite funziona consentendo ai browser e ai proprietari di siti web di limitare quali richieste cross-site, se presenti, dovrebbero includere cookie specifici. Ciò può aiutare a ridurre l'esposizione degli utenti agli attacchi CSRF, che inducono il browser della vittima a inviare una richiesta che innesca un'azione dannosa sul sito web vulnerabile. Poiché queste richieste in genere richiedono un cookie associato alla sessione autenticata della vittima, l'attacco fallirà se il browser non lo include.

Tutti i principali browser supportano attualmente i seguenti livelli di restrizione SameSite:
- **Strict:** se un cookie è impostato con l'attributo SameSite=Strict, i browser non lo invieranno in nessuna richiesta cross-site. In parole povere, questo significa che se il sito di destinazione per la richiesta non corrisponde al sito attualmente mostrato nella barra degli indirizzi del browser, non includerà il cookie. Sebbene questa sia l'opzione più sicura, può avere un impatto negativo sull'esperienza utente nei casi in cui è auspicabile la funzionalità cross-site;
- **Lax:** le restrizioni Lax SameSite significano che i browser invieranno il cookie nelle richieste cross-site, ma solo se vengono soddisfatte entrambe le seguenti condizioni:
	- la richiesta utilizza il metodo GET;
	- la richiesta è risultata da una navigazione di primo livello da parte dell'utente, come il clic su un collegamento.
	Ciò significa che il cookie non è incluso nelle richieste POST cross-site, ad esempio. Poiché le richieste POST sono generalmente utilizzate per eseguire azioni che modificano dati o stati (almeno secondo le best practice), è molto più probabile che siano il bersaglio di attacchi CSRF. Allo stesso modo, il cookie non è incluso nelle richieste in background, come quelle avviate da script, iframe o riferimenti a immagini e altre risorse;
- **None:** se un cookie è impostato con l'attributo SameSite=None, questo disabilita effettivamente tutte le restrizioni SameSite, indipendentemente dal browser. Di conseguenza, i browser invieranno questo cookie in tutte le richieste al sito che lo ha emesso, anche quelle che sono state attivate da siti di terze parti completamente estranei. Ad eccezione di Chrome, questo è il comportamento predefinito utilizzato dai principali browser se non viene fornito alcun attributo SameSite quando si imposta il cookie. Esistono motivi legittimi per disabilitare SameSite, ad esempio quando il cookie è destinato a essere utilizzato da un contesto di terze parti e non concede al portatore l'accesso a dati o funzionalità sensibili. I cookie di tracciamento sono un tipico esempio. Quando si imposta un cookie con SameSite=None, il sito web deve includere anche l'attributo Secure, che garantisce che il cookie venga inviato solo in messaggi crittografati tramite HTTPS. In caso contrario, i browser rifiuteranno il cookie e non verrà impostato.

Gli sviluppatori possono configurare manualmente un livello di restrizione per ogni cookie che impostano, ottenendo così un maggiore controllo su quando questi cookie vengono utilizzati. Per farlo, devono solo includere l'attributo SameSite nell'intestazione di risposta Set-Cookie, insieme al loro valore preferito:
`Set-Cookie: session=0F8tgdOhi9ynR1M9wa3ODa; SameSite=Strict`

Sebbene ciò offra una certa protezione contro gli attacchi CSRF, nessuna di queste restrizioni fornisce immunità garantita.
## Come prevenire le vulnerabilità CSRF
Possiamo prevenire le vulnerabilità CSRF dividendo le criticità in tre categorie:
- utilizzare token CSRF;
- utilizzare le restrizioni sui cookie SameSite rigorose;
- maggiore attenzione agli attacchi cross-origin e same-site.
### Utilizzare token CSRF
Il modo più robusto per difendersi dagli attacchi CSRF è includere un token CSRF nelle richieste pertinenti. Il token deve soddisfare i seguenti criteri:
- imprevedibile con elevata entropia, come per i token di sessione in generale;
- legato alla sessione dell'utente;
- rigorosamente convalidato in ogni caso prima che venga eseguita l'azione pertinente.

**Come dovrebbero essere generati i token CSRF?** I token CSRF devono essere imprevedibili e dotati di alta entropia, analogamente ai token di sessione. È consigliato usare un generatore di numeri pseudo-casuali crittograficamente sicuro (CSPRNG), combinato con un timestamp e un chiave segreta statica. Per maggiore sicurezza, si può aggiungere entropia specifica dell’utente e applicare un hash forte, rendendo più difficile l’analisi dei token da parte di un attaccante.

**Come devono essere trasmessi i token CSRF?** I token CSRF devono essere trattati come segreti e gestiti in modo sicuro. È preferibile trasmetterli tramite un campo nascosto in un modulo HTML inviato con il metodo POST. Questo campo dovrebbe essere posizionato all’inizio del documento HTML, prima di qualsiasi input controllabile dall’utente, per mitigare attacchi basati sulla manipolazione del DOM. L’inserimento del token nella query string dell’URL è meno sicuro, poiché può essere registrato o trasmesso a terzi. Un’alternativa più sicura è trasmettere i token tramite intestazioni personalizzate, anche se ciò richiede l’uso di richieste XHR e può risultare complesso. I token CSRF non devono mai essere trasmessi tramite cookie.

**Come devono essere convalidati i token CSRF?** Quando si genera un token CSRF, deve essere archiviato nei dati della sessione utente lato server. Alla ricezione di una richiesta, l’applicazione deve verificare che il token fornito corrisponda a quello memorizzato nella sessione, indipendentemente dal metodo HTTP o dal tipo di contenuto. Le richieste prive di token o con token non validi devono essere rifiutate.
### Utilizzare le restrizioni sui cookie SameSite rigorose
Oltre a implementare una convalida token CSRF affidabile, è consigliato di impostare esplicitamente le tue restrizioni SameSite con ogni cookie che emetti. In questo modo, puoi controllare esattamente in quali contesti verrà utilizzato il cookie, indipendentemente dal browser.

Anche se tutti i browser alla fine adottassero la politica "Lax-by-default", questa non sarebbe adatta a tutti i cookie e può essere aggirata più facilmente rispetto alle restrizioni Strict. Nel frattempo, l'incoerenza tra diversi browser significa anche che solo un sottoinsieme dei tuoi utenti trarrà vantaggio da qualsiasi protezione SameSite.

Idealmente, dovresti utilizzare la politica Strict per impostazione predefinita; quindi, abbassarla a Lax solo se hai una buona ragione per farlo. Non disabilitare mai le restrizioni SameSite con SameSite=None a meno che tu non sia pienamente consapevole delle implicazioni di sicurezza.
### Maggiore attenzione agli attacchi cross-origin e same-site
Sebbene le restrizioni SameSite correttamente configurate forniscano una buona protezione dagli attacchi cross-site, è fondamentale comprendere che sono completamente impotenti contro gli attacchi cross-origin e same-site.

Se possibile, consigliamo di isolare i contenuti non sicuri, come i file caricati dagli utenti, su un sito separato da qualsiasi funzionalità o dato sensibile. Quando si testa un sito, assicurarsi di controllare attentamente tutta la superficie di attacco disponibile appartenente allo stesso sito, inclusi tutti i domini fratelli.
