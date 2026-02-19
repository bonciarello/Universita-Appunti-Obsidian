---
aliases: [Document Object Model, DOM]
tags: [web-computing]
---
Il **DOM** (*Document Object Model*) è l'interfaccia di programmazione per i documenti [[HTML]] e XML. Rappresenta la pagina web come una struttura ad albero, dove ogni nodo è un oggetto (es. un tag, un attributo, un testo).
Grazie al DOM, [[JavaScript]] può accedere e modificare dinamicamente contenuto, struttura e stile della pagina.
## Struttura e Nodi
L'albero del DOM è composto da diversi tipi di nodi, tutti derivati dall'interfaccia base `Node`:
1.  **Document Node:** La radice dell'albero (`document`).
2.  **Element Node:** I tag [[HTML]] (`<div>`, `<p>`, `<body>`).
3.  **Text Node:** Il testo contenuto nei tag.
4.  **Attribute Node:** Gli attributi dei tag (`href`, `src`).

```javascript
// Accesso agli elementi principali
var html = document.documentElement; // <html>
var head = document.head;            // <head>
var body = document.body;            // <body>
```
### Relazioni tra Nodi (Navigazione)
Ogni nodo ha proprietà per "muoversi" nell'albero:
*   `parentNode`: Il genitore.
*   `childNodes`: Lista di tutti i figli (inclusi nodi di testo/spazi vuoti).
*   `children`: Lista dei soli **elementi** figli (spesso più utile).
*   `firstChild` / `lastChild`: Primo/Ultimo figlio.
*   `nextSibling` / `previousSibling`: Fratello successivo/precedente.
## Selezione degli Elementi
Per manipolare un elemento, prima dobbiamo trovarlo.
### Metodi Classici
```javascript
// Trova elemento per ID (Unico)
var titolo = document.getElementById("main-title");

// Trova per Tag (Ritorna una HTMLCollection - simile array)
var paragrafi = document.getElementsByTagName("p");

// Trova per Classe
var bottoni = document.getElementsByClassName("btn");
```
### Metodi Moderni (Consigliati)
Usano i selettori CSS.
```javascript
// Restituisce IL PRIMO elemento che corrisponde
var primoParagrafo = document.querySelector("div.content > p");

// Restituisce TUTTI gli elementi (NodeList)
var tuttiLink = document.querySelectorAll("a.nav-link");
```
## Manipolazione del Contenuto
### Lettura/Scrittura
*   **`innerHTML`**: Legge/Scrive l'[[HTML]] interno (processa i tag).
    ```javascript
    div.innerHTML = "<strong>Nuovo contenuto</strong>";
    ```
*   **`textContent`**: Legge/Scrive solo il testo (ignora i tag, più sicuro e veloce).
    ```javascript
    div.textContent = "Testo semplice";
    ```
*   **`value`**: Per i campi di input (form).
    ```javascript
    input.value = "Nuovo valore";
    ```
### Creazione e Modifica Strutturale
Metodi per aggiungere o rimuovere nodi dinamicamente.
```javascript
// 1. Crea nuovi nodi
var nuovoP = document.createElement("p");
var testo = document.createTextNode("Ciao Mondo");

// 2. Assembla
nuovoP.appendChild(testo); // Mette il testo nel p

// 3. Inserisci nel documento
document.body.appendChild(nuovoP); // Aggiunge in fondo al body

// Altri metodi utili:
// parent.insertBefore(new, reference);
// parent.removeChild(child);
// parent.replaceChild(new, old);
```
## Gestione Attributi e Classi
### Attributi Standard
Possiamo leggere/scrivere attributi [[HTML]] (`href`, `src`, `id`...).
```javascript
var img = document.querySelector("img");
img.getAttribute("src");
img.setAttribute("alt", "Foto profilo");
img.removeAttribute("title");
```
### Manipolazione Classi (CSS)
Invece di manipolare la stringa `className`, usiamo l'interfaccia moderna `classList`.
```javascript
var box = document.getElementById("box");

box.classList.add("active");    // Aggiunge classe
box.classList.remove("hidden"); // Rimuove classe
box.classList.toggle("dark");   // Aggiunge se manca, rimuove se c'è
box.classList.contains("active"); // true/false
```
## Manipolazione Stile (`style`)
Possiamo modificare lo stile CSS inline dell'elemento.
**Nota:** Le proprietà CSS con trattino (`background-color`) diventano camelCase (`backgroundColor`).

```javascript
var box = document.getElementById("box");

box.style.width = "100px";
box.style.backgroundColor = "red";
box.style.display = "none"; // Nasconde l'elemento
```

> [!TIP] Best Practice
> Evita di impostare molti stili inline con JS. È meglio aggiungere/rimuovere una **classe CSS** predefinita che contiene tutti gli stili necessari.
## Browser Object Model (BOM)
Oltre al documento (`document`), [[JavaScript]] può interagire con il browser tramite l'oggetto globale `window`.

*   **`window.location`**: URL corrente, redirect (`location.href = '...'`).
*   **`window.history`**: Navigazione avanti/indietro (`history.back()`).
*   **`window.navigator`**: Info sul browser/OS (`navigator.userAgent`).
*   **`window.screen`**: Info sullo schermo dell'utente.
*   **`window.innerHeight`**: Altezza viewport.
