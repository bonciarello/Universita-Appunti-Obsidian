---
aliases: [SSRF, Server-side request forgery]
tags: [cyber-offence-and-defence]
---
La falsificazione delle richieste lato server (SSRF, Server-side request forgery) è una vulnerabilità della [[Sicurezza|sicurezza]] web che consente a un aggressore di indurre l'applicazione lato server a inviare richieste a una posizione non desiderata.

In un tipico attacco SSRF, l'aggressore potrebbe far sì che il server stabilisca una connessione a servizi interni all'interno dell'infrastruttura dell'organizzazione. In altri casi, potrebbe essere in grado di forzare il server a connettersi a sistemi esterni arbitrari. Ciò potrebbe far trapelare dati sensibili, come le credenziali di autorizzazione.
### Qual è l'impatto degli attacchi SSRF?
Un attacco SSRF riuscito può spesso causare azioni non autorizzate o accesso ai dati all'interno dell'organizzazione. Ciò può avvenire nell'applicazione vulnerabile o in altri sistemi back-end con cui l'applicazione può comunicare. In alcune situazioni, la vulnerabilità SSRF potrebbe consentire a un aggressore di eseguire comandi arbitrari.

Un exploit SSRF che causa connessioni a sistemi di terze parti esterne potrebbe causare attacchi dannosi. Questi possono sembrare originati dall'organizzazione che ospita l'applicazione vulnerabile.
## Attacchi SSRF comuni
Gli attacchi SSRF spesso sfruttano le relazioni di fiducia per intensificare un attacco dall'applicazione vulnerabile ed eseguire azioni non autorizzate. Queste relazioni di fiducia potrebbero esistere in relazione al server o in relazione ad altri sistemi back-end all'interno della stessa organizzazione.
### Attacchi SSRF contro il server
In un attacco SSRF contro il server, l'aggressore fa sì che l'applicazione effettui una richiesta HTTP al server che ospita l'applicazione, tramite la sua interfaccia di rete loopback. Ciò comporta in genere la fornitura di un URL con un nome host come `127.0.0.1` (un indirizzo IP riservato che punta all'adattatore loopback) o `localhost` (un nome comunemente utilizzato per lo stesso adattatore).

Ad esempio, immagina un'applicazione di shopping che consente all'utente di visualizzare se un articolo è disponibile in magazzino in un particolare negozio. Per fornire le informazioni sulle scorte, l'applicazione deve interrogare varie API REST back-end. Lo fa passando l'URL all'endpoint API back-end pertinente tramite una richiesta HTTP front-end. Quando un utente visualizza lo stato delle scorte di un articolo, il suo browser effettua la seguente richiesta:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
```

Ciò fa sì che il server effettui una richiesta all'URL specificato, recuperi lo stato delle scorte e lo restituisca all'utente.

In questo esempio, un aggressore può modificare la richiesta per specificare un URL locale al server:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://localhost/admin
```

Il server recupera il contenuto dell'URL `/admin` e lo restituisce all'utente.

Un aggressore può visitare l'URL `/admin`, ma la funzionalità amministrativa è normalmente accessibile solo agli utenti autenticati. Ciò significa che un aggressore non vedrà nulla di interessante. Tuttavia, se la richiesta all'URL `/admin` proviene dalla macchina locale, i normali controlli di accesso vengono ignorati. L'applicazione concede l'accesso completo alla funzionalità amministrativa, perché la richiesta sembra provenire da una posizione attendibile.

Perché le applicazioni si comportano in questo modo e si fidano implicitamente delle richieste che provengono dalla macchina locale? Questo può verificarsi per vari motivi:
- il controllo di accesso potrebbe essere implementato in un componente diverso che si trova di fronte al server dell'applicazione. Quando viene effettuata una connessione al server, il controllo viene ignorato;
- per scopi di disaster recovery, l'applicazione potrebbe consentire l'accesso amministrativo senza effettuare l'accesso a qualsiasi utente proveniente dalla macchina locale. Ciò fornisce un modo per un amministratore di ripristinare il sistema se perde le proprie credenziali. Ciò presuppone che solo un utente completamente fidato arriverebbe direttamente dal server;
- l'interfaccia amministrativa potrebbe ascoltare su un numero di porta diverso per l'applicazione principale e potrebbe non essere raggiungibile direttamente dagli utenti.

