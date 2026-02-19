---
aliases: [DTS, Dettagli test software]
tags: [ingegneria-del-software]
---
## Processi di test

Abbiamo due tipi di test che devono testare tutto il sistema:

- **Test dei componenti:** test dei singoli componenti del programma. Solitamente la responsabilità è dello sviluppatore del componente (tranne a volte per i sistemi critici). I test derivano dall'esperienza dello sviluppatore;
- **Test del sistema:** collaudo di gruppi di componenti integrati per realizzare un sistema o sottosistema. La responsabilità di un team di test indipendente. I test si basano su una specifica di sistema.

Gli obiettivi dei processi di test sono o per validarlo o per trovare difetti:

- **Test di convalida (validation testing):** dimostrare allo sviluppatore e al cliente del sistema che il software soddisfa i suoi requisiti. Un test riuscito mostra che il sistema funziona come previsto;
- **Test dei difetti (defect testing):** scoprire vizi o difetti del software il cui comportamento è scorretto o non conforme alle sue specifiche. Un test di successo è un test che fa funzionare il sistema in modo errato e quindi espone un difetto nel sistema. I test mostrano la presenza e non l'assenza di difetti.

Lo schema di seguito il processo di test del software:
![[softwareTestingProcess.png]]

## Politiche di test

Solo test approfonditi possono dimostrare che un programma è privo di difetti: tuttavia, è impossibile eseguire test esaustivi.

Le politiche di test definiscono l'approccio da utilizzare nella selezione dei test di sistema:

- Tutte le funzioni a cui si accede tramite i menu devono essere testate;
- Devono essere testate le combinazioni di funzioni a cui si accede attraverso lo stesso menu;
- Laddove è richiesto l'input dell'utente, tutte le funzioni devono essere testate con input corretto e errato.

## Test del sistema

**Implica l'integrazione di componenti per creare un sistema o un sottosistema.**

Può comportare il test di un incremento da consegnare al cliente.

Abbiamo due fasi:

- **Test di integrazione:** il team di test ha accesso al codice sorgente del sistema. Il sistema viene testato poiché i componenti sono integrati;
- **Test di rilascio:** il team di test verifica l'intero sistema da consegnare come una scatola nera.

## Test d'integrazione

Implica la costruzione di un sistema dai suoi componenti e il test per i problemi che derivano dalle interazioni dei componenti.

Abbiamo due tipi di integrazioni:

- **Integrazione dall'alto verso il basso:** sviluppa lo scheletro del sistema e popolalo con i componenti;
- **Integrazione dal basso:** integra i componenti dell'infrastruttura, quindi aggiungi componenti funzionali.

Per semplificare la localizzazione degli errori, i sistemi dovrebbero essere integrati in modo incrementale.

## Test di rilascio

È il processo di test di una versione di un sistema che verrà distribuito ai clienti.

**L'obiettivo principale è aumentare la fiducia del fornitore che il sistema soddisfi i suoi requisiti.**

I test di rilascio sono solitamente *black-box* o *test funzionali*.

## Test black-box

Un esempio di test **black-box** è testare se la macchina funziona oppure no senza vedere cosa c'è dentro la macchina: se funziona, allora il test è superato.

![[blackBox.png]]

## Linee guida per i test

Le linee guida per i test sono suggerimenti per il team di test per aiutarli a scegliere i test che riveleranno difetti nel sistema. Alcuni esempi di linee guida:

- Scegliere input che costringano il sistema a generare tutti i messaggi di errore;
- Progettare gli input che causano l'overflow dei buffer;
- Ripetere più volte lo stesso input o la stessa serie di input;
- Forza la generazione di output non validi;
- Forza i risultati del calcolo a essere troppo grandi o troppo piccoli.

## [[Casi d'uso]]

**I [[Casi d'uso|casi d'uso]] possono essere una base per derivare i test per un sistema:** aiutano a identificare le operazioni da testare e aiutano a progettare i casi di test richiesti.

Da un diagramma di sequenza associato si possono identificare gli ingressi e le uscite da creare per le prove.

## Test delle prestazioni

Parte del test di rilascio può comportare il test delle proprietà emergenti di un sistema, come le prestazioni e l'affidabilità.

**I test delle prestazioni di solito comportano la pianificazione di una serie di test in cui il carico aumenta costantemente fino a quando le prestazioni del sistema diventano inaccettabili.**

## Test dello stress

Esercita il sistema oltre il suo carico massimo di progetto: stressare il sistema spesso fa emergere dei difetti.

I sistemi non dovrebbero fallire in modo catastrofico e lo stress test verifica la perdita inaccettabile di servizi o dati.

**Lo stress test è particolarmente rilevante per i sistemi distribuiti** che possono presentare un grave degrado quando una rete viene sovraccaricata.

## Test dei componenti

**Il test del componente o dell'unità è il processo di test dei singoli componenti in isolamento.** È un processo di verifica dei difetti.

Le componenti possono essere:

- funzioni o metodi individuali all'interno di un oggetto;
- classi di oggetti con diversi attributi e metodi;
- componenti compositi con interfacce definite utilizzati per accedere alle loro funzionalità.

## Progettazione del banco di prova

Coinvolge la progettazione dei casi di test (input e output) utilizzati per testare il sistema.

L'obiettivo della progettazione dei casi di test è creare una serie di test che siano efficaci nella convalida e nel test dei difetti.

Approcci alla progettazione sono:

- test basati sui requisiti;
- test delle partizioni;
- prove strutturali.

### Test basati sui requisiti

Un principio generale dell'ingegneria dei requisiti è che i requisiti dovrebbero essere testabili.

**Il test basato sui requisiti è una tecnica di test di convalida in cui si considera ogni requisito e si ricava una serie di test per quel requisito.**

### Test delle partizioni

I dati di input e i risultati di output spesso rientrano in classi diverse in cui tutti i membri di una classe sono correlati.

Ognuna di queste classi è una partizione o dominio di equivalenza in cui il programma si comporta in modo equivalente per ogni membro della classe.

I casi di test dovrebbero essere scelti da ciascuna partizione.

## Test del percorso

**L'obiettivo del test del percorso è garantire che l'insieme dei casi di test sia tale che ogni percorso del programma venga eseguito almeno una volta.**

Il punto di partenza per il test del percorso è un grafico di flusso del programma che mostra i nodi che rappresentano le decisioni del programma e gli archi che rappresentano il flusso di controllo.

Le istruzioni con condizioni sono quindi nodi nel grafo di flusso.

## Automazione dei test

**Il test è una fase di processo costosa.** I banchi di prova forniscono una gamma di strumenti per ridurre il tempo richiesto e i costi totali di prova. Sistemi come [[JUnit|Junit]] supportano l'esecuzione automatica dei test.

**La maggior parte degli ambienti di test sono sistemi aperti perché le esigenze di test sono specifiche dell'organizzazione.**

A volte sono difficili da integrare con workbench di progettazione e analisi chiusi.