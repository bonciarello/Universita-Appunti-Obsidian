## 1. Progettazione Security-Aware
L'approccio corretto consiste nell'integrare la sicurezza fin dall'inizio (**Security by Design**), non come un'aggiunta finale.
Le quattro fasi principali sono:
1.  **Requisiti:** Definire cosa proteggere.
2.  **Progetto:** Scegliere l'architettura.
3.  **Implementazione:** Scrivere codice sicuro.
4.  **Test/Garanzia:** Verificare la resistenza del sistema.

### Software vs Hardware
*   **Software:** Facile da modificare ma vulnerabile e con performance inferiori.
*   **Hardware:** Difficile da cambiare ma offre vantaggi di sicurezza non facilmente bypassabili.
    *   **AES-NI:** Istruzioni Intel per la crittografia veloce.
    *   **Intel SGX:** Supporto per il calcolo crittografato nel cloud.
    *   **Intel MPX:** Protezione della memoria.
    *   **Funzioni Fisiche non Clonabili (PUF):** Sorgente di casualità hardware unica per macchina.

## 2. Casi di Abuso (Abuse Cases)
A differenza dei casi d'uso, descrivono ciò che il sistema **non deve permettere**.
*   *Esempio:* Un utente che riesce a fingere di essere un manager per cambiare i tassi di interesse.
*   **Esempi di rimozione misure:**
    *   File password non cifrato $\rightarrow$ furto di tutte le credenziali.
    *   Mancanza di Nonce $\rightarrow$ **Replay attack** su transazioni bancarie.

## 3. Principi di Progettazione
*   **Prevenzione:** Eliminare i difetti alla radice.
*   **Mitigazione:** Ridurre il danno se un difetto viene sfruttato.
*   **Rilevamento e Recupero:** Identificare l'attacco e ripristinare il sistema.

### Miti della Sicurezza
*   **"HTTPS risolve tutto":** Falso. Protegge solo il transito. Bug applicativi (SQLi, XSS) persistono anche sotto HTTPS.
*   **"robots.txt impedisce l'accesso":** Falso. È solo una guida per i crawler onesti, non un controllo di accesso.
*   **"Usiamo le blacklist, siamo sicuri":** Falso. Sono facili da creare ma incomplete contro attacchi nuovi o varianti.
*   **"Il cloud è sicuro di per sé":** Falso. La sicurezza è una responsabilità condivisa (SLA).
*   **"L'API interna è nascosta":** Falso. Se l'app web può accedervi, un attaccante che controlla l'app può fare lo stesso.

## 4. Architettura di Sicurezza di Google
Google utilizza una filosofia basata sull'**Isolamento** e sull'attività di guardia **AAA (Authentication, Authorization, Accounting)**.

### Caratteristiche principali:
*   **Piattaforma:** Pool di data center interconnessi.
*   **GFE (Google Front-End):** Gestisce le connessioni in ingresso trasformandole in formati sicuri.
*   **RPC Layer:** Tutte le comunicazioni interne sono crittografate.
*   **Titan (Chip di sicurezza):** Ogni macchina Google ha un chip Titan che firma l'hash del BIOS e del SO all'avvio per garantire che l'infrastruttura sia fidata.

## 5. Gestione della Disponibilità (DoS Mitigation)
*   Distribuzione del carico su enormi risorse.
*   **Autenticare il prima possibile.**
*   Minimizzare il consumo di risorse prima dell'autenticazione.
*   Priorità alle risorse per gli utenti già autenticati.