Questo tipo di relazioni di fiducia, in cui le richieste provenienti dalla macchina locale vengono gestite in modo diverso rispetto alle richieste ordinarie, spesso rendono SSRF una vulnerabilità critica.
### Attacchi SSRF contro altri sistemi back-end
In alcuni casi, il server applicativo è in grado di interagire con sistemi back-end che non sono direttamente raggiungibili dagli utenti. Questi sistemi hanno spesso indirizzi IP privati non instradabili. I sistemi back-end sono normalmente protetti dalla topologia di rete; quindi, hanno spesso un blocco di [[Sicurezza|sicurezza]] più debole. In molti casi, i sistemi back-end interni contengono funzionalità sensibili a cui chiunque sia in grado di interagire con i sistemi può accedere senza autenticazione.

Nell'esempio precedente, immagina che ci sia un'interfaccia amministrativa all'URL back-end `https://192.168.0.68/admin`. Un aggressore può inviare la seguente richiesta per sfruttare la vulnerabilità SSRF e accedere all'interfaccia amministrativa:

```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://192.168.0.68/admin
```
## Blind SSRF
Le vulnerabilità blind SSRF si verificano quando un'applicazione può essere indotta a inviare una richiesta HTTP back-end a un URL fornito, ma la risposta dalla richiesta back-end non viene restituita nella risposta front-end dell'applicazione.

**Qual è l'impatto delle vulnerabilità blind SSRF?** L'impatto delle vulnerabilità blind SSRF è spesso inferiore a quello delle vulnerabilità SSRF dirette a causa della loro natura unidirezionale. Non possono essere sfruttate in modo banale per recuperare dati sensibili dai sistemi back-end, sebbene in alcune situazioni possano essere sfruttate per ottenere l'esecuzione completa del codice remoto.
## Aggirare le difese SSRF comuni (prevenzione)
È comune vedere applicazioni contenenti comportamenti SSRF insieme a difese volte a prevenire lo sfruttamento dannoso. Spesso, queste difese possono essere aggirate.
### SSRF con filtri di input basati su blacklist
Alcune applicazioni bloccano l'input contenente nomi host come `127.0.0.1` e `localhost`, o URL sensibili come `/admin`. In questa situazione, puoi spesso aggirare il filtro usando le seguenti tecniche:
- usa una rappresentazione IP alternativa di `127.0.0.1`, come `2130706433`, `017700000001` o `127.1`;
- registra il tuo nome di dominio che si risolve in `127.0.0.1`. Puoi usare `spoofed.burpcollaborator.net` a questo scopo;
- offusca le stringhe bloccate usando la codifica URL o la variazione di maiuscole/minuscole;
- fornisci un URL che controlli, che reindirizza all'URL di destinazione. Prova a usare diversi codici di reindirizzamento, così come diversi protocolli per l'URL di destinazione. Ad esempio, è stato dimostrato che il passaggio da un URL `http:` a `https:` durante il reindirizzamento aggira alcuni filtri anti-SSRF.
### SSRF con filtri di input basati su whitelist
Alcune applicazioni consentono solo input corrispondenti, una whitelist di valori consentiti. Il filtro può cercare una corrispondenza all'inizio dell'input o contenuta al suo interno. Potresti riuscire a bypassare questo filtro sfruttando le incongruenze nell'analisi URL.

