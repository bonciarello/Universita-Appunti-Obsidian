Se io non so quante risorse devo impiegare, non so che cifra devo chiedere al cliente. Il software è intangibile quindi non si sa quanto lavoro abbiamo realizzato. Non è possibile fare una stima precisa dei costi di produzione. Allora dobbiamo porci delle domande:

- quanto impegno è richiesto per completare un'attività?
- quanto tempo di calendario è necessario per completare un'attività?
- qual è il costo totale di un'attività?

Cosa compone la stima dei costi del software?

- **costi hardware e software;**
- **costi di viaggio e formazione;**
- **costi di lavoro (il fattore dominante nella maggior parte dei progetti):** gli stipendi degli ingegneri coinvolti nel progetto, Costi sociali e assicurativi;
- **costi dello sforzo devono tenere conto delle spese generali:** costi di costruzione, riscaldamento, illuminazione, costi di rete e comunicazioni, costi delle strutture condivise (es. biblioteca, ristorante del personale, ecc.).

## Costi e prezzi

Vengono fatte delle stime per scoprire il costo, per lo sviluppatore, di produrre un sistema software.

Non esiste una relazione semplice tra il costo di sviluppo e il prezzo addebitato al cliente.

Considerazioni organizzative, economiche, politiche e commerciali più ampie influiscono sul prezzo praticato.

Ammesso che noi siamo in grado di stabilire il costo del nostro software, questo costo poi viene utilizzato per determinare il prezzo del software.

## Fattori di prezzo del software

I fattori per determinare i costi sono:

- **Opportunità di mercato:** un'organizzazione di sviluppo può proporre un prezzo basso perché desidera entrare in un nuovo segmento del mercato del software. Accettare un basso profitto su un progetto può dare l'opportunità di un profitto maggiore in seguito. L'esperienza acquisita può consentire lo sviluppo di nuovi prodotti;
- **Incertezza della stima dei costi:** se un'organizzazione non è sicura della sua stima dei costi, può aumentare il suo prezzo di qualche contingenza oltre al suo normale profitto;
- **Termini contrattuali:** un cliente può essere disposto a consentire allo sviluppatore di mantenere la proprietà del codice sorgente e riutilizzarlo in altri progetti. Il prezzo addebitato può quindi essere inferiore rispetto a se il codice sorgente del software viene consegnato al cliente;
- **Volatilità dei requisiti:** se è probabile che i requisiti cambino, un'organizzazione può abbassare il prezzo per aggiudicarsi un contratto. Dopo l'aggiudicazione del contratto, possono essere addebitati prezzi elevati per le modifiche ai requisiti;
- **Salute finanziaria:** gli sviluppatori in difficoltà finanziarie possono abbassare il prezzo per ottenere un contratto. È meglio realizzare un profitto o un pareggio inferiore al normale piuttosto che chiudere l'attività.

## Produttività del software

**Una misura della velocità con cui i singoli ingegneri coinvolti nello sviluppo del software producono software e documentazione associata.**

Non orientato alla qualità, sebbene l'assicurazione della qualità sia un fattore nella valutazione della produttività.

In sostanza, vogliamo misurare le funzionalità utili prodotte per unità di tempo.

## Misure di produttività

Per misurare la produttività del software ci sono due fattori:

- **Misure relative alle dimensioni** basate su alcuni output del processo software. Potrebbero essere righe del codice sorgente fornito, istruzioni del codice oggetto, ecc.;
- **Misure relative alle funzioni** basate su una stima della funzionalità del software fornito. I punti funzione sono i più noti di questo tipo di misura.

## Righe di codice (LOC)

La misura è stata proposta per la prima volta quando i programmi sono stati digitati su schede con una riga per scheda. Come corrisponde a istruzioni come in Java che possono estendersi su più righe o dove possono esserci più istruzioni su una riga.

Quali programmi dovrebbero essere contati come parte del sistema?

Questo modello presuppone che vi sia una relazione lineare tra le dimensioni del sistema e il volume della documentazione.

