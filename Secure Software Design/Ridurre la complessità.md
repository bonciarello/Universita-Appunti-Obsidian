---
aliases: [RC, Ridurre complessità]
tags: [secure-software-design]
---
Le entità possono transitare in stati inconsistenti, pertanto bisogna garantire che queste vengano gestite adeguatamente. Ci sono dei pattern che ci aiutano a fare questo:
- **Partially immutable entities:** tutto ciò che non si aspetta debba mutare deve essere immutabile;
- **Entity state objects (single thread):** vengono modellate alcune entità per rappresentare gli stati in cui piò vertere il sistema;
- **Entity snapshot (multi-thread):** anche qui condividere oggetti mutevoli può indurre a deadlock o problemi di sincronizzazione. In questo caso si realizzano degli snapshot immutabili (foto) di queste entità. Generalmente viene delegata un'altra classe per realizzarli, l'unico metodo che deve essere sincronizzato è quello di create dello snapshot;
- **Entity relay (decomposition):** è un pattern che permette di gestire entità con numerosi stati. L'idea è quello di identificare le "fasi della vita" dell'entità e realizzare per ciascuna di esse una classe. Quindi quando cambia stato cambia anche l'entità. Quando il numero di stati diventa molto complesso da gestire l'ideale sarebbe quello di realizzare famiglie di stati. Questo pattern è utile quando esiste una sottospecie di catena di montaggio.

## Partially immutable entities
Quando qualcosa è mutevole, c'è il rischio che qualche altra parte del codice lo modifichi: quando qualcosa cambia nel codice, c'è il rischio che cambi in modo indesiderato. Modifiche indesiderate possono verificarsi perché qualche altro pezzo del codice è rotto o perché qualcuno ha identificato un punto debole e lo ha utilizzato per lanciare un attacco.

Un modo efficace per evitare questi problemi attraverso la progettazione è rendere le entità parzialmente immutabili.
Quando ci sono attributi che non dovrebbero cambiare, rendi le entità parzialmente immutabili per evitare errori che interrompono l'integrità.

Passiamo ora a un aspetto più complicato delle entità: il fatto che il comportamento, o comportamento consentito, può cambiare a seconda dello stato in cui si trovano le entità.

## Entity state objects
Una cosa che rende difficile lavorare con le entità è che non tutte le azioni sono consentite in tutti gli stati.

Il progetto deve garantire che queste regole sugli stati delle entità siano rispettate. In caso contrario, possono verificarsi problemi di [[Sicurezza|sicurezza]]. La gestione mancante, incompleta o interrotta dello stato dell'entità è qualcosa che incontriamo spesso in quasi tutte le basi di codice di dimensioni significative.
La causa di questo problema di [[Sicurezza|sicurezza]] è che le regole statali spesso non sono affatto progettate o sono implicite e vaghe. Spesso è ovvio che non c'è uno sforzo cosciente nel design; piuttosto, le regole sono apparse gradualmente nella base di codice, molto probabilmente caso per caso.

La manifestazione nel codice è spesso una di due varianti: regole incorporate nei metodi di servizio o istruzioni *if* nei metodi di entità.

Vediamo regolarmente entità che sono solo *structs* (classi con campi di dati privati, setter e getter). Le regole su come le entità possono comportarsi devono, quindi, essere rispettate dai metodi di servizio. In pratica, questo potrebbe essere possibile avviarlo quando il sistema è piccolo: ma man mano che il codice si evolve nel tempo, il rispetto delle regole diventa incoerente.

Una versione leggermente migliore è quando i metodi di entità supportano le regole di stato ma anche questo approccio potrebbe portare a incoerenze sfruttabili.

Abbiamo spesso visto queste istruzioni *if* sparse nel profondo dei metodi di entità. Quando si scava nella storia del codice, diventa spesso chiaro che sono stati aggiunti uno per uno per gestire alcuni casi speciali.

**Quando incontri un'entità con molti *if* al suo interno, prova a disegnare un grafico di stato e dare un nome a ciascuno stato.**

*Suggeriamo che lo stato di entità sia esplicitamente progettato e implementato come una classe a sé stante.* Con questo approccio, l'oggetto *state* viene utilizzato come oggetto aiutante delegato per l'entità. Ogni chiamata all'entità viene prima verificata con l'oggetto *state*.

Avere una logica del tipo *vivo o morto* nell'entità risulterebbe probabilmente in un paio di istruzioni *if* che diminuirebbero sia la leggibilità che la verificabilità e, nel tempo, porterebbero a un codice meno sicuro.

L'estrazione della gestione dello stato in un oggetto separato rende il codice dell'entità molto più robusto e molto meno soggetto a sottili problemi di integrità aziendale come i clienti che evitano di pagare i propri ordini prima che vengano spediti. Si consiglia di utilizzare un oggetto di stato separato quando sono presenti almeno due stati con regole diverse e quando le transizioni tra di loro non sono del tutto banali. Probabilmente non useremmo un oggetto di stato separato per rappresentare lo stato di una lampadina ma per qualsiasi cosa più complicata di così, ti consigliamo di considerare l'utilizzo di un oggetto di stato separato.

Sfortunatamente, ciò porta ad altri problemi, come **capacità limitata** e **potenziali deadlock**.

## Entity snapshot
Gli ambienti multithread sono ambienti in cui è possibile accedere alla stessa istanza di entità da più thread. In una soluzione ad alte prestazioni in cui i tempi di risposta sono critici, si desidera evitare di colpire il [[Database|database]].

