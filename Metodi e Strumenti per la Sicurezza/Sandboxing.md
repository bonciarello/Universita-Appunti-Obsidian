Il **sandboxing** è un meccanismo di isolamento progettato per eseguire applicazioni (potenzialmente non affidabili) in un ambiente protetto, garantendo che eventuali errori o comportamenti malevoli non danneggino il resto del sistema.

## 1. Ambiti di applicazione
È fondamentale per:
*   Programmi arbitrari scaricati da Internet.
*   Codice che manipola dati complessi provenienti dalla rete.
*   Applicazioni che devono essere isolate per prevenire l'escalation dei privilegi.

## 2. Tecniche di Isolamento

### Isolamento del Sistema Operativo
Utilizza le primitive del kernel (UID/GID in UNIX) per isolare i processi. Tuttavia, questo isolamento lavora a grana grossa.

### Virtual Machine (VM)
Rappresentano l'isolamento più stringente. Ogni VM ha un proprio Sistema Operativo e risorse virtualizzate.
*   **Pro:** Se una VM è infetta, il malware è confinato. Il codice non deve essere modificato per essere "sandboxato".
*   **Contro:** Overhead di CPU/memoria. I driver dell'host possono essere un punto di vulnerabilità (vulnerabilità del monitor virtuale).

### Isolamento a livello di linguaggio e binario
Offre una grana più fine:
*   **JavaScript:** Isolamento logico all'interno del browser.
*   **Strumentazione Binaria (NativeClient):** Modifica o verifica il codice eseguibile per garantirne la sicurezza.

## 3. Sandboxing x86 nei Browser
L'obiettivo è eseguire codice nativo (C/C++) nel browser per ottenere alte prestazioni (giochi, editing video) o supportare app legacy, senza compromettere la sicurezza dell'host.

### Approcci storici e moderni:
1.  **Fiducia nello sviluppatore:** Chiedere all'utente di autorizzare codice firmato. *Limite:* L'utente spesso non è consapevole dei rischi.
2.  **Protezione Hardware:** Eseguire il codice in uno spazio utente sicuro. *Limite:* Richiede supporto specifico del kernel e dell'hardware, spesso affetti da vulnerabilità proprie.
3.  **Software Fault Isolation (SFI):** Verificare ogni istruzione del binario x86 prima dell'esecuzione. Questa è la tecnica usata in **Native Client (NaCl)** di Google.

## 4. Native Client (NaCl)
Garantisce che un modulo non esegua codice non autorizzato e non acceda a memoria fuori dai limiti.
*   **Verifica:** Il codice deve seguire regole rigide (es. nessuna istruzione privilegiata, allineamento delle istruzioni).
*   **Interfaccia:** Il codice sandboxato comunica con l'esterno solo tramite interfacce controllate (PPAPI), prevenendo chiamate di sistema dirette al kernel dell'host.
