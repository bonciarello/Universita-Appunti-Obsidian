## Che cos'è un coding assistant
Un **coding assistant** non è solo uno strumento che scrive codice, ma *un sistema avanzato basato su modelli linguistici capace di gestire attività complesse*.

Funziona seguendo **tre fasi principali**:
1. Comprendere il contesto (errore, file, codice coinvolto)
2. Pianificare una soluzione
3. Eseguire le modifiche

Il punto cruciale è che, per operare davvero, deve interagire con l’esterno (file, comandi, documentazione), cosa che i modelli linguistici da soli non possono fare.

Per risolvere questo limite si usa il **tool use**, un *sistema che permette al modello di richiedere azioni* (come leggere file o eseguire comandi) *tramite istruzioni speciali*.

Alcuni modelli sono particolarmente efficaci nell’uso degli strumenti, offrendo vantaggi come *gestione di task complessi*, *maggiore estensibilità* e *migliore sicurezza*.
## Aggiungere contesto
La **gestione del contesto** è fondamentale quando si lavora con Claude su progetti di coding: fornire troppe informazioni inutili peggiora le prestazioni, mentre fornire solo quelle rilevanti le migliora.

Elementi chiave:
- Il comando `/init` analizza il progetto e genera un file `CLAUDE.md` con un riepilogo di struttura, comandi e convenzioni.
- Il file `CLAUDE.md` funge da guida persistente per Claude e può essere personalizzato.
- Esistono tre livelli di configurazione:
    - **globale** (per tutti i progetti);
    - di progetto condiviso;
    - **locale personale**.
- È possibile aggiungere istruzioni personalizzate (anche tramite “memory mode” con #);
- Con il simbolo `@` si possono includere file specifici nel contesto;
- I file citati nel `CLAUDE.md` vengono automaticamente inclusi in ogni richiesta.
## Fare modifiche
Quando lavori con Claude su progetti esistenti, è importante comunicare in modo preciso e scegliere il giusto approccio in base alla complessità del task.

Elementi chiave:
- gli **screenshot** sono molto efficaci per indicare esattamente cosa modificare nell’interfaccia;
- la **Planning Mode** serve per task complessi che richiedono analisi del progetto: Claude esplora i file, crea un piano e aspetta conferma (utile per modifiche strutturali e multi-file);
- le **Thinking Modes** regolano il livello di ragionamento (da base fino a “Ultrathink”) (utile per logica complessa e debugging);
- possono essere combinati, ma consumano più risorse (token)
## Controllare il contesto
Per lavorare efficacemente con Claude su task complessi è fondamentale controllare il flusso della conversazione.

Elementi chiave:
- **Escape**: interrompe la risposta per correggere la direzione o focalizzare il task.
- **Escape + memory (#)**: utile per correggere errori ricorrenti e evitarli in futuro.
- **Rewind (Escape x2)**: permette di tornare indietro nella conversazione eliminando contesto inutile.
- **/compact**: comprime la conversazione mantenendo le informazioni importanti.
- **/clear**: resetta completamente il contesto.
## Skills
Claude Code permette di creare **comandi personalizzati** per automatizzare task ripetitivi.

Elementi chiave:
- i comandi si definiscono creando file `.md` nella cartella .claude/commands.
- il nome del file diventa il comando (es. `audit.md` → `/audit`).
- possono eseguire workflow completi (audit, fix, test, ecc.).
- supportano **argomenti dinamici** tramite `$ARGUMENTS`.
## Server MCP
Claude Code può essere esteso con **MCP (Model Context Protocol) servers**, che forniscono nuovi strumenti e capacità al modello.

Esempio:
- **MCP Server più popolare** è Playwright che permette a Claude di controllare un browser per test e generazione di componenti;
- **Installazione:** `claude mcp add playwright npx @playwright/mcp@latest`;
- **Gestione permessi:** aggiungere `"mcp__playwright"` all’array `allow` in `.claude/settings.local.json` evita prompt ripetuti.
- **Esempio pratico:** Claude può aprire un browser, generare un componente, analizzarne lo stile e aggiornare prompt di generazione per migliorare il design.
- **Vantaggi:** risultati visivi più creativi, prompt ottimizzati, design asimmetrici, gradienti originali.
- **Altri MCP Servers:** database, API testing, filesystem, cloud, automazioni strumenti di sviluppo.
## Integrazione GitHub
Claude Code offre una **integrazione ufficiale con GitHub Actions**, che permette a Claude di lavorare direttamente all’interno delle **pull request** e delle **issue**. Con questa integrazione, puoi installare facilmente l’app Claude sul tuo repository, aggiungere la **API key** e generare automaticamente un **pull request** che contiene i workflow di default. Una volta unito, questi workflow vengono inseriti nella cartella `.github/workflows` e sono subito operativi.

Quando menzioni **@claude** in un’**issue** o in una **pull request**, Claude analizza la richiesta, crea un **piano di task** e lo esegue con pieno accesso alla tua **codebase**, rispondendo direttamente nella conversazione. Allo stesso modo, quando crei una **pull request**, Claude effettua una **review automatica** delle modifiche, analizzando il loro **impatto** e generando un **report dettagliato**.

Puoi anche **personalizzare i workflow** per il tuo progetto, aggiungendo step di **setup** dell’ambiente, fornendo **istruzioni personalizzate** (`custom_instructions`) per spiegare a Claude come è strutturato il progetto, configurando eventuali **MCP server** e definendo in maniera esplicita tutti gli **strumenti consentiti** (`allowed_tools`). È importante testare prima task semplici e poi quelli complessi, per assicurarsi che Claude lavori correttamente.
## Introduzione agli hooks
Claude Code permette di usare i **Hooks**, strumenti che ti consentono di eseguire comandi **prima** o **dopo** che Claude utilizzi un **tool**. Questa funzionalità è particolarmente utile per creare **workflow automatizzati**, come applicare **code formatter** dopo modifiche ai file, eseguire **test** quando i file cambiano o limitare l’accesso a determinati file.

Il funzionamento dei **Hooks** si inserisce nel normale flusso di Claude: quando invii una richiesta, il modello decide se usare uno strumento e Claude Code lo esegue. I **PreToolUse Hooks** vengono eseguiti **prima** dell’uso del tool, mentre i **PostToolUse Hooks** agiscono **dopo** l’esecuzione.

I **Hooks** si configurano nei file di impostazioni di Claude, a livello **globale** (`~/.claude/settings.json`), **progetto condiviso** (`.claude/settings.json`) o **personale** (`.claude/settings.local.json`). Possono essere scritti a mano o creati usando il comando /hooks dentro Claude Code.

Un **PreToolUse Hook** può, ad esempio, intercettare il tool Read e decidere se **consentire** l’operazione o **bloccarla**, mentre un **PostToolUse Hook** può intervenire su strumenti come Write o Edit per eseguire operazioni aggiuntive come **formattare automaticamente** un file appena modificato.

Tra le applicazioni pratiche dei **Hooks** ci sono:
- **formattazione del codice**: applicare style o linting dopo le modifiche;
- **testing automatico**: eseguire test su file modificati;
- **controllo accessi**: bloccare Claude dall’interagire con file sensibili;
- **qualità del codice**: far girare linters o type checker e fornire feedback;
- **logging**: tracciare quali file sono stati letti o modificati;
- **validazione**: controllare convenzioni di nomi o standard di codice.