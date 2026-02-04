Un **privilegio** è la capacità di accedere o modificare una risorsa. Se un'applicazione compromessa ha privilegi elevati, l'attaccante potrà manipolare l'intero sistema. La **Privilege Separation** mira a ridurre questo rischio aumentando la granularità del controllo degli accessi.

## 1. Principio del Minimo Privilegio
Il concetto cardine è: *"Ogni modulo di sistema dovrebbe avere solo i privilegi minimi necessari per compiere il proprio lavoro"*.
*   Dividendo l'applicazione in moduli isolati, se un attaccante controlla il modulo $M_1$, potrà accedere solo alle risorse $D_1$ associate ad esso, senza compromettere il resto del sistema.

## 2. Struttura di Controllo
Il modello standard prevede un **Reference Monitor** che intercepte ogni richiesta di accesso a una risorsa. Il monitor valuta:
1.  Il **processo** richiedente.
2.  La **richiesta** specifica.
3.  La **policy** di sicurezza.

## 3. Meccanismi di UNIX
In UNIX, i soggetti attivi sono i **principals** (processi identificati da User ID - **UID** e Group IDs - **GID**).
*   **Root (UID 0):** Può bypassare quasi tutti i controlli.
*   **Oggetti protetti:** File, socket, memoria, altri processi.

### Access Control List (ACL) e Permessi
Il kernel consulta le ACL per decidere se un processo può operare su un oggetto.
*   **Inode:** Struttura associata a file/directory contenente UID proprietario, GID e permessi (rwx) per proprietario, gruppo e altri (rappresentati in ottale, es. `644`).
*   **chroot:** Cambia la directory root apparente per un processo, confinandolo in una porzione del file system.
*   **File Descriptors:** Funzionano come ticket di accesso ottenuti all'apertura del file. Possono essere passati tra processi tramite socket.

## 4. Architetture a confronto: Apache vs OKWS

### Il problema di Apache
In architetture come Apache, se un componente (es. un modulo PHP) è compromesso tramite SQL Injection o Buffer Overflow, l'attaccante ottiene i privilegi del server web, potendo accedere a sorgenti, file nascosti o eseguire codice remoto.

### OKWS (OK Web Server)
Progettato per la massima sicurezza (siti di dating), OKWS divide le funzionalità in moduli specializzati:
*   **Dispatcher (okd):** Gestisce le richieste.
*   **Logger (oklogd):** Registra i login.
*   **Template Generator (pubd):** Genera le pagine.
*   **Launcher (okld):** L'unico che opera come **root** per avviare gli altri moduli.
*   **dbproxy:** Un intermediario critico che isola il database.

**Vantaggi di OKWS:**
*   Ogni modulo ha un UID/GID distinto ed è confinato da `chroot`.
*   Comunicazione tramite UNIX domain socket.
*   Il **dbproxy** permette solo query predefinite tramite template SQL e autorizza le operazioni tramite token (20 byte). Se un modulo è compromesso, può eseguire solo le query associate al suo token.

## 5. Capabilities
Mentre le ACL si basano sull'identità dell'utente (chi sei?), le **Capabilities** si basano sul possesso di un ticket (cosa hai?).
*   **Capability:** Una sequenza casuale di bit che funge da gestore per una risorsa.
*   **Vantaggi:** Il monitor non deve conoscere l'identità del processo; le capabilities possono essere trasferite a runtime.
*   **Svantaggi:** La revoca è molto più complessa rispetto alle ACL.

## 6. Role-Based Access Control (RBAC)
Utilizzato tipicamente nei DBMS, introduce una gerarchia di ruoli. Gli utenti non ricevono permessi direttamente, ma vengono assegnati a ruoli, semplificando la gestione di sistemi complessi.
