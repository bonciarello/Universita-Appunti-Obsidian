---
aliases: [kerberos]
tags: [metodi-e-strumenti-per-la-sicurezza]
---
Nato a metà anni '80, **Kerberos** è un protocollo progettato per l'autenticazione distribuita in rete, permettendo a utenti, applicazioni e computer di autenticarsi a vicenda senza inviare password in chiaro.

## 1. Modello di Fiducia
*   **Kerberos Server (Ente Fidato):** Unico punto di fiducia centrale per tutte le macchine della rete.
*   **Chiavi Simmetriche:** Ogni utente/server condivide una chiave segreta solo con il Kerberos Server.
*   **Mutua Autenticazione:** Client e server si riconoscono reciprocamente grazie alla mediazione del server centrale.

## 2. Componenti e Protocollo
*   **KDC (Key Distribution Center):** Gestisce le chiavi.
*   **TGS (Ticket Granting Service):** Rilascia i ticket per i servizi specifici.
*   **Tickets:** Funzionano come **capabilities** riutilizzabili per una durata limitata.

### Fasi del Protocollo
1.  **Richiesta TGS:** Il client $C$ contatta il KDC dichiarando la propria identità e richiedendo di parlare con il TGS.
2.  **Risposta TGS:** Il KDC invia a $C$ un ticket per il TGS.
3.  **Richiesta Servizio:** $C$ presenta il ticket al TGS.
4.  **Ticket Servizio:** Il TGS restituisce a $C$ un ticket per il server di destinazione $M$ (es. server di stampa). Il messaggio è cifrato con la chiave tra Client e TGS e contiene:
    *   La chiave di sessione tra Client e $M$.
    *   Il ticket per $M$ (cifrato con la chiave tra $M$ e Kerberos).
5.  **Accesso:** $C$ presenta il ticket a $M$. Avviene la mutua autenticazione e i due iniziano a comunicare con la chiave condivisa.

## 3. Evoluzione e Difetti

### Differenze tra Versioni
*   **V4:** Utilizzava DES. Gli autenticatori non erano legati al messaggio, permettendo usi non previsti.
*   **V5 (Standard attuale, usato in AD):** Supporta AES. Include informazioni sul messaggio corrente nell'autenticatore per prevenire usi impropri. Richiede un **timestamp** cifrato nella richiesta iniziale per prevenire attacchi brute force e replay.

### Vulnerabilità note
*   **Reflection Attack:** Client e server usano la stessa chiave per andata e ritorno. Se un attaccante forza il server a cifrare un contenuto scelto da lui (es. in un server mail), può scoprire informazioni.
*   **Brute Force:** Se le richieste non sono autenticate, chiunque può chiedere ticket cifrati con chiavi altrui per tentare il cracking offline.
