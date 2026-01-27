È la descrizione di come un insieme di persone che utilizzano il sistema ci fa vedere come questi lo utilizzano e perchè quelle persone è interessata a svolgere quella sequenza di persone.

## Sezione del caso d'uso

- **Nome del caso d'uso:** inizia con un verbo;
- **Portata:** il sistema che si sta progettando;
- **Livello:** "obiettivo utente" o "sottofunzione";
- **Attore primario:** chiede al sistema di fornirgli i suoi servizi;
- **Parti interessate e Interessi:** a chi interessa questo caso d'uso e che cosa desidera;
- **Pre-condizioni:** che cosa deve essere vero all'inizio del caso d'uso - e vale la pena di dire al lettore; 
- **Garanzia di successo:** che cosa deve essere vero se il caso d'uso viene completato con successo - e vale la pena di dire al lettore;
- **Scenario principale di successo:** uno scenario comune di attraversamento del caso d'uso, di successo e incondizionato;
- **Estensioni:** scenari alternativi, di successo e di fallimento;
- **Requisiti speciali:** requisiti non funzionali correlati;
- **Elenco delle varianti tecnologiche e dei dati:** varianti nei metodi I/O e nel formato dei dati;
- **Frequenza di ripetizione:** frequenza prevista di esecuzione del caso d'uso;
- **Varie:** altri aspetti, come per esempio i problemi aperti.

## Caso d'uso UC1: Elabora Vendita (Process Sale)

Quello che segue è un esempio basato su questo template:

> *Si noti che questo è l'esempio principale del libro di caso d'uso dettagliato, relativo allo studio di casoNextGen. Esso mostra molti elementi e aspetti comuni.*
> 
> Probabilmente l'esempio mostra molto più di quanto si vorrebbe sapere su un sistema POS, ma poichè si tratta di un POS reale, mostra la capacità dei casi d'uso e dei relativi scenari di cogliere i requisiti del mondo reale, che sono complessi.

Ecco come sviluppare i casi d'uso di questo problema:

