HTTPS nasce per risolvere le problematiche di riservatezza, integrità e autenticazione nelle comunicazioni web.

## 1. Problematiche principali
Ad oggi, la sicurezza di rete deve rispondere a sei sfide:
1.  Evitare lettura o modifica dei file in transito.
2.  Garantire l'identità del server.
3.  Proteggere dalla manipolazione dei record DNS.
4.  Impedire l'uso malevolo di JavaScript lato client.
5.  Proteggere le credenziali dall'invio a server illegittimi.
6.  Assicurarsi che l'inserimento delle credenziali avvenga in modo diretto e sicuro.

## 2. Transport Layer Security (TLS)
Il protocollo TLS (evoluzione di SSL) fornisce:
*   **Scambio chiavi:** RSA, Diffie-Hellman, PSK.
*   **Autenticazione:** RSA, DSA, ECDSA.
*   **Crittografia simmetrica:** AES, 3DES, RC4.
*   **Integrità:** HMAC-MD5, HMAC-SHA.

### Lo Scambio delle Chiavi
1.  **Client Hello:** Messaggio di saluto dal client.
2.  **Server Hello:** Risposta con il cifrario scelto e la chiave pubblica $PKS$.
3.  **Key Generation:** Entrambe le parti derivano le chiavi di scrittura (per cifrare) e i segreti MAC (per l'integrità).
4.  **Change Cipher Spec:** Messaggio che segnala il passaggio alla crittografia simmetrica per tutti i dati successivi.

## 3. Certification Authority (CA) e Certificati
Per essere certi dell'identità di un server, ci si affida a una terza parte fidata: la **CA**.
*   **Certificato:** Una tupla `{S, PK}` (Soggetto, Chiave Pubblica) firmata digitalmente dalla CA.
*   **Trust:** I browser contengono un elenco predefinito di CA fidate.

### Controllo della validità
I certificati hanno una scadenza. Per verificare se sono stati revocati prima del tempo:
*   **CRL (Certificate Revocation List):** Liste scaricabili (spesso pesanti).
*   **OCSP (Online Certificate Status Protocol):** Query in tempo reale per verificare lo stato di un certificato.

## 4. Problematiche e Limiti
Nonostante la robustezza crittografica, HTTPS presenta criticità:
*   **Compromissione delle CA:** Se un server di una CA viene violato, l'attaccante può emettere certificati falsi per qualsiasi sito.
*   **Configurazione errata:** Certificati scaduti o non corrispondenti. Spesso i browser permettono all'utente di ignorare l'errore (succede nel 60% dei casi).
*   **Contenuti Misti (Mixed Content):** Pagine HTTPS che caricano elementi (immagini, script) via HTTP. Un avversario può alterare questi elementi per compromettere la sicurezza della pagina.
*   **Interfaccia Utente:** Il simbolo del "lucchetto" indica solo che la connessione è cifrata e il certificato è valido, non necessariamente che il sito sia onesto.
