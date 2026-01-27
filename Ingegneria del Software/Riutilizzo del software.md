I sistemi sono spesso progettati componendo componenti esistenti che sono stati utilizzati in altri sistemi.

Il riutilizzo sistematico del software può ottenere un software migliore, più rapidamente e a costi inferiori.

Il riuso si può applicare ad ogni livello del software:

- **Riutilizzo del sistema applicativo:** l'intero sistema applicativo può essere riutilizzato incorporandolo senza modifiche in altri sistemi (riutilizzo COTS) o sviluppando famiglie applicative;
- **Riutilizzo dei componenti:** i componenti di un'applicazione dai sottosistemi ai singoli oggetti possono essere riutilizzati;
- **Riutilizzo di oggetti e funzioni:** i componenti software che implementano un singolo oggetto o funzione ben definiti possono essere riutilizzati.

Ci sono modi diversi per applicare il riuso, di fatto lo sviluppo del software è basato sul riuso.

Il riutilizzo è possibile a vari livelli, da semplici funzioni a sistemi applicativi completi.

## Approcci al riuso

![[riusoSoftwareSchema.png]]

- **Modelli di progettazione:** le astrazioni generiche che si verificano tra le applicazioni sono rappresentate come modelli di progettazione che mostrano oggetti e interazioni astratti e concreti;
- **Sviluppo basato su componenti:** i sistemi vengono sviluppati integrando componenti (raccolte di oggetti) conformi agli standard dei modelli dei componenti;
- **Framework applicativi:** raccolte di classi astratte e concrete che possono essere adattate ed estese per creare sistemi applicativi;
- **Wrapping del sistema legacy:** sistemi legacy che possono essere "avvolti" definendo un insieme di interfacce e fornendo accesso a questi sistemi legacy attraverso queste interfacce;
- **Sistemi orientati ai servizi:** i sistemi sono sviluppati collegando servizi condivisi che possono essere forniti esternamente;
- **Linee di prodotti applicativi:** un tipo di applicazione è generalizzato attorno a un'architettura comune in modo che possa essere adattato in modi diversi per i diversi clienti;
- **Integrazione COTS:** i sistemi vengono sviluppati integrando sistemi applicativi esistenti;
- **Applicazioni verticali configurabili:** un sistema generico è progettato in modo da poter essere configurato in base alle esigenze dei clienti del sistema specifico;
- **Librerie di programmi:** sono disponibili per il riutilizzo librerie di classi e funzioni che implementano le astrazioni di uso comune;
- **Generatori di programmi:** un sistema generatore incorpora la conoscenza di un particolare tipo di applicazione e può generare sistemi o frammenti di sistema in quel dominio;
- **Sviluppo software orientato all'aspetto:** i componenti condivisi vengono intrecciati in un'applicazione in punti diversi quando il programma viene compilato.

## Design pattern

**Un modello di progettazione è un modo per riutilizzare la conoscenza astratta di un problema e della sua soluzione.**
Un modello è una descrizione del problema e l'essenza della sua soluzione.
Dovrebbe essere sufficientemente astratto da essere riutilizzato in contesti diversi.
I modelli si basano spesso su caratteristiche dell'oggetto come l'ereditarietà e il polimorfismo.

## Riutilizzo dei prodotti COTS

Detto anche COTS - Commercial Off-The-Shelf systems, **i sistemi COTS sono generalmente sistemi applicativi completi che offrono un'API (Application Programming Interface).**
La realizzazione di grandi sistemi integrando i sistemi COTS è ora una strategia di sviluppo praticabile per alcuni tipi di sistemi come i sistemi di e-commerce.
Il **vantaggio principale** è lo sviluppo più rapido delle applicazioni e, di solito, costi di sviluppo inferiori.

## Problemi di integrazione del sistema COTS

- **Mancanza di controllo su funzionalità e prestazioni:** i sistemi COTS possono essere meno efficaci di quanto sembri;
- **Problemi con l'interoperabilità del sistema COTS:** diversi sistemi COTS possono fare ipotesi diverse, il che significa che l'integrazione è difficile;
- **Nessun controllo sull'evoluzione del sistema:** i fornitori COTS, non gli utenti del sistema, controllano l'evoluzione;
- **Supporto dai fornitori COTS:** i fornitori COTS potrebbero non offrire supporto per tutta la durata del prodotto.

Molto spesso un sistema già pronto non è sempre una buona cosa, infatti può prendere strade differenti oppure non essere più aggiornato per perdita di interesse.

## Linee di prodotti software

Le linee di prodotti software o le famiglie di applicazioni sono applicazioni con funzionalità generiche: possono essere adattate e configurate.

L'adattamento può comportare: configurazione dei componenti e del sistema, aggiunta di nuovi componenti al sistema, selezione da una libreria di componenti esistenti e modifica dei componenti per soddisfare nuovi requisiti.

## Sistemi ERP

**Un sistema ERP (Enterprise Resource Planning) è un sistema generico che supporta processi aziendali comuni come ordini e fatturazione, produzione, ecc.**

Sono molto utilizzati nelle grandi aziende: rappresentano probabilmente la forma più comune di riutilizzo del software.

Il nucleo generico viene adattato includendo moduli e incorporando la conoscenza dei processi e delle regole aziendali.