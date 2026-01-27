Gli eventi sono il cuore dell'interattività in JavaScript. Permettono di eseguire codice in risposta ad azioni dell'utente (click, digitazione) o del browser (caricamento pagina).
## Gestione degli Eventi
Esistono tre modi per gestire gli eventi.
### Attributi HTML (Sconsigliato)
Vecchio stile, mischia logica e contenuto.
```html
<button onclick="alert('Ciao!')">Cliccami</button>
```
### Proprietà del DOM
Più pulito, ma permette un solo gestore per evento.
```javascript
var btn = document.getElementById("myBtn");
btn.onclick = function() {
    console.log("Cliccato!");
};
```
### Event Listener (Consigliato)
Standard moderno. Permette di aggiungere più gestori allo stesso evento e offre più controllo.
```javascript
var btn = document.getElementById("myBtn");

btn.addEventListener("click", function(e) {
    console.log("Gestore 1");
});

btn.addEventListener("click", function(e) {
    console.log("Gestore 2"); // Eseguito dopo il primo
});
```
## L'Oggetto Evento (`e`)
Quando si verifica un evento, il browser passa automaticamente un oggetto (spesso chiamato `e` o `event`) alla funzione di callback. Questo oggetto contiene dettagli sull'evento.
```javascript
document.addEventListener("click", function(e) {
    console.log("Tipo:", e.type);       // "click"
    console.log("Target:", e.target);   // L'elemento cliccato
    console.log("X:", e.clientX);       // Coordinate mouse
});
```
### Metodi Importanti dell'Oggetto Evento
1.  **`e.preventDefault()`**: Blocca l'azione predefinita del browser.
    *   Esempio: Impedisce a un link di cambiare pagina o a un form di inviarsi.
    ```javascript
    link.addEventListener("click", function(e) {
        e.preventDefault();
        console.log("Navigazione bloccata");
    });
    ```
2.  **`e.stopPropagation()`**: Ferma la propagazione dell'evento (vedi sotto).
## Flusso degli Eventi: Bubbling vs Capturing
Quando clicchi su un elemento annidato (es. uno `<span>` dentro un `<div>` dentro `<body>`), l'evento non riguarda solo lo span, ma attraversa l'albero del DOM.
### Le due fasi
1.  **Capturing (Cattura):** L'evento scende dalla radice (`window`) fino al target.
2.  **Target:** L'evento raggiunge l'elemento.
3.  **Bubbling (Risalita):** L'evento risale dal target fino alla radice. **(Default)**
### Esempio Pratico
```html
<div id="padre">
    <button id="figlio">Clicca</button>
</div>
```

```javascript
// Bubbling (Default: false)
document.getElementById("padre").addEventListener("click", () => console.log("Padre"), false);
document.getElementById("figlio").addEventListener("click", () => console.log("Figlio"), false);

// Output al click su figlio:
// 1. "Figlio"
// 2. "Padre"
```
Se volessimo intercettare l'evento *prima* che arrivi al figlio (fase di Capturing), useremmo `true` come terzo parametro.
## Tipi di Eventi Comuni
### Mouse
*   `click`: Pressione e rilascio tasto sinistro.
*   `dblclick`: Doppio click.
*   `mouseover` / `mouseout`: Il mouse entra/esce dall'elemento.
*   `mousemove`: Il mouse si muove sopra l'elemento.
### Tastiera
*   `keydown`: Tasto premuto.
*   `keyup`: Tasto rilasciato.
*   `keypress`: (Deprecato) Tasto che produce un carattere.
### Form
*   `submit`: Invio del modulo.
*   `focus` / `blur`: Un campo ottiene/perde il focus.
*   `change`: Il valore di un input è cambiato (e perso focus).
*   `input`: Il valore cambia (mentre si digita).
### Finestra/Documento
*   `load`: Pagina e risorse (immagini) caricate completamente.
*   `DOMContentLoaded`: HTML caricato (senza aspettare le immagini). **Molto usato.**
*   `resize`: Ridimensionamento finestra.
*   `scroll`: Scorrimento pagina.
### Esempio: Validazione Form
```javascript
var form = document.getElementById("loginForm");

form.addEventListener("submit", function(e) {
    var user = document.getElementById("username").value;
    
    if (user === "") {
        e.preventDefault(); // Blocca l'invio
        alert("Inserisci un nome utente!");
    }
});
```
