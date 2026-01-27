**Ajax** non è una tecnologia singola, ma un approccio allo sviluppo web che permette a una pagina di aggiornare i propri dati in modo **asincrono**, ovvero senza ricaricare l'intera pagina (evitando il cosiddetto *postback*).
## Il Modello Ajax
In un'applicazione tradizionale, ogni interazione (click, invio form) richiede una nuova richiesta HTTP e il server risponde inviando una pagina HTML completa. 

Con Ajax:
1.  **Evento:** L'utente compie un'azione.
2.  **Richiesta:** JavaScript crea un oggetto `XMLHttpRequest` (o usa la moderna API `fetch`) e invia una richiesta al server in background.
3.  **Elaborazione:** Il server processa la richiesta e restituisce solo i dati necessari (solitamente in formato **JSON**).
4.  **Aggiornamento:** JavaScript riceve i dati e aggiorna solo una parte specifica del DOM.
## XMLHttpRequest (L'oggetto Core)
È l'oggetto JavaScript che permette la comunicazione HTTP asincrona.
### Esempio di Richiesta GET (Nativa)
```javascript
var xhr = new XMLHttpRequest();

// 1. Configurazione (Metodo, URL, Asincrono=true)
xhr.open("GET", "api/dati?id=123", true);

// 2. Gestore dell'evento di risposta
xhr.onreadystatechange = function() {
    if (xhr.readyState === 4) { // Operazione completata
        if (xhr.status === 200) {
            console.log("Dati ricevuti:", xhr.responseText);
            document.getElementById("risultato").innerHTML = xhr.responseText;
        } else {
            console.error("Errore HTTP:", xhr.status);
        }
    }
};

// 3. Invio
xhr.send();
```
## JSON: Lo Standard per i Dati
Sebbene l'acronimo contenga "XML", oggi si usa quasi esclusivamente **JSON** (*JavaScript Object Notation*) perché più leggero e facile da processare in JS.

```javascript
// Da stringa JSON (ricevuta dal server) a oggetto JS
var obj = JSON.parse('{"nome": "Mario", "eta": 30}');

// Da oggetto JS a stringa JSON (da inviare al server)
var str = JSON.stringify(obj);
```
## Considerazioni su Usabilità e Sicurezza
### Vantaggi
*   **Interattività:** L'utente non sperimenta interruzioni (niente schermo bianco durante il caricamento).
*   **Risparmio di Banda:** Si scambiano solo piccoli frammenti di dati, non intere pagine HTML.
### Svantaggi e Sfide
*   **Tasto Indietro:** Ajax non aggiorna automaticamente la cronologia del browser. Tornando indietro, l'utente potrebbe finire alla pagina precedente a quella dove ha iniziato l'interazione Ajax.
*   **SEO:** I motori di ricerca faticano a indicizzare contenuti caricati dinamicamente (problema ridotto con i moderni crawler).
*   **Stato di Attesa:** È fondamentale mostrare un **loader** (es. una GIF o un testo "Caricamento...") per far capire all'utente che l'operazione è in corso.
### Sicurezza

> [!WARNING] Importante
> *   **Same-Origin Policy:** Per sicurezza, Ajax può fare richieste solo allo stesso dominio da cui è stata caricata la pagina (salvo configurazioni CORS o uso di JSONP).
> *   **Validazione Lato Server:** Non fidarsi mai dei controlli JavaScript. Un utente malintenzionato può bypassare Ajax e inviare richieste SQL Injection direttamente al server.
## Gestione dei Timeout
In caso di rete lenta, la richiesta potrebbe "sparire". È bene implementare un limite massimo di attesa.

```javascript
var xhr = new XMLHttpRequest();
xhr.open("GET", "data.json", true);
xhr.timeout = 5000; // 5 secondi

xhr.ontimeout = function () {
    alert("La richiesta ha impiegato troppo tempo!");
};
xhr.send();
```
