La server-side template injection (SSTI) si verifica quando un aggressore è in grado di utilizzare la sintassi di template nativa per iniettare un payload dannoso in un template, che viene poi eseguito lato server.

I template engines sono progettati per generare pagine web combinando template fissi con dati volatili. Gli attacchi di SSTI possono verificarsi quando l'input dell'utente viene concatenato direttamente in un template, anziché essere passato come dati. Ciò consente agli aggressori di iniettare direttive di template arbitrarie per manipolare il template engine, spesso consentendo loro di assumere il controllo completo del server. Come suggerisce il nome, i payload del SSTI vengono consegnati e valutati lato server, rendendoli potenzialmente molto più pericolosi di una tipica injection di template lato client.

**Qual è l'impatto di una SSTI?** Le vulnerabilità dell’SSTI possono esporre i siti web a una varietà di attacchi a seconda del template engine in questione e di come esattamente l'applicazione lo utilizza. In alcune rare circostanze, queste vulnerabilità non rappresentano un rischio reale per la sicurezza. Tuttavia, la maggior parte delle volte, l'impatto dell’SSTI può essere catastrofico. Nella parte più grave della scala, un aggressore può potenzialmente ottenere l'esecuzione di codice remoto, assumendo il pieno controllo del server back-end e utilizzandolo per eseguire altri attacchi all'infrastruttura interna. Anche nei casi in cui l'esecuzione completa del codice remoto non è possibile, un aggressore può spesso utilizzare l’SSTI come base per numerosi altri attacchi, ottenendo potenzialmente l'accesso in lettura a dati sensibili e file arbitrari sul server.
### Come nascono le vulnerabilità di SSTI?
Le vulnerabilità di SSTI si verificano quando l'input dell'utente viene concatenato in template anziché essere trasmesso come dati. I template statici che forniscono semplicemente segnaposto in cui viene eseguito il rendering di contenuti dinamici non sono generalmente vulnerabili all'SSTI. L'esempio classico è un'e-mail che saluta ogni utente con il suo nome, come il seguente estratto da un template Twig:

`$output = $twig->render("Dear {first_name},", array("first_name" => $user.first_name) );`

Questo non è vulnerabile agli SSTI perché il nome dell'utente viene semplicemente trasmesso al template come dati. Tuttavia, poiché i template sono semplicemente stringhe, gli sviluppatori web a volte concatenano direttamente l'input dell'utente nei template prima del rendering. Prendiamo un esempio simile a quello sopra, ma questa volta gli utenti sono in grado di personalizzare parti dell'e-mail prima che venga inviata. Ad esempio, potrebbero essere in grado di scegliere il nome utilizzato:

`$output = $twig->render("Dear " . $_GET['name']);`

In questo esempio, invece di un valore statico passato al modello, parte del modello stesso viene generata dinamicamente utilizzando il parametro GET `name`. Poiché la sintassi del modello viene valutata lato server, questo consente potenzialmente a un aggressore di posizionare un payload di SSTI all'interno del parametro name come segue:

`http://vulnerable-website.com/?name={{bad-stuff-here}}`

Vulnerabilità come questa sono talvolta causate accidentalmente a causa di una progettazione scadente del modello da parte di persone non familiari con le implicazioni di sicurezza. Come nell'esempio sopra, potresti vedere diversi componenti, alcuni dei quali contengono input utente, concatenati e incorporati in un modello. In un certo senso, questo è simile alle vulnerabilità di SQLi che si verificano in istruzioni preparate scritte male.

Tuttavia, a volte questo comportamento è effettivamente implementato intenzionalmente. Ad esempio, alcuni siti web consentono deliberatamente a determinati utenti privilegiati, come gli editor di contenuti, di modificare o inviare modelli personalizzati in base alla progettazione. Ciò rappresenta chiaramente un enorme rischio per la sicurezza se un aggressore è in grado di compromettere un account con tali privilegi.
## Creazione di un attacco di SSTI
L'identificazione delle vulnerabilità legate al SSTI e l'elaborazione di un attacco riuscito comportano in genere il seguente processo di alto livello: Detect (rilevazione), Identify (identificazione), Exploit (sfruttamento).
### Detect (rilevazione)
Le vulnerabilità di SSTI spesso passano inosservate perché richiedono una ricerca mirata. Tuttavia, una volta individuate, possono essere facili da sfruttare, soprattutto in ambienti non sandbox. Il primo passo è il rilevamento, che può iniziare con il fuzzing: iniettare caratteri speciali nel template, come `${{<%[%'"}}%\,` per verificare se il server genera eccezioni, indicando una possibile vulnerabilità.

