---
aliases: [GC, Gestione Chiavi]
tags: [network-security]
---
## 1. Introduzione
L'uso sicuro degli algoritmi delle chiavi crittografiche dipende dalla protezione delle chiavi crittografiche. La **gestione delle chiavi crittografiche** è il processo di amministrazione o gestione delle chiavi crittografiche per un sistema crittografico. Implica la generazione, la creazione, la protezione, l'archiviazione, lo scambio, la sostituzione e l'uso di chiavi e consente restrizioni selettive per determinate chiavi. Oltre alla restrizione dell'accesso, la gestione delle chiavi comporta anche il monitoraggio e la registrazione dell'accesso, dell'uso e del contesto di ciascuna chiave.

Per la [[Crittografia Simmetrica|crittografia simmetrica]], sono generalmente desiderabili modifiche frequenti della chiave per limitare la quantità di dati compromessi se un utente malintenzionato apprende la chiave. Pertanto, la forza di qualsiasi sistema crittografico risiede nella tecnica di distribuzione delle chiavi, termine che si riferisce ai mezzi per consegnare una chiave a due parti che desiderano scambiare dati, senza consentire ad altri di vedere la chiave.

Per due parti A e B, la distribuzione delle chiavi può essere ottenuta in diversi modi:
1.  A può selezionare una chiave e consegnarla fisicamente a B;
2.  Una terza parte può selezionare la chiave e consegnarla fisicamente ad A e B;
3.  Se A e B hanno utilizzato in precedenza o di recente una chiave, una parte può trasmettere all'altra la nuova chiave, crittografata utilizzando la vecchia chiave;
4.  Se A e B dispongono ciascuno di una connessione crittografata a una terza parte C, C può fornire una chiave sui collegamenti crittografati ad A e B.

## 2. KTC e KDC
Esistono due diverse opzioni per la distribuzione delle chiavi. Le opzioni in questione sono:

*   **Key Translation Center (KTC):** trasferisce chiavi simmetriche per comunicazioni future tra due entità, almeno una delle quali ha la capacità di generare o acquisire chiavi simmetriche da sola;
*   **Key Distribuition Center (KDC):** genera e distribuisce chiavi di sessione. A tale scopo, le entità A e B richiedono una chiave di sessione temporanea che durerà per la durata di una connessione logica: la chiave master di lunga durata con una terza parte coinvolta nella fornitura della chiave di sessione.

A causa dell'inefficienza dei sistemi crittografici a chiave pubblica, vengono utilizzati per la crittografia diretta di blocchi relativamente piccoli. Uno degli usi più importanti di un sistema crittografico a chiave pubblica è crittografare le chiavi segrete per la distribuzione.

## 3. Distribuzione delle Chiavi Pubbliche
Diverse tecniche sono state proposte per la distribuzione delle chiavi pubbliche:

### Public Announcement
Se esiste un algoritmo a chiave pubblica ampiamente accettato, come RSA, qualsiasi partecipante può inviare la propria chiave pubblica a qualsiasi altro partecipante o trasmettere la chiave ad una community. Chiunque può falsificare un simile annuncio pubblico. Cioè, un utente potrebbe fingere di essere l'utente A e inviare una chiave pubblica a un altro partecipante o trasmettere tale chiave pubblica fino a quando l'utente A non scopre il falso e avvisa gli altri partecipanti.

Questa tecnica viene anche chiamata **public announcement with pinning**: una volta ottenuta la chiave pubblica, la inserisci in un file per conservarla in un luogo sicuro in modo da poterla avere per conversazioni successive. Ad esempio, SSH utilizza il pinning durante il processo di connessione ad un server remoto. La prima volta che proviamo a connetterci a un nuovo server, SSH controlla su un file specifico chiamato `known hosts` se ci sono chiavi associate al server. Una volta che abbiamo deciso di stabilire la connessione, memorizza la chiave pubblica negli known hosts in modo che possiamo usarla in seguito. La prossima volta che proviamo a parlare con quel server, l'SSH lo cerca nel file e, se è presente, il client deve eseguire un'autenticazione con challange. Ovviamente, poiché questo meccanismo si basa sugli known hosts, dobbiamo riflettere sul fatto che una manomissione del file significherebbe la corruzione dell'intero processo.

### Public Available Directory
Mantiene una directory dinamica pubblicamente disponibile di chiavi pubbliche. La manutenzione e la distribuzione dell'elenco pubblico dovrebbero essere responsabilità di qualche entità o organizzazione fidata. Se un avversario riesce a ottenere o a calcolare la chiave privata dell'autorità di directory, l'avversario potrebbe distribuire autorevolmente chiavi pubbliche contraffatte e successivamente impersonare qualsiasi partecipante e intercettare i messaggi inviati a qualsiasi partecipante. Un altro modo per raggiungere lo stesso fine è che l'avversario manometta i registri tenuti dall'autorità.

### Public-Key Authority
Avremo un controllo più forte sulla distribuzione delle chiavi pubbliche dalla directory. Come prima, lo scenario presuppone che un'autorità centrale mantenga una directory dinamica delle chiavi pubbliche di tutti i partecipanti. Inoltre, ogni partecipante conosce in modo affidabile una chiave pubblica per l'autorità, con solo l'autorità che conosce la chiave privata corrispondente.

