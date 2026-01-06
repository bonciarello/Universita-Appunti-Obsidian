Il controllo degli accessi è l'applicazione di vincoli su chi o cosa è autorizzato a eseguire azioni o ad accedere alle risorse. Nel contesto delle applicazioni web, il controllo degli accessi dipende dall'autenticazione e dalla gestione delle sessioni:
- l'autenticazione conferma che l'utente è chi dice di essere;
- la gestione delle sessioni identifica quali richieste HTTP successive vengono effettuate dallo stesso utente;
- il controllo degli accessi determina se all'utente è consentito eseguire l'azione che sta tentando di eseguire.

I controlli degli accessi non funzionanti sono comuni e spesso presentano una vulnerabilità critica alla sicurezza. La progettazione e la gestione dei controlli degli accessi sono un problema complesso e dinamico che applica vincoli aziendali, organizzativi e legali a un'implementazione tecnica. Le decisioni sulla progettazione del controllo degli accessi devono essere prese da esseri umani; quindi, il potenziale di errori è elevato.
## Controlli di accesso verticali
I controlli di accesso verticali sono meccanismi che limitano l'accesso a funzionalità sensibili a specifici tipi di utenti.

Con i controlli di accesso verticali, diversi tipi di utenti hanno accesso a diverse funzioni dell'applicazione. Ad esempio, un amministratore potrebbe essere in grado di modificare o eliminare l'account di qualsiasi utente, mentre un utente normale non ha accesso a queste azioni. I controlli di accesso verticali possono essere implementazioni più dettagliate di modelli di sicurezza progettati per applicare policy aziendali come la separazione dei compiti e il privilegio minimo.
## Controlli di accesso orizzontali
I controlli di accesso orizzontali sono meccanismi che limitano l'accesso alle risorse a utenti specifici.

Con i controlli di accesso orizzontali, diversi utenti hanno accesso a un sottoinsieme di risorse dello stesso tipo. Ad esempio, un'applicazione bancaria consentirà a un utente di visualizzare le transazioni ed effettuare pagamenti dai propri conti, ma non dai conti di nessun altro utente.
## Controlli di accesso dipendenti dal contesto
I controlli di accesso dipendenti dal contesto limitano l'accesso alle funzionalità e alle risorse in base allo stato dell'applicazione o all'interazione dell'utente con essa.

I controlli di accesso dipendenti dal contesto impediscono a un utente di eseguire azioni nell'ordine sbagliato. Ad esempio, un sito web di vendita al dettaglio potrebbe impedire agli utenti di modificare il contenuto del carrello dopo aver effettuato il pagamento.
## Esempi di controlli di accesso non funzionanti
Le vulnerabilità del controllo degli accessi non funzionanti si verificano quando un utente riesce ad accedere a risorse o a eseguire azioni che non dovrebbe essere in grado di fare.
### Vertical privilege escalation
Se un utente può ottenere l'accesso a funzionalità a cui non è autorizzato ad accedere, allora si tratta di vertical privilege escalation. Ad esempio, se un utente non amministrativo può ottenere l'accesso a una pagina di amministrazione in cui può eliminare account utente, allora si tratta di vertical privilege escalation.

**Funzionalità non protetta** Nella sua forma più elementare, il vertical privilege escalation si verifica quando un'applicazione non impone alcuna protezione per funzionalità sensibili. Ad esempio, le funzioni amministrative potrebbero essere collegate dalla pagina di benvenuto di un amministratore ma non dalla pagina di benvenuto di un utente. Tuttavia, un utente potrebbe essere in grado di accedere alle funzioni amministrative navigando verso l'URL di amministrazione pertinente.

Ad esempio, un sito web potrebbe ospitare funzionalità sensibili al seguente URL:
`https://insecure-website.com/admin`

Questo potrebbe essere accessibile a qualsiasi utente, non solo agli utenti amministrativi che hanno un collegamento alla funzionalità nella loro interfaccia utente. In alcuni casi, l'URL amministrativo potrebbe essere divulgato in altre posizioni, come il file robots.txt:
`https://insecure-website.com/robots.txt`

