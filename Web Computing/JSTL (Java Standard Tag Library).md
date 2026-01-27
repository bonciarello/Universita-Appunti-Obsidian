La **JSTL** (*JavaServer Pages Standard Tag Library*) è una collezione di tag personalizzati che permettono di implementare logica comune (cicli, condizioni, formattazione) nelle JSP senza ricorrere agli scriptlet Java.

Per usarla, bisogna dichiarare la taglib nell'intestazione della JSP:
```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
```
## Tag Core Principali (`c:`)
### Ciclo `forEach` (Iterazione)
Usato per iterare su collezioni (Liste, Array, Mappe) o per cicli numerici.

**Esempio Numerico (da 1 a 10):**
```jsp
<c:forEach var="i" begin="1" end="10">
    Conteggio: ${i} <br/>
</c:forEach>
```

**Esempio su Collezione:**
```jsp
<ul>
    <c:forEach var="prodotto" items="${listaProdotti}">
        <li>${prodotto.nome} - ${prodotto.prezzo} €</li>
    </c:forEach>
</ul>
```
### Condizione `if`
Esegue il contenuto solo se la condizione è vera. **Nota:** Non esiste il tag `c:else`.
```jsp
<c:if test="${user.loggato}">
    <p>Benvenuto, ${user.nome}!</p>
</c:if>
```
### Scelta Multipla `choose`
Equivale al costrutto `switch-case` o `if-else if-else`.
```jsp
<c:choose>
    <c:when test="${voto < 18}">
        Bocciato
    </c:when>
    <c:when test="${voto >= 18 && voto < 24}">
        Sufficiente
    </c:when>
    <c:otherwise>
        Ottimo!
    </c:otherwise>
</c:choose>
```
## Gestione degli URL (`c:url`)
Il tag `<c:url>` è fondamentale per gestire correttamente gli URL all'interno dell'applicazione, gestendo automaticamente l'aggiunta del nome del contesto (context path) e l'URL rewriting (per il mantenimento della sessione).
```jsp
<%-- Creazione link sicuro --%>
<a href="<c:url value='/home' />">Torna alla Home</a>

<%-- Inserimento parametri dinamici --%>
<c:url var="urlModifica" value="/edit">
    <c:param name="id" value="${prodotto.id}" />
</c:url>
<a href="${urlModifica}">Modifica</a>
```
## Perché usare JSTL invece degli Scriptlet?
1.  **Leggibilità:** Il codice somiglia all'HTML, facilitando il lavoro ai front-end designer.
2.  **Meno Errori:** Riduce la complessità sintattica (niente parentesi graffe aperte/chiuse sparse).
3.  **Manutenibilità:** Separa nettamente la logica di visualizzazione da quella Java.
