---
aliases: [bootstrap]
tags: [web-computing]
---
**Bootstrap** è il framework CSS più popolare al mondo per lo sviluppo di siti responsive e mobile-first. Fornisce una collezione di stili CSS predefiniti e componenti [[JavaScript]] (modali, carousel, tooltip) pronti all'uso.
## Installazione

1.  **CDN (Consigliato per test):** Includi i link CSS e JS nell'`<head>` e prima della chiusura del `<body>`.
    ```html
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
    ```
2.  **Download:** Scarica i file e ospitali nel tuo progetto.
3.  **Package Manager:** `npm install bootstrap`.
## Il Grid System (Sistema a Griglia)
Il cuore di Bootstrap è la **griglia a 12 colonne**. Permette di impaginare il contenuto in righe e colonne che si adattano alla larghezza dello schermo.
### Struttura Base
1.  **`.container`**: Il contenitore principale (centra il contenuto).
2.  **`.row`**: Una riga orizzontale (azzera i margini negativi).
3.  **`.col-`**: Le colonne vere e proprie (devono sommare a 12).

```html
<div class="container">
  <div class="row">
    <div class="col-4">Colonna 1 (33%)</div>
    <div class="col-4">Colonna 2 (33%)</div>
    <div class="col-4">Colonna 3 (33%)</div>
  </div>
</div>
```
### Breakpoints (Punti di interruzione)
Bootstrap definisce suffissi per adattare il layout a diverse dimensioni:
*   (nessun suffisso): Smartphone (< 576px)
*   **sm**: Small (≥ 576px)
*   **md**: Medium (Tablet, ≥ 768px)
*   **lg**: Large (Desktop, ≥ 992px)
*   **xl**: Extra Large (≥ 1200px)

Esempio Responsive:
```html
<!-- 
  Su Mobile (xs): 1 colonna intera (col-12 implicito)
  Su Tablet (md): 2 colonne (50% e 50%)
  Su Desktop (lg): 3 colonne (4:4:4) 
-->
<div class="row">
  <div class="col-12 col-md-6 col-lg-4">Elemento A</div>
  <div class="col-12 col-md-6 col-lg-4">Elemento B</div>
  <div class="col-12 col-md-12 col-lg-4">Elemento C</div>
</div>
```
## Tipografia e Colori
Bootstrap sovrascrive lo stile di default del browser con un look più moderno.
### Testo
*   Headings (`h1`-`h6`): Font più pulito.
*   `.lead`: Paragrafo in evidenza (font leggermente più grande).
*   `.text-muted`: Testo grigio chiaro.
*   `.text-center`, `.text-end`: Allineamento.
### Colori Semantici (Utility Classes)
Si applicano al testo (`text-`) o allo sfondo (`bg-`).

| Classe | Colore | Significato |
| :--- | :--- | :--- |
| `.text-primary` | Blu | Principale |
| `.text-success` | Verde | Successo/Conferma |
| `.text-danger` | Rosso | Errore/Pericolo |
| `.text-warning` | Giallo | Attenzione |
| `.text-info` | Azzurro | Informazione |
| `.text-light` | Bianco | Chiaro |
| `.text-dark` | Nero | Scuro |

```html
<p class="text-danger bg-light">Errore critico su sfondo chiaro!</p>
```
## Componenti Principali
Bootstrap offre decine di componenti. Ecco i più usati:
### Bottoni
```html
<button class="btn btn-primary">Salva</button>
<button class="btn btn-outline-danger">Elimina</button>
```
### Navbar
```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">MioSito</a>
    <!-- Menu... -->
  </div>
</nav>
```
### Alert
```html
<div class="alert alert-success" role="alert">
  Operazione completata con successo!
</div>
```
### Card (Schede)
Molto usate per liste di prodotti o post.
```html
<div class="card" style="width: 18rem;">
  <img src="..." class="card-img-top" alt="...">
  <div class="card-body">
    <h5 class="card-title">Titolo</h5>
    <p class="card-text">Descrizione breve.</p>
    <a href="#" class="btn btn-primary">Vai</a>
  </div>
</div>
```
## 9.5 Margin e Padding (Spacing Utilities)
Bootstrap offre classi rapide per i margini (`m`) e il padding (`p`).
Formato: `{property}{sides}-{size}`
*   **Property:** `m` (margin), `p` (padding).
*   **Sides:** `t` (top), `b` (bottom), `s` (start/left), `e` (end/right), `x` (orizzontale), `y` (verticale).
*   **Size:** da `0` a `5` (es. `3` è lo standard, circa 1rem).
Esempi:
*   `.mt-3`: Margin Top livello 3.
*   `.p-5`: Padding su tutti i lati livello 5.
*   `.mx-auto`: Centra orizzontalmente (margin-left/right auto).
*   `.py-2`: Padding verticale livello 2.
