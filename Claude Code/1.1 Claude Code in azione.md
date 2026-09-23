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

Ogni server MCP deve essere esplicitamente autorizzato, strumento per strumento (o server per server tramite wildcard). Il sistema funziona su **due livelli**:
1. Il server MCP deve essere configurato/abilitato
2. I suoi strumenti devono essere esplicitamente elencati in `--allowedTools`

In pratica si usano pattern con wildcard, ad esempio `mcp__github__*` per autorizzare tutti gli strumenti del server GitHub, `mcp__atlassian__*` per Atlassian, ecc. — ma non esiste un unico permesso globale che copra tutto automaticamente.
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
## Definire un hooks
Claude Code permette di usare i **Hooks** per intercettare e controllare le chiamate agli strumenti (**tool calls**) prima o dopo la loro esecuzione, offrendo un controllo molto preciso su ciò che Claude può fare all’interno dell’ambiente di sviluppo.

Per costruire un **Hook**, devi prima scegliere se utilizzare un **PreToolUse** o un **PostToolUse**: il primo consente di **bloccare** l’esecuzione di un tool prima che avvenga, mentre il secondo interviene **dopo** che l’operazione è stata completata. Successivamente, devi definire quali **tool** monitorare, specificando esattamente quali chiamate devono attivare il comportamento del tuo hook.

Il cuore del sistema è un **comando personalizzato** che riceve informazioni sulla tool call sotto forma di **JSON** tramite standard input. Questo JSON contiene dettagli come il **nome del tool**, i **parametri di input** e informazioni sulla sessione. Il comando deve quindi analizzare questi dati e decidere come comportarsi.

Il controllo avviene tramite gli **exit code**: se il comando termina con codice **0**, l’operazione viene consentita; se termina con codice **2** (solo nei PreToolUse), la chiamata viene **bloccata**. In questo caso, eventuali messaggi scritti nello standard error vengono inviati a Claude come **feedback**, spiegando il motivo del blocco.

Un caso d’uso tipico è la protezione di file sensibili, come i file **.env**. Monitorando tool come **Read** o **Grep**, puoi impedire a Claude di accedere a percorsi riservati, mantenendo comunque una comunicazione chiara sul perché l’azione è stata negata.
## Implementare un hooks
Questo esempio mostra come costruire un **Hook personalizzato** in Claude Code per impedire l’accesso a file sensibili come i **.env**, proteggendo così variabili d’ambiente e dati riservati durante lo sviluppo.

Per ottenere questo comportamento, si utilizza un **PreToolUse Hook**, che permette di intercettare le chiamate agli strumenti **prima** che vengano eseguite. La configurazione viene definita nel file `.claude/settings.local.json`, dove si specificano due elementi fondamentali: il **matcher**, che indica quali tool monitorare (in questo caso **Read** e **Grep**), e il **command**, ovvero lo script che verrà eseguito quando questi tool vengono chiamati.

Quando Claude tenta di utilizzare uno strumento, il tuo hook riceve i dettagli della richiesta sotto forma di **JSON**, inclusi il **nome del tool**, i **parametri** e il **percorso del file**. Lo script legge questi dati dallo standard input e verifica se il percorso contiene riferimenti a file sensibili, come **.env**.
  
Se viene rilevato un accesso non autorizzato, lo script restituisce un **exit code 2**, bloccando l’operazione e inviando un messaggio di errore a Claude tramite lo standard error. Claude interpreta questo feedback e informa l’utente che l’azione è stata impedita da un hook.

Una volta configurato e riavviato Claude Code, puoi testare il comportamento chiedendo di leggere il file .env: il sistema intercetterà la richiesta e la bloccherà automaticamente, sia nel caso di accesso diretto (**Read**) sia di ricerca (**Grep**).

Questo approccio offre diversi vantaggi: garantisce una **protezione preventiva** dei dati sensibili, mantiene una comunicazione **trasparente** con Claude, supporta più strumenti grazie al matcher e fornisce **feedback chiari**. Inoltre, può essere facilmente esteso per proteggere altri file o directory, implementando regole di sicurezza più avanzate.
## Problematiche di configurazione degli hooks
Un punto importante quando si lavora con gli **hooks** in Claude Code riguarda alcune **problematiche di configurazione e sicurezza**, spesso definite come _gotchas_.

La documentazione raccomanda di usare sempre **percorsi assoluti** per gli script degli hook invece di percorsi relativi. Questo perché i percorsi assoluti riducono i rischi di **path interception** e **binary planting**, aumentando la sicurezza dell’esecuzione.

Tuttavia, questa scelta introduce un problema pratico: i file di configurazione diventano difficili da **condividere tra sviluppatori**, perché ogni macchina ha un diverso percorso assoluto del progetto. In altre parole, lo stesso hook funzionerebbe solo sulla macchina su cui è stato configurato.

Per risolvere questo problema si utilizza un file **settings.example.json**, che contiene i riferimenti agli script usando un placeholder chiamato **$PWD** (present working directory). Questo permette di mantenere una configurazione generica e portabile.

Quando viene eseguito un progetto su un nuovo computer, uno script di inizializzazione (**init-claude.js**) sostituisce automaticamente il placeholder **$PWD** con il percorso assoluto reale della macchina in uso. Successivamente copia il file di esempio e lo rinomina in **settings.local.json**, generando così una configurazione valida per quell’ambiente specifico.  

Questo approccio consente di combinare due esigenze fondamentali: da un lato la **sicurezza**, garantita dall’uso di percorsi assoluti, e dall’altro la **portabilità**, che permette di condividere facilmente la configurazione tra diversi sviluppatori senza modifiche manuali.
## Claude Code SDK
Il **Claude Code SDK** permette di eseguire Claude Code in modo **programmatico** all’interno di applicazioni e script, estendendo le sue capacità oltre l’uso da terminale. È disponibile per **TypeScript**, **Python** e tramite **CLI**, e utilizza esattamente lo stesso motore e gli stessi strumenti della versione standard, rendendolo ideale per integrazioni in workflow più complessi.

Una caratteristica importante è che l’SDK eredita automaticamente tutte le **configurazioni** e i **settings** presenti nella directory del progetto, garantendo coerenza tra utilizzo manuale e automatizzato. Inoltre, per impostazione predefinita opera in modalità **read-only**, quindi può leggere file, eseguire ricerche e analizzare il codice, ma non può modificarlo senza esplicita autorizzazione.

L’interazione con l’SDK avviene tramite una funzione come **query**, che invia un **prompt** a Claude e restituisce i messaggi della conversazione passo dopo passo. Questo consente di osservare direttamente il flusso di ragionamento e ottenere una risposta finale strutturata.

Se necessario, è possibile abilitare operazioni di scrittura specificando i **permessi** tramite allowedTools, oppure configurando l’accesso direttamente nei file di impostazione di Claude. Questo permette di controllare con precisione quali azioni Claude può eseguire all’interno del sistema.

Il vero valore del **Claude Code SDK** emerge quando viene integrato in **pipeline di sviluppo** più ampie. Può essere utilizzato, ad esempio, per creare **git hooks** che analizzano automaticamente il codice, script di **build** che ottimizzano le performance, strumenti per la **manutenzione del codice**, generazione automatica di **documentazione** o controlli di qualità all’interno di pipeline **CI/CD**.