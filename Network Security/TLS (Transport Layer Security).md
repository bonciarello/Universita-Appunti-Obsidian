---
aliases: [TLS, Transport Layer Security]
tags: [network-security]
---
**Transport Layer Security (TLS)** è uno dei servizi di [[Sicurezza|sicurezza]] più utilizzati. TLS è uno standard Internet che si è evoluto da un protocollo commerciale noto come **Secure Sockets Layer (SSL)**. TLS è un servizio generico implementato come un insieme di protocolli che si basano su TCP (ed eventualmente su UDP, in tal caso, è anche chiamato **DTLS**).

Per generalità completa, TLS potrebbe essere fornito come parte della suite di protocolli sottostante e quindi essere trasparente per le applicazioni. In alternativa, TLS può essere incorporato in pacchetti specifici.

Il protocollo **TLS Record** fornisce servizi di [[Sicurezza|sicurezza]] di base a vari protocolli di livello superiore. In particolare, l'**Hypertext Transfer Protocol (HTTP)** può operare su TLS. Tre protocolli di livello superiore sono definiti come parte di TLS:
*   Il protocollo **Handshake**;
*   Il protocollo **Change Cipher Spec**;
*   Il protocollo **Alert**.
Un quarto protocollo, il protocollo **Heartbeat**, è definito in un RFC separato.

## 1. Concetti Chiave

Due importanti concetti TLS sono:

*   **Sessione TLS:** è un'associazione tra un client e un server. Le sessioni definiscono un insieme di parametri di [[Sicurezza|sicurezza]] crittografici, che possono essere condivisi tra più connessioni. Le sessioni vengono utilizzate per evitare la costosa negoziazione di nuovi parametri di [[Sicurezza|sicurezza]] per ogni connessione;
*   **Connessione TLS:** è un trasporto che fornisce un tipo adeguato di servizio. Le connessioni sono transitorie ed ogni connessione è associata a una sessione.

Ci sono un certo numero di stati associati a ciascuna sessione: una volta stabilita una sessione, c'è uno stato operativo corrente. Inoltre, durante il protocollo Handshake, vengono creati stati in sospeso. Dopo la conclusione positiva del protocollo Handshake, gli stati pendenti diventano gli stati attuali.

### Parametri di Stato della Sessione
Uno stato della sessione è definito dai seguenti parametri:
*   **Identificatore di sessione:** una sequenza di byte arbitraria scelta dal server per identificare uno stato di sessione attivo o ripristinabile;
*   **Certificato peer;**
*   **Metodo di compressione:** l'algoritmo utilizzato per comprimere i dati prima della crittografia;
*   **Specifica di cifratura:** specifica l'algoritmo di crittografia dei dati (come AES, ecc.) e un algoritmo hash (come MD5 o SHA-1) utilizzato per il calcolo MAC;
*   **Master Secret:** codice segreto di 48 byte condiviso tra il client e il server;
*   **Ripristinabile:** flag che indica se la sessione può essere utilizzata per avviare nuove connessioni.

### Parametri di Stato della Connessione
Uno stato di connessione è definito dai seguenti parametri:
*   **Random server e client:** sequenze di byte scelte dal server e dal client per ciascuna connessione;
*   **Server write MAC secret:** la chiave segreta utilizzata nelle operazioni MAC sui dati inviati dal server;
*   **Client write MAC secret:** la chiave segreta utilizzata nelle operazioni MAC sui dati inviati dal client;
*   **Server write key:** la chiave di [[Crittografia Simmetrica|crittografia simmetrica]] per i dati crittografati dal server e decrittografati dal client;
*   **Client write key:** la chiave di [[Crittografia Simmetrica|crittografia simmetrica]] per i dati crittografati dal client e decrittografati dal server;
*   **Numeri di sequenza:** ciascuna parte mantiene numeri di sequenza separati per i messaggi trasmessi e ricevuti per ciascuna connessione. Quando una parte invia o riceve un "messaggio di modifica della specifica di cifratura", il numero di sequenza appropriato viene impostato su zero. I numeri di sequenza non possono superare $2^{64}-1$.

## 2. Protocollo TLS Record
Il protocollo TLS Record fornisce due servizi per le connessioni TLS:
1.  **Riservatezza:** definisce una chiave segreta condivisa utilizzata per la crittografia convenzionale dei payload TLS;
2.  **Integrità del messaggio:** definisce anche una chiave segreta condivisa utilizzata per formare un Message Authentication Code (MAC).

Questo protocollo prende un messaggio applicativo da trasmettere, frammenta i dati in blocchi gestibili, facoltativamente comprime i dati, applica un MAC, crittografa, aggiunge un'intestazione e trasmette l'unità risultante in un segmento TCP.

## 3. Altri Protocolli TLS

### Protocollo Heartbeat
Il protocollo Heartbeat viene utilizzato per verificare se la comunicazione è ancora sicura. Può essere paragonato ad un'operazione di ping e il server risponde con lo stesso corpo del messaggio di richiesta.

### Protocollo Change Cipher Spec
Il protocollo Change Cipher Spec è costituito da un singolo messaggio costituito da un singolo byte con il valore 1. L'unico scopo di questo messaggio è far sì che lo stato in sospeso venga copiato nello stato corrente, che aggiorna la suite di crittografia da utilizzare su questa connessione.

