---
aliases: [css]
tags: [web-computing]
---
I **CSS** (*Cascading Style Sheets*) servono a separare il **contenuto** ([[HTML]]) dalla **presentazione** (stile grafico).
Vantaggi principali:
*   **Riutilizzabilità:** Lo stesso stile può essere applicato a molte pagine.
*   **Manutenibilità:** Basta modificare un solo file `.css` per cambiare il design di tutto il sito.
*   **Separazione dei ruoli:** Chi scrive i contenuti non deve preoccuparsi della grafica.
## Sintassi di Base
Una regola CSS è composta da un **selettore** e da un blocco di **dichiarazioni**.
```css
/* Selettore { Proprietà: Valore; } */
h1 {
    color: blue;
    font-size: 24px;
}
```
### Come includere il CSS
Esistono tre modi, in ordine di preferenza:
1.  **Esterno (Consigliato):** File separato linkato nell'`<head>`.
    ```html
    <link rel="stylesheet" href="style.css">
    ```
2.  **Interno:** Blocco `<style>` dentro l'`<head>`.
    ```html
    <style>
        body { background-color: #f0f0f0; }
    </style>
    ```
3.  **Inline (Sconsigliato):** Direttamente nel tag [[HTML]].
    ```html
    <p style="color: red;">Testo rosso</p>
    ```
## Selettori
Servono a indicare *quali* elementi [[HTML]] modificare.

| Tipo | Sintassi | Esempio | Descrizione |
| :--- | :--- | :--- | :--- |
| **Universale** | `*` | `* { margin: 0; }` | Seleziona *tutti* gli elementi. |
| **Elemento** | `tag` | `p { color: black; }` | Tutti i paragrafi `<p>`. |
| **Classe** | `.nome` | `.alert { color: red; }` | Elementi con `class="alert"`. |
| **ID** | `#nome` | `#header { height: 50px; }` | L'unico elemento con `id="header"`. |
| **Discendente** | `A B` | `div p { ... }` | Tutti i `p` dentro un `div`. |
| **Figlio diretto**| `A > B`| `ul > li { ... }` | Solo i `li` figli diretti di `ul`. |
### Pseudo-classi
Definiscono uno *stato* dell'elemento.
```css
a:hover { color: orange; }  /* Quando il mouse passa sopra */
a:visited { color: gray; }  /* Link già visitato */
input:focus { border: 1px solid blue; } /* Quando l'utente clicca nel campo */
```
## Box Model
Ogni elemento [[HTML]] è considerato una scatola rettangolare composta da 4 strati (dall'interno all'esterno):
1.  **Content:** Il contenuto vero e proprio (testo, immagine).
2.  **Padding:** Spazio *interno* tra contenuto e bordo. Prende il colore di sfondo.
3.  **Border:** Il bordo che circonda il padding.
4.  **Margin:** Spazio *esterno* che separa l'elemento dagli altri. È trasparente.

```css
div {
    width: 200px;           /* Larghezza contenuto */
    padding: 20px;          /* Spazio interno */
    border: 5px solid black;/* Bordo */
    margin: 30px;           /* Distanza dagli altri */
}
```

> [!WARNING] Calcolo della larghezza totale
> Di default, `width` si riferisce solo al contenuto.
> Larghezza totale = `width` + `padding` (dx+sx) + `border` (dx+sx).
> Per evitare calcoli complessi, si usa spesso: `box-sizing: border-box;` che include padding e border nella width dichiarata.
## Posizionamento (`position`)
La proprietà `position` cambia il modo in cui gli elementi si dispongono.
*   **`static` (default):** Segue il normale flusso della pagina. `top`, `left`, ecc. non hanno effetto.
*   **`relative`:** L'elemento rimane nel flusso normale, ma può essere spostato (es. `top: 10px`) rispetto alla sua posizione originale.
*   **`absolute`:** L'elemento viene **rimosso dal flusso** (non occupa più spazio) e posizionato rispetto al primo genitore "non statico" (di solito un contenitore `relative`).
*   **`fixed`:** Rimosso dal flusso e posizionato rispetto alla **finestra del browser** (viewport). Non scrolla con la pagina (es. navbar fissa in alto).
*   **`sticky`:** Un ibrido: si comporta come `relative` finché non si scrolla fino a un certo punto, poi diventa "fisso".
## Float e Clear (Legacy Layout)
Prima di Flexbox e Grid, si usava `float` per impaginare (es. immagini col testo a fianco).
*   **`float: left/right;`**: Spinge l'elemento a lato e fa "scorrere" il testo attorno ad esso.
*   **`clear: both;`**: Impedisce a un elemento di avere float ai lati (usato per "andare a capo" dopo un float).

> [!NOTE] Modern Layout
> Oggi `float` si usa quasi solo per le immagini nel testo. Per il layout strutturale (colonne, griglie) si usano **Flexbox** e **CSS Grid**.
## Testo e Font
*   **`font-family`:** Tipo di carattere. Usare sempre una famiglia generica di riserva.
    `font-family: "Helvetica Neue", Arial, sans-serif;`
*   **`font-size`:** Dimensione.
    *   `px`: Assoluta (pixel).
    *   `em`: Relativa al font-size del genitore.
    *   `rem`: Relativa al font-size della radice (`html`).
*   **`font-weight`:** Spessore (`normal`, `bold`, `700`).
*   **`text-align`:** Allineamento (`left`, `center`, `right`, `justify`).
*   **`color`:** Colore del testo (es. `red`, `#FF0000`, `rgb(255, 0, 0)`).
## Colori e Sfondi
```css
body {
    background-color: #ffffff;
    background-image: url('bg.jpg');
    background-repeat: no-repeat;
    background-position: center top;
    background-size: cover; /* Adatta l'immagine per coprire tutto */
}
```
