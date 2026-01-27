Le **Servlet** sono componenti lato server scritti in Java che estendono le funzionalità di un web server. Sono il cuore del framework Java EE (Enterprise Edition) per il web e gestiscono il modello **Request/Response**.
## Architettura 3-Tier
Le applicazioni web moderne sono divise in tre livelli logici:
1.  **Presentation Tier:** L'interfaccia utente (HTML, CSS, JS).
2.  **Logic Tier:** Il cervello dell'app (Servlet, Application Server come Tomcat). Si occupa della decodifica richieste, logica di business e sicurezza.
3.  **Persistence Tier:** Dove i dati vengono salvati (Database, RDBMS).
## Cosa sono le Servlet?
In termini pratici, una Servlet è una classe Java che estende `HttpServlet`. Gira all'interno di un **Web Container** (es. Apache Tomcat), che si occupa di gestire la rete e il ciclo di vita della servlet.
### Esempio Base: HelloServlet
```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        
        // Legge parametri dalla URL: ?nome=Mario
        String nome = request.getParameter("nome");
        if (nome == null) nome = "Mondo";

        // Imposta il tipo di contenuto
        response.setContentType("text/html");
        
        // Scrive la risposta
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>Ciao " + nome + "!</h1>");
        out.println("</body></html>");
    }
}
```
## Ciclo di Vita della Servlet
Il container gestisce la servlet in tre fasi principali:
1.  **`init()`**: Eseguito una sola volta quando la servlet viene caricata in memoria. Si usa per operazioni costose (es. aprire connessione DB).
2.  **`service()`**: Chiamato ad ogni richiesta. Delega il lavoro a `doGet`, `doPost`, `doPut`, `doDelete` in base al metodo HTTP.
3.  **`destroy()`**: Eseguito prima che la servlet venga rimossa dalla memoria (es. allo spegnimento del server).

> [!WARNING] Multithreading
> Di default, il container usa **un'unica istanza** della servlet per gestire molteplici richieste contemporaneamente in thread separati. Bisogna evitare l'uso di variabili d'istanza per memorizzare dati della richiesta, altrimenti si rischiano problemi di concorrenza.
## Gestione dello Stato
HTTP è stateless. Per riconoscere l'utente tra più richieste, Java fornisce due strumenti:
### Cookie
Dati salvati sul browser del client.
```java
// Creazione
Cookie c = new Cookie("user", "mario");
c.setMaxAge(3600); // 1 ora
response.addCookie(c);

// Lettura
Cookie[] cookies = request.getCookies();
```
### Sessione (`HttpSession`)
Dati salvati sul server, associati a un ID (JSESSIONID) solitamente scambiato via cookie.
```java
// Ottieni o crea sessione
HttpSession session = request.getSession(true);

// Salva dato
session.setAttribute("carrello", mioOggetto);

// Recupera dato
Carrello c = (Carrello) session.getAttribute("carrello");

// Termina sessione (Logout)
session.invalidate();
```
## Navigazione: Include e Forward
Le servlet possono delegare il lavoro ad altre risorse (altre servlet o JSP).
*   **`forward()`**: Il controllo passa completamente alla nuova risorsa. L'URL nel browser **non cambia**.
    ```java
    request.getRequestDispatcher("/success.jsp").forward(request, response);
    ```
*   **`include()`**: Il contenuto di un'altra risorsa viene incluso nella risposta corrente.
*   **`sendRedirect()`**: Il server dice al browser di fare una nuova richiesta a un altro URL. L'URL nel browser **cambia**.
    ```java
    response.sendRedirect("login.html");
    ```
## Scoped Objects (Ambiti)
In Java Web esistono 4 ambiti dove salvare attributi (coppie chiave-valore):
1.  **Request Scope:** Valido solo per la singola richiesta (es. dati da passare a una JSP via forward).
2.  **Session Scope:** Valido finché l'utente non chiude il browser o scade il timeout.
3.  **Application Scope (`ServletContext`):** Globale per tutta l'applicazione (tutti gli utenti).
4.  **Page Scope:** (Solo nelle JSP) Valido per la singola pagina.
