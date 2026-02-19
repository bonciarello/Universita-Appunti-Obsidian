---
aliases: [Oggetto]
tags: [ingegneria-del-software]
---
L'analisi orientata agli oggetti, la progettazione e la programmazione sono correlate ma distinte.

- **OOA:** sviluppo di un modello a oggetti del dominio dell'applicazione;
- **OOD:** sviluppo di un modello di sistema orientato agli oggetti per implementare i requisiti;
- **OOP:** realizzare un OOD utilizzando un linguaggio di programmazione OO come Java o C++.

Gli oggetti sono astrazioni di entità del mondo reale o di sistema e si gestiscono da soli. 
Gli oggetti sono indipendenti e incapsulano informazioni sullo stato e sulla rappresentazione.
La funzionalità del sistema è espressa in termini di servizi oggetto.
Le aree dati condivise vengono eliminate. Gli oggetti comunicano tramite il passaggio di messaggi.
Gli oggetti possono essere distribuiti ed eseguiti in sequenza o in parallelo.

I vantaggi della OOD:

- **manutenzione più semplice:** gli oggetti possono essere intesi come entità autonome;
- **riutilizzabilità:** gli oggetti sono componenti potenzialmente riutilizzabili;
- **metafora naturale:** potrebbe esserci una mappatura ovvia dalle entità del mondo reale agli oggetti di sistema.

## Unified Modeling Language

Negli anni '80 e '90 sono state proposte diverse notazioni per descrivere i progetti orientati agli oggetti.

**Unified Modeling Language è un'integrazione di queste notazioni ed è utile quando si mette mano su codice che non si conosce.**

Descrive le notazioni per una serie di modelli diversi che possono essere prodotti durante l'analisi e la progettazione OO.

Ora è uno standard de facto per la modellazione OO.

## Processo di progettazione orientato agli oggetti

I processi di progettazione strutturata implicano lo sviluppo di diversi modelli di sistema.

Richiedono molto impegno per lo sviluppo e la manutenzione di questi modelli e, per i sistemi piccoli, questo potrebbe non essere conveniente.

Tuttavia, per i grandi sistemi sviluppati da gruppi diversi, i modelli di progettazione sono un meccanismo di comunicazione essenziale.

## Fasi del processo

Evidenzia le attività chiave senza essere vincolato ad alcun processo proprietario come il RUP. Possibili attività chiavi:

- definire il contesto e le modalità di utilizzo del sistema;
- progettare l'[[Architettura|architettura]] del sistema;
- identificare i principali oggetti di sistema;
- sviluppare modelli di progettazione;
- specificare le interfacce degli oggetti.

## Contesto e modelli di utilizzo del sistema

- **Contesto di sistema:** un modello statico che descrive altri sistemi nella ambiente. Utilizzare un modello di sottosistema per mostrare altri sistemi. La diapositiva seguente mostra i sistemi intorno al sistema della stazione meteorologica;
- **Modello di utilizzo del sistema:** un modello dinamico che descrive come il sistema interagisce con il suo ambiente. Usa [[Casi d'uso|casi d'uso]] per mostrare le interazioni.

## [[Architettura]] a strati

![[architetturaStrati.png]]

## [[Casi d'uso]] per la stazione meteorologica

Per dare un rapido accesso ai requisiti, si utilizza il diagramma dei [[Casi d'uso|casi d'uso]].

![[casiDUso.png]]

- **Sistema:** stazione meteorologica;
- **Caso d'uso:** report;
- **Azione:** sistema di raccolta dati meteo, stazione meteo;
- **Dati:** la stazione meteorologica invia un riepilogo dei dati meteorologici che sono stati raccolti dagli strumenti nel periodo di raccolta al sistema di raccolta dei dati meteorologici. I dati inviati sono le temperature minime e medie massime del suolo e dell'aria, le pressioni atmosferiche massima, minima e media, la velocità del vento massima, minima e media, le precipitazioni totali e la direzione del vento campionata a intervalli di 5 minuti;
- **Stimolo:** il sistema di raccolta dei dati meteorologici stabilisce un collegamento modem con la stazione meteorologica e richiede la trasmissione dei dati.;
- **Risposta:** I dati riepilogati vengono inviati al sistema di raccolta dei dati meteorologici;
- **Commenti:** Di solito alle stazioni meteorologiche viene chiesto di segnalare una volta all'ora, ma questa frequenza potrebbe differire da una stazione all'altra e potrebbe essere modificata in futuro.

## Progettazione architettonica

Una volta comprese le interazioni tra il sistema e il relativo ambiente, si utilizzano queste informazioni per progettare l'[[Architettura|architettura]] del sistema.

Normalmente non dovrebbero esserci più di 7 entità in un modello architettonico.

Ricollegandoci all'esempio di prima:

![[architetturaMeteo.png]]

## Identificazione dell'oggetto

**L'identificazione di oggetti (o classi di oggetti) è la parte più difficile della progettazione orientata agli oggetti.**

*Non esiste una "formula magica" per l'identificazione degli oggetti*: si basa sull'abilità, l'esperienza e la conoscenza del dominio dei progettisti di sistemi.

L'identificazione degli oggetti è un processo iterativo. È improbabile che tu lo faccia bene la prima volta.

Possibili approcci all'identificazione sono:

- utilizzare un approccio grammaticale basato su una descrizione del sistema in linguaggio naturale (utilizzato nel metodo Hood OOD);
- basare l'identificazione su elementi tangibili nel dominio dell'applicazione;
- utilizzare un approccio comportamentale e identificare gli oggetti in base a ciò che partecipa a quale comportamento;
- utilizzare un'analisi basata su scenari. Gli oggetti, gli attributi dei metodi in ogni scenario sono identificati.

## Modelli di progettazione

I modelli di progettazione mostrano gli oggetti e le classi di oggetti e le relazioni tra queste entità. Abbiamo modelli statici e dinamici:

- **Modelli statici:** descrivono la struttura statica del sistema in termini di classi di oggetti e relazioni.
- **Modelli dinamici:** descrivono le interazioni dinamiche tra gli oggetti.

## Modelli di sequenza

I modelli di sequenza mostrano la sequenza delle interazioni degli oggetti che hanno luogo. Le caratteristiche:

- gli oggetti sono disposti orizzontalmente nella parte superiore;
- il tempo è rappresentato verticalmente in modo che i modelli vengano letti dall'alto verso il basso;
- le interazioni sono rappresentate da frecce etichettate. Diversi stili di freccia rappresentano diversi tipi di interazione;
- un rettangolo sottile in una linea di vita di un oggetto rappresenta il momento in cui l'oggetto è l'oggetto di controllo nel sistema.

## Specifica dell'interfaccia dell'oggetto

Le interfacce degli oggetti devono essere specificate in modo che gli oggetti e gli altri componenti possano essere progettati in parallelo.

I progettisti dovrebbero evitare di progettare la rappresentazione della interfaccia, ma dovrebbero nasconderla nell'oggetto stesso.

Gli oggetti possono avere diverse interfacce che sono punti di vista sui metodi forniti.

L'UML utilizza i diagrammi di classe per la specifica dell'interfaccia, ma può essere utilizzato anche Java.