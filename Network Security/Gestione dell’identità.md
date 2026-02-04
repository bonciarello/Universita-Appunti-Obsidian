L’autenticazione tramite credenziali si compone di più fasi:
1.  Memorizzazione lato client;
2.  Inserimento;
3.  Trasmissione;
4.  Validazione;
5.  Memorizzazione lato server.

Durante la fase di autenticazione agiscono diversi attori:
*   Un utente con le sue credenziali;
*   Un servizio a cui si desidera accedere;
*   Un dispositivo per l’host, un dispositivo per il server e il media di trasmissione.

## 1. Fasi dell'Autenticazione

### Memorizzazione lato client
In questa fase, le credenziali (nome utente e password) possono essere salvate in modo sicuro sul dispositivo dell’utente, ad esempio, tramite un gestore di password. Lo scopo è facilitare l’inserimento delle credenziali per l’utente, riducendo la necessità di ricordarle o inserirle manualmente ogni volta. Ci sono tre modi per salvare una password:
*   **Mental storage:** pratica di memorizzare le password nella propria mente;
*   **Paper storage:** pratica di memorizzare le password su supporti fisici, come carta o fogli scritti;
*   **Digital storage:** pratica di memorizzare le password in forma digitale, utilizzando appositi strumenti o applicazioni per la gestione delle password:
    *   **Local password managers:** non così bene perché di solito sono sotto attacco. LPM deve ancora fare affidamento sulla memoria umana perché si sblocca il database solo se l'utente inserisce la chiave corretta per aprirlo;
    *   **USB password managers:** possono essere utilizzati insieme agli LPM per evitare la possibilità che il database interno di LPM possa essere rubato e compromesso. Il database viene memorizzato nella penna USB e viene inserito nella porta solo quando è necessario.

### Inserimento
In questa fase, l’utente inserisce le proprie credenziali (nome utente e password) nell’interfaccia di autenticazione dell’applicazione o del sito web. Fornisce al sistema le informazioni necessarie per identificare e autenticare l’utente. Lo stage di inserimento è forse una delle fasi più delicate. Si può essere soggetti a:
*   **Snooping:** qualcuno che affianco a te guarda e spia le password;
*   **Keylogger:** permette di memorizzare o inviare su internet le password di qualcun altro. Questo può essere realizzato sia tramite hardware ma anche con un software. Ci sono alcuni antivirus capaci di identificare entrambe le specie.

### Trasmissione
In questa fase, le credenziali inserite vengono inviate dal client al server di autenticazione tramite una connessione sicura, spesso crittografata (come HTTPS). Lo scopo è assicurare che le credenziali viaggino in modo sicuro dal client al server, prevenendo intercettazioni e attacchi MITM.

Anche lo stage di trasmissione è soggetto a possibili attacchi: non tutti i siti ancora supportano oppure non hanno protezioni riguardo l’**SSLSTRIP**. Sarebbe meglio utilizzare protocolli basati sulle challange (HTTP Digest, CHAP, MS-CHAPv2, EAP-SRP). Il **pineapple Wi-Fi** veniva usata dagli amministratori di rete per effettuare dei test. Oggi possono essere usati come rogue AP ed effettuare attacchi MITM.

### Mitigazioni
Ci sono ad oggi delle tecniche che permettono di limitare, in parte, la possibilità di stealing delle credenziali. Uno tra questi è sicuramente l’**autenticazione a fattore multiplo (two multi factor authenticator)** dove si è cercato di associare a qualcosa di “facilmente” compromettibile (password) con qualcosa di più complesso (introduzione di un OTP).

Ci sono alcune linee guida nella creazione di una password:
*   Preferire password lunghe;
*   Preferire password non comuni;
*   Non cambiare troppo spesso la password.

È una buona pratica quella di memorizzare server-side un “**salt**” (nonce) in maniera tale da generare l’hash della password utilizzando come materiale randomico quest’ultimo. L’utilizzo di un salt permette di mitigare anche attacchi di tipo **RainbowTable**.

