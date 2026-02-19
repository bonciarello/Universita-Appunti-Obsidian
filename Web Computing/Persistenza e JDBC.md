---
aliases: [Persistenza JDBC, PJ]
tags: [web-computing]
---
La **persistenza** è la capacità di un'applicazione di salvare i dati in modo che sopravvivano alla chiusura del programma. Nelle Java Web App, questo avviene solitamente tramite [[Database|database]] relazionali (RDBMS) e la libreria **JDBC**.
## Il Conflitto di Impedenza (Impedance Mismatch)
Esiste una differenza strutturale tra il mondo degli oggetti (Java) e quello delle relazioni (SQL):
*   **Struttura:** Java usa oggetti e riferimenti; SQL usa tabelle e chiavi esterne.
*   **Accesso:** In Java navighiamo tra oggetti (`gruppo.getStudenti()`); in SQL usiamo le `JOIN`.
*   **Tipi:** Alcuni tipi Java non hanno un corrispettivo diretto in SQL (es. collezioni, tipi personalizzati).
## Strategie di Persistenza
### 1. Forza Bruta
Scrivere query SQL direttamente all'interno della logica di business.
*   **Svantaggio:** Codice disordinato, difficile da mantenere e testare.
### 2. Pattern DAO (Data Access Object) - Consigliato
Si separa la logica di accesso ai dati dal resto dell'app.
*   **DTO (Data Transfer Object):** Classi semplici (JavaBeans) che contengono i dati (es. classe `Studente`).
*   **DAO:** Interfacce e classi che implementano le operazioni **CRUD** (Create, Retrieve, Update, Delete).
### 3. ORM (Object-Relational Mapping)
Framework come **Hibernate** o **JPA** che automatizzano la conversione tra oggetti e tabelle.
## JDBC (Java [[Database]] Connectivity)
JDBC è l'API standard di Java per interagire con i [[Database|database]].
### Esempio di CRUD (Retrieve)
```java
public Studente findByPrimaryKey(String matricola) {
    Connection conn = dataSource.getConnection();
    Studente s = null;
    String query = "SELECT * FROM studente WHERE matricola = ?";

    try (PreparedStatement st = conn.prepareStatement(query)) {
        st.setString(1, matricola);
        ResultSet rs = st.executeQuery();
        
        if (rs.next()) {
            s = new Studente();
            s.setMatricola(rs.getString("matricola"));
            s.setNome(rs.getString("nome"));
            // ...
        }
    } catch (SQLException e) { e.printStackTrace(); }
    return s;
}
```
## Tecniche Avanzate
### Lazy Load (Caricamento Pigro)
Per evitare di caricare migliaia di record inutilmente (es. caricare tutti gli studenti ogni volta che guardo un Gruppo), si usa il caricamento *on-demand*.
*   **Pattern Proxy:** Si restituisce un "finto" oggetto (Proxy) che interroga il DB solo quando viene chiamato il metodo `getStudenti()`.
### Chiavi Surrogate e IdBroker
Invece di usare chiavi naturali (come il Codice Fiscale), si preferiscono chiavi numeriche generate dal sistema (**chiavi surrogate**).
*   **IdBroker:** Una classe o procedura che garantisce la generazione di ID univoci (es. tramite `SEQUENCE` in PostgreSQL o `AUTO_INCREMENT` in [[MySQL]]).
### Aggiornamenti in Cascata
Quando si salva un oggetto complesso (es. un `Gruppo` con una lista di `Studenti`), il DAO deve preoccuparsi di salvare ricorsivamente tutti i figli che non sono ancora presenti nel DB.
```java
public void save(Gruppo g) {
    // 1. Salva il gruppo e ottieni l'ID
    // 2. Itera sugli studenti del gruppo
    for (Studente s : g.getStudenti()) {
        if (studenteDao.exists(s.getMatricola())) {
            studenteDao.update(s);
        } else {
            studenteDao.insert(s);
        }
    }
}
```
