L'**OWASP** (Open Web Application Security Project) è un'organizzazione fondamentale per la web security che stila periodicamente la classifica dei 10 rischi più critici per le applicazioni industriali.

## 1. Classifica 2021 (Principali Rischi)

### A01:2021 – Broken Access Control
Si verifica quando non vengono verificati i permessi di accesso alle risorse.
*   **Scenari:** Utilizzo di parametri non controllati in una query (es. cambiare `account_id` nell'URL per vedere dati altrui) o accesso diretto a URL amministrativi senza privilegi.
*   **Impatto:** L'attaccante può visualizzare, modificare o eliminare dati e compiere azioni non autorizzate.

### A02:2021 – Cryptographic Failures
Precedentemente noto come Sensitive Data Exposure.
*   **Scenari:** Crittografia automatica del database che però decifra i dati subito dopo il recupero, permettendo a una SQLi di leggere i dati in chiaro. Mancanza di TLS o uso di algoritmi di cifratura obsoleti.

### A03:2021 – Injection
Include SQL, NoSQL, OS e LDAP injection.
*   **Vettori:** Qualsiasi sorgente di dati non fidata (parametri URL, input form, header) che viene interpretata come comando o query.

### A04:2021 – Insecure Design
Si concentra sui difetti di architettura. Un'implementazione perfetta non può risolvere un progetto intrinsecamente insicuro.
*   **Scenario:** Un sistema di prenotazione cinema che permette di bloccare centinaia di posti senza deposito, causando perdite finanziarie (Business Logic flaw).

### A05:2021 – Security Misconfiguration
Errori di configurazione comuni.
*   **Scenari:** Lasciare applicazioni di esempio o password di default (`admin/admin`). Directory listing abilitato (permette di scaricare file `.class` e decompilarli). Messaggi d'errore troppo dettagliati che rivelano versioni software vulnerabili.

### A08:2021 – Software and Data Integrity Failures
Relativo al codice e all'infrastruttura che non proteggono l'integrità.
*   **Scenari:** Aggiornamenti firmware non firmati. **Deserializzazione non sicura** (es. inviare oggetti Java serializzati tra client e server; un attaccante può usare strumenti come *Java Serial Killer* per eseguire codice remoto).

### A10:2021 – Server Side Request Forgery (SSRF)
La gravità aumenta con l'uso dei servizi cloud.
*   **Scenario:** Un attaccante usa il server vulnerabile per scansionare le porte della rete interna o accedere ai metadati dell'istanza cloud (es. IP `169.254.169.254` di AWS) per rubare credenziali IAM.
