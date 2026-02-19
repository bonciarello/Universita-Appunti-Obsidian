---
aliases: [jsp]
tags: [web-computing]
---
Le **JSP** (*Java Server Pages*) sono una tecnologia lato server che permette di creare pagine web dinamiche mischiando codice [[HTML]] statico con frammenti di codice Java.

> [!NOTE] Relazione con le Servlet
> In realtà, le JSP sono un'astrazione delle Servlet. Al primo caricamento, il Web Container trasforma automaticamente il file `.jsp` in una classe Java (`Servlet`), la compila e la esegue.
## Sintassi e Tag Speciali
Esistono diversi tipi di tag per inserire logica Java in una pagina [[HTML]].
### Scriptlet: `<% ... %>`
Contiene blocchi di codice Java generico (loop, condizioni).
```jsp
<%
    String user = request.getParameter("user");
    if (user == null) user = "Ospite";
%>
<p>Benvenuto, <%= user %></p>
```
### Espressioni: `<%= ... %>`
Valuta un'espressione Java, la converte in stringa e la stampa direttamente nella pagina. **Nota:** Non serve il punto e virgola finale.
```jsp
<p>Il totale è: <%= 10 + 20 %> €</p>
<p>Data corrente: <%= new java.util.Date() %></p>
```
### Dichiarazioni: `<%! ... %>`
Usato per dichiarare variabili d'istanza o metodi della classe Servlet generata.
```jsp
<%! 
    private int contatore = 0; 
    public int raddoppia(int n) { return n * 2; }
%>
```
### Direttive: `<%@ ... %>`
Forniscono istruzioni al container su come gestire la pagina.
*   **Page:** `<%@ page contentType="text/html" import="java.util.*" %>`
*   **Include:** `<%@ include file="header.html" %>` (Inclusione statica a tempo di traduzione).
*   **Taglib:** Usata per importare librerie di tag come JSTL.
## Esempio: Pagina Dinamica
Esempio di una tabella generata con un ciclo Java:
```jsp
<html>
<body>
    <table border="1">
        <% for(int i=1; i<=5; i++) { %>
            <tr>
                <td>Riga numero:</td>
                <td><%= i %></td>
            </tr>
        <% } %>
    </table>
</body>
</html>
```
## Oggetti Impliciti
Nelle JSP sono disponibili degli oggetti predefiniti senza doverli dichiarare:
*   **`request`**: L'`HttpServletRequest`.
*   **`response`**: L'`HttpServletResponse`.
*   **`session`**: L'`HttpSession`.
*   **`out`**: Il buffer di output (`JspWriter`).
*   **`application`**: Il `ServletContext`.
## Inclusione: Statica vs Dinamica
1.  **Direttiva Include (`<%@ include ... %>`):** Il file viene copiato *dentro* la JSP prima della compilazione. Se il file incluso cambia, la JSP non sempre viene ricompilata. Ideale per file statici (es. copyright).
2.  **Azione Include (`<jsp:include page="..." />`):** L'inclusione avviene *a runtime*. La JSP chiama la risorsa esterna, ne prende il risultato e lo inserisce. Più flessibile.