La specifica URL contiene una serie di funzionalità che probabilmente verranno trascurate quando gli URL implementano l'analisi e la convalida ad hoc utilizzando questo metodo:
- puoi incorporare le credenziali in un URL prima del nome host, utilizzando il carattere `@`. Ad esempio: `https://expected-host:fakepassword@evil-host`
- puoi utilizzare il carattere `#` per indicare un frammento URL. Ad esempio: `https://evil-host#expected-host`
- puoi sfruttare la gerarchia di denominazione DNS per inserire l'input richiesto in un nome DNS completamente qualificato che controlli. Ad esempio: `https://expected-host.evil-host`
- puoi codificare i caratteri URL per confondere il codice di analisi URL. Ciò è particolarmente utile se il codice che implementa il filtro gestisce i caratteri codificati in URL in modo diverso rispetto al codice che esegue la richiesta HTTP back-end. Puoi anche provare a codificare due volte i caratteri; alcuni server decodificano in modo ricorsivo l'input ricevuto tramite URL, il che può portare a ulteriori discrepanze.
- puoi usare combinazioni di queste tecniche insieme.
### Bypassare i filtri SSRF tramite reindirizzamento aperto
A volte è possibile bypassare le difese basate sui filtri sfruttando una vulnerabilità di reindirizzamento aperto.

Nell'esempio precedente, immagina che l'URL inviato dall'utente sia rigorosamente convalidato per impedire lo sfruttamento dannoso del comportamento SSRF. Tuttavia, l'applicazione i cui URL sono consentiti contiene una vulnerabilità di reindirizzamento aperto. A condizione che l'API utilizzata per effettuare la richiesta HTTP back-end supporti i reindirizzamenti, puoi creare un URL che soddisfi il filtro e che risulti in una richiesta reindirizzata al target back-end desiderato.

Ad esempio, l'applicazione contiene una vulnerabilità di reindirizzamento aperto in cui il seguente URL:
`/product/nextProduct?currentProductId=6&path=http://evil-user.net`
restituisce un reindirizzamento a:
`http://evil-user.net`

È possibile sfruttare la vulnerabilità di reindirizzamento aperto per aggirare il filtro URL e sfruttare la vulnerabilità SSRF come segue:
```http
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin
```

Questo exploit SSRF funziona perché l'applicazione convalida prima che l'URL `stockAPI` fornito si trovi su un dominio consentito, il che è vero. L'applicazione quindi richiede l'URL fornito, che attiva il reindirizzamento aperto. Segue il reindirizzamento e invia una richiesta all'URL interno scelto dall'aggressore.
## Trovare la superficie di attacco nascosta per le vulnerabilità SSRF
Molte vulnerabilità di falsificazione delle richieste lato server sono facili da trovare, perché il normale traffico dell'applicazione coinvolge parametri di richiesta contenenti URL completi. Altri esempi di SSRF sono più difficili da individuare.
### URL parziali nelle richieste
A volte, un'applicazione inserisce solo un nome host o parte di un percorso URL nei parametri di richiesta. Il valore inviato viene quindi incorporato lato server in un URL completo che viene richiesto. Se il valore viene prontamente riconosciuto come nome host o percorso URL, la potenziale superficie di attacco potrebbe essere ovvia. Tuttavia, la sfruttabilità come SSRF completo potrebbe essere limitata perché non si controlla l'intero URL che viene richiesto.
### URL nei formati di dati
Alcune applicazioni trasmettono dati in formati con una specifica che consente l'inclusione di URL che potrebbero essere richiesti dall'analizzatore di dati per il formato. Un esempio ovvio di ciò è il formato di dati XML, che è stato ampiamente utilizzato nelle applicazioni web per trasmettere dati strutturati dal client al server. Quando un'applicazione accetta dati in formato XML e li analizza, potrebbe essere vulnerabile all'iniezione XXE. Potrebbe anche essere vulnerabile a SSRF tramite XXE. Ne parleremo più in dettaglio quando esamineremo le vulnerabilità di iniezione XXE.
### SSRF tramite l'intestazione Referer
Alcune applicazioni utilizzano software di analisi lato server per tracciare i visitatori. Questo software spesso registra l'intestazione Referer nelle richieste, in modo da poter tracciare i link in arrivo. Spesso il software di analisi visita tutti gli URL di terze parti che compaiono nell'intestazione Referer. Ciò viene in genere fatto per analizzare i contenuti dei siti di riferimento, incluso il testo di ancoraggio utilizzato nei link in arrivo. Di conseguenza, l'intestazione Referer è spesso una superficie di attacco utile per le vulnerabilità SSRF.