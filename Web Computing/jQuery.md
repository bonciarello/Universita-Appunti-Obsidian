---
aliases: [jquery]
tags: [web-computing]
---
**jQuery** è una libreria [[JavaScript]] veloce e concisa che semplifica l'attraversamento del DOM, la gestione degli [[Eventi|eventi]], l'animazione e le interazioni [[Ajax]]. Il suo motto è: *"Write less, do more"*.

> [!NOTE] Nota Storica
> Oggi molte funzioni di jQuery sono integrate nativamente in [[JavaScript]] (ES6+), ma jQuery rimane fondamentale per lavorare su codice legacy e per la sua semplicità sintattica.
## Inizializzazione
Per usare jQuery, includi lo script e assicurati che il DOM sia pronto prima di eseguire il codice.
```javascript
// Sintassi standard
$(document).ready(function() {
    console.log("DOM pronto!");
});

// Sintassi abbreviata (Consigliata)
$(function() {
    console.log("DOM pronto!");
});
```
Il simbolo `$` è un alias per la funzione `jQuery`.
## Selettori
jQuery usa i selettori CSS per trovare gli elementi.
```javascript
$("p")            // Tutti i tag <p>
$(".classe")      // Elementi con class="classe"
$("#id")          // Elemento con id="id"
$("ul > li")      // Figli diretti
$("input[type='text']") // Attributi specifici
$(":button")      // Pseudo-selettore per bottoni
```
### Traversing (Navigazione)
Una volta selezionato un elemento, ci si può muovere nell'albero:
*   `.parent()`: Genitore diretto.
*   `.children()`: Figli diretti.
*   `.find("span")`: Cerca discendenti (a qualsiasi livello).
*   `.next()` / `.prev()`: Fratello successivo / precedente.
*   `.closest("div")`: Il più vicino antenato che corrisponde al selettore.
## Manipolazione DOM e CSS
### Lettura e Scrittura
Molti metodi jQuery funzionano sia come *getter* (senza parametri) che come *setter* (con parametri).
```javascript
// Testo e HTML
$("#box").text();          // Legge testo
$("#box").text("Ciao");    // Scrive testo
$("#box").html("<b>Ciao</b>"); // Scrive HTML

// Valori Input
$("#input").val();         // Legge valore
$("#input").val("Nuovo");  // Imposta valore

// Attributi
$("img").attr("src");      // Legge src
$("img").attr("src", "foto.jpg"); // Cambia src
$("img").removeAttr("title");

// Classi CSS
$("#box").addClass("active");
$("#box").removeClass("hidden");
$("#box").toggleClass("selected");
```
### Stile CSS
```javascript
// Singola proprietà
$("p").css("color", "red");

// Oggetto multiplo
$("p").css({
    "color": "red",
    "font-size": "20px",
    "background-color": "#f0f0f0"
});
```
## Gestione [[Eventi]]
Sintassi semplificata rispetto a `addEventListener`.
```javascript
// Click
$("#btn").click(function() {
    alert("Click!");
});

// Hover
$("#menu").hover(
    function() { $(this).addClass("hover"); }, // Mouse enter
    function() { $(this).removeClass("hover"); } // Mouse leave
);

// Metodo on() (Consigliato per eventi dinamici/delegati)
$(document).on("click", ".dynamic-btn", function() {
    console.log("Funziona anche su elementi creati dopo il caricamento pagina");
});
```
## Effetti e [[Animazioni]]
jQuery include effetti visivi pronti all'uso.
*   `show()` / `hide()` / `toggle()`: Mostra/Nasconde (cambia `display`).
*   `fadeIn()` / `fadeOut()`: Dissolvenza.
*   `slideUp()` / `slideDown()`: Scorrimento verticale.
```javascript
// Animazione personalizzata
$("#box").animate({
    left: "+=50px",
    opacity: 0.5
}, 1000); // Durata 1 secondo
```
## [[Ajax|AJAX]] con jQuery
Semplifica enormemente le chiamate asincrone rispetto al vecchio `XMLHttpRequest`.
### Metodo `$.ajax()` (Generico)
```javascript
$.ajax({
    url: "api/dati",
    type: "GET",
    dataType: "json",
    success: function(data) {
        console.log("Dati ricevuti:", data);
    },
    error: function(xhr, status, error) {
        console.error("Errore:", error);
    }
});
```
### Scorciatoie
*   `$.get(url, callback)`
*   `$.post(url, data, callback)`
*   `$.getJSON(url, callback)`
*   `$("#div").load("pagina.html")`: Carica [[HTML]] remoto dentro un elemento.
## Estensioni (Plugin)
jQuery è estendibile tramite plugin. Per crearne uno:
```javascript
$.fn.cambiaColore = function(colore) {
    // 'this' è l'oggetto jQuery su cui viene chiamato il plugin
    this.css("color", colore);
    return this; // Importante per mantenere il concatenamento (chaining)
};

// Utilizzo
$("p").cambiaColore("green").slideUp();
```