### Validazione
In questa fase, il server di autenticazione riceve le credenziali e le confronta con quelle memorizzate nel proprio database per verificare l’identità dell’utente. Lo scopo è determinare se le credenziali fornite corrispondono a un account valido, consentendo o negando l’accesso. Usare direttamente le funzioni hash per autenticare e archiviare le password è una soluzione semplice ma debole come usare le funzioni hash senza salt, quindi è da evitare.

## 2. Protocollo SRP (Secure Remote Protocol)
Un protocollo che merita una menzione è **SRP (Secure Remote Protocol)**. Il protocollo SRP rappresenta il protocollo più all’avanguardia per l’autenticazione e per la memorizzazione delle credenziali che permette al server di autenticare un client senza che questo trasmetta in chiaro la password sulla rete. Il suo funzionamento è il seguente:

1.  Carol invia il suo username a Steve;
2.  A questo punto Steve fetcha il proprio database e cerca il salt e il verifier associato a quell’account. Il salt viene inviato a Carol la quale computerà la chiave privata sfruttando l’informazione appena inviata e la sua password reale;
3.  A questo punto Carol genera la sua chiave effimera A attraverso un numero a (con $1 < a < n$) e la invia a Steve;
4.  Steve effettua lo stesso procedimento generando anche egli una chiave effimera B che invia prontamente a Carol;
5.  Sfruttando le proprietà dell’algebra modulare sia Carol che Steve potranno generare il valore condiviso S con i dati a loro disposizione;
6.  Entrambi hashano il valore S rendendolo una session key robusta;
7.  A questo punto Carol invia la sua chiave di sessione M[1] e aspetta che venga verificata;
8.  Lo stesso procedimento viene ripetuto da Steve mandandogli la sua chiave di sessione M[2].

## 3. Infrastrutture Tripla AAA
È un'infrastruttura in cui fornisci tre funzioni:
*   **Authentication:** dimostrare l'identità degli utenti;
*   **Authorization:** una volta che un utente è loggato, si vuole dare all'utente il potere relativo al suo ruolo, cosa è autorizzato a fare o meno;
*   **Accounting:** registra le azioni degli utenti ai fini dell'affidabilità e della responsabilità.

In questa infrastruttura c'è un cambio di paradigma: finora, stavamo solo analizzando l'applicazione client-server, ma ora vogliamo analizzare una nuova parte: abbiamo un client, un server e un servizio di distribuzione delle chiavi che gestisce la gestione dell'identità. Con **KDC (Key Distribution Center)**, l'identità di tutti i soggetti coinvolti nella struttura è comprovata dalla conoscenza della password o di un codice segreto.

Se il client desidera parlare con il servizio ma il servizio non risponderà fino a quando il client non dimostrerà la propria identità ed entrambi hanno condiviso una chiave di sessione, il client parla con il KDC e ottiene un pacchetto come questo:
$E(Ka,[Ks || IDa || IDb || N1]) || E(Kb,[Ks || IDa])$

Il pacchetto è crittografato con una chiave segreta conosciuta solo dal client e dal KDC. La prima parte del pacchetto è la seguente:
$E(Ka,[Ks || IDa || IDb || N1])$
Può essere ricostruita solo dal client e contiene la chiave di sessione, l'ID del client e del servizio e un valore Nounce N1 che serve per evitare che chi ha intercettato in precedenza il pacchetto possa rigiocare il pacchetto di autenticazione.

La seconda parte della stringa non può essere decrittografata dal client perché è crittografata utilizzando la chiave del servizio: quindi, solo il destinatario può decrittografarla e contiene una versione crittografata della chiave di sessione (Ks) e l'ID del client. Quando il client riceve questo pacchetto, il client può decifrarlo solo se è il proprietario della chiave e ciò che ha ottenuto è:
$E(Kb,[Ks || IDa])$
Questo è un numero speciale utilizzato come token di autenticazione che verrà inviato al servizio. Il servizio lo decritterà con la sua chiave Kb e verificherà se la seconda parte (tra le parentesi quadre) è IDa, l'ID del client. Questo è possibile solo se il pacchetto proviene realmente dal KDC.

