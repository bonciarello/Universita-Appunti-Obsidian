È il processo di conversione delle specifiche di sistema in un sistema eseguibile.

Con la progettazione software si progetta una struttura software che rilasci la specifica.

L'implementazione è tradurre questa struttura in un programma eseguibile.

Le fasi della progettazione sono:

- progettazione architettonica;
- specifiche astratte;
- progettazione dell'interfaccia;
- progettazione dei componenti;
- progettazione della struttura dei dati;
- progettazione dell'algoritmo.

## Architettura software

La progettazione architettonica è l'identificazione dei sottosistemi e le specifiche dei quadri di controllo e comunicazione.

Una descrizione dell'architettura software è l'output di questo processo di progettazione.

## Strutturazione del sistema

Interessato alla scomposizione del sistema in sottosistemi interagenti.

Il progetto architettonico è normalmente espresso come un diagramma a blocchi che presenta una panoramica della struttura del sistema.

Possono essere sviluppati anche modelli più specifici che mostrano come i sottosistemi condividono i dati, sono distribuiti e si interfacciano tra loro.

## Schemi a blocchi e lineari

Sono schemi molto astratti, non mostrano la natura delle relazioni tra i componenti e le proprietà visibili esternamente dei sottosistemi.

Utile per la comunicazione con gli stakeholder e per la pianificazione del progetto.

## Vantaggi dell'architettura esplicita

- **Comunicazione agli stakeholder:** l'architettura può essere utilizzata come fulcro di discussione da parte degli stakeholder del sistema;
- **Analisi del sistema:** significa che è possibile analizzare se il sistema può soddisfare i suoi requisiti non funzionali;
- **Riutilizzo su larga scala:** l'architettura può essere riutilizzabile in una vasta gamma di sistemi.

## Architettura e caratteristiche del sistema

L'architettura consente di localizzare le operazioni critiche:

- **Prestazione:** localizzare le operazioni critiche e ridurre al minimo le comunicazioni. Utilizzare componenti grandi anziché a grana fine;
- **Protezione:** utilizzare un'architettura a più livelli con risorse critiche nei livelli interni;
- **Sicurezza:** localizzare le funzioni critiche per la sicurezza in un numero limitato di sottosistemi;
- **Disponibilità:** includere componenti e meccanismi ridondanti per la tolleranza agli errori;
- **Manutenibilità:** utilizzare componenti intercambiabili a grana fine.

## Decisioni di progettazione architettonica

Quando andiamo a fare la progettazione dell'architettura, dobbiamo porci diverse domande: come sarà distribuito il sistema? Quali stili architettonici sono appropriati? Quale strategia di controllo dovrebbe essere utilizzata? ecc.

## Modelli architettonici

Utilizzato per documentare un progetto architettonico. Abbiamo vari modelli:

- Modello strutturale statico che mostra i principali componenti del sistema.
- Modello di processo dinamico che mostra la struttura di processo del sistema.
- Modello di interfaccia che definisce le interfacce del sottosistema.
- Modello di relazioni come un modello di flusso di dati che mostra le relazioni del sottosistema.
- Modello di distribuzione che mostra come i sottosistemi sono distribuiti tra i computer.

Durante il processo di progettazione possono essere prodotti diversi modelli architettonici.

Ogni modello presenta diverse prospettive sull'architettura.

## Organizzazione del sistema

**Riflette la strategia di base utilizzata per strutturare un sistema.** Tre stili organizzativi sono ampiamente utilizzati:

- uno stile di repository di dati condiviso;
- uno stile di servizi e server condivisi;
- una macchina astratta o uno stile a strati.

### Reposity centrale condiviso

È stata una delle prime architetture utilizzate per i mainframe.

I sottosistemi devono scambiare dati: i dati condivisi sono conservati in un database o repository centrale e possono essere consultati da tutti i sottosistemi; ciascun sottosistema mantiene il proprio database e passa i dati esplicitamente ad altri sottosistemi.

Quando devono essere condivise grandi quantità di dati, il modello di condivisione del repository è più comunemente utilizzato.

I *vantaggi* sono: un modo efficiente per condividere grandi quantità di dati e il modello di condivisione viene pubblicato come schema del repository.

Gli *svantaggi* sono: i sottosistemi devono concordare un modello di dati del repository, l'evoluzione dei dati è difficile e costosa, nessun ambito per specifiche politiche di gestione e difficile da distribuire in modo efficiente.

### Modello client-server

Un modello alternativo che ha avuto un grande successo nei primi anni 2000 è il modello client-server: **è un modello di sistema distribuito che mostra come i dati e l'elaborazione sono distribuiti su una gamma di componenti**.

Si ha a disposizione un set di server autonomi che forniscono servizi specifici come, per esempio, stampa, gestione dati, ecc.

Con questo modello, abbiamo un insieme di client che richiedono questi servizi e una rete che consente ai client di accedere ai server.

I *vantaggi* sono: la distribuzione dei dati è semplice, fa un uso efficace dei sistemi in rete (potrebbe richiedere hardware più economico) ed è facile aggiungere nuovi server o aggiornare i server esistenti.

Gli *svantaggi* sono: nessun modello di dati condiviso, quindi i sottosistemi utilizzano un'organizzazione dei dati diversa (lo scambio di dati può essere inefficiente), gestione ridondante in ogni server e nessun registro centrale di nomi e servizi (potrebbe essere difficile scoprire quali server e servizi sono disponibili).

### Modello a strati

**Il modello a strati è un modello che permette di identificare immediatamente le funzionalità ed aiuta a strutturare la possibilità di rimuovere uno strato e non cambiare il risultato.**