Il tempo di andata e ritorno per recuperare i dati dal [[Database|database]] ucciderebbe le risposte rapide che cerchi in queste situazioni. Invece, potresti tenere in memoria le tue entità il più possibile. Tutti i thread che devono funzionare con un'entità recuperano i dati dalla cache e la rappresentazione dell'entità viene condivisa tra i thread. Ciò si traduce in tempi di risposta rapidi e capacità elevata, ma pone un onere aggiuntivo sulla progettazione delle entità: devono vivere bene in un ambiente con più thread.

Un modo per gestire questa situazione sarebbe aggiungere molte parole chiave sincronizzate al codice, ma ciò comporterebbe molti thread in attesa l'uno dell'altro e ridurrebbe drasticamente la capacità: peggio ancora, potrebbe causare un deadlock, in cui due thread si aspettano l'un l'altro indefinitamente.

Quando si progetta con il modello *entity snapshot*, si dispone di un'entità, ma tale entità non è rappresentata nel codice tramite una classe di entità mutabile. Al contrario, ci sono istantanee dell'entità che vengono utilizzate per guardare quell'entità e agire. Questo è più facilmente descritto attraverso una metafora.

Il modello *entity snapshot* supporta anche l'integrità poiché l'istantanea è immutabile, non c'è alcun rischio che la rappresentazione muti in uno stato falloso. Un'entità ordinaria con metodi che cambiano il suo stato è vulnerabile a bug di quel tipo, ma l'istantanea non lo è. C'è un codice che cambia lo stato dei dati sottostanti e quel codice può contenere bug, ma almeno lo snapshot utilizzato per mostrare lo stato dell'entità non può cambiare.

L'implementazione di entità che utilizzano il modello *entity snapshot* consente loro di vivere bene in un ambiente multithread senza causare gli svantaggi che si ottengono se si spruzza il codice con la sincronizzazione. Evitare inoltre conflitti consentendo al [[Database|database]] di gestire la sincronizzazione delle transazioni, con tutti i problemi di blocco coinvolti.

## Entity relay
Molte entità hanno un numero ragionevolmente basso di stati separati e sono abbastanza facili da comprendere.

Un grafo dello stato dell'entità cresce e diventa abbastanza grande e meno facile da comprendere. Potrebbe benissimo essere progettato in questo modo fin dall'inizio, ma più spesso un tale progetto è il risultato di una lunga storia di molti cambiamenti. La maggior parte delle modifiche è stata probabilmente percepita come una piccola correzione al momento in cui sono state apportate, ma il risultato cumulativo con il passare del tempo è di molti stati.

Può essere difficile comprendere tutti i possibili stati e transizioni per un'entità del genere, anche quando si guarda il grafico. Implementare questi stati nel codice e garantire che tutte le diverse regole siano applicate in tutti i diversi stati sarebbe un incubo. Quando questa entità viene implementata come una singola classe, quella classe diventa così complessa da rischiare di contenere incongruenze difficili da individuare.

L'idea di base del modello *entity relay* è quella di dividere la durata della vita dell'entità in fasi e lasciare che ciascuna entità rappresenti la propria fase. Quando una fase è finita, l'entità scompare e un altro tipo di entità prende il sopravvento, come una staffetta.

Il potere del modello *entity relay* deriva dalla capacità di dividere la durata complessiva della vita dell'entità in fasi e modellare invece le fasi come un'entità dopo l'altra. Affinché ciò funzioni correttamente, non dovrebbe esserci alcun ritorno a una fase precedente: se ci sono loopback, puoi ancora applicare la stessa idea, ma la semplicità della metafora del relè è persa, così come gran parte del guadagno.

Quando applichi questa strategia, cerca i posti in cui non puoi tornare indietro, in modo che un'entità precedente nella catena non abbia mai bisogno di rinascere. È anche preferibile se c'è un solo stato finale per ogni entità nella staffetta che dà origine a una nuova entità: il prossimo corridore in gara.

Affinché questo modello sia prezioso, devi vedere tre fattori in atto:
- troppi stati in un'entità;
- fasi in cui non si torna mai a una fase precedente;
- semplici transizioni da una fase all'altra e pochi punti di transizione (preferibilmente uno solo).

Se il numero di stati nella tua entità è gestibile così com'è, non c'è motivo di introdurre la complessità di diverse entità con nomi diversi. Il potere del concatenamento di entità deriva dalla semplicità che una volta che un'entità precedente è terminata, hai finito.

Se è possibile riaprire un'entità precedente, sarebbe paragonabile a un corridore che consegna il testimone a un corridore precedente.

Se ci sono molti modi in cui un'entità precedente può dar luogo a un'entità successiva, è necessario considerare se i vantaggi dell'inoltro di entità superano i costi. C'è una semplicità nell'avere un solo posto dove nasce l'entità successiva. Se ce ne sono diversi, prendi in considerazione il rimodellamento.

## Conclusione
In sintesi, le entità possono essere progettate per essere parzialmente immutabili. La gestione dello stato è più facile da testare e sviluppare quando viene estratta in un oggetto separato. Gli ambienti multithread per capacità elevate richiedono un'attenta progettazione. Il blocco del [[Database|database]] può porre un limite alla disponibilità delle entità. Gli snapshot di entità sono un modo per riguadagnare un'elevata disponibilità in ambienti multithread. L'*entity relay* (quando l'adempimento di un'entità ne dà origine a un'altra) è un modo alternativo per modellare un'entità che ha molti stati diversi.
