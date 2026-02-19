---
aliases: [UA]
tags: [metodi-e-strumenti-per-la-sicurezza]
---
L'autenticazione è la prima linea di difesa e si sviluppa in due fasi:
1.  **Identificazione:** l'utente presenta un identificatore unico e non segreto (es. username).
2.  **Verifica:** l'utente dimostra la corrispondenza con l'identità dichiarata tramite informazioni segrete o uniche.

## 1. Mezzi di Autenticazione
L'autenticazione può basarsi su:
*   **Qualcosa che conosci:** Password, PIN, risposte a domande.
*   **Qualcosa che hai:** Token, smart card, chiave fisica (dongle).
*   **Qualcosa che sei (Biometrica statica):** Impronta digitale, retina, volto.
*   **Qualcosa che fai (Biometrica dinamica):** Voce, scrittura, ritmo di battitura.

## 2. Sistemi basati su Password
Le password sono segreti condivisi.

### Tipologie di Attacco
*   **Attacco al singolo account:** Tentativi di guessing (mitigato dal **throttling** e dai timeout).
*   **Attacco alle password popolari:** Provare password comuni su molti account.
*   **Monitoring (Sniffing):** Intercettazione delle password inviate in chiaro (mitigato da HTTPS).

### Forza della Password ed [[Entropia]]
La forza è misurata dall'[[Entropia|entropia]] (caos). Bit necessari per simbolo:
*   Cifre (base 10): 3.32 bit.
*   Lettere minuscole (az): 4.70 bit.
*   Caratteri ASCII stampabili (94): 6.55 bit.

**Il problema umano:** Le password non sono casuali. Nel 2016, le 5000 password più comuni coprivano il 20% degli utenti. L'[[Entropia|entropia]] reale è spesso di soli **20 bit**.

### Strategie di miglioramento
*   **Controllo reattivo:** Segnalare password deboli dopo che sono state scelte.
*   **Controllo proattivo:** Password meter che guidano l'utente durante la scelta (es. Data-Driven Password Meter con 21 euristiche).
*   **Consigli moderni:** Evitare vincoli innaturali (es. "metti un carattere speciale"), preferire la lunghezza, ammettere spazi ed emoji, eliminare la scadenza obbligatoria.

## 3. Memorizzazione delle Password
1.  **In chiaro:** Estremamente pericoloso (vulnerabile a insider e outsider).
2.  **Cifrato:** Richiede la protezione della chiave di cifratura sul server.
3.  **Hashing:** Salvare l'hash della password (es. SHA-256).
4.  **Salting:** Aggiungere una stringa casuale (**Salt**) prima di calcolare l'hash.
    *   *Perché?* Impedisce l'uso di **Rainbow Tables**. L'attaccante deve pre-calcolare gli hash per ogni possibile valore del salt, aumentando lo spazio di ricerca di un fattore $2^s$. Questa è la soluzione standard attuale.

## 4. Autenticazione a Due Fattori (2FA)
1.  **SMS:** Il server invia un codice temporaneo (meno sicuro per via del SIM swapping).
2.  **TOTP (Time-based One-Time Password):** Codice generato da un'app tramite $H(\text{segreto} \ || 	ext{ora})$.
3.  **U2F (Universal 2nd Factor):** Dongle USB con coppia di chiavi. Il server invia una **challenge** che il dongle firma con la propria chiave privata.
