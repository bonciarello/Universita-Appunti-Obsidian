L'**HTML** (*HyperText Markup Language*) è il linguaggio standard per la creazione di pagine web. Non è un linguaggio di programmazione, ma un linguaggio di **markup** (marcatura) che descrive la struttura del contenuto tramite **tag**.
## Panoramica
### Concetti Base
*   **Tag (Elemento):** Istruzioni racchiuse tra parentesi angolari (es. `<html>`). Solitamente funzionano in coppia: un tag di apertura e uno di chiusura.
*   **W3C (World Wide Web Consortium):** L'organizzazione che definisce gli standard web.
*   **Doctype:** Istruzione che dice al browser quale versione di HTML stiamo usando. Per HTML5 è semplicemente `<!DOCTYPE html>`.
### Evoluzione: Da XHTML a HTML5
*   **XHTML:** Versione più rigorosa basata su XML (tag minuscoli obbligatori, chiusura sempre necessaria, attributi quotati).
*   **HTML5:** Lo standard attuale. È più flessibile, retrocompatibile e introduce nuovi tag semantici (es. `<nav>`, `<article>`) e API per app web complesse.
## Struttura di una Pagina HTML
Ogni pagina HTML ben formata segue questa struttura ad albero:
```html
<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <title>Titolo della Pagina</title>
    <!-- Qui vanno CSS, Meta-tag e Script -->
</head>
<body>
    <!-- Qui va il contenuto visibile -->
    <h1>Benvenuti</h1>
    <p>Questo è un paragrafo.</p>
</body>
</html>
```
*   **`<html>`**: L'elemento radice.
*   **`<head>`**: Contiene metadati non visibili direttamente (titolo scheda, encoding caratteri, link a CSS/JS).
*   **`<body>`**: Contiene tutto ciò che l'utente vede (testi, immagini, video).
## Elementi Fondamentali
### Testo e Titoli
*   **Headings:** Da `<h1>` (più importante) a `<h6>` (meno importante). Usarli per la gerarchia semantica, non per la grandezza del testo.
*   **Paragrafi:** `<p>Testo del paragrafo</p>`. È un elemento **block** (va a capo).
*   **Inline vs Block:**
    *   **Block:** Occupa tutta la larghezza (es. `<div>`, `<p>`, `<h1>`).
    *   **Inline:** Occupa solo lo spazio necessario e non va a capo (es. `<span>`, `<a>`, `<strong>`).
### Liste
```html
<!-- Lista Non Ordinata (Bullet points) -->
<ul>
    <li>Elemento 1</li>
    <li>Elemento 2</li>
</ul>

<!-- Lista Ordinata (Numerata) -->
<ol>
    <li>Primo</li>
    <li>Secondo</li>
</ol>
```
### Link (Anchor)
L'attributo `href` definisce la destinazione.
```html
<!-- Link assoluto -->
<a href="https://www.google.com">Vai a Google</a>

<!-- Link relativo (nello stesso sito) -->
<a href="pagina2.html">Vai a Pagina 2</a>
```
### Immagini
Tag "self-closing" (non ha chiusura). L'attributo `alt` è fondamentale per l'accessibilità.
```html
<img src="foto.jpg" alt="Descrizione della foto" width="300">
```
### Tabelle
Struttura classica: `<table>` (contenitore), `<tr>` (riga), `<th>` (intestazione), `<td>` (cella dati).
```html
<table border="1">
    <tr>
        <th>Nome</th>
        <th>Voto</th>
    </tr>
    <tr>
        <td>Mario</td>
        <td>28</td>
    </tr>
</table>
```
## HTML5 Semantico
HTML5 introduce tag che spiegano il *significato* del contenuto, non solo l'aspetto. Questo aiuta i motori di ricerca (SEO) e gli screen reader.

| Tag | Descrizione |
| :--- | :--- |
| `<header>` | Intestazione della pagina o di una sezione (logo, titolo). |
| `<nav>` | Menu di navigazione principale. |
| `<main>` | Contenuto principale della pagina (unico per pagina). |
| `<article>` | Contenuto indipendente e riutilizzabile (es. post di un blog). |
| `<section>` | Sezione tematica di un contenuto. |
| `<aside>` | Contenuto correlato ma separato (barra laterale). |
| `<footer>` | Piè di pagina (copyright, contatti). |
Esempio di struttura semantica:
```html
<header>
    <h1>Il Mio Blog</h1>
    <nav>...</nav>
</header>
<main>
    <article>
        <h2>Titolo Articolo</h2>
        <p>Testo...</p>
    </article>
</main>
<footer>&copy; 2021</footer>
```
## Form e Input
I **Form** servono per inviare dati al server. Gli attributi chiave sono `action` (dove inviare i dati) e `method` (GET o POST).
```html
<form action="/login" method="POST">
    
    <!-- Input di testo -->
    <label for="user">Username:</label>
    <input type="text" id="user" name="username" required>
    
    <!-- Password -->
    <label for="pass">Password:</label>
    <input type="password" id="pass" name="password">
    
    <!-- Radio Button (Scelta singola) -->
    <input type="radio" name="sesso" value="M"> M
    <input type="radio" name="sesso" value="F"> F
    
    <!-- Checkbox (Scelta multipla) -->
    <input type="checkbox" name="news" checked> Iscriviti alla newsletter
    
    <!-- Dropdown list -->
    <select name="paese">
        <option value="it">Italia</option>
        <option value="fr">Francia</option>
    </select>
    
    <!-- Pulsante invio -->
    <button type="submit">Invia</button>

</form>
```
### Nuovi Input HTML5
HTML5 aggiunge validazione automatica e tastiere specifiche su mobile:
*   `<input type="email">`: Valida formato email.
*   `<input type="date">`: Selettore calendario.
*   `<input type="number">`: Solo numeri.
*   `<input type="range">`: Slider.
*   `<input type="search">`: Campo ricerca.
