Le nuove versioni dei sistemi software vengono create man mano che cambiano:

- per macchine/OS differenti;
- offrendo diverse funzionalità;
- su misura per particolari esigenze dell'utente.

La gestione della configurazione riguarda la gestione dei sistemi software in evoluzione:

- il cambio di sistema è un'attività di squadra;
- CM mira a controllare i costi e gli sforzi necessari per apportare modifiche a un sistema.

Coinvolge lo sviluppo e l'applicazione di procedure e standard per gestire un prodotto software in evoluzione.

Il CM può essere visto come parte di un processo di gestione della qualità più generale.

Quando vengono rilasciati in CM, i sistemi software sono talvolta chiamati linee di base in quanto sono un punto di partenza per ulteriori sviluppi.

## Pianificazione della gestione della configurazione

Potrebbe essere necessario gestire tutti i prodotti del processo software cioè specifiche, disegni, programmi, dati di test e manuali utente.

Migliaia di documenti separati possono essere generati per un sistema software grande e complesso.

## Piano CM

- Definisce i tipi di documenti da gestire e uno schema di denominazione dei documenti.
- Definisce chi si assume la responsabilità delle procedure CM e della creazione delle linee di base.
- Definisce i criteri per il controllo delle modifiche e la gestione delle versioni.
- Definisce i record CM che devono essere mantenuti. Descrive gli strumenti che dovrebbero essere utilizzati per assistere il processo di CM ed eventuali limitazioni al loro utilizzo.
- Definisce il processo di utilizzo dello strumento.
- Definisce il database CM utilizzato per registrare le informazioni di configurazione.
- Può includere informazioni come il CM di software esterno, audit di processo, ecc.

## Identificazione dell'elemento di configurazione

I progetti di grandi dimensioni producono in genere migliaia di documenti che devono essere identificati in modo univoco.

Alcuni di questi documenti devono essere conservati per tutta la durata del software.

Lo schema di denominazione dei documenti deve essere definito in modo che i documenti correlati abbiano nomi correlati.

Uno schema gerarchico con nomi a più livelli è probabilmente l'approccio più flessibile.

Utilizzare un database di configurazione collegato a risorse.

## Storia delle derivazioni

**Questo è un record delle modifiche applicate a un documento o un componente di codice.**

Dovrebbe registrare, a grandi linee, la modifica apportata, la motivazione della modifica, chi ha apportato la modifica e quando è stata attuata.

Può essere incluso come commento nel codice. Se viene utilizzato uno stile di prologo standard per la cronologia delle derivazioni, gli strumenti possono elaborarlo automaticamente.

Un esempio di storia delle derivazioni:

```java
// BANKSEC project (IST 6087)
//
// BANKSEC-TOOLS/AUTH/RBAC/USER_ROLE
//
// Object: currentRole
// Author: N. Perwaiz
// Creation date: 10th November 2002
//
// Lancaster University 2002
//
// Modification history
// Version ModifierDate Change Reason
// 1.0 J. Jones 1/12/2002 Add header Submitted to CM
// 1.1 N. Perwaiz 9/4/2003New field Change req. R07/02
```

## Versione, variante e release

Una **versione** è un'istanza di un sistema che è funzionalmente distinta in qualche modo da altre istanze di sistema.

Una **variante** è un'istanza di un sistema funzionalmente identico ma non funzionalmente distinto dalle altre istanze di un sistema.

Una **release** è un'istanza di un sistema che viene distribuita agli utenti esterni al team di sviluppo.

## Identificazione della versione

Le procedure per l'identificazione della versione dovrebbero definire un modo univoco per identificare le versioni dei componenti.

Esistono tre tecniche di base per l'identificazione dei componenti:

- Numerazione versione;
- Identificazione basata sugli attributi;
- Identificazione orientata al cambiamento.

## Gestione dei release

I rilasci devono incorporare le modifiche imposte al sistema da errori rilevati dagli utenti e da modifiche hardware. Devono anche incorporare nuove funzionalità di sistema.

**La pianificazione del rilascio riguarda il momento in cui rilasciare una versione di sistema come release.**

## Release di sistema

Non solo un insieme di programmi eseguibili, può includere anche:

- file di configurazione che definiscono come è configurata la release per una particolare installazione;
- file di dati necessari per il funzionamento del sistema;
- un programma di installazione o uno script di shell per installare il sistema sull'hardware di destinazione;
- documentazione elettronica e cartacea;
- packaging e pubblicità associata.

I sistemi ora vengono normalmente rilasciati su dischi ottici (CD o DVD) o come file di installazione scaricabili dal web.

## Problemi di release

Il cliente potrebbe non volere una nuova versione del sistema: potrebbe essere soddisfatto del sistema attuale poiché la nuova versione potrebbe fornire funzionalità indesiderate.

La gestione delle versioni non deve presumere che tutte le versioni precedenti siano state accettate. Tutti i file necessari per una versione devono essere ricreati quando viene installata una nuova versione.

## Costruzione del sistema

**È il processo di compilazione e collegamento di componenti software in un sistema eseguibile.**

Diversi sistemi sono costituiti da diverse combinazioni di componenti.

Questo processo è ora sempre supportato da strumenti automatizzati guidati da "script di compilazione".

![[costruzioneSistema.png]]

## Strumenti CASE per la gestione della configurazione

I processi CM sono standardizzati e comportano l'applicazione di procedure predefinite.

È necessario gestire grandi quantità di dati. Il supporto dello strumento CASE per CM è quindi essenziale.

Sono disponibili strumenti CASE maturi per supportare la gestione della configurazione che vanno da strumenti stand-alone a workbench CM integrati.
