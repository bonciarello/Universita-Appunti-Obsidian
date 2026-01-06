OAuth è un framework di autorizzazione comunemente utilizzato che consente ai siti web e alle applicazioni web di richiedere un accesso limitato all'account di un utente su un'altra applicazione. Fondamentalmente, OAuth consente l'utente di concedere questo accesso senza esporre le proprie credenziali di accesso all'applicazione richiedente. Ciò significa che gli utenti possono definire con precisione quali dati desiderano condividere anziché dover cedere il controllo completo del proprio account a una terza parte.

Il processo OAuth di base è ampiamente utilizzato per integrare funzionalità di terze parti che richiedono l'accesso a determinati dati dall'account di un utente. Ad esempio, un'applicazione potrebbe utilizzare OAuth per richiedere l'accesso all'elenco dei contatti e-mail in modo da poter suggerire persone con cui connettersi. Tuttavia, lo stesso meccanismo viene utilizzato anche per fornire servizi di autenticazione di terze parti, consentendo agli utenti di accedere con un account che hanno con un sito web diverso.
### Come funziona OAuth 2.0?
OAuth 2.0 è stato originariamente sviluppato come un modo per condividere l'accesso a dati specifici tra applicazioni. Funziona definendo una serie di interazioni tra tre parti distinte:
- **applicazione client:** il sito web o l'applicazione web che desidera accedere ai dati dell'utente;
- **proprietario della risorsa:** l'utente ai cui dati l'applicazione client desidera accedere;
- **fornitore di servizi OAuth:** il sito web o l'applicazione che controlla i dati dell'utente e l'accesso a essi. Supportano OAuth fornendo un'API per interagire sia con un server di autorizzazione che con un server di risorse.

Esistono numerosi modi diversi in cui può essere implementato il processo OAuth effettivo: questi sono noti come tipi di concessione OAuth. In generale, entrambi questi tipi di concessione comportano le seguenti fasi:
1. l'applicazione client richiede l'accesso a un sottoinsieme dei dati dell'utente, specificando quale tipo di concessione desidera utilizzare e che tipo di accesso desidera;
2. all'utente viene chiesto di accedere al servizio OAuth e di dare esplicitamente il proprio consenso per l'accesso richiesto;
3. l'applicazione client riceve un token di accesso univoco che dimostra di avere l'autorizzazione dall'utente per accedere ai dati richiesti. Il modo esatto in cui ciò avviene varia in modo significativo a seconda del tipo di concessione;
4. l'applicazione client utilizza questo token di accesso per effettuare chiamate API recuperando i dati rilevanti dal server delle risorse.

OAuth, originariamente progettato per l’autorizzazione, si è evoluto anche come metodo per autenticare gli utenti, spesso utilizzato per l’accesso tramite account di social media (es. “Accedi con Facebook”). Questo processo sfrutta i flussi standard di OAuth, adattandoli per ottenere dati identificativi (es. email) dal server di risorse, consentendo all’app client di autenticare l’utente. L’esperienza finale per l’utente somiglia al Single Sign-On (SSO). Tuttavia, l’implementazione presenta vulnerabilità specifiche in questo contesto.
### Come nascono le vulnerabilità dell'autenticazione OAuth?
Le vulnerabilità dell'autenticazione OAuth sorgono in parte perché la specifica OAuth è relativamente vaga e flessibile per progettazione. Sebbene vi siano una manciata di componenti obbligatori richiesti per la funzionalità di base di ogni tipo di concessione, la stragrande maggioranza dell'implementazione è completamente facoltativa. Ciò include molte impostazioni di configurazione necessarie per mantenere al sicuro i dati degli utenti. In breve, ci sono molte opportunità per le cattive pratiche di insinuarsi.

Uno degli altri problemi chiave con OAuth è la generale mancanza di funzionalità di sicurezza integrate. La sicurezza si basa quasi interamente sugli sviluppatori che utilizzano la giusta combinazione di opzioni di configurazione e implementano le proprie misure di sicurezza aggiuntive in aggiunta, come una convalida di input robusta.

A seconda del tipo di concessione, i dati altamente sensibili vengono inviati anche tramite il browser, il che presenta varie opportunità per un aggressore di intercettarli.
## Tipi di concessione OAuth
### Tipo di concessione del codice di autorizzazione (code)
Il tipo di concessione del codice di autorizzazione, sebbene inizialmente sembri complesso, diventa chiaro con le basi giuste. Questo flusso utilizza reindirizzamenti per ottenere il consenso dell’utente e concedere un codice di autorizzazione al client. Il client scambia poi questo codice con un token di accesso tramite un canale sicuro server-to-server, invisibile all’utente finale.
Grazie all’utilizzo del back-channel e all’uso del `client_secret` per autenticarsi, i dati sensibili (come il token di accesso) non transitano attraverso il browser, rendendo questo tipo di concessione uno dei più sicuri. È particolarmente consigliato per applicazioni lato server.