Le SSTI si verificano in due contesti distinti, ognuno con metodi di rilevamento specifici. Anche se il fuzzing non produce risultati conclusivi, è fondamentale testare approcci mirati per identificare e sfruttare correttamente la vulnerabilità.

**Code context**
Le vulnerabilità di SSTI possono verificarsi quando un input utente viene inserito in un’espressione di template, ad esempio come parametro in una funzione di rendering. Questo tipo di contesto è facilmente trascurabile durante un’analisi perché non produce sintomi evidenti come XSS e può sembrare simile a un semplice accesso a una hashmap.

Per rilevare SSTI in questi casi, si procede in due fasi:
- verificare che il parametro non sia vulnerabile a XSS, iniettando HTML arbitrario e osservando il risultato (vuoto, errori o tag codificati indicano assenza di XSS);
- tentare di uscire dalla sintassi predefinita con operatori tipici del linguaggio di templating usato, aggiungendo HTML arbitrario.

Se l’output include il codice HTML iniettato insieme al risultato atteso, è un segnale chiaro della presenza di SSTI.
### Identify (identificazione)
Dopo aver rilevato una potenziale vulnerabilità di SSTI, il passo successivo è identificare il template engine utilizzato. Molti linguaggi di templating hanno sintassi simili, progettate per evitare conflitti con i caratteri HTML, il che rende relativamente semplice testarli con payload di probing.

Abbiamo diversi approcci:
- **errore di sintassi:** inserire un’espressione non valida (es. `<%=foobar%>`). Se il server restituisce un messaggio di errore, questo può rivelare il motore e persino la sua versione. Ad esempio, un errore in ERB (Ruby) potrebbe includere riferimenti specifici al file di libreria.
- **payload specifici:** testare diverse sintassi di motori comuni (Twig, Jinja2, ERB, ecc.) e osservare come vengono interpretate. Alcuni payload possono funzionare in più motori, ad esempio `{{7*'7'}}` produce risultati diversi in Twig (49) e Jinja2 (7777777). Non affidarsi a un solo test: utilizzare più payload per ridurre il rischio di identificazioni errate.

Questo processo aiuta a restringere rapidamente le opzioni e ad adattare gli exploit al motore specifico.
### Exploit
Dopo aver rilevato l'esistenza di una potenziale vulnerabilità e aver identificato con successo il motore dei template, puoi iniziare a cercare modi per sfruttarla.

**Leggere la documentazione**
A meno che non si conosca già a menadito il template engine, leggere la sua documentazione è solitamente il primo punto da cui iniziare. Sebbene questo potrebbe non essere il modo più entusiasmante di trascorrere il proprio tempo, è importante non sottovalutare quanto utile possa essere la documentazione come fonte di informazioni.

Abbiamo diversi approcci:
- **imparare la sintassi di base del modello:** imparare la sintassi di base è ovviamente importante, insieme alle funzioni chiave e alla gestione delle variabili. Anche qualcosa di semplice come imparare a incorporare blocchi di codice nativi nel template può a volte portare rapidamente a un exploit. Ad esempio, una volta che sai che è in uso il motore di template Mako basato su Python, ottenere l'esecuzione di codice remoto potrebbe essere semplice come:
  ```python
  <%
   import os
   x=os.popen('id').read()
   %>
   ${x}
  ```
  In un ambiente non sandbox, ottenere l'esecuzione di codice remoto e utilizzarlo per leggere, modificare o eliminare file arbitrari è altrettanto semplice in molti motori di template comuni. 

- **leggere le implicazioni sulla sicurezza:** oltre a fornire le basi su come creare e utilizzare i modelli, la documentazione potrebbe anche fornire una sorta di sezione "Sicurezza". Il nome di questa sezione varierà, ma di solito delineerà tutte le cose potenzialmente pericolose che le persone dovrebbero evitare di fare con il modello. Questa può essere una risorsa inestimabile, fungendo persino da una sorta di promemoria per i comportamenti da cercare durante l'audit, nonché come sfruttarli. Anche se non esiste una sezione "Sicurezza" dedicata, se un particolare oggetto o funzione incorporata può rappresentare un rischio per la sicurezza, c'è quasi sempre un avviso di qualche tipo nella documentazione. L'avviso potrebbe non fornire molti dettagli, ma come minimo dovrebbe contrassegnare questo particolare incorporato come qualcosa da investigare. Ad esempio, in ERB, la documentazione rivela che è possibile elencare tutte le directory e quindi leggere file arbitrari come segue:
  `<%= Dir.entries('/') %>`
  `<%= File.open('/example/arbitrary-file').read %>`
  
