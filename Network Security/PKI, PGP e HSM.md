---
aliases: [PH, "PKI, PGP HSM"]
tags: [network-security]
---
## 1. PKI (Public-Key Infrastructure)
**Public-key infrastructure (PKI)** è un insieme di politiche, processi, piattaforme server, software e workstation utilizzati allo scopo di amministrare certificati e coppie di chiavi pubblica-privata, inclusa la possibilità di emettere, mantenere e revocare certificati a chiave pubblica. L'obiettivo principale per lo sviluppo di una PKI è consentire l'acquisizione sicura, comoda ed efficiente delle chiavi pubbliche.

Le architetture PKI devono soddisfare i seguenti requisiti:
*   Qualsiasi partecipante può leggere un certificato per determinare il nome e la chiave pubblica del proprietario del certificato;
*   Qualsiasi partecipante può verificare che il certificato provenga dall'autorità di certificazione e non sia contraffatto;
*   Solo l'autorità di certificazione può creare e aggiornare i certificati;
*   Qualsiasi partecipante può verificare la valuta del certificato.

I componenti essenziali di una PKI sono:
*   **End Entity:** un utente finale identificabile nel nome del soggetto di un certificato a chiave pubblica;
*   **Certificate Authority (CA):** un'autorità fidata da uno o più utenti per creare e assegnare certificati a chiave pubblica;
*   **Registration Authority (RA):** un componente facoltativo che può essere utilizzato per scaricare molte delle funzioni amministrative che normalmente assume una CA. La RA è normalmente associata al processo di registrazione dell'entità finale ed include la verifica dell'identità dell'entità finale che tenta di registrarsi con la PKI;
*   **Repository:** denota qualsiasi metodo per archiviare e recuperare informazioni relative a PKI, come certificati a chiave pubblica e CRL;
*   **Relying Party:** qualsiasi utente o agente che si affida ai dati in un certificato per prendere una decisione.

Si consideri una Relying Party, Alice deve utilizzare la chiave pubblica di Bob. Alice deve prima ottenere in modo affidabile e sicuro una copia della chiave pubblica della CA. Se Alice desidera inviare dati crittografati a Bob, verifica con il repository per determinare se il certificato di Bob è stato revocato e, in caso contrario, ottiene una copia del certificato di Bob. Alice può quindi utilizzare la chiave pubblica di Bob per crittografare i dati inviati a Bob.

Nella vita di tutti i giorni, un'azienda potrebbe dover fare affidamento su più CA e più repository. Le CA possono essere organizzate in modo gerarchico, con una CA radice ampiamente attendibile che firma il certificato a chiave pubblica delle CA subordinate.

> **Commentato [CP5]:** La public-key infrastructure (PKI) è un sistema che gestisce chiavi crittografiche e certificati digitali. Serve per creare, distribuire, gestire, verificare e revocare certificati digitali, che sono usati per autenticare l’identità di persone e dispositivi e per cifrare le comunicazioni. PKI utilizza coppie di chiavi pubblica e privata per garantire la [[Sicurezza|sicurezza]] delle informazioni.

## 2. PGP (Pretty Good Privacy)
**Pretty Good Privacy (PGP)** è una famiglia di software di crittografia per autenticazione e privacy, da cui è derivato lo standard **OpenPGP**. Con PGP è possibile cifrare ogni tipo di dato o file ed è consuetudine vederlo impiegato per scambiarsi posta elettronica, dato che le e-mail non hanno un sistema di cifratura nativo. PGP e l'S/MIME sono infatti due standard RFC per lo scambio di posta elettronica.

> **Commentato [CP6]:** Il Pretty Good Privacy (PGP) è un programma di crittografia usato per proteggere la comunicazione via email. Utilizza una combinazione di crittografia a chiave simmetrica e asimmetrica per garantire la riservatezza e l’autenticità dei messaggi. PGP permette agli utenti di crittografare e firmare digitalmente le loro email, rendendo difficile per chiunque tranne il destinatario previsto leggere il contenuto del messaggio.

PGP usa la crittografia a chiave asimmetrica, nella quale il destinatario del messaggio ha generato precedentemente una coppia di chiavi collegate fra loro: una chiave pubblica ed una privata. La chiave pubblica del destinatario serve al mittente per cifrare una chiave di sessione per un algoritmo di [[Crittografia Simmetrica|crittografia simmetrica]]; questa chiave viene quindi usata per cifrare il testo in chiaro del messaggio. Il destinatario di un messaggio protetto da PGP decifra prima la chiave di sessione inclusa nel messaggio usando la sua chiave privata. Decifra poi il testo usando la chiave di sessione con l'algoritmo simmetrico.

Una strategia simile può essere usata per capire se un messaggio è stato alterato: il mittente usa PGP per 'firmare' il messaggio con l'algoritmo di firma **RSA** o **Digital Signature Algorithm (DSA)**. Per fare questo, PGP calcola il digest dal testo in chiaro e crea, da questo digest, la firma digitale usando la chiave privata del mittente. Il destinatario del messaggio calcola il digest dal testo in chiaro decifrato, e poi usa la chiave pubblica del mittente ed il valore del digest firmato con l'algoritmo di firma. Se la firma corrisponde al digest del testo in chiaro ricevuto, si presuppone (con un grande margine di [[Sicurezza|sicurezza]]) che il messaggio ricevuto non sia stato alterato né accidentalmente né volontariamente da quando è stato firmato.

### Rete di Fiducia (Web of Trust)
PGP include delle precauzioni per la distribuzione delle chiavi pubbliche in “certificati d'identità” i quali sono costruiti crittograficamente, in maniera tale da rendere qualsiasi manomissione o disturbo accidentale facilmente rilevabile. Rendere un certificato effettivamente impossibile da modificare senza lasciare tracce non è sufficiente. Questo può prevenire la manomissione solo dopo la creazione del certificato, non prima. Gli utenti devono anche verificare in qualche maniera che la chiave pubblica in un certificato appartenga effettivamente alla persona o ente che ne reclama la paternità. Per questo, PGP racchiude un sistema di “voto” dei certificati; è chiamata **rete di fiducia**. Quando un nuovo utente installa e inizia ad usare PGP, all'utente viene chiesto se vuole essere inserito all'interno di tale rete di fiducia.

## 3. HSM (Hardware Security Module)
**Hardware Sign Module (HSM)** sono hardware utilizzati per firmare documenti e file. HSM integra chip crittografici dedicati per la generazione e la protezione delle chiavi.