Come risposta, il servizio invia al client un pacchetto come questo:
$E(Ks,N2)$
Questo pacchetto deve verificare che il client abbia la stessa chiave di sessione. Se lo è, il client risponde con un altro pacchetto cifrato:
$E(Ks,f(N2))$
In questo modo, sia il client che il servizio dimostrano la propria identità.

I vantaggi di questa tipologia di infrastruttura sono:
*   Il servizio non necessita di conoscere le credenziali di autenticazione del client;
*   L'autenticazione è centralizzata nel KDC.

## 4. Schemi di Autenticazione
Si può sostituire il KDC con un sistema chiamato **Identity Provider (IDP)** che è un meccanismo di controllo dell'identità utilizzato per identificare noi stessi.

Immaginiamo un'infrastruttura con un IDP e molti fornitori di servizi che si affidano all'IDP per conoscere l'identità degli utenti. Se l'utente desidera utilizzare il servizio, deve fornire una prova della sua identità e per farlo deve dimostrare di aver parlato con l'IDP e l'IDP gli ha restituito la prova da utilizzare per dimostrare la sua identità.

Esistono due tipi di schemi di autenticazione come questo:

*   **SAML (Security Association Mark-up Language):** è uno standard aperto per lo scambio di dati di autenticazione e autorizzazione tra diverse parti, tipicamente tra un provider di identità (IDP) e un provider di servizi (SP). Consente il Single Sign-On (SSO), permettendo agli utenti di autenticarsi una sola volta e accedere a più applicazioni e servizi senza dover ripetere il processo di login. È utilizzato in contesti aziendali per facilitare l’accesso sicuro a servizi cloud, applicazioni web e altre risorse digitali. Un esempio di applicazione del SAML è lo SPID italiano. Il secondo e il terzo passaggio sono momenti molto delicati perché possono esserci attacchi di phishing che simulano in qualche modo il server di autorizzazione e ti reindirizzano a una pagina che è abbastanza simile al login IDP ma non è il vero login IDP. Il token generato dall'IDP è condiviso tra il client e il server e finché non conoscono lo stesso token, le loro credenziali vengono autenticate e controllate. Ogni richiesta dal client al server ha il token all'interno.
*   **OAuth:** è un protocollo di autorizzazione aperto che consente a un’applicazione di accedere a risorse di un utente su un’altra applicazione senza dover condividere le credenziali dell’utente. Permette agli utenti di concedere a terze parti un accesso limitato ai loro dati su altre piattaforme senza condividere la password. Utilizzato per consentire applicazioni come social media, servizi di posta elettronica e altre piattaforme online di interagire tra loro, ad esempio, permettendo a un’app di terze parti di postare su un account di social media dell’utente. Il messaggio di concessione dell'autorizzazione viene inviato dal server al client e dal client all'IDP. Il token di concessione dell'accesso viene inviato dall'IDP al client e dal client al server. L'IDP è sia un provider di autenticazione che un provider di autorizzazione: il client ottiene il token di concessione dell'accesso solo se effettua correttamente il login con l'IDP.

I punti positivi sono:
*   Se un utente malintenzionato interrompe il servizio, non riesce a trovare nulla di valore all'interno del server sugli utenti;
*   L'autorizzazione e l'autenticazione sono separate dal resto.

I punti negativi sono:
*   IDP è il centro dell'infrastruttura: qualsiasi servizio tu voglia utilizzare, l'IDP viene chiamato dai server o da te; quindi, l'IDP deve gestire migliaia e migliaia di richieste di autenticazione al secondo e le prestazioni possono essere un problema, anche un problema di sicurezza. Se un utente malintenzionato rompe l'IDP, ha accesso al mondo degli account utente, indipendentemente dal servizio, ha tutte le informazioni possibili.
*   Se l'IDP è inattivo, anche gli altri servizi sono inattivi, indipendentemente dal fatto che funzionino ancora all'interno. Tutti i token di autenticazione diventano non validi e non possono essere rinnovati.
