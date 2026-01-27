Il pattern **MVC** è l'architettura standard per separare la logica di business dall'interfaccia utente nelle applicazioni web. Dividere un'app in queste tre componenti facilita la manutenibilità e il lavoro di squadra.
## Le Componenti
### Model (Il Dato)
Rappresenta lo stato dell'applicazione e le regole di business. In Java, è solitamente composto da **POJO** (*Plain Old Java Objects*) o **JavaBeans**. Non ha idea di come i dati verranno visualizzati.
### View (La Presentazione)
È l'interfaccia che l'utente vede. In ambito Java Web, sono le pagine **JSP** o template (es. Thymeleaf). La View riceve i dati dal Model tramite il Controller e li visualizza. Non dovrebbe contenere logica complessa.
### Controller (Il Regista)
Gestisce le richieste HTTP in entrata.
1.  Riceve la richiesta dal client (es. tramite una **Servlet**).
2.  Interagisce con il Model per recuperare o salvare dati.
3.  Decide quale View mostrare e gliela "passa" insieme ai dati.
## Flusso di Lavoro (Scenario Tipico)
1.  **Richiesta:** L'utente invia un form o clicca un link.
2.  **Controller:** Una Servlet intercetta la richiesta, valida i parametri e chiama un servizio (Model).
3.  **Model:** Esegue il calcolo o interroga il database e restituisce un oggetto (es. `Utente`).
4.  **Inoltro:** Il Controller salva l'oggetto `Utente` nel `request scope` e inoltra la richiesta alla JSP.
5.  **View:** La JSP legge l'oggetto dal `request` e genera l'HTML finale.
## Esempio di Controller (Servlet)
```java
public void doGet(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
    
    // 1. Logica di business (Model)
    String operazione = request.getParameter("op");
    DatoRisultato dati = MioService.calcola(operazione);
    
    // 2. Salvataggio dati per la View
    request.setAttribute("risultato", dati);
    
    // 3. Selezione della View (Forward)
    String view = "/risultato.jsp";
    if (dati == null) view = "/errore.jsp";
    
    RequestDispatcher dispatcher = request.getRequestDispatcher(view);
    dispatcher.forward(request, response);
}
```
## Vantaggi di MVC
*   **Separazione delle responsabilità:** Se voglio cambiare il database, tocco solo il Model. Se voglio cambiare il look, tocco solo la View.
*   **Supporto a più interfacce:** Posso usare lo stesso Model per servire una pagina HTML (View JSP) o una risposta JSON per un'App Mobile.
*   **Testabilità:** È molto più semplice testare la logica nel Model se non è mescolata ai tag HTML.