- **cerca exploit noti:** un altro aspetto fondamentale dello sfruttamento delle vulnerabilità di iniezione di template lato server è essere bravi a trovare risorse aggiuntive online. Una volta che sei in grado di identificare il motore di template utilizzato, dovresti cercare sul web eventuali vulnerabilità che altri potrebbero aver già scoperto. Grazie all'uso diffuso di alcuni dei principali motori di template, a volte è possibile trovare exploit ben documentati che potresti essere in grado di modificare per sfruttare il tuo sito web di destinazione.

**Esplora l'ambiente**
A questo punto, potresti esserti già imbattuto in un exploit funzionante usando la documentazione. In caso contrario, il passo successivo è esplorare l'ambiente e provare a scoprire tutti gli oggetti a cui hai accesso.

Molti template engine espongono un oggetto "self" o "environment" di qualche tipo, che agisce come uno spazio dei nomi contenente tutti gli oggetti, i metodi e gli attributi supportati dal motore di template. Se tale oggetto esiste, puoi potenzialmente utilizzarlo per generare un elenco di oggetti che sono nell'ambito. Ad esempio, nei linguaggi di template basati su Java, a volte puoi elencare tutte le variabili nell'ambiente usando la seguente injection:

`${T(java.lang.System).getenv()}`

Ciò può costituire la base per la creazione di una breve lista di oggetti e metodi potenzialmente interessanti da investigare ulteriormente.

È importante tornare a notare che i siti web conterranno sia oggetti incorporati forniti dal modello sia oggetti personalizzati e specifici del sito forniti dallo sviluppatore web. Dovresti prestare particolare attenzione a questi oggetti non standard perché è particolarmente probabile che contengano informazioni sensibili o metodi sfruttabili. Poiché questi oggetti possono variare tra diversi modelli all'interno dello stesso sito web, tieni presente che potresti dover studiare il comportamento di un oggetto nel contesto di ogni modello distinto prima di trovare un modo per sfruttarlo.

Sebbene l'SSTI possa potenzialmente portare all'esecuzione di codice remoto e al controllo completo del server, in pratica ciò non è sempre possibile. Tuttavia, solo perché hai escluso l'esecuzione di codice remoto, ciò non significa necessariamente che non ci sia il potenziale per un diverso tipo di exploit. Puoi comunque sfruttare le vulnerabilità dell'SSTI per altri exploit di elevata gravità, come il Path Traversal, per ottenere l'accesso a dati sensibili.

**Crea un attacco personalizzato**
Quando exploit noti non funzionano, crea un attacco personalizzato analizzando il template engine e le sue funzioni accessibili. Identifica vulnerabilità secondarie, combina tecniche per aggirare sandbox e testa iterativamente i payload. Anche in sistemi sicuri, sfrutta piccoli errori per costruire exploit complessi ed efficaci.
## Come prevenire le vulnerabilità di SSTI
Il modo migliore per prevenire la SSTI è non consentire agli utenti di modificare o inviare nuovi template. Tuttavia, a volte ciò è inevitabile a causa di requisiti aziendali.

Uno dei modi più semplici per evitare di introdurre vulnerabilità di SSTI è utilizzare sempre un template engine "senza logica", come **Mustache**, a meno che non sia assolutamente necessario. Separare il più possibile la logica dalla presentazione può ridurre notevolmente l'esposizione agli attacchi basati su template più pericolosi.

Un'altra misura è quella di eseguire il codice degli utenti solo in un ambiente sandbox in cui i moduli e le funzioni potenzialmente pericolosi sono stati completamente rimossi. Sfortunatamente, il sandboxing del codice non attendibile è intrinsecamente difficile e soggetto a bypass.

Infine, un altro approccio complementare è accettare che l'esecuzione di codice arbitrario è quasi inevitabile e applicare il proprio sandboxing distribuendo il proprio ambiente di template in un contenitore Docker bloccato, ad esempio.