*È utilizzato per modellare l'interfacciamento di sottosistemi.*

Organizza il sistema in un insieme di livelli (o macchine astratte) ciascuno dei quali fornisce un insieme di servizi.

Supporta lo sviluppo incrementale di sottosistemi in diversi livelli: quando l'interfaccia di un livello cambia, viene interessato solo il livello adiacente.

## Sottosistemi e moduli

Quando andiamo a strutturare un sotto sistema, dobbiamo capire che lavorerà per fatti suoi. Come faccio a decidere come strutturarlo?

- **Un *sottosistema* è un sistema a sé stante** il cui funzionamento è indipendente dai servizi forniti da altri sottosistemi.
- **Un *modulo* è un componente di sistema** che fornisce servizi ad altri componenti ma normalmente non sarebbe considerato come un sistema separato.

## Decomposizione modulare

È un altro livello strutturale in cui i sottosistemi sono scomposti in moduli.

Esistono due modelli di decomposizione modulari coperti:

- un **modello a oggetti** in cui il sistema è scomposto in oggetti interagenti;
- un **modello di pipeline o flusso di dati** in cui il sistema viene scomposto in moduli funzionali che trasformano gli input in output.

Se possibile, le decisioni sulla concorrenza dovrebbero essere ritardate fino all'implementazione dei moduli.

Quest'ultimo modello ha reso famoso UNIX: inizialmente potevano eseguire un task alla volta, più avanti potevano offrire dei programmi che potevano combinarsi tra di loro per ottenere il risultato.

## Modelli di oggetti

Struttura il sistema in un insieme di oggetti liberamente accoppiati con interfacce ben definite.

La scomposizione orientata agli oggetti si occupa di identificare le classi di oggetti, i loro attributi e le operazioni.

Una volta implementati, gli oggetti vengono creati da queste classi e alcuni modelli di controllo vengono utilizzati per coordinare le operazioni sugli oggetti.

I vantaggi sono:

- gli oggetti sono debolmente accoppiati: la loro implementazione può essere modificata senza influenzare altri oggetti;
- gli oggetti possono riflettere entità del mondo reale;
- i linguaggi di implementazione OO sono ampiamente utilizzati.

Tuttavia, le modifiche all'interfaccia degli oggetti possono causare problemi e le entità complesse possono essere difficili da rappresentare come oggetti.

## Pipelining orientato alla funzione

Le trasformazioni funzionali elaborano i loro input per produrre output. Può essere indicato come un modello di pipe e filtro (come nella shell UNIX).

Le varianti di questo approccio sono molto comuni. Quando le trasformazioni sono sequenziali, si tratta di un modello sequenziale batch ampiamente utilizzato nei sistemi di elaborazione dati.

Non proprio adatto per i sistemi interattivi. I vantaggi sono:

- supporta il riutilizzo della trasformazione;
- organizzazione intuitiva per la comunicazione con gli stakeholder;
- facile aggiungere nuove trasformazioni;
- relativamente semplice da implementare come sistema simultaneo o sequenziale.

Tuttavia, richiede un formato comune per il trasferimento dei dati lungo la pipeline ed è difficile supportare l'interazione basata sugli eventi.

## Stili di controllo

**Riguardano il flusso di controllo tra i sottosistemi e si distingue dal modello di scomposizione del sistema.**

Abbiamo un controllo centralizzato: un sottosistema ha la responsabilità generale del controllo e avvia e arresta altri sottosistemi.

Abbiamo anche un controllo basato sugli eventi: ciascun sottosistema può rispondere a eventi generati esternamente da altri sottosistemi o dall'ambiente del sistema.

## Controllo centralizzato

Un sottosistema di controllo si assume la responsabilità di gestire l'esecuzione di altri sottosistemi.

Abbiamo due modelli:

- **Modello di risposta alla chiamata:** modello di subroutine top-down in cui il controllo inizia all'inizio di una gerarchia di subroutine e si sposta verso il basso. Applicabile a sistemi sequenziali;
- **Modello manager:** applicabile a sistemi concorrenti. Un componente del sistema controlla l'arresto, l'avvio e il coordinamento di altri processi del sistema. Può essere implementato in sistemi sequenziali come case statement.

## Sistemi basati su eventi

Guidato da eventi generati esternamente in cui la tempistica dell'evento è al di fuori del controllo dei sottosistemi che elaborano l'evento.

Abbiamo due principali modelli event-driven:

- **Modelli di trasmissione:** un evento viene trasmesso a tutti i sottosistemi. Qualsiasi sottosistema in grado di gestire l'evento può farlo;
- **Modelli guidati da interruzioni:** utilizzato nei sistemi in tempo reale in cui gli interrupt vengono rilevati da un gestore di interrupt e passati a qualche altro componente per l'elaborazione.

## Modello di trasmissione

**Efficace nell'integrazione di sottosistemi su diversi computer in una rete.**

I sottosistemi registrano un interesse per eventi specifici. Quando questi si verificano, il controllo viene trasferito al sottosistema che può gestire l'evento.

Il criterio di controllo non è incorporato nell'evento e nel gestore dei messaggi. I sottosistemi decidono sugli eventi di loro interesse.

Tuttavia, i sottosistemi non sanno se o quando verrà gestito un evento.

## Sistemi guidati da interruzioni

**Utilizzato nei sistemi in tempo reale in cui è essenziale una risposta rapida a un evento.**

Esistono tipi di interrupt noti con un gestore definito per ogni tipo.

Ogni tipo è associato a una posizione di memoria e uno switch hardware provoca il trasferimento al relativo gestore.

Consente una risposta rapida ma complessa da programmare e difficile da validare.
