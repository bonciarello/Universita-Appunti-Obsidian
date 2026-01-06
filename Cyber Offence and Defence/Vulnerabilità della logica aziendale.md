Le vulnerabilità della logica aziendale sono difetti nella progettazione e nell'implementazione di un'applicazione che consentono a un aggressore di suscitare comportamenti indesiderati manipolando funzionalità legittime.

Questi difetti sono spesso il risultato di una mancata previsione di stati insoliti dell'applicazione. Sono anche noti come **"difetti logici"**.

## Caratteristiche principali
*   **Invisibilità:** Spesso non vengono rilevati dagli scanner automatizzati perché richiedono la comprensione del dominio aziendale.
*   **Unicità:** Sono spesso specifici per l'applicazione e la sua funzionalità.
*   **Origine:** Nascono da ipotesi errate su come gli utenti interagiranno con l'applicazione.

## Esempi di vulnerabilità della logica

### 1. Fiducia eccessiva nei controlli lato client
Gli sviluppatori assumono che gli utenti interagiranno solo tramite l'interfaccia fornita. Un aggressore può intercettare e modificare i dati (es. tramite **Burp Suite**) dopo la convalida del browser ma prima che raggiungano il server.

### 2. Mancata gestione di input non convenzionali
L'applicazione non prevede valori fuori dai limiti normali.
*   **Esempio:** Un trasferimento bancario che non controlla se l'importo è negativo. Inviare un valore negativo potrebbe sottrarre fondi dal destinatario e aggiungerli al mittente, aggirando i controlli sul saldo.
*   **Test:** Inviare numeri eccezionalmente alti/bassi o stringhe anormalmente lunghe.

### 3. Difetti specifici del dominio
Legati allo scopo specifico del sito.
*   **Esempio (Shopping):** Applicare uno sconto per ordini superiori a `$1000` e poi rimuovere articoli dal carrello mantenendo lo sconto attivo.
*   **Esempio (Social):** Trovare un modo logico per forzare un gran numero di utenti a seguirti.

### 4. Fornitura di un oracolo di crittografia
L'applicazione permette agli utenti di inserire dati che vengono crittografati e restituiti. Un attaccante può usare questa funzione per generare input cifrati validi da usare in altre parti sensibili del sistema.

### 5. Discrepanze del parser email
Differenze nel modo in cui diverse parti dell'applicazione (es. validatore vs server email) interpretano un indirizzo email, permettendo registrazioni non autorizzate.

## Come prevenire le vulnerabilità della logica
1.  **Comprensione del dominio:** Assicurarsi che sviluppatori e tester comprendano appieno le regole aziendali.
2.  **Evitare ipotesi:** Non assumere mai che l'utente si comporti in modo "normale".
3.  **Logica lato server:** Implementare sempre controlli di integrità e convalida robusti sul server.
4.  **Codice chiaro:** Scrivere codice leggibile per facilitare l'individuazione di falle logiche.
5.  **Documentazione:** Annotare tutte le ipotesi fatte durante la progettazione di transazioni e flussi di lavoro.