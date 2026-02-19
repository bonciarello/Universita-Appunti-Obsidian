---
aliases: [JR, JSONP richieste cross-domain]
tags: [web-computing]
---
Il principale ostacolo nelle richieste asincrone è la **Same-Origin Policy**: un browser impedisce a uno script caricato da `sitoA.com` di fare richieste [[Ajax]] verso `sitoB.com`. Questo serve a prevenire attacchi malevoli, ma limita lo scambio legittimo di dati tra domini.

**JSONP** (*JSON with Padding*) è un hack storico utilizzato per superare questa limitazione prima della diffusione dello standard **CORS** (*Cross-Origin Resource Sharing*).
## Il Principio di Funzionamento
Sebbene i tag `<script>` non possano fare richieste [[Ajax]] verso altri domini, essi **possono** caricare script esterni da qualsiasi URL.

JSONP sfrutta questa caratteristica:
1.  Invece di richiedere un file JSON puro (che il browser bloccherebbe), il client richiede uno **script eseguibile**.
2.  Il server non risponde con `{ "dati": "..." }`, ma avvolge (*padding*) i dati in una chiamata a una funzione [[JavaScript]]: `miaCallback({ "dati": "..." });`.
3.  Il browser esegue lo script e la funzione `miaCallback` (definita precedentemente nel client) riceve i dati come argomento.
## Esempio Pratico
### Lato Client (Preparazione)
Dobbiamo creare dinamicamente un tag `<script>` e definire la funzione che elaborerà i dati.
```javascript
// 1. Definisco la funzione di callback
function elaboraDati(data) {
    console.log("Dati ricevuti dal server remoto:", data.titolo);
}

// 2. Creo il tag script verso il server esterno
var script = document.createElement('script');
script.src = 'https://api.remota.com/libri?cb=elaboraDati'; // Passo il nome della funzione

// 3. Lo aggiungo alla pagina per far partire la richiesta
document.head.appendChild(script);
```
### Lato Server (Risposta)
Il server deve leggere il parametro `cb` e generare una risposta dinamica.
```php
// Esempio in PHP
$dati = '{"id": 1, "titolo": "I Promessi Sposi"}';
$callback = $_GET['cb']; // 'elaboraDati'

// Risultato finale: elaboraDati({"id": 1, "titolo": "I Promessi Sposi"});
echo $callback . '(' . $dati . ');';
```
## JSONP con [[jQuery]]
[[jQuery]] automatizza tutto il processo. Se l'URL contiene `callback=?`, [[jQuery]] capisce che si tratta di JSONP, crea la funzione temporanea e pulisce i tag script creati.
```javascript
$.getJSON("https://api.remota.com/libri?callback=?", function(data) {
    console.log(data.titolo);
});
```
## Limiti di JSONP
*   **Solo GET:** Poiché si basa sul tag `<script>`, supporta solo richieste di tipo GET. Non è possibile inviare dati complessi tramite POST.
*   **[[Sicurezza]]:** Stai eseguendo codice [[JavaScript]] proveniente da un server esterno. Se il server esterno viene compromesso, può eseguire codice maligno nel tuo browser.
*   **Gestione Errori:** È difficile intercettare errori HTTP (es. 404 o 500) con i tag script.

> [!NOTE] Modernità
> Oggi la soluzione standard è **CORS**, che permette al server di dichiarare tramite header HTTP (`Access-Control-Allow-Origin`) quali domini sono autorizzati a fare richieste [[Ajax]]. JSONP è considerato una tecnica legacy.