Il flusso per il tipo di concessione del codice di autorizzazione di OAuth è il seguente:
1. **richiesta di autorizzazione:** il client richiede all’endpoint `/authorization` il consenso dell’utente per accedere ai dati specifici;
2. **accesso e consenso:** l’utente accede e approva l’accesso ai dati richiesti;
3. **concessione del codice di autorizzazione:** se approvato, il server reindirizza il browser dell’utente con un codice di autorizzazione;
4. **richiesta del token di accesso:** il client scambia il codice per un token di accesso tramite un canale sicuro;
5. **concessione del token di accesso:** il server rilascia un token che il client può usare per accedere ai dati dell’utente;
6. **chiamata API:** il client utilizza il token per richiedere i dati all’endpoint `/userinfo`;
7. **concessione delle risorse:** il server restituisce i dati richiesti, che il client usa per autenticare l’utente.
### Tipo di concessione implicita (implicit)
Il tipo di concessione implicita è più semplice e diretto: l’applicazione client riceve il token di accesso immediatamente dopo il consenso dell’utente, senza dover scambiare un codice di autorizzazione.
Tuttavia, questa semplicità ha un costo in termini di sicurezza. Poiché tutte le comunicazioni avvengono tramite reindirizzamenti del browser e manca un back-channel sicuro, il token di accesso è più vulnerabile a eventuali attacchi. Questo tipo di concessione è più indicato per applicazioni a pagina singola e applicazioni desktop native, dove la gestione di un `client_secret` sul back-end è complessa o impossibile.

Il flusso per il tipo di concessione implicita di OAuth è il seguente:
1. **richiesta di autorizzazione:** il client invia una richiesta all’endpoint `/authorization`, ma con il parametro `response_type` impostato su token, indicando che desidera ricevere direttamente un token di accesso;
2. **accesso e consenso dell’utente:** l’utente accede e fornisce il consenso, seguendo lo stesso processo del flusso del codice di autorizzazione;
3. **concessione del token di accesso:** dopo il consenso, il server OAuth reindirizza il browser dell’utente al `redirect_uri`, includendo il token di accesso come frammento URL (`#access_token=...`). Il client deve utilizzare uno script per estrarre il token dall’URL;
4. **chiamata API:** con il token estratto, il client può effettuare richieste API all’endpoint `/userinfo`, includendo il token nell’intestazione `Authorization`;
5. **concessione delle risorse:** il server verifica il token e restituisce i dati richiesti, come il nome utente o l’e-mail, che il client può utilizzare per autenticare l’utente.

Il flusso implicito di OAuth semplifica l’acquisizione del token di accesso, ma presenta alcune differenze rispetto al flusso del codice di autorizzazione. Questo flusso, pur essendo più semplice, è meno sicuro poiché il token transita nel browser ed è più vulnerabile ad attacchi. È quindi più adatto ad applicazioni dove non è possibile utilizzare un back-channel sicuro.
## Sfruttamento delle vulnerabilità di autenticazione OAuth
Le vulnerabilità possono verificarsi sia nell'implementazione di OAuth nell'applicazione client sia nella configurazione del servizio OAuth stesso.
### Vulnerabilità nell'applicazione client OAuth
#### Implementazione non corretta del tipo di concessione implicita
Il flusso implicito di OAuth, usato soprattutto per SPA, invia il token tramite browser. Per mantenere la sessione, il client invia i dati utente al server con una POST, ottenendo un cookie. Se il token non viene verificato correttamente, un attaccante può manipolare i dati e impersonare altri utenti.
#### Protezione CSRF difettosa
Sebbene molti componenti dei flussi OAuth siano facoltativi, alcuni di essi sono fortemente consigliati, a meno che non vi sia un motivo importante per non utilizzarli. Un esempio del genere è il parametro `state`.
Il parametro `state` dovrebbe idealmente contenere un valore non indovinabile, come l'hash di qualcosa legato alla sessione dell'utente quando avvia per la prima volta il flusso OAuth. Questo valore viene quindi passato avanti e indietro tra l'applicazione client e il servizio OAuth come una forma di token CSRF per l'applicazione client. Pertanto, se noti che la richiesta di autorizzazione non invia un parametro `state`, questo è estremamente interessante dal punto di vista di un aggressore. Ciò significa potenzialmente che possono avviare un flusso OAuth da soli prima di ingannare il browser di un utente affinché lo completi, in modo simile a un attacco CSRF tradizionale. Ciò può avere gravi conseguenze a seconda di come OAuth viene utilizzato dall'applicazione client.

