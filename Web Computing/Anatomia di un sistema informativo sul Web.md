---
aliases: [Anatomia sistema informativo Web, ASIW]
tags: [web-computing]
---
Un **Sistema Informativo sul Web** (SIW) è un'applicazione che sfrutta l'infrastruttura del Web per fornire servizi e informazioni agli utenti.
## Web vs Internet
È importante distinguere i due termini:
*   **Internet:** È l'infrastruttura di rete globale (hardware, router, cavi, protocolli TCP/IP).
*   **Web (WWW):** È uno dei tanti servizi che girano *sopra* Internet, basato su documenti ipertestuali ([[HTML]]) e il [[Protocollo HTTP|protocollo HTTP]].
### Pilastri del Web (Tim Berners-Lee)
1.  **URL:** Identificatore univoco per ogni risorsa.
2.  **HTTP:** Protocollo di comunicazione tra client e server.
3.  **[[HTML]]:** Linguaggio per descrivere il contenuto.
4.  **Web Server:** Software che risponde alle richieste.
5.  **Browser:** Software per visualizzare i contenuti.
## [[Architettura]] Client-Server
Il funzionamento del Web si basa su un'interazione continua:
1.  Il **Client** (Browser) invia una richiesta HTTP tramite un URL.
2.  Il **Server** riceve la richiesta, la elabora e invia una risposta (es. un file [[HTML]]).
3.  Il **Client** interpreta la risposta e la mostra all'utente.
## [[Architettura]] a 3 Livelli (3-Tier)
I sistemi moderni non sono monoliti, ma divisi in tre strati logici:
1.  **Presentation Tier (Front-end):** 
    *   Si occupa dell'[[Interfaccia utente|interfaccia utente]]. 
    *   Tecnologie: [[HTML]], CSS, [[JavaScript]].
    *   Software: Web Server (es. Apache, Nginx).
2.  **Logic Tier (Back-end):**
    *   Contiene la logica applicativa (il "cervello"). Genera contenuti dinamici.
    *   Tecnologie: Java (Servlet), Python, Node.js.
    *   Software: Application Server (es. Tomcat).
3.  **Persistence Tier (Data):**
    *   Gestisce il salvataggio permanente dei dati.
    *   Tecnologie: SQL, RDBMS (es. PostgreSQL, [[MySQL]]).
## Tecnologie Web Fondamentali
Un sito web moderno nasce dall'unione di tre linguaggi:
*   **[[HTML]]:** Definisce la **struttura** (cosa c'è nella pagina).
*   **CSS:** Definisce lo **stile** (come appare).
*   **[[JavaScript]]:** Definisce il **comportamento** (cosa succede al click).
## Concetti di Design e Dati
### Pattern MVC (Model-View-Controller)
Dividere l'app in tre parti: **Model** (i dati), **View** (l'interfaccia), **Controller** (la logica che li unisce).
### CRUD
Le 4 operazioni fondamentali su ogni [[Database|database]]:
*   **C**reate (INSERT)
*   **R**etrieve (SELECT)
*   **U**pdate (UPDATE)
*   **D**elete (DELETE)
### ORM (Object Relational Mapping)
Librerie che permettono di gestire i dati del [[Database|database]] come se fossero normali oggetti Java, automatizzando la scrittura del codice SQL noioso e ripetitivo.