Anche se l'URL non è reso pubblico, un aggressore potrebbe riuscire a utilizzare un elenco di parole per forzare la posizione della funzionalità sensibile (APPRENTICE: Unprotected admin functionality).

In alcuni casi, la funzionalità sensibile viene nascosta assegnandole un URL meno prevedibile. Questo è un esempio della cosiddetta "sicurezza per oscurità". Tuttavia, nascondere la funzionalità sensibile non fornisce un controllo di accesso efficace perché gli utenti potrebbero scoprire l'URL offuscato in vari modi.

Immagina un'applicazione che ospita funzioni amministrative al seguente URL:
`https://insecure-website.com/administrator-panel-yb556`

Questo potrebbe non essere direttamente indovinabile da un aggressore. Tuttavia, l'applicazione potrebbe comunque far trapelare l'URL agli utenti. L'URL potrebbe essere divulgato in JavaScript che costruisce l'interfaccia utente in base al ruolo dell'utente:

```javascript
<script>
var isAdmin = false;
if (isAdmin) {
...
var adminPanelTag = document.createElement('a');
adminPanelTag.setAttribute('https://insecure-website.com/administrator-panel-yb556');
adminPanelTag.innerText = 'Admin panel';
...
}
</script>
```

Questo script aggiunge un collegamento all'interfaccia utente dell'utente se è un utente amministratore. Tuttavia, lo script contenente l'URL è visibile a tutti gli utenti, indipendentemente dal loro ruolo (APPRENTICE: Unprotected admin functionality with unpredictable URL).

**Metodi di controllo degli accessi basati su parametri** Alcune applicazioni determinano i diritti di accesso o il ruolo dell'utente al momento dell’accesso; quindi, memorizzano queste informazioni in una posizione controllabile dall'utente. Potrebbe trattarsi di:
- un campo nascosto;
- un cookie;
- un parametro stringa di query preimpostato.

L'applicazione prende decisioni di controllo degli accessi in base al valore inviato. Ad esempio:
`https://insecure-website.com/login/home.jsp?admin=true`
`https://insecure-website.com/login/home.jsp?role=1`

Questo approccio non è sicuro perché un utente può modificare il valore e accedere a funzionalità per cui non è autorizzato, come le funzioni amministrative (APPRENTICE: User role controlled by request parameter) (APPRENTICE: User role can be modified in user profile).

**Controllo di accesso interrotto a causa di una configurazione errata della piattaforma** Alcune applicazioni applicano controlli di accesso a livello di piattaforma. Lo fanno limitando l'accesso a URL e metodi HTTP specifici in base al ruolo dell'utente. Ad esempio, un'applicazione potrebbe configurare una regola come segue:

`DENY: POST, /admin/deleteUser, managers`

Questa regola nega l'accesso al metodo POST sull'URL /admin/deleteUser, per gli utenti nel gruppo manager. In questa situazione possono verificarsi vari problemi, che portano a bypass del controllo di accesso.

Alcuni framework applicativi supportano vari header HTTP non standard che possono essere utilizzati per sovrascrivere l'URL nella richiesta originale, come X-Original-URL e X-Rewrite-URL. Se un sito web utilizza rigorosi controlli front-end per limitare l'accesso in base all'URL, ma l'applicazione consente che l'URL venga sovrascritto tramite un header di richiesta, potrebbe essere possibile bypassare i controlli di accesso utilizzando una richiesta come la seguente:

```http
POST / HTTP/1.1
X-Original-URL: /admin/deleteUser
...
```

Un attacco alternativo riguarda il metodo HTTP utilizzato nella richiesta. I controlli front-end descritti nelle sezioni precedenti limitano l'accesso in base all'URL e al metodo HTTP. Alcuni siti web tollerano diversi metodi di richiesta HTTP quando eseguono un'azione. Se un aggressore può utilizzare il metodo GET (o un altro) per eseguire azioni su un URL limitato, può aggirare il controllo di accesso implementato a livello di piattaforma (PRACTITIONER: Method-based access control can be circumvented).
### Horizontal privilege escalation
L'horizontal privilege escalation si verifica se un utente riesce ad accedere alle risorse appartenenti a un altro utente, anziché alle proprie risorse di quel tipo. Ad esempio, se un dipendente può accedere ai record di altri dipendenti oltre che ai propri, allora si tratta di un'escalation orizzontale dei privilegi.

