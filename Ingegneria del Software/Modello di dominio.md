---
aliases: [Modello dominio, MD]
tags: [ingegneria-del-software]
---
Il passo essenziale dell'analisi orientata agli oggetti è la decomposizione di un dominio in concetti o oggetti significativi.

Un **modello di dominio** è una rappresentazione *visuale* di classi concettuali o di oggetti del mondo reale di un dominio [MO95, Fowler96]. I modelli di dominio sono stati chiamati anche **modelli concettuali, modelli degli oggetti di dominio** e **modelli degli oggetti di analisi**.

> **Definizione:** in UP, il termine "modello di dominio" indica una rappresentazione di classi concettuali del mondo reale, non di oggetti software. Il termine *non* indica un insieme di diagrammi che descrivono classi software, lo strato del dominio di un'[[Architettura|architettura]] software o oggetti software con responsabilità.

UP definisce il modello di dominio come uno degli elaborati che si possono creare nella disciplina di modellazione del business. Più precisamente, il modello di dominio di UP è una specializzazione del **modello degli oggetti di business (BOM)** di UP che "è incentrato sulla spiegazione di "cose" e prodotti importanti per un dominio di business" [RUP]. Ciò significa che un modello di dominio è incentrato su un solo dominio, come per esempio tutto ciò che riguarda il POS. Il BOM più ampio (poichè può portare a un'eccessiva modellazione preliminare) è un modello multi-dominio esteso, speso molto ampio e difficile da creare, che riguarda l'intero business e tutti i relativi sottodomini.

Applicando la notazioe UML, un modello di dominio è illustrato con un insieme di **diagrammi delle classi** in cui non sono definite operazioni (firme di metodi). Esso fornisce un *punto di vista concettuale* e può mostrare:

- oggetti di dominio o classi concettuali;
- associazioni tra classi concettuali;
- attributi di classi concettuali.

## Definizione: il modello di dominio è un "dizionario visuale"

L'uso di un linguaggio visuale come la notazione UML permette di capire più facilmente i termini e soprattutto le relazioni tra di essi, poichè il cervello umano è bravo a comprendere elementi mostrati graficamente e linee di connessione,.

Pertanto il modello di dominio è un *dizionario visuale* delle astrazioni significative, della terminologia del dominio e del contenuto informativo del dominio.

## Definizione: il modello di dominio non è una raffigurazione degli oggetti software aziendali

Un modello di dominio di UP è una visualizzazione di oggetti del mondo reale in un dominio di interesse, *non* di oggetti software (come classi Java o C#) oppure di oggetti software con responsabilità (seconda e terza tabella).

Pertanto i seguenti elementi non sono adatti a essere mostrati in un modello di dominio.

- Elementi software, come una finestra o una base di dati, a meno che il dominio che si sta modellando non riguardi concetti software, come un modello per interfacce grafiche utente.
- Responsabilità o metodi.

![[dominio1.png]]

### Modello di dominio di UP

Vista delle parti interessate dei concetti significativi del dominio.

> *Payment* nel modello di dominio è un concetto, ma *Payment* nel modello di progetto è una classe software. Non sono la stessa cosa, ma il primo ha ispirato il nome e la definizione del secondo. In tal modo si riduce il salto rappresentazionale. Questa è una delle grandi idee della tecnologia a oggetti.

![[dominio2.png]]

### Modello di progetto di UP

Lo sviluppatore orientato agli oggetti ha tratto ispirazione dal dominio del mondo reale per la creazione di classi software.

Pertanto, il salto rappresentazionale tra il modo in cui le parti interessate concepiscono il dominio e la sua rappresentazione nel software è diminuito.

## Elenco di categorie di classi concettuali

- **Transazioni commerciali:** sono aspetti critici (riguardano denaro), dunque si inizi con le transazioni (ad es. *Sale, Payment, Reservation*);
- **Elementi/righe di transazioni:** le transazioni spesso sono composte da righe per gli articoli correlati, quindi queste vanno considerate subito dopo le transazioni (ad es. *SalesLineItem*);
- **Prodotto o servizio correlato a una transazione o a una riga di transazione per articolo:** le transazioni sono per qualcosa (un prodotto o un servizio). Vanno considerate subito dopo (ad es. *Item Flight, Seat, Meal*);
- **Dove viene registrata la transazione?** Importante (ad es. *Register, Ledger, FlightManifest*);
- **Ruoli di persone o organizzazioni correlati alle transazioni; attori nei [[Casi d'uso|casi d'uso]]:** normalmente dobbiamo sapere quali sono le parti coinvolte in una transazione (ad es. *Cashier, Customer, Store MonopolyPlayer Passenger, Airline*);
- **Luogo della transazione; luogo del servizio** (ad es. *Store Airport, Plane, Seat*);
- **[[Eventi]] significativi, spesso con un'ora o un luogo che è necessario ricordare** (ad es. *Sale, Payment MonopolyGame Flight*);
- **Oggetti fisici:** questo è particolarmente importante quando si crea software per il controllo di dispositivi, oppure simulazioni (ad es. *Item, Register Board, Piece, Die Airplane*);
- **Descrizioni di oggetti** (ad es. *ProductDescription, FlightDescription*);
- **Cataloghi:** le descrizioni sono spesso contenute in un catalogo (ad es. *ProductCatalog, FlightCatalog*);
- **Contenitori di oggetti (fisici o informazioni)** (ad es. *Store, Bin Board Airplane*);
- **Oggetti in un contenitore** (ad es. *Item Square (in una Board) Passanger*);
- **Altri sistemi che collaborano** (ad es. *CreditAuthorizationSystem AirTrafficControl*);
- **Registrazioni di questioni finanziarie, di lavoro, contrattuali e legali** (ad es. *Receipt, Ledger MaintenanceLog*);
- **Strumenti finanziari** (ad es. *Cash. Check, LineOfCredit TicketCredit*);
- **Piani, manuali, documenti cui si fa regolarmente riferimento per eseguire il lavoro** (ad es. *DailyPriceChangeList RepairSchedule*);

> **Linea guida:** occorre prestare particolare attenzione con questo metodo; una corrispondenza meccanica da nome a classe non è possibile, poichè le parole nel linguaggio naturale sono ambigue.

Nondimeno, l'analisi linguistica è un'altra fonte di ispirazione. I [[Casi d'uso|casi d'uso]] in formato dettagliato sono un'ottima descrizione a cui ispirarsi per questa analisi. Per esempio, si può riutilizzare lo scenario corrente del caso d'uso *Process Sale*.

> **Scenario principale di successo (o Flusso di base):** 
> 1. Il Cliente arriva alla cassa POS con gli articoli e/o i servizi da acquistare;
> 2. Il Cassiere inizia una nuova vendita;
> 3. Il Cassiere inserisce il codice identificativo dell'articolo;
> 4. Il Sistema registra la riga di vendita per l'articolo e mostra la descrizione dell'articolo, il suo prezzo, il totale parziale. Il prezzo è calcolato in base a un insieme di regole di prezzo;
> - *Il Cassiere ripete i passi 3-4 fino a che non indica che ha terminato;*
> 5. Il Sistema mostra il totale con le imposte calcolate;
> 6. Il Cassiere riferisce il totale al Cliente, e richiede il pagamento;
> 7. Il Cliente paga e il Sistema gestisce il pagamento;
> 8. Il Sistema registra la vendita completata e invia informazioni sulla vendita e sul pagamento ai sistemi esterni di Contabilità (per la contabilità e le commissioni) e di Inventario (per l'aggiornamento dell'inventario);
> 9. Il Sistema genera la ricevuta;
> 10. Il Cliente va via con la ricevuta e gli articoli acquistati.
> 
> **Estensioni (o Flussi alternativi):**
> 
> ...
> 
> - 7a. Pagamento in contanti: 
>     1. Il Cassiere inserisce l'importo in contanti presentato dal Cliente;
>     2. Il Sistema mostra il resto dovuto e apre il cassetto della cassa;
>     3. Il Cassiere deposita il contante presentato e restituisce il resto in contanti al Cliente;
>     4. Il Sistema registra il pagamento in contanti;

## Linea guida: pensare come un cartografo; utilizzare i termini del dominio

La strategia del cartografo può essere applicata sia alle mappe che ai modelli di dominio.

> **Linea guida:** creare un modello di dominio usando lo spirito con cui lavora un cartografo.
> 
> - Utilizzare i nomi esistenti sul territorio. Per esempio, se si sviluppa un modello per le prenotazioni aeree, si assegni al cliente il nome "Passeggero", termine utilizzato dal personale delle compagnie aeree, anzichè il troppo generico "Cliente".
> - Escludere caratteristiche irrilevanti o fuori dalla portata. Per esempio, nel modello di dominio del Monopoly per l'iterazione 1 non vengono ulizzate le carte (come la carta "Esci gratis di prigione"), quindi non è opportuno mostrare una classe *Card} nel modello per questa iterazione.
> - Non aggiungere cose che non ci sono.

Il principio è simile alla strategia *Use the Domain Vocabulary* [Coad95].

## Linea guida: modellare il mondo non reale

Alcuni sistemi software riguardano domini che hanno ben poche analogie coi domini naturali o aziendali; un esempio è il software per le telecomunicazioni. Tuttavia è possibile creare un modello di dominio anche per questi domini. È necessario un grado elevato di astrazione, evitando di usare tecniche di progettazione non orientate agli oggetti e prestando attenzione alla terminologia principale e ai concetti che utilizzano gli esperti del dominio.

Per esempio, ecco alcune classi concettuali candidate per il dominio di un commutatore per telecomunicazioni: *Message, Connection, Port, Dialog, Route e Protocol*.

## Linea guida: quando sono utili le classi descrizione

> **Linea guida:** aggiungere una classe descrizione (per esempio, *ProductDescription*) nei seguenti casi:
> - Quando è necessaria una descrizione di un articolo o servizio, indipendentemente dall'attuale esistenza di istanze di tali articoli o servizi.
> - Quando l'eliminazione delle istanze degli oggetti che descrivono (per esempio, *Item*) darebbe luogo a una perdita di informazioni che invece è necessario conservare, ma che sono state erroneamente associate all'oggetto eliminato.
> - Quando si vogliono ridurre le informazioni ridondanti o ripetute.

![[dominio3.png]]

## Associazioni

È utile trovare e mostrare associazioni necessarie per soddisfare i requisiti informativi degli scenari correnti in corso di sviluppo, nonchè quelle che contribuiscono alla comprensione del dominio.

Un'**associazione** è una relazione tra classi (più precisamente, tra istanze di queste classi) che indica una connessione significativa e interessante.

In UML, un'associazione è definitiva come "la relazione semantica tra due o più classificatori che comporta connessioni tra le rispettive istanze".

## Linea guida: quando mostrare un'associazione

Le associazioni che è utile mostrare sono solitamente quelle che implicano la conoscenza di una relazione che deve essere memorizzata per una certa durata di tempo, che a seconda del contesto potrebbe essere di millisecondi o di anni. In altre parole, *tra quali oggetti è necessaria una certa **memoria** di una relazione?*

Per esempio, dobbiamo *ricordare* quali istanze di *SalesLineItem* sono associate a un'istanza di *Sale*? Certamente si, altrimenti non sarebbe possibile ricostruire una vendita, stampare una ricevuta o calcolare il totale di una vendita.

E dobbiamo ricordare le *Sale* completate in un *Ledger*, ai fini legali e di contabilità.

> **Linea guida:** considerare l'inclusione delle seguenti associazioni in un modello di dominio.
> - Associazioni per cui la conoscenza della relazione deve essere conservata per una qualche durata (associazioni "da ricordare").
> - Associazioni derivate dall'elenco di associazioni comuni.

![[dominio4.png]]

> **Attenzione:** la freccia della direzione di lettura non ha significato in termini di modello; è solo un aiuto per il lettore del diagramma.

## Linea guida: come assegnare il nome a un'associazione in UML

> **Linea guida:** assegnare il nome a un'associazione in base al formato *NomeClasse-LocuzioneVerbale-NomeClasse*, in cui la locuzione verbale (ovvero, una frase formata da un verbo principale, più eventuali complemento, oggetto e avverbi) crea una sequenza leggibile e significativa.

Nomi di associazioni semplici come "Ha" oppure "Usa" vanno se possibile evitati, poichè raramente migliorano la comprensione del dominio. Per esempio:

- *Sale Paid-by CashPayment:* un cattivo esempio (poichè non migliora la comprensione) è: *Sale Uses CashPayment*;
- *Player Is-on Square:* un cattivo esempio è: *Player Has Square*.

I nomi delle associazioni devono iniziare con una lettera maiuscola, poichè un'associazione rappresenta un classificatore di collegamenti tra istanze; in UML, i nomi dei classificatori devono iniziare con una lettera maiuscola. Due formati comuni e ugualmente validi per il nome composto di una associazione sono:

- *Records-current*
- *RecordsCurrent*

Ecco un elenco di associazioni comuni:
- **A è una transazione correlata a un'altra transazione B;**
- **A è un elemento/riga di una transazione B;**
- **A è un prodotto o servizio r una transazione (o riga per l'articolo) B;**
- **A è un ruolo relativo a una transazione B;**
- **A è una parte fisica o logica di B;**
- **A è contenuto fisicamente o logicamente in B;**
- **A è una descrizione per B;**
- **A è noto/registrato/memorizzato/riportato/acquistato in B;**
- **A è un membro di B;**
- **A è una sottounità organizzativa di B;**
- **A utilizza o gestisce o possiede B;**
- **A è vicino/prossimo a B.**

## Attibuti

È utile identificare gli attributi delle classi concettuali necessari per soddisfare i requisiti informativi per gli scenari correnti in corso di sviluppo. Un **attributo** è un valore logico (un dato) di un oggetto.

![[dominio5.png]]

Per esempio, una ricevuta (che riporta le informazioni di una vendita) nel caso d'uso *Process Sale* normalmente comprende, tra l'altro, una data e un'ora, il nome e l'indirizzo del negozio e l'ID del cassiere. Pertanto:

- *Sale* necessita di un attributo *dateTime*;
- *Store* necessita di *name* e *address*;
- *Cashier* necessita di un ID.

![[dominio6.png]]

![[dominio7.png]]

Il metodo più utile per esprimere il fatto che un *Cashier* utilizza un *Register* è tremite un'associazione, non con un attributo.

> **Linea guida:** gli attributi in un modello di dominio devono preferibilmente essere di **tipi di dato**. Tra i tipi di dato più comuni ci sono: *Boolean, Date (o DateTime), Number, Character, String (Text), Time*.
> 
> Alcuni tipi comuni *Address, Color, Geometrics (Point, Rectangle), Phone Number, Social Security Number (Codice Fiscale), Universal Product Code (UPC), SKU, ZIP (CAP) o codice postali, tipi enumerati.*

Per riprendere un esempio precedente, un equivoco comune consiste nel modellare un concetto complesso del dominio come un attributo. Per esempio, l'aeroporto del destinatario di un volo non è solo una stringa; è qualcosa di complesso, che occupa molti chilometri quadrati di spazio. Pertanto, *Flight* dovrebbe essere correlato ad *Airport* mediante un'associazione, non un attributo.

> **Linea guida:** classi concettuali vanno correlate con un'associazione, non con un attributo.

![[dominio8.png]]

Ma non è soltanto questo (per esempio, i codici identificativi degli articoli hanno anche delle parti secondarie). In effetti, è utile avere nel modello di dominio una classe chiamata *ItemID (o ItemIdentifier)*, e designare il tipo dell'attributo come tale. Per esempio, *itemID:ItemIdentifier*.

Applicando le linee guida agli attributi del modello di dominio per POS si ottiene la seguente analisi:

- Il codice identificativo degli articoli è un'astrazione di vari schemi di codifica comuni, compreso UPC-A, UPC-E e la famiglia degli schemi EAN. Questi schemi di codifica numerica hanno parti secondarie che identificano il produttore, il prodotto, il paese (per EAN), nonchè una cifra che corrisponde alla somma di controllo per la convalida. Pertanto, ci dovrebbe essere una classe tipo di dato *ItemID*, dato che soddisfa molte delle linee guida proposte.
- Gli attributi *price* e *amount* dovrebbero essere di una classe tipo di dato *Money*, poichè sono quantità in unità monetaria.
- L'attributo *address* dovrebbe essere di una classe tipo di dato Address, poichè contiene sezioni separate.

Di seguito le linee guida relative a quando è utile modellare i tipi di dato.

> **Linea guida:** rappresentare nel modello di dominio come una nuova classe tipo di dato ciò che inizialmente può essere considerato un numero o una stringa se: 
> - **È composto da sezioni separate:** per esempio, numero di telefono, nome di persona;
> - **Ci sono operazioni a esso associate, come il parsing o la convalida:** numero di previdenza sociale, codice fiscale;
> - **Ha altri attributi:** un prezzo promozionale potrebbe avere una data di inizio (effettiva) e una data di fine;
> - **È una quantità con un'unità di misura:** l'importo di un pagamento ha un'unità monetaria;
> - **È un'astrazione di uno o più tipi con alcune di queste qualità:** il codice identificativo di articolo nel dominio delle vendite è una generalizzazione di tipi come Universal Product Code (UPC) e European Article Number (EAN).

![[dominio9.png]]

## Conclusione: il modello di dominio è corretto?

Non esiste un solo modello di dominio corretto. Tutti i modelli sono approssimazioni del dominio che si sta tentando di capire; il modello di dominio è in primo luogo uno strumento di comprensione e di comunicazione all'interno di un gruppo particolare.

Una domanda migliore è: il modello di dominio è utile? Un modello di dominio utile coglie le astrazioni essenziali e le informazioni richieste per capire il dominio nel contesto dei requisiti correnti, e aiuta le persone nella comprensione del dominio (i suoi concetti, la terminologia e le relazioni esistenti).