Considera un sito web che consente agli utenti di accedere utilizzando un meccanismo classico basato su password o collegando il proprio account a un profilo di social media tramite OAuth. In questo caso, se l'applicazione non riesce a utilizzare il parametro `state`, un aggressore potrebbe potenzialmente dirottare l'account dell'utente vittima sull'applicazione client, associandolo al suo account di social media.

Si noti che se il sito consente agli utenti di effettuare l'accesso esclusivamente tramite OAuth, il parametro `state` è presumibilmente meno critico. Tuttavia, non utilizzare un parametro `state` può comunque consentire agli aggressori di costruire attacchi CSRF di accesso, tramite i quali l'utente viene ingannato e indotto ad accedere all'account dell'aggressore.
### Vulnerabilità nel servizio OAuth
#### Fuga di codici di autorizzazione e token di accesso
Forse la vulnerabilità più infame basata su OAuth si verifica quando la configurazione del servizio OAuth stesso consente agli aggressori di rubare codici di autorizzazione o token di accesso associati agli account di altri utenti. Rubando un codice o un token valido, l'aggressore potrebbe essere in grado di accedere ai dati della vittima. In definitiva, ciò può compromettere completamente il suo account: l'aggressore potrebbe potenzialmente accedere come utente vittima su qualsiasi applicazione client registrata con questo servizio OAuth.

A seconda del tipo di concessione, un codice o un token viene inviato tramite il browser della vittima all'endpoint `/callback` specificato nel parametro `redirect_uri` della richiesta di autorizzazione. Se il servizio OAuth non riesce a convalidare correttamente questo URI, un aggressore potrebbe essere in grado di costruire un attacco di tipo CSRF, inducendo il browser della vittima ad avviare un flusso OAuth che invierà il codice o il token a un `redirect_uri` controllato dall'aggressore.

Nel caso del flusso del codice di autorizzazione, un aggressore potrebbe potenzialmente rubare il codice della vittima prima che venga utilizzato. Possono quindi inviare questo codice all'endpoint legittimo `/callback` dell'applicazione client (il `redirect_uri` originale) per ottenere l'accesso all'account dell'utente. In questo scenario, un aggressore non ha nemmeno bisogno di conoscere il segreto client o il token di accesso risultante. Finché la vittima ha una sessione valida con il servizio OAuth, l'applicazione client completerà semplicemente lo scambio di codice/token per conto dell'aggressore prima di effettuare l'accesso all'account della vittima.
Nota che l'utilizzo della protezione `state` o `nonce` non impedisce necessariamente questi attacchi perché un aggressore può generare nuovi valori dal proprio browser.

Anche i server di autorizzazione più sicuri richiederanno l'invio di un parametro `redirect_uri` quando si scambia il codice. Il server può quindi verificare se questo corrisponde a quello ricevuto nella richiesta di autorizzazione iniziale e rifiutare lo scambio in caso contrario. Poiché ciò avviene nelle richieste server-to-server tramite un back-channel sicuro, l'attaccante non è in grado di controllare questo secondo parametro `redirect_uri`.
## Come prevenire le vulnerabilità di autenticazione OAuth
Per prevenire vulnerabilità OAuth, sia i provider che le app client devono implementare controlli robusti, specialmente sul parametro `redirect_uri`. L’OAuth offre poca protezione integrata, quindi la sicurezza dipende dagli sviluppatori.

**Per i provider OAuth:**
- richiedere la registrazione di un whitelist di `redirect_uri` e validare con confronto esatto, byte per byte;
- enforce il parametro `state`, legandolo alla sessione utente per prevenire attacchi CSRF;
- verificare che il token sia emesso per lo stesso `client_id` che effettua la richiesta e controllare il scope del token.

**Per le app client OAuth:**
- comprendere a fondo il funzionamento di OAuth per evitare errori di implementazione;
- usare il parametro `state`, anche se non obbligatorio;
- inviare il `redirect_uri` sia all’endpoint `/authorization` che a `/token`;
- per app mobili/desktop, usare PKCE per proteggere l’access code in assenza di `client_secret`;
- validare correttamente `id_token` secondo gli standard OpenID Connect;
- proteggere i codici di autorizzazione da leak (Referer headers, script esterni, ecc.) e non includerli nei file JS dinamici.
