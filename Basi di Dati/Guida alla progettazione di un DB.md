---
aliases: [Guida progettazione DB, GPD]
tags: [basi-di-dati]
---
## Fasi preliminari
Progettare una base di dati significa definirne il contenuto e la struttura che essa deve avere.
Per quanto riguarda le metodologie di progettazione, essendo che le [[Basi di Dati|basi di dati]] sono sempre più complesse e sofisticate, è necessario un approccio sistematico.
L'obiettivo della progettazione è produrre i seguenti risultati:
- uno schema dei dati;
- un insieme di sottoschemi di applicazione;
- un insieme di programmi applicativi.
Le fasi di progettazione sono:
- raccolta ed analisi dei requisiti;
- [[Progettazione concettuale|progettazione concettuale]];
- progettazione logica;
- [[Progettazione fisica|progettazione fisica]].
Ogni fase si basa su un modello che permette di generare una rappresentazione formale della base di dati ad un dato livello di astrazione.

L’utilizzo dei modelli appropriati permette di verificare le caratteristiche della base di dati e comunicare con i futuri utenti della base di dati.

## Step della progettazione

![[stepProgettazione.PNG]]

## Raccolta ed analisi dei requisiti
È la fase in cui si raccolgono le specifiche informali ed eterogenee che i vari utenti danno delle procedure da automatizzare mediante un DBMS.
Le sotto fasi principali sono:
- **requisiti informativi:** caratteristiche dei dati;
- **requisiti sui processi:** operazioni sui dati;
- **requisiti sui vincoli di integrità:** proprietà dei dati e delle operazioni;
- **disambiguazione delle specifiche dell'utente.**
L'analisi in genere inizia con la raccolta dei primi requisiti e spesso procede per *raffinamenti successivi* delle specifiche raccolte.
Gli spunti per l'interazione con il committente sono:
- effettuare spesso verifiche di comprensione e coerenza;
- verificare anche per mezzo di esempi (generali e relativi a casi limite);
- richiedere definizioni e classificazioni;
- far evidenziare gli aspetti essenziali rispetto a quelli marginali.

## Documentazione descrittiva
Le regole generali per formare la documentazione scritta sono:
- costruire un *glossario dei termini*;
- individuare omonimi e sinonimi e unificare i termini;
- rendere esplicito il riferimento fra termini;
- riorganizzare le frasi per concetti.
I documenti da produrre sono:
- descrizione sintetica della realtà d'interesse;
- glossario dei termini; 

![[glossarioTermini.PNG]]

    - i dati: 

![[datiRealtaInteresse.PNG]]

        1. *analisi puntuali*
        1. *analisi aggregate*
- **raffinamento** delle specifiche e **individuazione** dei concetti di base; 

![[raffinamentoEindividuazione.PNG]]

## [[Progettazione concettuale]]
A partire dai requisiti informativi viene creato uno **schema concettuale**, cioè una descrizione formalizzata e integrata delle esigenze aziendali, espressa in modo **indipendente** dal DBMS adottato.

Lo schema prodotto nella progettazione è concettuale si basa sul modello concettuale: quest'ultimo è una descrizione ad alto livello indipendente dall'implementazione ed è la prima rappresentazione formale e del tutto indipendente dall'implementazione della base di dati (indipendente anche dal tipo di DBMS che sarà utilizzato - relazionale, object-relational, gerarchico, …).

Tipicamente, lo schema finale viene generato attraverso la produzione di diversi schemi ER corrispondenti ai vari livelli di raffinamento.

## Qualità di uno schema concettuale
- **CORRETTEZZA:** uno schema ER è corretto quando utilizza propriamente i costrutti messi a disposizione dal modello ER. Possono esserci errori sintattici (uso non ammesso di costrutti) oppure errori semantici (uso di costrutti che non rispetta la definizione);
- **COMPLETEZZA:** uno schema ER è completo quando rappresenta tutti i dati di interesse e tutte le operazioni possono essere eseguite a partire dai concetti descritti dallo schema;
- **LEGGIBILITÀ:** uno schema ER è leggibile quando rappresenta tutti i dati in maniera naturale e comprensibile;
- **MINIMALITÀ:** uno schema E-R è minimale quando tutte le specifiche sui dati sono rappresentate una sola volta nello schema. Uno schema non è minimale quando esistono delle ridondanze, cioè concetti che possono essere derivati da altri. Non sempre una ridondanza è indesiderata, ma può nascere da precise scelte progettuali

## Progettazione schema concettuale
I documenti prodotti dalla progettazione sono:
1. schema ER finale;
1. vincoli dello schema non esprimibili dal modello ER, espressi in linguaggio naturale in modo preciso e puntuale;
1. dizionario dei dati (descrizione schematica di entità e delle relazioni dello schema ER);
1. discussione di scelte critiche nella produzione dello schema ER finale.

## Schema ER finale
Lo schema ER finale deve essere prodotto in forma completa, indicando:
1. gli *attributi* di ogni entità e di ogni relationship;
1. gli *identificatori* di ogni entità;
1. le *cardinalità* di ogni relationship;
1. i *ruoli* per le relationship (se necessari).

## Dizionario dei dati
Sarebbe la descrizione schematica dei dati espressi nello schema ER. Contiene:
    - descrizione del suo significato;
    - descrizione e dominio di ogni attributo (quando necessario); se l'attributo è derivato, la relativa regola di computazione;
    - identificatori dell'entità (con specifico riferimento alle relationship e alle entità coinvolte in eventuali identificazioni esterne);
    - descrizione del suo significato;
    - descrizione e dominio di ogni attributo;
    - ruolo della partecipazione di ogni entità coinvolta nelle relationship mettendo in evidenza il significato del ruolo (motivando, se necessario, la scelta delle cardinalità).

## Progettazione logica
È la traduzione dello schema concettuale nel modello dei dati del DBMS. Il risultato sarebbe lo schema logico nel DDL del DBMS. Gli aspetti considerati durante la progettazione logica sarebbero integrità e consistenza, [[Sicurezza|sicurezza]] ed efficienza.

La progettazione logica consiste nelle seguenti fasi:
- ristrutturazione dello schema concettuale;
- traduzione canonica;
- valutazione ed eventuale modifica dello schema.

## [[Progettazione fisica]]
In questa fase vengono scelte le caratteristiche fisiche di realizzazione della base di dati.
Il risultato sarebbe lo schema fisico che descrive le strutture di memorizzazione e accesso ai dati (es. clustering, indici, ...)