Contare le righe di codice è una misura delicata perchè dipendente dalla tecnologia. Quindi si fa una media delle righe di codice tra un programmatore e l'altro.

## Confronti di produttività

Più basso è il livello della lingua, più produttivo è il programmatore: la stessa funzionalità richiede più codice da implementare in un linguaggio di livello inferiore rispetto a un linguaggio di alto livello.

Più il programmatore è prolisso, maggiore è la produttività: le misure di produttività basate su righe di codice suggeriscono che i programmatori che scrivono codice prolisso sono più produttivi dei programmatori che scrivono codice compatto.

## Punti funzione

Per attaccare una misura, un'altra tecnica per stimare il costo sono i punti funzione.

Basato su una combinazione di caratteristiche del programma

- ingressi e uscite esterni;
- interazioni dell'utente;
- interfacce esterne;
- file utilizzati dal sistema.

A ciascuno di questi è associato un peso e il conteggio dei punti funzione viene calcolato moltiplicando ciascun conteggio grezzo per il peso e sommando tutti i valori.

> *UFC = (number of elements of given type) * (weight)*

Il conteggio dei punti funzione è modificato dalla complessità del progetto.

## Object points

**Ci sono anche altri modi per fare la stima dei costi cioè gli object point. Sono più facili da usare, sono più naturali e meno astratti. È un oggetto da realizzare (es. 200 pagine web, quindi 200 object point).**

Gli object points sono un'alternativa (denominati anche punti applicazione), NON sono gli stessi delle classi oggetto.

Il numero di object points in un programma è una stima ponderata di:

- il numero di schermate separate visualizzate;
- il numero di report prodotti dal sistema;
- il numero di moduli di programma che devono essere sviluppati per integrare il codice del database.

Gli object points sono più facili da stimare da una specifica rispetto ai punti funzione: riguardano semplicemente schermate, report e moduli del linguaggio di programmazione.

Possono quindi essere stimati in una fase abbastanza precoce del processo di sviluppo.

In questa fase è molto difficile stimare il numero di righe di codice in un sistema.

## Fattori che influenzano la produttività

La produttività viene condizionata da altri fattori come per esempio:

- **Esperienza nel dominio dell'applicazione:** la conoscenza del dominio dell'applicazione è essenziale per uno sviluppo software efficace. È probabile che gli ingegneri che già comprendono un dominio siano i più produttivi;
- **Qualità del processo:** il processo di sviluppo utilizzato può avere un effetto significativo sulla produttività;
- **Dimensioni del progetto:** più grande è un progetto, maggiore è il tempo necessario per le comunicazioni del team. Meno tempo è disponibile per lo sviluppo, quindi la produttività individuale è ridotta;
- **Supporto tecnologico:** una buona tecnologia di supporto come gli strumenti CASE, i sistemi di gestione della configurazione, ecc. può migliorare la produttività;
- **Ambiente di lavoro:** un ambiente di lavoro tranquillo con aree di lavoro private contribuisce a migliorare la produttività.

## Qualità e produttività

Tutte le metriche basate su volume/unità di tempo sono errate perché non tengono conto della qualità.

La produttività può generalmente essere aumentata a scapito della qualità.

Non è chiaro come siano correlate le metriche di produttività/qualità.

Se i requisiti cambiano costantemente, un approccio basato sul conteggio delle righe di codice non è significativo poiché il programma stesso non è statico.

## Tecniche di stima

Per la stima del costo, ci sono diversi modi:

