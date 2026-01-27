L'**Expression Language** (EL) è una sintassi semplificata per accedere ai dati memorizzati nei JavaBeans e negli oggetti scoped (Request, Session, Application) all'interno delle JSP, senza dover scrivere codice Java (`scriptlet`).
L'obiettivo è rimuovere i tag `<% ... %>` dalle JSP per renderle più leggibili e focalizzate solo sulla presentazione.
## Sintassi di Base
Tutte le espressioni EL iniziano con `${` e finiscono con `}`.

```jsp
<%-- Vecchio stile scriptlet --%>
<%= request.getAttribute("utente") %>

<%-- Nuovo stile EL --%>
${utente}
```

> [!TIP] Vantaggio: Gestione Null
> Se un attributo non esiste, EL non stampa nulla (mentre lo scriptlet potrebbe stampare `null`). Inoltre, EL gestisce automaticamente le eccezioni come `NullPointerException`.
## Accesso alle Proprietà (Dot Notation)
EL usa la "Dot Notation" per accedere alle proprietà di un oggetto. Quando scriviamo `${oggetto.proprieta}`, EL chiama automaticamente il metodo **getter** corrispondente.

Esempio con un oggetto `Persona`:
```jsp
${studente.nome}    <%-- Chiama studente.getNome() --%>
${studente.eta}     <%-- Chiama studente.getEta() --%>
```
## Accesso a Collezioni e Mappe
Oltre al punto `.`, si può usare la notazione con parentesi quadre `[]`.
### Array e Liste
Si usa l'indice numerico.
```jsp
${listaStudenti[0]}      <%-- Primo elemento --%>
${arrayVoti[i]}          <%-- Indice dinamico --%>
```
### Map (Dizionari)
Si usa la chiave.
```jsp
${mappaCitta["Roma"]}    <%-- Equivale a mappa.get("Roma") --%>
${mappaCitta.Roma}       <%-- Notazione punto (se la chiave non ha caratteri speciali) --%>
```
### Quando usare `[]` invece di `.`?
1.  Per nomi di proprietà con caratteri illegali (es. `${header["User-Agent"]}`).
2.  Per accedere a indici di array/liste.
3.  Quando la chiave è una variabile (es. `${mappa[variabileChiave]}`).
## Oggetti Impliciti EL
EL fornisce oggetti pronti all'uso per accedere ai metadati della richiesta.
*   **`param`**: Parametri della query string (es. `${param.id}`).
*   **`header`**: Header HTTP (es. `${header["host"]}`).
*   **`cookie`**: Accesso ai cookie.
*   **`sessionScope`**: Forza la ricerca solo in sessione.
*   **`requestScope`**: Forza la ricerca solo nel request.
*   **`pageContext`**: Accesso a tutto il contesto JSP.

Esempio utile:
`Benvenuto, ${sessionScope.user.nome}` (cerca l'utente solo in sessione, ignorando eventuali omonimi nel request).
