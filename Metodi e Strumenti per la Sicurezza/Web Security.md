---
aliases: [WS]
tags: [metodi-e-strumenti-per-la-sicurezza]
---
La [[Sicurezza|sicurezza]] web si è evoluta parallelamente alla complessità dei browser e delle tecnologie ([[HTML]], [[JavaScript]], DOM, [[Ajax|AJAX]]).

## 1. Fondamenti: URL e HTTP
*   **URL:** Identifica una risorsa tramite protocollo, credenziali, server (IP/Hostname), porta, path e parametri.
*   **HTTP:** Protocollo stateless (senza memoria).
    *   **GET:** Dati nell'URL (impliciti), non dovrebbe avere side-effect.
    *   **POST:** Dati nel corpo della richiesta (espliciti), può avere side-effect.
*   **Status Code:** 1xx (info), 2xx (successo), 3xx (reindirizzamento), 4xx (errore client), 5xx (errore server).
*   **Cookie:** Coppie chiave-valore inviate dal server (`Set-cookie`) e memorizzate dal client per gestire lo stato della sessione.

## 2. SQL Injection (SQLi)
Avviene quando l'input dell'utente viene concatenato direttamente in una query SQL.
*   **Obiettivo:** Modificare la logica della query per bypassare l'autenticazione o estrarre dati.
*   **Union-based SQLi:** Sfrutta l'operatore `UNION` per unire i risultati della query originale con quelli di una tabella scelta dall'attaccante.
    *   *Tecnica:* Usare `ORDER BY` per indovinare il numero di colonne.
*   **Blind SQLi:** Il risultato non è visibile nella pagina. L'attaccante usa "oracoli" (es. la pagina cambia se la condizione è vera) o il tempo (es. `sleep(10)` se la condizione è vera) per indovinare i dati carattere per carattere.
*   **Information Schema:** [[Database]] meta-dati (in [[MySQL]]/PostgreSQL) usato per scoprire i nomi di tabelle e colonne.

### Mitigazione SQLi
*   **Whitelisting:** Accettare solo input conformi a una lista fissa.
*   **Sanitizzazione (Blacklisting/Escaping):** Rimuovere caratteri pericolosi (es. `'`, `;`). *Rischio:* Facilmente bypassabile con encoding diversi.
*   **Prepared Statements:** La soluzione migliore. Si definisce un template di query e si passano i parametri separatamente, impedendo al [[Database|database]] di interpretarli come codice.
*   **ORM (Object-Relational Mapper):** Usare framework (es. CodeIgniter) che astrazgono le query.

## 3. Session Hijacking e CSRF

### Session Hijacking
Furto del cookie di sessione tramite sniffing, attacchi al server o predizione (se l'ID di sessione non è abbastanza casuale).
*   **Esempio Twitter:** Usava un cookie `auth_token` statico che rimaneva valido anche dopo il logout.
*   **Difese:** Session ID lunghi e casuali, HTTPS, scadenza dei cookie.

### Cross-site Request Forgery (CSRF / C-Surf)
L'attaccante induce il browser della vittima (già autenticata su un sito) a inviare una richiesta malevola verso quel sito.
*   **Mitigazione:**
    *   **Referer check:** Verificare la provenienza della richiesta (ma può essere rimosso).
    *   **Anti-CSRF Tokens:** Inserire un valore segreto e casuale in ogni form/link che il client deve restituire per validare la richiesta.

## 4. [[JavaScript]] e Same Origin Policy (SOP)
La **SOP** è il pilastro della [[Sicurezza|sicurezza]] dei browser: uno script può accedere solo a risorse (cookie, DOM) che hanno la stessa **origine** (schema + hostname + porta).

## 5. Cross-Site Scripting (XSS)
Attacco per sovvertire la SOP iniettando script malevoli in una pagina web.
*   **Stored XSS (Persistent):** Lo script viene salvato sul server (es. in un profilo utente).
    *   *Caso Samy (MySpace):* Uno script si auto-propagava nei profili dei visitatori, rendendo Samy "amico" di un milione di persone in 20 ore.
*   **Reflected XSS:** Lo script è contenuto in un URL e viene "riflettuto" dal server nella pagina di risposta (es. risultati di una ricerca).

### Mitigazione XSS
*   **Filtraggio/Escape:** Rimuovere tag `<script>` (complesso per via degli encoding e dei tag CSS/XML).
*   **HttpOnly Cookies:** Impedisce a [[JavaScript]] di leggere i cookie di sessione.
*   **Content Security Policy (CSP):** Il server comunica al browser quali origini sono autorizzate per caricare script e risorse.

## 6. DNS Rebinding
Attacco avanzato per aggirare la SOP.
1.  L'attaccante attira la vittima su un sito controllato.
2.  Il server DNS dell'attaccante risponde con un TTL brevissimo.
3.  Dopo che il browser ha caricato la pagina, il DNS cambia l'IP del dominio puntandolo all'IP di una macchina interna alla rete della vittima (es. un router).
4.  Lo script malevolo ora può inviare richieste alla macchina interna perché l'origine (il nome dominio) sembra la stessa.

## 7. File Disclosure e SSRF
*   **File Disclosure:** Accesso a file sensibili (configurazioni, credenziali, codice sorgente) tramite vulnerabilità come il **[[Path Traversal]]** (`../../etc/passwd`).
*   **Server-Side Request Forgery (SSRF):** L'attaccante forza il server a effettuare richieste verso altre macchine (spesso interne o metadati cloud come l'IP `169.254.169.254` di AWS).
    *   *Mitigazione:* Whitelist di host autorizzati, isolamento della rete interna.