- **Portata:** applicazione POS NextGen;
- **Livello:** obiettivo utente;
- **Attore primario:** cassiere;
- **Parti interessate e Interessi:** 
    - *Cassiere (Cashier):* vuole un inserimento dei dati preciso e rapido. Non vuole errori nei pagamenti, perchè gli ammanchi di cassa vengono detratti dal suo stipendio;
    - *Addetto alle vendite (Salesperson):* vuole che le commissioni sulle vendite siano aggiornate;
    - *Cliente (Customer):* vuole effettuare acquisti e fruire di un servizio rapido, nel modo più semplice possibile possibile. Vuole una visualizzazione chiara degli articoli inserite dei loro prezi. Vuole una prova d'acquisto per una eventuale restituzione o sostituzione;
    - *Azienda (Company):* vuole registrare accuratamente le transazioni effettuate e soddisfare gli interessi dei clienti. Vuole che vengano registrati i pagamenti da riscuotere tramite il Servizio di Autorizzazione di Pagamento. Vuole una certa tolleranza ai guasti per consentire di effettuare vendite anche se alcuni componenti del server (per esempio, l'autorizzazione remota di pagamento con credito) non sono disponibili. Vuole un aggiornamento automatico e rapido della contabilità e dell'inventario;
    - *Direttore (Manager):* vuole essere in grado di eseguire rapidamente operazioni di sovrascrittura, e risolvere in modo semplice i problemi del Cassiere;
    - *Enti governativi fiscali (Government Tax Agencies):* vogliono riscuotere le imposte su ciascuna vendita. Possono essere più enti: nazionale, regionale e provinciale;
    - *Servizio di Autorizzazione di Pagamento (Payment Authorization Service):* vuole ricevere le richieste elettroniche di autorizzazione nel formato e nel protocollo corretto. Vuole una contabilità dettagliata dei suoi debiti verso il negozio.
- **Pre-condizioni:** il Cassiere è identificato e autenticato;
- **Garanzia di successo (o Post-condizioni):** la vendita viene salvata. Le imposte sono calcolate correttamente. La contabilità e l'inventario sono aggiornati. Le commissioni sono registrate. Viene generata una ricevuta. Le approvazioni alle autorizzazioni di pagamento sono registrate;
- **Scenario principale di successo (o Flusso di base):** 
    1. Il Cliente arriva alla cassa POS con gli articoli e/o i servizi da acquistare;
    2. Il Cassiere inizia una nuova vendita;
    3. Il Cassiere inserisce il codice identificativo dell'articolo;
    4. Il Sistema registra la riga di vendita per l'articolo e mostra la descrizione dell'articolo, il suo prezzo, il totale parziale. Il prezzo è calcolato in base a un insieme di regole di prezzo;
    - *Il Cassiere ripete i passi 3-4 fino a che non indica che ha terminato;*
    5. Il Sistema mostra il totale con le imposte calcolate;
    6. Il Cassiere riferisce il totale al Cliente, e richiede il pagamento;
    7. Il Cliente paga e il Sistema gestisce il pagamento;
    8. Il Sistema registra la vendita completata e invia informazioni sulla vendita e sul pagamento ai sistemi esterni di Contabilità (per la contabilità e le commissioni) e di Inventario (per l'aggiornamento dell'inventario);
    9. Il Sistema genera la ricevuta;
    10. Il Cliente va via con la ricevuta e gli articoli acquistati.
- **Estensioni (o Flussi alternativi):** 
    - *a. In quel momento, il Direttore chiede di eseguire un'operazione di sovrascrittura: 
        1. Il Sistema passa alla modalità autorizzata "Direttore";
        2. Il Direttore o il Cassiere esegue un'operazione nella modalità "Direttore"; per esempio, riprendere una vendita sospesa su un altro registratore di cassa, annullare una vendita, cambiare la chiusura di cassa, e così via;
        3. Il Sistema torna alla modalità autorizzata "Cassiere".
    - *b. In qualsiasi momento, il Sistema fallisce: per consentire il ripristino e una gestione corretta della contabilità della contabilità, bisogna garantire che tutto lo stato transazionale significativo possa essere ripristinato, a partire da qualsiasi passo dello scenario: 
        1. Il Cassiere riavvia il Sistema, si autentica, e richiede il ripristino dello stato precedente;
        2. Il Sistema ricostruisce lo stato precedente: 
            - 2a. Il Sistema rileva delle anomalie che impediscono il ripristino: 
                1. Il Sistema segnala un errore al Cassiere, registra l'errore e passa a uno stato pulito;
                2. Il Cassiere inizia una nuova vendita.
    - 1a. Il Cliente o il Direttore chiedono di riprendere una vendita sospesa: 
        1. Il Cassiere esegue l'operazione di ripresa e inserisce il codice identificativo della vendita da riprendere;
        2. Il Sistema visualizza lo stato della vendita ripresa, con il totale parziale: 
            - 2a. Vendita non trovata: 
                1. Il Sistema segnala l'errore al Cassiere;
                2. Il Cassiere probabilmente inizia una nuova vendita e reinserisce tutti gli articoli.
        3. Il Cassiere continua con vendita (probabilmente inserendo altri articoli o gestendo il pagamento).
    - 2-4a. Il Cliente dice al Cassiere di godere di un'esenzione delle imposte (per esempio, perchè è anziano): 
        1. Il Cassiere verifica, quindi inserisce il codice per lo stato di esenzione delle imposte;
        2. Il Sistema registra lo stato (che utilizzerà durante il calcolo delle imposte).
    - 3a. Codice identificativo dell'articolo non valido (non trovato nel sistema); 
        1. Il Sistema segnala l'errore e rifiuta l'inserimento;
        2. Il Cassiere risponde all'errore: 
            - 2a. C'è un codice identificativo dell'articolo leggibile (per esempio, un codice UPC numerico): 
                1. Il Cassiere inserisce il codice dell'articolo manualmente;
                2. Il Sistema visualizza descrizione e prezzo: 
                    - 2a. Codice identificativo dell'articolo non valido: Il Sistema segnala l'errore. Il Cassiere prova in un altro modo;
            - 2b. Non c'è un codice identificativo dell'articolo, ma sul cartellino è presente un prezzo: 
                1. Il Cassiere chiede al Direttore di eseguire un'operazione di sovrascrittura;
                2. Il Direttore esegue la sovrascrittura;
                3. Il Cassiere richiede l'inserimento manuale del prezzo, inserendo il prezzo e richiede la tassazione standard per questo importo (poiché non vi sono informazioni sul prodotto, il calcolatore delle imposte non saprebbe altrimenti come effettuare la tassazione);
            - 2c. Il Cassiere esegue Aiuto Ricerca Prodotto per ottenere il vero codice identificativo dell'articolo e il suo prezzo;
            - 2d. Altrimenti, il Cassiere chiede a un dipendente il codice identificativo effettivo dell'articolo o il suo prezzo, e inserisce il codice o il prezzo manualmente (vedi sopra);
    - 3b. Ci sono più articoli della stessa categoria e non è importante tenere traccia dell'identità univoca dell'articolo (per esempio, 5 confezioni di hamburger vegetariani): 
        1. Il Cassiere può inserire il codice identificativo della categoria dell'articolo e la quantità;
    - 3c. L'articolo richiede l'inserimento manuale della categoria e del prezzo (come fiori o cartoline con un prezzo applicato): 
        1. Il Cassiere inserisce manualmente il particolare codice della categoria, più il prezzo;
    - 3-6a. Il Cliente chiede al Cassiere di eliminare (ovvero, di annullare) un articolo dell'acquisto (questo è consentito solo se il valore dell'articolo è inferiore al limite di annullamento per il Cassiere, altrimenti è necessaria un'operazione di sovrascrittura da parte del direttore): 
        1. Il Cassiere inserisce il codice identificativo dell'articolo da rimuovere dalla vendita;
        2. Il Sistema elimina l'articolo e visualizza il totale parziale aggiornato: 
            - 2a. Il prezzo dell'articolo supera il limite di annullamento per il Cassiere: 
                1. Il Sistema segnala l'errore, e suggerisce una sovrascrittura da parte del Direttore;
                2. Il Cassiere richiede la sovrascrittura da parte del Direttore, la ottiene e ripete l'operazione;
    - 3-6b. Il Cliente dice al Cassiere di annullare la vendita: 
        1. Il Cassiere annulla la vendita sul Sistema;
    - 3-6c. Il Cassiere sospende la vendita: 
        1. Il Sistema registra la vendita in modo tale che possa essere ripresa su qualsiasi registratore POS;
        2. Il Sistema presenta una "ricevuta di sospensione", che comprende un elenco degli articoli inseriti e un codice identificativo della vendita, da usare per recuperare e riprendere la vendita;
    - 4a. Il prezzo dell'articolo fornito dal sistema non è accettato (per esempio, il Cliente si è lamentato di qualcosa e gli viene offerto un prezzo inferiore): 
        1. Il Cassiere richiede l'approvazione dal Direttore;
        2. Il Direttore esegue un'operazione di sovrascrittura;
        3. Il Cassiere inserisce manualmente il prezzo corretto per l'articolo;
        4. Il Sistema mostra il nuovo prezzo;
    - 5a. Il Sistema rileva un fallimento nella comunicazione con il servizio esterno di calcolo delle imposte: 
        1. Il Sistema riavvia il servizio sul nodo POS, e prosegue: 
            - 1a. Il Sistema rileva che il servizio non riprende: 
                1. Il Sistema segnale l'errore;
                2. Il Cassiere può calcolare le imposte e inserirle manualmente, oppure annullare la vendita;
    - 5b. Il Cliente dice di aver diritto a uno sconto (per esempio in quanto dipendente oppure cliente abituale): 
        1. Il Cassiere segnala la richiesta di sconto;
        2. Il Cassiere inserisce il codice identificativo del Cliente;
        3. Il Sistema mostra il totale con lo sconto applicato, calcolato in base alle regole sugli sconti;
    - 5c. Il Cliente dice di avere un credito sul proprio conto, da applicare alla vendita: 
        1. Il Cassiere segnala la richiesta di credito;
        2. Il Cassiere inserisce il codice identificativo del Cliente;
        3. Il Sistema applica il credito al prezzo, e riduce il credito residuo nella stessa misura;
    - 6a. Il Cliente dice che intende pagare in contanti, ma non ha abbastanza contanti: 
        1. Il Cassiere chiede un metodo di pagamento alternativo; 
            - Il Cliente dice al Cassiere di annullare la vendita. Il Cassiere annulla la vendita sul Sistema.
    - 7a. Pagamento in contanti: 
        1. Il Cassiere inserisce l'importo in contanti presentato dal Cliente;
        2. Il Sistema mostra il resto dovuto e apre il cassetto della cassa;
        3. Il Cassiere deposita il contante presentato e restituisce il resto in contanti al Cliente;
        4. Il Sistema registra il pagamento in contanti;
    - 7b. Pagamento con carta di credito: 
        1. Il Cliente inserisce le informazioni sulla propria carta di credito;
        2. Il Sistema visualizza il pagamento per la verifica;
        3. Il Cassiere conferma: 
            - 3a. Il Cassiere annulla l'operazione di pagamento: 
                - Il Sistema ritorna alla modalità "inserimento articolo";
        4. Il Sistema invia una richiesta di autorizzazione al pagamento a un sistema esterno di Servizio di Autorizzazione di Pagamento, per richiedere l'approvazione del pagamento: 
            - 4a. Il Sistema rileva un problema nella comunicazione con il sistema esterno: 
                1. Il Sistema segnala l'errore al Cassiere;
                2. Il Cassiere chiede al Cliente un metodo di pagamento alternativo;
        5. Il Sistema riceve l'approvazione di pagamento, segnala l'approvazione al Cassiere e apre il cassetto (per inserire la ricevuta di pagamento firmata): 
            - 5a. Il Sistema riceve un rifiuto per il pagamento: 
                1. Il Sistema segnala il rifiuto al Cassiere;
                2. Il Cassiere chiede al Cliente un metodo di pagamento alternativo;
            - 5b. Il Sistema rileva un timeout nell'attesa della risposta: 
                1. Il Sistema segnala il timeout al Cassiere;
                2. Il Cassiere può riprovare, oppure chiedere al Cliente un metodo di pagamento alternativo;
        6. Il Sistema registra il pagamento con carta di credito, che comprende l'approvazione del pagamento;
        7. Il Sistema presenta un meccanismo di inserimento della firma per il pagamento;
        8. Il Cassiere chiede al Cliente la firma per la ricevuta del pagamento con carta di credito. Il Cliente inserisce la firma;
        9. Se la firma sulla ricevuta è cartacea, il Cassiere mette la ricevuta nel cassetto e lo chiude;
    - 7c. Pagamento con assegno...
    - 7d. Pagamento con carta bancomat...
    - 7e. Il Cassiere annulla l'operazione di pagamento: 
        1. Il Sistema ritorna alla modalità "inserimento articolo";
    - 7f. Il Cliente presenta dei buoni: 
        1. Prima di gestire i pagamento, il Cassiere registra ciascun buono. Il Sistema riduce il prezzo di conseguenza. Il Sistema registra i buoni utilizzati, per motivi di contabilità: 
            - 1a. Il buono inserito non è valido per nessuno degli articoli acquistati: 
                1. Il Sistema segnala l'errore al Cassiere;
    - 9a. Il Cliente richiede una ricevuta regalo (senza indicazione del prezzo): 
        1. Il Cassiere richiede la ricevuta e il Sistema la presenta;
    - 9b. La stampante ha esaurito la carta: 
        1. Se il Sistema riesce a rilevare l'errore, segnala il problema;
        2. Il Cassiere sostituisce la carta;
        3. Il Cassiere richiede un'altra ricevuta.
- **Requisiti speciali:** 
    - Interfaccia utente di tipo touch screen su un monitor piatto grande. Il testo deve essere visibile da una distanza di un metro;
    - Risposta all'autorizzazione di credito entro 30 secondi il 90% delle volte;
    - In qualche modo si desidera un ripristino robusto quando non riesce l'accesso ai servizi remoti, come per esempio il sistema d'inventario;
    - Internazionalizzazione della lingua sul testo visualizzato;
    - ...
- **Elenco delle varianti tecnologiche e dei dati:** 
    - *a. Richiesta di sovrascrittura da parte del Direttore effettuata passando una scheda apposita attraverso un lettore di schede, oppure inserendo un codice di autorizzazione con la tastiera;
    - 3a. Codice identificativo dell'articolo inserito tramite lettore laser di codici a barre (se il codice a barre è presente) oppure tramite tastiera;
    - 3b. Il codice identificativo dell'articolo può essere basato su uno tra gli schemi di codifica UPC, EAN, JAN o SKU;
    - 7a. Le informazioni sulla carta di credito sono inserite tramite lettore di schede o tramite tastiera;
    - 7b. Firma per il pagamento con carta di credito ottenuta su ricevuta cartacea. Ma si prevede che, entro due anni, molti vorranno la cattura digitale della firma;
- **Frequenza di ripetizione:** potrebbe essere quasi ininterrotta;
- **Problemi aperti:** 
    - come variano le leggi fiscali?
    - esaminare la questione del ripristino dei servizi remoti;
    - quale personalizzazione è necessaria per le diverse aziende?
    - il Cassiere deve estrarre e portare via il cassetto della cassa quando effettua il logout?
    - il Cliente può usare direttamente il lettore di schede o lo deve fare il Cassiere?

Questo caso d'uso è basato sui requisiti di un vero sistema POS, sviluppato con una progettazione OO e implementato da Java. Il caso d'uso è stato presentato in modo più illustrativo che esaustivo. Ciononostante presenta dettaglio e complessità sufficienti per dimostrare, realisticamente, che un caso d'uso dettagliato può registrare numerosi dettagli dei requisiti. Questo esempio sarà usato come modello per discutere diversi aspetti relativi ai casi d'uso.

## Linea guida: come trovare i casi d'uso

I casi d'uso hanno come scopo quello di soddisfare gli obiettivi degli attori primari. Quindi, la procedura di base per trovarli è la seguente:

1. Scegliere i confini del sistema. È un'applicazione software, un sistema costituito da hardware e software insieme, tutto questo più una persona che lo utilizza, o addirittura un'intera organizzazione?
2. Identificare gli attori primari - coloro che raggiungono i propri obiettivi attraverso l'utilizzo dei servizi del sistema;
3. Identificare gli obiettivi di ciascun attore primario;
4. Definire i casi d'uso che soddisfano gli obiettivi degli utenti; il loro nome va scelto in base all'obiettivo. Di solito, i casi d'uso a livello di obiettivo utente saranno in corrispondenza biunivoca con gli obiettivi degli utenti ma, come si vedrà, c'è almeno un'eccezione a questa regola.

Naturalmente, nello sviluppo iterativo ed evolutivo, non tutti gli obiettivi o i casi d'uso saranno identificati completamente o correttamente fin dall'inizio. Anche la scoperta dei requisiti è evolutiva.

### Passo 1: stabilire i confini del sistema

Per questo studio di caso, il sistema in discussione è il sistema POS stesso; tutto ciò che è al suo esterno è fuori dai confini del sistema, compresi il cassiere, il servizio di autorizzazione al pagamento e così via.

Per chiarire la definizione dei confini del sistema in corso di progettazione, è utile sapere che gli attori primari e gli attori di supporto sono considerati esterni al sistema. Una volta identificati gli attori esterni, i confini diventano più chiari. Per esempio, la responsabilità delle autorizzazioni ai pagamenti rientra completamente nei confini del sistema? No, è di un attore esterno, il Servizio di Autorizzazione di Pagamento.

### Passo 2 e 3: trovare gli attori primari e i loro obiettivi

Non è naturale cercare di identificare, in stretta sequenza, prima gli attori primari e poi gli obiettivi degli utenti; in un workshop sui requisiti, le persone effettuano un brainstorming e considerano insieme entrambi gli aspetti. Alcune volte sono gli obiettivi a svelare gli attori, o viceversa.

> **Linee guida:** eseguire prima il brainstorming degli attori primari, poichè questo fornisce il contesto per un'ulteriore indagine.

### Altre domande che aiutano a trovare attori e obiettivi

Oltre agli attori primari e agli obiettivi più evidenti, le seguenti domande aiutano a identificarne altri che potrebbero sfuggire.

- *Chi avvia e arresta il sistema?*
- *Chi si occupa dell'amministrazione del sistema?*
- *Chi si occupa della gestione degli utenti e della sicurezza?*
- *Il "tempo" è un attore, nel senso che il sistema esegue operazioni in risposta ad alcuni eventi temporali?*
- *Esiste un processo di monitoraggio che riavvia il sistema se si verifica un errore?*
- *Chi valuta le attività e le prestazioni del sistema?*
- *Come vengono gestiti gli aggiornamenti del software? Sono aggiornamenti automatici o a richiesta?*
- *Chi valuta i log? Vi si accede in remoto?*
- *Oltre agli attori primari umani, ci sono sistemi software o robotizzati esterni che utilizzano i servizi del sistema?*
- *Chi viene avvisato quando si verificano errori o guasti?*

### Come organizzare gli attori e gli obiettivi

Ci sono almeno due approcci per organizzare gli attori e i loro obiettivi:

1. Man mano che vengono scoperti, vengono disegnati in un diagramma dei casi d'uso, chiamando i casi d'uso come gli obiettivi;
2. Scrivere dapprima un elenco attori-obiettivi, rivederlo e raffinarlo, per poi disegnare il diagramma dei casi d'uso.

Se si crea un elenco attori-obiettivi allora, in termini di elaboratori di UP, è utile riportare questo elenco in una sezione dell'elaborato Visione.

- **Stile a scatola nera:** 
    - Il Sistema registra la vendita.
- **Non a scatola nera:** 
    - Il Sistema memorizza la vendita in una base di dati, oppure (ancora peggio);
    - Il Sistema esegue un'istruzione SQL INSERT per la vendita...

## Linea guida: adottare un punto di vista dell'attore e dell'attore-obiettivo

La seguente definizione di caso d'uso di RUP è stata formulata dall'inventore dei casi d'uso, Ivar Jacobson.

> Un caso d'uso è un insieme di istanze di casi d'uso,in cui ciascuna istanza è una sequenza di azioni che un sistema esegue per produrre un risultato osservabile e di valore *per uno specifico attore*.

Un'estensione è costituita da due parti: la condizione e la gestione.

> **Linea guida:** quando è possibile, la condizione va scritta qualcosa che possa essere *rilevato* dal sistema o da un attore.

> **Linea guida:** scrivere i casi d'uso in uno stile essenziale; ignorare l'interfaccia utente, concentrarsi sullo scopo dll'attore.

Tutti i precedenti casi d'uso mostrati, come *Elabora Vendita*, sono stati scritti adottando uno stile essenziale.

## Esempi a confronto

### Stile essenziale
Si supponga che il caso d'uso *Gestisce Utenti* richieda l'identificazione e l'autenticazione:

> ...
> 1. L'Amministratore si identifica.
> 2. Il Sistema autentica l'identità.

> **Linea guida:** la descrizione delle condizioni e diramazioni e della loro gestione va rimandata alla sezione delle Estensioni.

Lo scenario è formato da una sequenza di passi, che possono essere di due tipi:

1. Un'interazione tra attori.
2. Una validazione (normalmente effettuata dal sistema).

## Tipi di test

### Test del capo

Il capo vi chiede: "Cosa avete fatto tutto il giorno?" e voi rispondete. "Il login!". Il vostro capo sarà felice?

Se non lo è, il caso d'uso non supera il test del capo, il che significa che non è fortemente mirato a ottenere risultati il cui valore sia misurabile. Potrebbe essere un caso d'uso a un livello più basso, ma non al livello a cui è desiderabile concentrarsi durante l'analisi dei requisiti.

Ciò non significa che bisogna sempre ignorare i casi d'uso che non superano il test del capo. L'autenticazione dell'utente può non superare il test del capo, ma può essere importante e difficile.

### Test EBP

La nozione di **Processo di Business Elementare (Elementary Business Process o EBP)** deriva dal settore dell'ingegneria dei processi di business.

> Un processo di business elementare è un'attività svolta da una persona in un determinato tempo e luogo, in risposta a un evento di business, che aggiunge un valore di business misurabile e lascia i dati in uno stato coerente; per esempio, *Approva un credito o Stabilisci il prezzo per un ordine*.

### Test della dimensione

Un caso d'uso è raramente costituito da una singola azione o passo; normalmente comprende diversi passi, e nel suo formato dettagliato spesso richiede da 3 a 10 pagine di testo. Un errore comune nella modellazione dei casi d'uso è definire un caso d'uso a sè formato da un singolo passo, all'interno in una sequenza di passi correlati, come la definizione di un caso d'uso chiamato *Inserisci il codice identificativo di un articolo*. Si può intravedere l'errore dalle sue dimensioni ridotte: il nome del caso d'uso suggerisce erroneamente che si tratta di un unico passo all'interno di una sequenza più ampia di passi, e se si immagina la lunghezza del testo dettagliato, sarà estremamente corto.

*Autentica Utente* potrebbe non superare il test del capo, ma essere sufficientemente complesso da richiedere un'analisi accurata, come per esempio nel caso di un'autenticazione di tipo "single sign-on".

## Applicare UML: diagrammi dei casi d'uso

UML fornisce una notazione dei diagrammi dei casi d'uso che consente di illustrare i nomi e gli attori dei casi d'uso, nonché le relazioni tra gli stessi.

![[applicareUML.png]]