### Protocollo Alert
Il protocollo Alert viene utilizzato per trasmettere avvisi relativi a TLS all'entità peer. Come con altre applicazioni che utilizzano TLS, i messaggi di avviso vengono compressi e crittografati, come specificato dallo stato corrente. Ogni messaggio in questo protocollo è composto da due byte: il primo byte assume il valore **warning (1)** o **fatal (2)** per trasmettere la gravità del messaggio, il secondo byte contiene un codice che indica l'avviso specifico.

## 4. Protocollo Handshake
Il protocollo Handshake è la parte più complessa di TLS. Questo protocollo consente al server e al client di autenticarsi a vicenda e di negoziare un algoritmo di crittografia e chiavi crittografiche da utilizzare per proteggere i dati inviati in un record TLS. Il protocollo Handshake viene utilizzato prima della trasmissione di qualsiasi dato dell'applicazione.

Il protocollo Handshake consiste in una serie di messaggi scambiati tra client e server. Ogni messaggio ha tre campi:
*   **Type:** indica uno dei 10 messaggi;
*   **Content:** i parametri associati a questo messaggio;
*   **Length:** la lunghezza del messaggio in byte.

I tipi di messaggi sono i seguenti:

Nel protocollo avviene uno scambio iniziale necessario per stabilire una connessione sicura tra client e server:

1.  **Hello:** il client invia un messaggio di `client_hello` al server, che contiene la versione del protocollo TLS supportata dal client, i parametri di crittografia preferiti e altre informazioni necessarie per l’inizio della comunicazione sicura;
2.  **Hello Acknowledgment:** il server risponde con un messaggio di `server_hello` al client. Questo messaggio contiene la versione del protocollo TLS scelta dal server, i parametri di crittografia negoziati e altre informazioni pertinenti;
3.  **Scambio delle chiavi:** durante questa fase, il server invia al client il suo certificato digitale, che contiene la sua chiave pubblica. Il client verifica l’autenticità del certificato utilizzando una catena di certificati e verifica l’identità del server;
4.  **Generazione delle chiavi:** dopo aver verificato l’autenticità del certificato del server, il client genera una chiave di sessione segreta e la crittografa con la chiave pubblica del server. La chiave di sessione crittografata viene inviata al server;
5.  **Finire la negoziazione:** sia il client che il server inviano messaggi di `finished` per confermare la corretta negoziazione dei parametri di crittografia e per stabilire la [[Sicurezza|sicurezza]] della connessione.

Una volta completato il protocollo Handshake, la connessione sicura è stabilita tra client e server. I dati trasmessi successivamente sono crittografati utilizzando la chiave di sessione condivisa, garantendo la riservatezza, l’integrità e l’autenticità dei dati scambiati. Il protocollo Handshake è critico per la [[Sicurezza|sicurezza]] di TLS, poiché consente l’autenticazione delle parti coinvolte e l’accordo sui parametri di crittografia. È importante che i client e i server implementino correttamente il protocollo Handshake per garantire la [[Sicurezza|sicurezza]] delle comunicazioni.

## 5. Attacchi e Vulnerabilità
Alcuni attacchi noti a TLS:

*   **SSLStrip:** quando un client richiede una pagina web, è possibile che la pagina restituita al client non sia la relativa pagina HTTPS ma una vecchia versione HTTP. In questi casi, un utente malintenzionato può essere un man-in-the-middle e intercettare la richiesta per il server Web e restituire al client una pagina HTTP falsa simile a quella reale ma una versione dannosa invece della versione HTTPS richiesta. È possibile mitigare questo attacco utilizzando uno speciale tipo di cookie **HSTS** che viene impostato ogni prima volta che un client avvia una connessione con un server Web: accettando il cookie, il client accetta che da quel momento ogni volta che richiede quella pagina Web, la risposta è sempre una pagina HTTPS e non HTTP; se riceve una pagina HTTP c'è un problema e deve interrompere la connessione;
*   **Heartbleed:** ha reso possibile l'accesso non autorizzato a dati sensibili attraverso una falla nel software di crittografia, mettendo a rischio la [[Sicurezza|sicurezza]] di numerose applicazioni web e server. La scoperta di Heartbleed ha portato a un'ampia riconfigurazione e patching dei server colpiti per risolvere la vulnerabilità e migliorare la [[Sicurezza|sicurezza]] delle comunicazioni online;
*   **BEAST (Browser Exploit Against SSL/TLS):** mirava a decifrare il traffico crittografato tra un utente e un server, consentendo agli aggressori di intercettare e leggere dati sensibili, come informazioni di accesso e sessioni di autenticazione. L'attacco BEAST è stato particolarmente rilevante poiché ha colpito le versioni più vecchie dei protocolli SSL/TLS, mettendo a rischio la [[Sicurezza|sicurezza]] delle comunicazioni online. Le contromisure sono state adottate per mitigare questa vulnerabilità e per migliorare la [[Sicurezza|sicurezza]] delle connessioni web;
*   Due diversi attacchi si basano sul monitoraggio della lunghezza del messaggio utilizzando il metodo di compressione **DEFLATE**: in questi casi, l'attaccante inizia a monitorare il campo della lunghezza e, in base al suo valore, tenta di inserire una stringa nel messaggio o di forzare il client a inviare più messaggi.
