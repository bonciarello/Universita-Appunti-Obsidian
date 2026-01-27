L'**HTTP** (HyperText Transfer Protocol) è un protocollo di livello applicativo fondamentale per il Web. È utilizzato per trasferire risorse (documenti, immagini, video) tra un **Client** (es. Browser) e un **Server**.

> [!INFO] Concetti Chiave
> - **Richiesta/Risposta (Request/Response):** Il client invia una richiesta, il server risponde.
> - **Risorsa:** Qualsiasi oggetto identificabile sul web (pagina HTML, immagine, servizio API).
> - **URI (Uniform Resource Identifier):** Stringa univoca che identifica una risorsa.
> - **Stateless:** Il server non "ricorda" le richieste precedenti dello stesso client.
## URI: Identificare le Risorse

L'**URI** (*Uniform Resource Identifier*) è l'identificatore generico di una risorsa. Si divide in due sottocategorie:
1.  **URN (Uniform Resource Name):** Identifica una risorsa tramite un nome univoco, indipendentemente dalla sua posizione o disponibilità (es. ISBN di un libro, Codice Fiscale).
2.  **URL (Uniform Resource Locator):** Identifica una risorsa specificando il **metodo** per accedervi e la sua **posizione** fisica.
### Struttura di un URL
La forma generale è: `protocollo://host:porta/percorso?query`

Esempio pratico: `http://www.example.com:8080/blog/articolo?id=123&lang=it`

| Componente | Esempio | Descrizione |
| :--- | :--- | :--- |
| **Protocollo** | `http` | Lo schema di accesso (altri: `https`, `ftp`, `mailto`). |
| **Host** | `www.example.com` | Indirizzo del server (nome dominio o IP). |
| **Porta** | `:8080` | Porta di ascolto (default HTTP: 80, HTTPS: 443). |
| **Percorso (Path)** | `/blog/articolo` | Posizione della risorsa nel file system del server. |
| **Query String** | `?id=123&lang=it` | Parametri passati alla risorsa (coppie `chiave=valore`). |
## Il Messaggio HTTP
Sia le richieste che le risposte seguono lo stesso formato testuale standard.
### Struttura Generica
```http
START-LINE
HEADER-1: Value
HEADER-2: Value

MESSAGE-BODY (opzionale)
```
1.  **Start-Line:** Definisce il tipo di messaggio (Richiesta o Risposta).
2.  **Headers:** Metadati (es. tipo di contenuto, lingua, cookie).
3.  **Riga Vuota:** Separa gli header dal corpo.
4.  **Message-Body:** Il contenuto vero e proprio (es. HTML della pagina o dati di un form).
## Metodi di Richiesta (Request Methods)
La "Start-Line" di una richiesta contiene: `METODO + PERCORSO + VERSIONE HTTP`.
### GET (Recupero)
Utilizzato per richiedere dati. I parametri sono visibili nell'URL (Query String).
*   **Sicuro:** Non modifica lo stato del server.
*   **Idempotente:** Eseguirlo più volte produce lo stesso risultato.
```http
GET /search?q=informatica HTTP/1.1
Host: www.google.com
Accept: text/html
```
### POST (Invio)
Utilizzato per inviare dati al server (es. form di login). I dati sono nel **Body** della richiesta e non nell'URL.
*   Non è idempotente (inviare due volte un pagamento potrebbe addebitarlo due volte).
```http
POST /login.php HTTP/1.1
Host: www.example.com
Content-Type: application/x-www-form-urlencoded

user=mario&pass=12345
```
### Altri Metodi
*   **HEAD:** Come GET, ma restituisce solo gli Header (senza Body). Utile per controllare se una risorsa esiste o è cambiata.
*   **PUT:** Carica/sostituisce una risorsa specifica.
*   **DELETE:** Cancella una risorsa.
*   **OPTIONS:** Chiede al server quali metodi sono supportati.
*   **TRACE:** Diagnostica (echo della richiesta).
## Codici di Stato (Response Codes)
La "Start-Line" di una risposta contiene: `VERSIONE HTTP + CODICE + MESSAGGIO`.
Esempio: `HTTP/1.1 200 OK`

| Classe | Descrizione | Esempi |
| :--- | :--- | :--- |
| **1xx** | Informativi | `100 Continue` |
| **2xx** | Successo | `200 OK` (Tutto bene), `201 Created` (Risorsa creata). |
| **3xx** | Redirezione | `301 Moved Permanently` (URL cambiato per sempre), `302 Found` (Temporaneo). |
| **4xx** | Errore Client | `400 Bad Request` (Richiesta malformata), `401 Unauthorized` (Serve login), `404 Not Found` (Non esiste). |
| **5xx** | Errore Server | `500 Internal Server Error` (Bug nel codice server), `503 Service Unavailable`. |
## Gestione dello Stato: Cookie e Sessioni
Poiché HTTP è **stateless** (senza memoria), servono meccanismi per riconoscere l'utente tra più richieste (es. mantenere il login o il carrello).
### Cookie
Piccoli file di testo inviati dal server e salvati dal browser.
1.  Il server invia l'header: `Set-Cookie: session_id=xyz123; Path=/; HttpOnly`
2.  Il browser lo salva.
3.  Alle successive richieste, il browser lo rimanda automaticamente: `Cookie: session_id=xyz123`
### Sicurezza e Autenticazione
*   **HTTP Basic Auth:** Invia username e password codificati in Base64 nell'header (poco sicuro se non su HTTPS).
    `Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=`
*   **HTTPS:** Fondamentale per cifrare la comunicazione, altrimenti header, cookie e dati POST viaggiano in chiaro.
