Kent Beck fornisce una discussione sulle tecniche/suggerimenti che nell'esperienza sua e dei suoi compagni di squadra migliorano il flusso di lavoro TDD. Tra questi:

- **pattern a barre rosse:** quando e dove scrivere i test e quando interromperli;
- **pattern a barre verdi:** come far passare i test il più velocemente possibile;
- **pattern di test:** tecniche per scrivere i test;
- **pattern di refactoring:** cosa/come refactoring nella fase di refactoring.

La maggior parte di essi è diventata "conoscenza implicita" tra gli sviluppatori e sono profondamente radicati nelle migliori pratiche e nella progettazione di biblioteche popolari. Ad esempio, quando il framework di unit test in stile *xUnit* era una cosa, l'organizzazione del test in "suite" e le apparecchiature era un argomento caldo e un punto di discussione: l'idea ha così tanto senso ed è così efficace che tutti i moderni framework di test lo usano, e ci sembra naturale ogni volta che lo incontriamo.

## Pattern a barre rosse
- **Test iniziale:** il primo test che scrivi dovrebbe essere qualcosa che sei sicuro di poter implementare
- **Spiegazione test:** include discussioni/suggerimenti con i colleghi come test nella codebase;
- **Test di regressione:** una volta trovato un bug, aggiungi un test nella tua suite che lo "copra".

## Pattern a barre verdi
- **Fake it:** va bene restituire le costanti per far passare un test all'inizio;
- **Triangolare:** ritardare l'introduzione dell'astrazione finché non si è sicuri che siano necessarie
- **Implementazione obbligata:** se qualcosa è "ovvio" per te, forse l'hai testato 100 volte, implementalo.
- **Uno a molti:** se è necessario lavorare con strutture dati, (ove applicabile) prima lavorare con uno/due elementi, quindi generalizzare a *n* elementi.

## Pattern di refactoring
Quando si esegue il refactoring, *idealmente*, i test dovrebbero sempre rimanere verdi. A tal fine, è necessario eseguire piccole modifiche al codice da refactoring ed eseguire nuovamente i test dopo ogni modifica.
- fattorizza la duplicazione;
- non disperdere i refactoring in troppi posti, cerca di concentrarti su piccole porzioni di codice. Introdurre/estrarre metodi o termini ausiliari per raggiungere questo obiettivo *(isola modifiche)*;
- mira a rendere identiche strutture simili, quindi uniscile (scomponile) *(riconcilia le differenze)*.

## Pattern di test
- **Child test:** se un test è troppo grande e continua a fallire, dividilo in test più piccoli e cerca di farli funzionare;
- ***Mock objects:*** simula le dipendenze (costose, ingombranti) tramite "oggetti falsi" che restituiscono costanti;
- **Isolated tests:** i test non dovrebbero influenzarsi a vicenda ed essere indipendenti dall'ordine;
- **Test list:** tieni traccia dei test eseguiti e dei test che vogliamo eseguire (caratteristiche mancanti);
- **Test Data, Realistic Data:** entrambi hanno il loro valore.

## Test double

I **test double** (informalmente indicati anche con la denominazione impropria di *mock*) sono uno strumento utilizzato in programmazione, specialmente orientata agli oggetti, come ausilio nello sviluppo di test unitari.

Un test double è un oggetto che presenta la stessa interfaccia di un oggetto appartenente all'applicazione, ma ha un comportamento semplificato (o addirittura "vuoto"). Dato che l'interfaccia è la stessa, l'oggetto applicativo può essere "rimpiazzato" dal suo test double in quei test in cui il comportamento dell'oggetto reale non è rilevante o addirittura non è riproducibile.

Le modalità con cui il test double viene sostituito all'oggetto reale sono in generale riconducibili alle tecniche di *dependency injection*.

### Tipi di test double
Il **concetto di test double** è molto ampio e può applicarsi a oggetti "controfigura" con caratteristiche strutturali e funzionalità molto diverse fra loro. La classificazione dei tipi di test double, e la corrispondente terminologia, è stata storicamente incerta, ma si è andata gradualmente standardizzando. Fra gli altri, hanno proposto la classificazione "standard" *Gerard Meszaros* (nel libro XUnit Test Patterns del 2007), *Martin Fowler* (Mocks Aren't Stubs) e *Robert "Uncle Bob" Martin*. La classificazione proposta da questi autori è stata adottata, tra l'altro, da Microsoft.

I principali tipi di test double secondo la classificazione comune sono i seguenti:
- **Dummy:** un test double privo di comportamento. L'applicazione tipica di un dummy è al caso in cui sia necessario passare un oggetto come parametro a un metodo, ma si prevede che tale oggetto non sarà usato nel particolare flusso di esecuzione previsto dal test;
- **Stub:** un test double che fornisce un comportamento fissato (per esempio, dotato di metodi che tornano sempre lo stesso valore, senza eseguire alcuna computazione). Viene usato per controllare l'"input indiretto" del codice sotto test;
- **Spy:** un test double che memorizza i messaggi (chiamate di metodo) che riceve dal codice sotto test, in modo che sia possibile in seguito verificare che siano soddisfatte determinate aspettative;
- **Mock:** una variante dello "spy" in cui il test double è responsabile anche di memorizzare esplicitamente le aspettative sul comportamento del codice sotto test, e di verificare che siano soddisfatte;
- **Fake:** test double con un comportamento programmabile, che simula quello dell'oggetto reale (per esempio un oggetto che implementa un database in memoria invece di accedere a un database reale).
