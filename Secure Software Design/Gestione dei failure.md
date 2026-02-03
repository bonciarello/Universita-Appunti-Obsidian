Molto spesso i **"fallimenti"** all'interno delle applicazioni vengono **gestiti attraverso le eccezioni**. Naturalmente queste non dovrebbero essere note all'utente finale poiché possono contenere informazioni delicate. 

Possiamo distinguere diversi momenti in cui desideriamo sollevare un'eccezione:
- **business exceptions:** previene movimenti illegali da un punto di vista del dominio (ad esempio, prelevare soldi su un conto privo di essi);
- **technical exceptions:** si preoccupa di controllare le regole del dominio (ad esempio, aggiungere item in contenitori troppo piccoli).

La scelta migliore sarebbe quello di dividere il più possibile queste due tipologie di eccezioni.

Inoltre le eccezioni dovrebbero essere utilizzate non per notificare comportamenti prevedibili, infatti generalmente un metodo ha due possibili output: *uno di successo* e *uno di insuccesso*.

Qualora il secondo venisse modellato attraverso le eccezioni, il workflow del programma stesso sarebbe compromesso. Talvolta, può essere utile definire dei "result object" i quali definiscono il comportamento del metodo in caso di insuccesso. 

Abbiamo alcuni vantaggi della progettazione dei fallimenti come risultati attesi e non eccezionali:
- *ambiguità tra eccezioni di dominio ed eccezioni tecniche:* le eccezioni di dominio vengono completamente rimosse;
- *eccezione payload che perde nei log:* gli errori non sono gestiti da un codice generico di gestione degli errori e, quindi, i dati trasportati dal payload non scivolano accidentalmente nei log degli errori;
- *perdita involontaria di informazioni sensibili:* gli errori vengono gestiti in un contesto che conosce ciò che è sensibile e ciò che non lo è e sa come gestire correttamente i dati sensibili.

## Progettare per la disponibilità
Non vuoi che la tua applicazione o servizio non sia disponibile. Eppure, non puoi pretendere di soddisfare ogni richiesta: c'è sempre un limite fisico. Meglio informare l'utente che il sistema è occupato che lasciarlo aspettare per sempre: implementare le code.

## Gestione di dati errati
I dati sono spesso sporchi: spazi qua e là, caratteri mancanti e caratteri speciali. Non tentare di riparare l'input perchè si potrebbero avere **flussi di iniezione** e **attacchi di secondo ordine** (la vulnerabilità sorge su un altro sistema, come il visualizzatore di log).

## Conclusione
In sintesi, separare le eccezioni aziendali dalle eccezioni tecniche è una buona strategia di progettazione perché i dettagli tecnici non appartengono al dominio: non dovresti mischiare eccezioni tecniche e commerciali usando lo stesso tipo. È buona pratica di progettazione non includere mai i dati aziendali nelle eccezioni tecniche, indipendentemente dal fatto che siano sensibili o meno. È possibile creare codice più sicuro progettando gli errori e trattandoli come risultati normali e non eccezionali. La disponibilità è un importante obiettivo di sicurezza per i sistemi software. Resilienza e reattività sono caratteristiche che aggiungono sicurezza migliorando la disponibilità di un sistema. È possibile utilizzare modelli di progettazione come interruttori automatici, paratie e timeout per progettare la disponibilità. La riparazione dei dati prima della convalida è pericolosa e dovrebbe essere evitata a tutti i costi. Non dovresti mai ripetere l'input testualmente.
