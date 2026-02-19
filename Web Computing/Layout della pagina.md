---
aliases: [Layout pagina, LP]
tags: [web-computing]
---
Il layout è l'arte di disporre gli elementi sulla pagina. Storicamente si usavano le tabelle (anni '90), poi i `float` (anni 2000), e oggi si usano **Flexbox** e **CSS Grid**.
## Centrare il Contenuto
Per evitare che il sito si "spalmi" su schermi enormi (es. monitor 4K), rendendo il testo illeggibile, si usa un contenitore centrale.
### Metodo Classico (Margin Auto)
Si definisce una larghezza massima per il contenitore e si impostano i margini laterali su `auto`.

```css
.container {
    width: 960px;       /* Larghezza fissa */
    max-width: 100%;    /* Per mobile: non superare la larghezza schermo */
    margin: 0 auto;     /* 0 sopra/sotto, auto a destra/sinistra */
}
```
## Tipi di Layout
### 1. Fixed-Width (Larghezza Fissa)
Le colonne hanno dimensioni in pixel (es. Sidebar 300px, Content 600px).
*   **Pro:** Controllo preciso del design.
*   **Contro:** Non si adatta agli schermi piccoli (crea scrollbar orizzontale) o lascia spazi vuoti su schermi grandi.
### 2. Liquid / Fluid (Liquido)
Le dimensioni sono in **percentuale** (`%`).
*   **Pro:** Si adatta a qualsiasi schermo.
*   **Contro:** Su schermi larghi le righe di testo diventano lunghissime (faticose da leggere).
### 3. Elastic (Elastico)
Le dimensioni sono in `em` o `rem`.
*   Il layout scala in base alla dimensione del font impostata dall'utente.
### 4. Responsive (Moderno)
Combina layout fluidi con **Media Queries** (`@media`) per cambiare struttura a determinati "punti di rottura" (breakpoints). È quello che fa [[Bootstrap]].
## Layout a Colonne (Tecniche)
### Metodo Vecchio: Float
Si usava `float: left` per allineare i div. Richiede calcoli precisi delle percentuali e l'uso di `clear` per ripristinare il flusso.
```css
/* Sidebar */
.sidebar {
    width: 30%;
    float: left;
}

/* Contenuto */
.content {
    width: 70%;
    float: left;
}

/* Pulizia finale */
.clearfix::after {
    content: "";
    clear: both;
    display: table;
}
```
### Metodo Moderno: Flexbox (Monodimensionale)
Ideale per barre di navigazione o allineare elementi in una riga/colonna.
```css
.container {
    display: flex; /* Attiva Flexbox */
}

.sidebar {
    flex: 1; /* Prende 1 quota di spazio */
}

.content {
    flex: 3; /* Prende 3 quote (quindi è il triplo della sidebar) */
}
```
### Metodo Moderno: CSS Grid (Bidimensionale)
Ideale per definire l'intera griglia della pagina (righe e colonne insieme).
```css
.grid-container {
    display: grid;
    grid-template-columns: 200px 1fr; /* Sidebar fissa 200px, resto flessibile */
    grid-gap: 20px; /* Spazio tra le colonne */
}
```
