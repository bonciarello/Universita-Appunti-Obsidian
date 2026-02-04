Gli schemi di autenticazione vengono valutati secondo tre metriche principali.

## 1. Metriche di Valutazione

### Usabilità (Usability)
*   **Easy-to-learn:** facilità di apprendimento e memorizzazione (Password: SI).
*   **Infrequent-errors:** affidabilità per l'utente legittimo (Password: Quasi-SI, causa errori di digitazione).
*   **Scalabilità per l'utente:** facilità d'uso su centinaia di account (Password: NO).
*   **Easy-recovery:** comodità nel recupero credenziali perse (Password: SI).
*   **Nothing-to-carry:** nessun oggetto fisico richiesto (Password: SI).

### Implementabilità (Deployability)
*   **Server-compatible:** compatibilità con i sistemi lato verificatore.
*   **Browser-compatible:** supporto nei browser standard senza plugin.

### Sicurezza (Security)
*   **Resilient-to-throttled-guessing:** resistenza a tentativi limitati (Password: NO, causa bassa entropia).
*   **Resilient-to-unthrottled-guessing:** resistenza a tentativi illimitati (Password: NO).
*   **Resilient-to-internal-observation:** resistenza allo sniffing locale (Password: NO).
*   **Resilient-to-phishing:** resistenza a siti falsi (Password: NO).
*   **No-trusted-third-party:** indipendenza da enti esterni (Password: SI).
*   **Resilient-to-other-verifier:** compromissione di un server non aiuta l'attacco ad altri (Password: NO).

## 2. Analisi Biometriche
Sfruttano caratteristiche fisiche o comportamentali.
*   **Problema:** Possibilità di falsi positivi (accettazione illegittima) e falsi negativi (rifiuto legittimo).
*   **Entropia stimata (2003):** Impronte (13.3 bit), Iride (19 bit), Voce (11.7 bit).

## 3. CAP Readers (Chip Authentication Program)
Sistemi composti da una smart card e un lettore fisico. L'utente inserisce la carta e un PIN (immune da keylogger software) per generare un codice di risposta.