Gli attacchi di horizontal privilege escalation possono utilizzare metodi di exploit simili a quelli del vertical privilege escalation. Ad esempio, un utente potrebbe accedere alla propria pagina account utilizzando il seguente URL:

`https://insecure-website.com/myaccount?id=123`

Se un aggressore modificasse il valore del parametro id in quello di un altro utente, potrebbe ottenere l'accesso alla pagina account di un altro utente e ai dati e alle funzioni associati.

In alcune applicazioni, il parametro exploitable non ha un valore prevedibile. Ad esempio, invece di un numero incrementale, un'applicazione potrebbe utilizzare identificatori univoci globali (GUID) per identificare gli utenti. Ciò potrebbe impedire a un aggressore di indovinare o prevedere l'identificativo di un altro utente. Tuttavia, i GUID appartenenti ad altri utenti potrebbero essere divulgati altrove nell'applicazione in cui gli utenti sono referenziati, come messaggi utente o recensioni.

In alcuni casi, l'applicazione rileva quando all'utente non è consentito accedere alla risorsa e restituisce un reindirizzamento alla pagina di login. Tuttavia, la risposta contenente il reindirizzamento potrebbe comunque includere alcuni dati sensibili appartenenti all'utente preso di mira, quindi l'attacco ha comunque successo (APPRENTICE: User ID controlled by request parameter with data leakage in redirect).
### Horizontal to vertical privilege escalation
Spesso, un attacco di horizontal privilege escalation può trasformarsi in un vertical privilege escalation, compromettendo un utente con più privilegi. Ad esempio, un'escalation orizzontale potrebbe consentire a un aggressore di reimpostare o catturare la password appartenente a un altro utente. Se l'aggressore prende di mira un utente amministrativo e compromette il suo account, può ottenere l'accesso amministrativo e quindi eseguire un'escalation dei privilegi verticale.

Un aggressore potrebbe essere in grado di ottenere l'accesso alla pagina dell'account di un altro utente utilizzando la tecnica di manomissione dei parametri già descritta per l'escalation dei privilegi orizzontale:
`https://insecure-website.com/myaccount?id=456`

Se l'utente di destinazione è un amministratore dell'applicazione, l'aggressore otterrà l'accesso a una pagina dell'account amministrativo. Questa pagina potrebbe rivelare la password dell'amministratore o fornire un mezzo per modificarla, oppure potrebbe fornire l'accesso diretto a funzionalità privilegiate (APPRENTICE: User ID controlled by request parameter with password disclosure).
### Riferimenti diretti non sicuri (IDOR)
I riferimenti diretti agli oggetti non sicuri (IDOR) sono un tipo di vulnerabilità del controllo di accesso che si verifica quando un'applicazione utilizza input forniti dall'utente per accedere direttamente agli oggetti.
Il termine IDOR è stato reso popolare dalla sua comparsa nella Top Ten di OWASP 2007. Tuttavia, è solo un esempio dei molti errori di implementazione del controllo di accesso che possono portare all'elusione dei controlli di accesso. Le vulnerabilità IDOR sono più comunemente associate all'horizontal privilege escalation, ma possono verificarsi anche in relazione alla vertical privilege escalation.

Esistono molti esempi di vulnerabilità del controllo degli accessi in cui i valori dei parametri controllati dall'utente vengono utilizzati per accedere direttamente a risorse o funzioni.

**Vulnerabilità IDOR con riferimento diretto agli oggetti del database** Considera un sito web che utilizza il seguente URL per accedere alla pagina dell'account del cliente, recuperando le informazioni dal database back-end:
`https://insecure-website.com/customer_account?customer_number=132355`

Qui, il numero del cliente viene utilizzato direttamente come indice di record nelle query eseguite sul database back-end. Se non sono presenti altri controlli, un aggressore può semplicemente modificare il valore customer_number, aggirando i controlli di accesso per visualizzare i record di altri clienti. Questo è un esempio di vulnerabilità IDOR che porta all'escalation dei privilegi orizzontali.

