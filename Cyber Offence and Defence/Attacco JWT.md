---
aliases: [AJ]
tags: [cyber-offence-and-defence]
---
## Definizione di JWT

I **JSON Web Token (JWT)** sono un formato standardizzato per l'invio di dati JSON crittograficamente firmati tra sistemi. Sono usati per autenticazione, gestione sessioni e controllo accessi.
A differenza dei token di sessione classici, il JWT è **stateless**: tutti i dati necessari sono nel token stesso (lato client).

Un JWT è composto da 3 parti separate da un punto:
1. **Header:** Metadati (algoritmo, tipo).
2. **Payload:** Le "claim" (dati utente come username, ruolo, scadenza).
3. **Signature:** Firma crittografica per verificare l'integrità.

Header e Payload sono oggetti JSON codificati in Base64Url. Possono essere facilmente decodificati e letti da chiunque. La [[Sicurezza|sicurezza]] dipende interamente dalla **Firma**.
## Attacchi JWT
Gli attacchi JWT mirano spesso ad aggirare l'autenticazione o elevare i privilegi inviando token modificati al server.
### 1. Accettazione di firme arbitrarie
Alcune librerie hanno metodi separati per `decode()` (solo decodifica) e `verify()` (verifica firma). Se lo sviluppatore usa solo `decode()`, l'applicazione accetterà qualsiasi token modificato senza controllarne la validità.
### 2. Accettazione di token senza firma (Algoritmo `none`)
L'header contiene il parametro `alg`. Se impostato su `none`, indica un "Unsecured JWT" senza firma.
Alcuni server accettano ciecamente questo algoritmo. Un aggressore può modificare il payload, impostare `"alg": "none"` nell'header e rimuovere la firma (lasciando il punto finale), ingannando il server.
### 3. Brute-force delle chiavi segrete
Alcuni algoritmi come **HS256** (simmetrico) usano una stringa segreta ("secret") per firmare.
- se il segreto è debole o predefinito, può essere indovinato tramite brute-force (es. usando **hashcat**).
- una volta scoperto il segreto, l'aggressore può generare e firmare validamente qualsiasi token.
Comando hashcat esempio:
`hashcat -a 0 -m 16500 <jwt> <wordlist>`
### 4. Iniezioni di parametri di intestazione (Header Injection)
Le specifiche JWS (JSON Web Signature) permettono parametri nell'header per gestire le chiavi.
- **jwk (JSON Web Key):** permette di incorporare la chiave pubblica direttamente nel token. Se il server si fida di qualsiasi chiave passata qui, l'aggressore può firmare il token con la propria chiave privata e inserire la pubblica nell'header.
- **jku (JSON Web Key Set URL):** fornisce un URL da cui scaricare le chiavi. L'aggressore può puntare a un proprio server che ospita le chiavi malevole.
- **kid (Key ID):** identifica quale chiave usare.
    - **Directory Traversal:** Se il `kid` viene usato come percorso file per cercare la chiave, un aggressore potrebbe usare `../../path/to/file` (o `/dev/null`) per forzare l'uso di un file noto/vuoto come chiave segreta (per algoritmi simmetrici).
    - **SQL Injection:** Se il `kid` è usato in una query [[Database|database]], potrebbe essere vettore di SQLi.
## Come prevenire gli attacchi JWT
- Utilizzare librerie aggiornate e mature.
- Eseguire sempre la verifica della firma (`verify`).
- Imporre una whitelist rigida di algoritmi consentiti (evitare `none`).
- Usare chiavi segrete lunghe, casuali e complesse.
- Non fidarsi ciecamente dei parametri header come `jwk`, `jku` o `kid`. Convalidare o whitelistare gli host e le chiavi.
