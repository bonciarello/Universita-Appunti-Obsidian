Il cambiamento del software è inevitabile:

- Nuovi requisiti emergono quando il software viene utilizzato;
- L'ambiente aziendale cambia;
- Gli errori devono essere riparati;
- Nuovi computer e apparecchiature vengono aggiunti al sistema;
- Potrebbe essere necessario migliorare le prestazioni o l'affidabilità del sistema.

Un problema chiave per le organizzazioni è l'implementazione e la gestione delle modifiche ai loro sistemi software esistenti.

Il modello a spirale dell'evoluzione specifica nuovi requisti, ecc. in loop.

![[modelSpiralEvolution.png]]

## Dinamiche di evoluzione del programma

**La dinamica dell'evoluzione del programma è lo studio dei processi di cambiamento del sistema.**
Lehman e Belady hanno proposto una serie di "leggi", dedotto dopo importanti studi empirici ed applicato a tutti i sistemi man mano che si sono evoluti. Ci sono osservazioni sensate piuttosto che leggi ed è applicabile a grandi sistemi sviluppati da grandi organizzazioni. Forse meno applicabile in altri casi.

## Leggi di Lehman

- **Continuo cambiamento:** un programma che viene utilizzato in un ambiente reale deve necessariamente cambiare o diventare progressivamente meno utile in quell'ambiente;
- **Complessità crescente:** quando un programma in evoluzione cambia, la sua struttura tende a diventare più complessa. Risorse supplementari devono essere dedicate alla conservazione e alla semplificazione della struttura;
- **Evoluzione del programma di grandi dimensioni:** l'evoluzione del programma è un processo di autoregolazione. Gli attributi di sistema come la dimensione, il tempo tra i rilasci e il numero di errori segnalati sono approssimativamente invarianti per ogni rilascio del sistema;
- **Stabilità organizzativa:** nel corso della vita di un programma, il suo tasso di sviluppo è approssimativamente costante e indipendente dalle risorse dedicate allo sviluppo del sistema;
- **Conservazione della familiarità:** nel corso della vita di un sistema, il cambiamento incrementale in ogni versione è approssimativamente costante;
- **Crescita continua:** le funzionalità offerte dai sistemi devono aumentare continuamente per mantenere la soddisfazione degli utenti;
- **Qualità in declino:** la qualità dei sistemi sembrerà in declino a meno che non vengano adattati ai cambiamenti nel loro ambiente operativo;
- **Sistema di feedback:** i processi di evoluzione incorporano sistemi di feedback multi-agente e multi-loop e devi trattarli come sistemi di feedback per ottenere un miglioramento significativo del prodotto.

## Manutenzione del software

**Significa modificare un programma dopo che è stato messo in uso.**
La manutenzione normalmente non comporta modifiche sostanziali all'architettura del sistema.
Le modifiche vengono implementate modificando i componenti esistenti e aggiungendo nuovi componenti al sistema.

La manutenzione è inevitabile: se l'ambiente sta cambiando, i requisiti cambiano. I sistemi DEVONO essere mantenuti quindi se devono rimanere utili in un ambiente.

## Distribuzione dello sforzo di manutenzione

Dove si spende di più nella manutenzione? Nell'aggiunta delle nuove funzioni.

![[effort.png]]

## Fattori di costo di manutenzione

- **Stabilità della squadra:** i costi di manutenzione si riducono se lo stesso personale è coinvolto per qualche tempo;
- **Responsabilità contrattuale:** gli sviluppatori di un sistema potrebbero non avere alcuna responsabilità contrattuale per la manutenzione, quindi non c'è alcun incentivo a progettare cambiamenti futuri;
- **Competenze del personale:** il personale addetto alla manutenzione è spesso inesperto e ha una conoscenza limitata del dominio;
- **Età e struttura del programma:** man mano che i programmi invecchiano, la loro struttura si degrada e diventano più difficili da comprendere e modificare.

## Processi di evoluzione

I processi di evoluzione dipendono dal tipo di software mantenuto, dai processi di sviluppo utilizzati e dalle competenze e dall'esperienza delle persone coinvolte.

Le proposte di cambiamento sono il driver per l'evoluzione del sistema. L'identificazione e l'evoluzione delle modifiche continuano per tutta la durata del sistema.

![[systemEvolutionProcess.png]]

## Reingegnerizzazione del sistema

**È la ristrutturazione o riscrittura parziale o totale di un sistema legacy senza modificarne le funzionalità.**

Applicabile quando alcuni ma non tutti i sottosistemi di un sistema più grande richiedono una manutenzione frequente.

La reingegnerizzazione implica l'aggiunta di sforzi per renderli più facili da mantenere. Il sistema può essere ristrutturato e ri-documentato.

I vantaggi della reingegnerizzazione del sistema sono:

- **Rischio ridotto:** esiste un rischio elevato nello sviluppo di nuovi software. Potrebbero esserci problemi di sviluppo, problemi di personale e problemi di specifica;
- **Costo ridotto:** il costo della reingegnerizzazione è spesso notevolmente inferiore ai costi di sviluppo di nuovo software.

## Attività di processo di reingegnerizzazione

- **Traduzione del codice sorgente:** converti il codice in una nuova lingua;
- **Ingegneria inversa:** analizzare il programma per capirlo;
- **Miglioramento della struttura del programma:** ristrutturare automaticamente per comprensibilità;
- **Modularizzazione del programma:** riorganizzare la struttura del programma;
- **Reingegnerizzazione dei dati:** pulizia e ristrutturazione dei dati di sistema.

## Evoluzione del sistema legacy

Le organizzazioni che si affidano a sistemi legacy devono scegliere una strategia per l'evoluzione di questi sistemi:

- smantellare completamente il sistema e modificare i processi aziendali in modo che non sia più necessario;
- continuare a mantenere il sistema;
- trasformare il sistema reingegnerizzando per migliorarne la manutenibilità;
- sostituire il sistema con un nuovo sistema.

La strategia scelta dovrebbe dipendere dalla qualità del sistema e dal suo valore commerciale.

Abbiamo delle categorie per sistemi legacy:

- **Bassa qualità, basso valore commerciale:** questi sistemi dovrebbero essere scartati;
- **Bassa qualità, alto valore commerciale:** forniscono un importante contributo commerciale ma sono costosi da mantenere. Dovrebbe essere riprogettato o sostituito se è disponibile un sistema adatto;
- **Alta qualità, basso valore commerciale:** sostituire con COTS, scartare completamente o mantenere;
- **Alta qualità, alto valore aziendale:** continuare a funzionare utilizzando la normale manutenzione del sistema.