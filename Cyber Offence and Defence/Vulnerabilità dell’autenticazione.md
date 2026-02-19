---
aliases: [vulnerabilità dell’autenticazione]
tags: [cyber-offence-and-defence]
---
L'autenticazione è il processo di verifica dell'identità di un utente o di un client. I siti web sono potenzialmente esposti a chiunque sia connesso a Internet. Ciò rende i meccanismi di autenticazione robusti parte integrante di un'efficace [[Sicurezza|sicurezza]] Web.

Esistono tre tipi principali di autenticazione:
*   **qualcosa che sai**, come una password o la risposta a una domanda di [[Sicurezza|sicurezza]] (fattori di conoscenza);
*   **qualcosa che hai**, ovvero un oggetto fisico come un telefono cellulare o un token di [[Sicurezza|sicurezza]] (fattori di possesso);
*   **qualcosa che sei o fai**, ad esempio, i tuoi dati biometrici o modelli di comportamento (fattori di inerenza).

## Differenza tra autenticazione e autorizzazione
*   **L'autenticazione** è il processo di verifica che un utente sia chi dichiara di essere.
*   **L'autorizzazione** implica la verifica se a un utente è consentito fare qualcosa.

## Autenticazione basata su password
Gli utenti forniscono credenziali (nome utente e password) che il sistema confronta con quelle memorizzate.

### Strategie di attacco e contromisure:
*   **Attacchi al dizionario offline:** Uso di elenchi di password comuni confrontati con hash rubati. *Contromisura:* impedire l'accesso ai file delle password.
*   **Attacchi specifici all'account:** Phishing o ingegneria sociale mirata. *Contromisura:* blocco dell'account dopo 5 tentativi falliti.
*   **Attacchi alle password più diffuse:** Tentativi di usare password deboli (es. `"123456"`) su molti account. *Contromisura:* inibire l'uso di password comuni.
*   **Workstation hijacking:** Accesso fisico a una sessione già aperta. *Contromisura:* invalidare le sessioni dopo un periodo di inattività.

## Password con hash e salt
Una password con hash è trasformata in una stringa univoca non reversibile.
*   **Salting:** Aggiunta di un valore casuale (il "salt") prima dell'hashing per rendere uniche le password simili e prevenire attacchi con "rainbow table".
*   **Bcrypt:** Un moderno schema di hashing che include una variabile di costo per rallentare il cracking brute-force.

## Token Protocol
Insieme di procedure che utilizzano stringhe uniche (token) invece delle sole password.
1.  **Token di accesso:** Usato per accedere alle risorse senza reinviare le credenziali (es. OAuth 2.0).
2.  **Scadenza:** Token a breve termine (accesso) e a lungo termine (refresh token).
3.  **Autenticazione stateless:** Come i JWT, contengono informazioni crittografate sull'utente senza che il server debba mantenere lo stato della sessione.
4.  **Revoca:** Necessità di invalidare token in caso di compromissione.

## Vulnerabilità comuni
*   **Brute-force:** Tentativi automatizzati per indovinare credenziali. Può essere facilitato da nomi utente prevedibili (es. email aziendali o "admin").
*   **Enumerazione dello username:** Identificare se un nome utente è valido osservando differenze nei:
    *   Codici di stato HTTP.
    *   Messaggi di errore.
    *   Tempi di risposta (es. il server impiega più tempo se l'utente esiste perché deve controllare la password).
*   **Protezione brute-force difettosa:** Blocchi IP che possono essere aggirati ruotando l'IP o blocchi account che possono essere sfruttati per l'enumerazione.
*   **Credential stuffing:** Uso di coppie `username:password` rubate da altri siti.

## Autenticazione multifattoriale (2FA)
Aggiunge un livello di [[Sicurezza|sicurezza]] richiedendo un secondo fattore.
*   **Bypass semplici:** A volte il sito carica la pagina "protetta" subito dopo il primo passaggio, senza verificare il completamento del secondo.
*   **Logica difettosa:** Ad esempio, poter modificare il cookie dell'account durante l'invio del codice 2FA per accedere all'account di un'altra vittima.
*   **Attacchi SMS:** Soggetti ad abusi tramite intercettazione o SIM swapping.

## Reimpostazione password
Processo intrinsecamente pericoloso.
*   **Tramite URL:** Deve usare token ad alta [[Entropia|entropia]]. Se l'URL usa solo parametri indovinabili (es. `?user=victim`), un aggressore può cambiare la password di chiunque.
*   **Password brute-force via password change:** Sfruttare moduli di modifica password che espongono il nome utente in campi nascosti.