Con la **verifica** il software deve essere conforme alle sue specifiche. Per esempio ci dobbiamo fare la domanda "stiamo costruendo il prodotto giusto?".

Con la **validazione** il software dovrebbe fare ciò che l'utente richiede realmente. Per esempio ci dobbiamo fare la domanda "stiamo costruendo il prodotto giusto?". 

Il processo V&V è un intero processo del ciclo di vita e deve essere applicato in ogni fase del processo software.

Ha due obiettivi principali: la scoperta di difetti in un sistema e la valutazione dell'utilità o meno del sistema in una situazione operativa.

V&V dovrebbe stabilire con certezza che il software è adatto allo scopo anche se non è privo di difetti: per questo motivo, deve essere sufficientemente buono per l'uso previsto.

La fiducia del V&V dipende dallo scopo del sistema, dalle aspettative degli utenti e dall'ambiente di marketing:

- **Funzione software:** il livello di fiducia dipende da quanto il software è critico per un'organizzazione;
- **Aspettative degli utenti:** gli utenti possono avere aspettative basse su alcuni tipi di software.
- **Ambiente di marketing:** portare un prodotto sul mercato in anticipo può essere più importante che trovare difetti nel programma.

In sintesi, avere un sistema è più importante di avere un sistema perfetto.

## Verifica statica e dinamica

Come si fa la verifica del software? Ispezione (statico, aprire il codice e leggerlo) o testing (dinamico, scrivere casi). Nel dettaglio:

- **Ispezioni software:** interessato all'analisi della rappresentazione del sistema statico per scoprire problemi (verifica statica): può essere integrato da documenti basati su strumenti e analisi del codice;
- **Test del software:** preoccupato per l'esercizio e l'osservazione del comportamento del prodotto (verifica dinamica): il sistema viene eseguito con i dati di test e viene osservato il suo comportamento operativo.

## Test del programma

**Può rivelare la presenza di errori NON la loro assenza.**

L'unica tecnica di convalida per i requisiti non funzionali poiché il software deve essere eseguito per vedere come si comporta.

Dovrebbe essere usato insieme alla verifica statica per fornire una copertura V&V completa.

Abbiamo due tipi di test:

- **Defect testing:** test progettati per scoprire i difetti del sistema. Un test dei difetti di successo è quello che rivela la presenza di difetti in un sistema;
- **Validation testing:** destinato a dimostrare che il software soddisfa i suoi requisiti. Un test di successo è quello che dimostra che un requisito è stato implementato correttamente.

## Testing e debugging

Il test dei difetti e il debug sono processi distinti. La verifica e la convalida riguardano l'accertamento dell'esistenza di difetti in un programma. Il debug si occupa di individuare e riparare questi errori. Il debug comporta la formulazione di un'ipotesi sul comportamento del programma, quindi il test di queste ipotesi per trovare l'errore di sistema.

## Piano di test del software

- **Processo di test:** una descrizione delle fasi principali del processo di test. Potrebbero essere come descritto in precedenza in questo capitolo;
- **Tracciabilità dei requisiti:** gli utenti sono più interessati al fatto che il sistema soddisfi i suoi requisiti e i test dovrebbero essere pianificati in modo che tutti i requisiti siano testati individualmente;
- **Articoli testati:** devono essere specificati i prodotti del processo software che devono essere testati;
- **Programma di test:** un programma di test generale e l'allocazione delle risorse per questo programma. Questo, ovviamente, è legato alla più generale tempistica di sviluppo del progetto;
- **Procedure di registrazione dei test:** non è sufficiente eseguire semplicemente i test. I risultati delle prove devono essere sistematicamente registrati. Deve essere possibile verificare il processo di test per verificare che sia stato eseguito correttamente;
- **Requisiti hardware e software:** questa sezione dovrebbe definire gli strumenti software necessari e l'utilizzo stimato dell'hardware;
- **Vincoli:** in questa sezione dovrebbero essere anticipati i vincoli che influenzano il processo di test, come la carenza di personale.

## Ispezioni software

**Si tratta di persone che esaminano la rappresentazione della fonte con l'obiettivo di scoprire anomalie e difetti.**

Le ispezioni non richiedono l'esecuzione di un sistema, quindi possono essere utilizzate prima dell'implementazione.

Possono essere applicati a qualsiasi rappresentazione del sistema (requisiti, progettazione, dati di configurazione, dati di test, ecc.).

Hanno dimostrato che è una tecnica efficace per scoprire gli errori del programma.

## Successo dell'ispezione

Molti difetti diversi possono essere scoperti in un'unica ispezione: nei test, un difetto può mascherarne un altro, quindi sono necessarie diverse esecuzioni.

Quindi, è probabile che i revisori abbiano visto i tipi di errore che si verificano comunemente.

## Ispezioni e test

**Ispezioni e prove sono tecniche di verifica complementari e non opposte.** Entrambi dovrebbero essere usati durante il processo V&V.

Le ispezioni possono verificare la conformità a una specifica ma non la conformità ai reali requisiti del cliente.

Le ispezioni non possono verificare caratteristiche non funzionali come prestazioni, usabilità, ecc.

## Ispezioni del programma

Approccio formalizzato alle revisioni dei documenti. **Destinato esplicitamente al rilevamento dei difetti** (non alla correzione).

I difetti possono essere errori logici, anomalie nel codice che potrebbero indicare una condizione errata (es. una variabile non inizializzata) o non conformità agli standard.

## Prerequisiti per l'ispezione

- Deve essere disponibile una specifica precisa;
- I membri del team devono avere familiarità con gli standard della organizzazione;
- Deve essere disponibile codice sintatticamente corretto o altre rappresentazioni di sistema;
- Dovrebbe essere preparata una lista di controllo degli errori;
- La direzione deve accettare che l'ispezione aumenterà i costi nelle prime fasi del processo software;
- La direzione non dovrebbe utilizzare le ispezioni per la valutazione del personale, ad esempio per scoprire chi commette errori.

## Procedura di ispezione

- Panoramica del sistema presentata al team di ispezione;
- Il codice dei documenti associati vengono distribuiti in anticipo al team di ispezione;
- Ha luogo l'ispezione e gli errori rilevati vengono annotati;
- Vengono apportate modifiche per riparare gli errori rilevati;
- La nuova ispezione può essere richiesta o meno.

## Utilizzo dell'analisi statica automatica

Particolarmente prezioso quando viene utilizzato un linguaggio come C che ha una tipizzazione debole e quindi molti errori non vengono rilevati dal compilatore,

Meno conveniente per linguaggi come Java che hanno un forte controllo del tipo e possono quindi rilevare molti errori durante la compilazione.

## Verifica e metodi formali

I metodi formali possono essere utilizzati quando viene prodotta una specifica matematica del sistema. Sono l'ultima tecnica di verifica statica.

Implicano un'analisi matematica dettagliata della specifica e possono sviluppare argomenti formali che un programma è conforme alla sua specifica matematica.

## Sviluppo software "Cleanroom"

Il nome deriva dal processo "Cleanroom" nella fabbricazione dei semiconduttori. **La filosofia è evitare i difetti piuttosto che rimuoverli.**

Questo processo di sviluppo del software si basa su sviluppo incrementale, specifica formale, verifica statica utilizzando argomenti di correttezza e test statistici per determinare l'affidabilità del programma.