Un aggressore potrebbe essere in grado di eseguire l'horizontal e vertical privilege escalation modificando l'utente in uno con privilegi aggiuntivi aggirando i controlli di accesso. Altre possibilità includono lo sfruttamento della perdita di password o la modifica dei parametri una volta che l'aggressore è atterrato nella pagina degli account dell'utente, ad esempio.

**Vulnerabilità IDOR con riferimento diretto ai file statici** Le vulnerabilità IDOR spesso si verificano quando risorse sensibili si trovano in file statici sul file system lato server. Ad esempio, un sito web potrebbe salvare le trascrizioni dei messaggi di chat su disco utilizzando un nome file incrementale e consentire agli utenti di recuperarle visitando un URL come il seguente:
`https://insecure-website.com/static/12144.txt`

In questa situazione, un aggressore può semplicemente modificare il nome file per recuperare una trascrizione creata da un altro utente e potenzialmente ottenere credenziali utente e altri dati sensibili.
### Vulnerabilità del controllo di accesso nei processi multi-step
Molti siti web implementano funzioni importanti in una serie di step. Ciò è comune quando:
- è necessario acquisire una varietà di input o opzioni;
- l'utente deve rivedere e confermare i dettagli prima che l'azione venga eseguita.

Ad esempio, la funzione amministrativa per aggiornare i dettagli dell'utente potrebbe comportare i seguenti step:
1. caricare il modulo che contiene i dettagli per un utente specifico;
2. inviare le modifiche;
3. rivedere le modifiche e confermare.

A volte, un sito web implementerà rigorosi controlli di accesso su alcuni di questi step, ma ne ignorerà altri. Immagina un sito web in cui i controlli di accesso vengono applicati correttamente al primo e al secondo step, ma non al terzo step. Il sito web presuppone che un utente raggiungerà lo step 3 solo se ha già completato i primi step, che sono correttamente controllati. Un aggressore può ottenere l'accesso non autorizzato alla funzione saltando i primi due step e inviando direttamente la richiesta per il terzo step con i parametri richiesti (PRACTITIONER: Multi-step process with no access control on one step).
### Controllo degli accessi basato su Referer
Alcuni siti web basano i controlli degli accessi sull'intestazione Referer inviata nella richiesta HTTP. L'intestazione Referer può essere aggiunta alle richieste dai browser per indicare quale pagina ha avviato una richiesta.

Ad esempio, un'applicazione impone in modo robusto il controllo degli accessi sulla pagina amministrativa principale in /admin, ma per le sottopagine come /admin/deleteUser ispeziona solo l'intestazione Referer. Se l'intestazione Referer contiene l'URL principale /admin, la richiesta è consentita.

In questo caso, l'intestazione Referer può essere completamente controllata da un aggressore. Ciò significa che possono falsificare richieste dirette a sottopagine sensibili fornendo l'intestazione Referer richiesta e ottenere un accesso non autorizzato (PRACTITIONER: Referer-based access control).
### Controllo degli accessi basato sulla posizione
Alcuni siti web applicano controlli di accesso basati sulla posizione geografica dell'utente. Ciò può applicarsi, ad esempio, ad applicazioni bancarie o servizi multimediali in cui si applicano legislazioni statali o restrizioni aziendali. Questi controlli di accesso possono spesso essere aggirati tramite l'uso di proxy web, VPN o la manipolazione dei meccanismi di geolocalizzazione lato client.
## Come prevenire le vulnerabilità del controllo di accesso
Le vulnerabilità del controllo di accesso possono essere prevenute adottando un approccio di difesa approfondita e applicando i seguenti principi:
- non affidarti mai solo all'offuscamento per il controllo di accesso;
- a meno che una risorsa non sia destinata a essere accessibile al pubblico, nega l'accesso per impostazione predefinita;
- ove possibile, utilizza un singolo meccanismo a livello di applicazione per applicare i controlli di accesso;
- a livello di codice, rendi obbligatorio per gli sviluppatori dichiarare l'accesso consentito per ciascuna risorsa e nega l'accesso per impostazione predefinita;
- esegui un audit e un test approfonditi dei controlli di accesso per assicurarti che funzionino come progettato.