- **Modellazione algoritmica dei costi:** viene utilizzato un modello basato su informazioni sui costi storici che mette in relazione alcune metriche del software (di solito la sua dimensione) al costo del progetto. Viene effettuata una stima di tale metrica e il modello prevede lo sforzo richiesto;
- **Giudizio di esperti:** vengono consultati diversi esperti sulle tecniche di sviluppo software proposte e sul dominio applicativo. Ciascuno stima il costo del progetto. Queste stime vengono confrontate e discusse. Il processo di stima scorre fino al raggiungimento di una stima concordata;
- **Stima per analogia:** questa tecnica è applicabile quando sono stati completati altri progetti nello stesso dominio applicativo. Il costo di un nuovo progetto è stimato per analogia con questi progetti completati. Myers (Myers 1989) fornisce una descrizione molto chiara di questo approccio;
- **Legge di Parkinson:** afferma che il lavoro si espande per riempire il tempo a disposizione. Il costo è determinato dalle risorse disponibili piuttosto che da una valutazione obiettiva. Se il software deve essere consegnato in 12 mesi e sono disponibili 5 persone, lo sforzo richiesto è stimato in 60 persone/mese;
- **Pricing to win:** si stima che il costo del software sia quello che il cliente ha a disposizione da spendere per il progetto. Lo sforzo stimato dipende dal budget del cliente e non dalla funzionalità del software.

## Pricing to win

Il progetto costa tutto ciò che il cliente deve spendere per esso.
I vantaggi è che ottieni il contratto. Lo svantaggio è che la probabilità che il cliente ottenga il sistema che desidera è piccola. I costi non riflettono accuratamente il lavoro richiesto.

Questo approccio può sembrare immorale e poco professionale.
Tuttavia, quando mancano informazioni dettagliate, potrebbe essere l'unica strategia appropriata.
Il costo del progetto viene concordato sulla base di una proposta di massima e lo sviluppo è vincolato da tale costo.
È possibile negoziare una specifica dettagliata o utilizzare un approccio evolutivo per lo sviluppo del sistema.

## Stima top-down e bottom-up

Ciascuno di questi approcci può essere utilizzato dall'alto verso il basso o dal basso verso l'alto:

- **Dall'alto verso il basso:** iniziare a livello di sistema e valutare la funzionalità complessiva del sistema e come questa viene fornita attraverso i sottosistemi;
- **Dal basso verso l'alto:** iniziare a livello di componente e stimare lo sforzo richiesto per ogni componente. Aggiungi questi sforzi per raggiungere una stima finale.

## Modellazione algoritmica dei costi

Il costo è stimato come una funzione matematica degli attributi di prodotto, progetto e processo i cui valori sono stimati dai project manager:

> *Effort = A*SizeB*M*

*A* è una costante dipendente dall'organizzazione, *B* riflette lo sforzo sproporzionato per grandi progetti e *M* è un moltiplicatore che riflette gli attributi di prodotto, processo e persone.

L'attributo del prodotto più comunemente utilizzato per la stima dei costi è la dimensione del codice.
La maggior parte dei modelli sono simili ma utilizzano valori diversi per *A*, *B* e *M*.

## Precisione della stima

La dimensione di un sistema software può essere conosciuta con precisione solo quando è terminata.

Diversi fattori influenzano la dimensione finale:

- utilizzo di COTS e componenti;
- linguaggio di programmazione;
- distribuzione del sistema.

Man mano che il processo di sviluppo progredisce, la stima delle dimensioni diventa più accurata.

## COCOMO 81

Se la complessità del progetto è:

- **semplice:** applicazioni ben comprese sviluppate da piccoli team ($PM = 2.4*(KDSI)^{1.05}*M$);
- **moderato:** progetti più complessi in cui i membri del team possono avere un'esperienza limitata dei sistemi correlati ($PM = 3.0*(KDSI)^{1.12}*M$);
- **embedded:** progetti complessi in cui il software è parte di un complesso fortemente accoppiato di hardware, software, normative e procedure operative ($PM = 3.6*(KDSI)^{1.20}*M$).

## Modelli COCOMO 2

COCOMO 2 incorpora una gamma di sottomodelli che producono stime software sempre più dettagliate.

I sottomodelli in COCOMO 2 sono:

- **Modello di composizione dell'applicazione:** utilizzato quando il software è composto da parti esistenti;
- **Modello di progettazione iniziale:** utilizzato quando i requisiti sono disponibili ma la progettazione non è ancora iniziata;
- **Modello di riutilizzo:** utilizzato per calcolare lo sforzo di integrazione di componenti riutilizzabili;
- **Modello post-architettura:** utilizzato una volta che l'architettura del sistema è stata progettata e sono disponibili ulteriori informazioni sul sistema.