1.  A contatta il public-key authority concatenando alla richiesta il timestamp;
2.  L’ente crittografa, con la propria chiave privata, la chiave pubblica di B e la richiesta;
3.  Una volta pervenuta la risposta, si controlla se il timestamp corrisponde a quello iniziale, in caso contrario l’ente non è affidabile;
4.  Successivamente si contatta B attraverso la sua chiave pubblica andando ad effettuare un meccanismo di autenticazione basato su Nonce;
5.  Quindi i due interlocutori si autentificano reciprocamente e si provvede successivamente a comunicare.

Tuttavia, i primi cinque messaggi devono essere utilizzati solo di rado perché sia A che B possono salvare la chiave pubblica dell'altro per un utilizzo futuro. Periodicamente, un utente dovrebbe richiedere nuove copie delle chiavi pubbliche dei suoi corrispondenti per garantire la validità.

### Public-Key Certificates
L'idea chiave di questa tecnica è che i partecipanti possano utilizzare i certificati per scambiare le chiavi senza contattare un'autorità a chiave pubblica, in modo affidabile come se le chiavi fossero ottenute direttamente da un'autorità a chiave pubblica. In sostanza, un certificato è costituito da una chiave pubblica, un identificatore del proprietario della chiave e l'intero blocco firmato da una terza parte fidata.

In genere, la terza parte è un **Certificate Authority (CA)** considerata attendibile dalla comunità degli utenti; quindi, la chiave pubblica del CA è bloccata da qualche parte in modo che possa essere utilizzata ogni volta che ne abbiamo bisogno. Tutte le chiavi pubbliche dell'autorità attendibile sono archiviate nel **truststore**. Un utente può presentare la propria chiave pubblica all'autorità in modo sicuro e ottenere un certificato: l'utente può quindi pubblicare il certificato. Chiunque necessiti della chiave pubblica di questo utente può ottenere il certificato e verificare che sia valido tramite la firma attendibile allegata.

Il cuore della tecnica è il certificato a chiave pubblica associato a ciascun utente. Si presuppone che questi certificati utente siano creati da un CA attendibile e inseriti nella directory dalla CA o dall'utente. Il server di directory stesso non è responsabile della creazione di chiavi pubbliche o della funzione di certificazione; fornisce semplicemente una posizione facilmente accessibile agli utenti per ottenere i certificati. La CA firma il certificato con la sua chiave privata. Se la chiave pubblica corrispondente è nota a un utente, quell'utente può verificare che un certificato firmato dalla CA sia valido.

## 4. Certificati

### Creazione di un Certificato e Validazione dell’Identità
Quando richiediamo un certificato, la nostra richiesta viene gestita da un'**autorità di registrazione** che è una sub-autorità i cui poteri sono assegnati dal CA. Il momento in cui il certificato viene richiesto e creato è molto importante perché l'emittente deve identificarsi. Se richiediamo un certificato per il nostro sito Web, possiamo dimostrare la nostra identità in due modi diversi:

*   Devi aggiungere una nuova voce nel server DNS per dimostrare che il dominio specifico è correlato a te;
*   Devi aggiungere un nodo specifico nel file [[HTML]] in modo che l'autorità possa verificare che abbiamo aggiunto il nodo.

Ovviamente questo controllo è soggetto a vulnerabilità perché, se qualcuno può controllare il server DNS o fingere di essere il proprietario del sito, tutti i controlli sono inutili.

### Revoca di un Certificato
Ogni certificato include un periodo di validità. In genere, un nuovo certificato viene emesso poco prima della scadenza di quello vecchio. Inoltre, a volte può essere desiderabile revocare un certificato prima che scada, per uno dei seguenti motivi:

1.  Si presume che la chiave privata dell'utente sia compromessa;
2.  L'utente non è più certificato da questa CA: i motivi includono che il nome del soggetto è cambiato, il certificato è stato sostituito o il certificato non è stato emesso in conformità con le politiche della CA;
3.  Si presume che il certificato della CA sia compromesso.

Ogni CA deve mantenere un elenco composto da tutti i certificati revocati ma non scaduti. Quando un utente riceve un certificato in un messaggio, l'utente deve determinare se il certificato è stato revocato. L'utente può controllare la directory ogni volta che riceve un certificato. Per evitare i ritardi associati alle ricerche nelle directory (e ai continui aggiornamenti), è probabile che l'utente mantenga una cache locale dei certificati e degli elenchi dei certificati revocati detti anche **Certificate Revocation List (CRL)**.

Ogni CRL (Certificate Revocation List) pubblicato nella directory è firmato dall'emittente e include il nome dell'emittente, la data di creazione dell'elenco, la data in cui è pianificata l'emissione del successivo CRL e una voce per ciascun certificato revocato. Ogni voce è composta dal numero di serie di un certificato e dalla data di revoca del certificato.

Per verificare se un certificato è stato revocato o meno, possiamo anche interrogare la lista con il protocollo **OCSP (Online Certificate Status Protocol)**. Le informazioni di revoca e lo stato di revoca si trovano direttamente nel certificato, questo significa che, se qualcuno ha il certificato, può manomettere il certificato stesso e nell'apposito campo.

### Convalida del Certificato
Quando controlliamo se un certificato è valido o meno, dobbiamo concentrarci su diversi aspetti:

*   Digest deve corrispondere a una chiave pubblica nota e accettata da una CA nel keystore;
*   La finestra temporale di validità deve coincidere con l'ora corrente;
*   L'ambito del certificato deve corrispondere;
*   Il certificato non deve essere già revocato;
*   Il controllo di qualità deve essere ok: dipende da come è configurato il client, può riguardare l'impossibilità di utilizzare algoritmi deprecati o utilizzare chiavi brevi.
