---
aliases: [Ricerca uniforme binaria, RUB]
tags: [algoritmi-e-strutture-dati]
---
## Differenze tra gli algoritmi

La *ricerca lineare* ha complessità:

> *O(N)*

La *ricerca binaria* ha complessità:

> log₂ N

Alla ricerca binaria deve essere applicato l'ordinamento del vettore: ***quindi se non è ordinato allora l'ordine insieme alla ricerca binaria è conveniente?***

Non è conveniente perché l'ordinamento costa sicuramente di più che una ricerca binaria ma ci sono alcuni casi in cui sarebbe sbagliato e cioè quando ci sono numerose operazioni di ricerca.

## Strutture dedicate per la ricerca di un elemento

Per la realizzazione di strutture dati efficienti, che ci permettono quindi di minimizzare il tempo di accesso, si possono adottare diverse strategie.
Ciononostante lo scopo rimane sempre lo stesso e cioè di individuare una funzione *f(x)* che ci permette di ricercare in tempi quasi immediati (*O(1)*) un dato elemento in una qualche struttura dati.

## Strategie adottate a lezione

- ***Ipotizzando*** di conoscere il range dei dati [min, max] possiamo creare una struttura (es. `Vector<bool>`) e sfruttare il valore del dato come indice di ricerca (*f(x)=x*). Questo tipi di approccio però introduce notevoli inefficiente a livello di memoria. Infatti dovremmo conoscere preventivamente il range di rappresentazione e, molto dello spazio occupato, alla fine non sarà completamente sfruttato.
- **FUNZIONE HASH:** è una funzione di accesso non biunivoca che, sfruttando opportunamente la struttura delle chiavi, distribuisce gli indirizzi calcolati nel modo più uniforme possibile su tutto lo spazio degli indirizzi in modo da ridurre il numero di collisioni.

    Esistono due tipi di funzioni Hash:
    - *Hash perfette*, cioè prive di collisioni (es. sistema posizionale => numeri molto grandi)
    - *Hash non perfette*, cioè esistono collisioni

    **Una collisione non è altro che il fenomeno in cui oggetti, con chiavi diverse, hanno stessa posizione nella tabella.**

## Gestione delle collisioni

Questo fenomeno può essere risolto andando a creare, in corrispondenza della collisione, una lista di oggetti.
Si viene ad introdurre, quindi, una ulteriore fase di ricerca.
Per ragioni computazionali, spiegate anche tramite numerosi test, il tipo di ricerca da dover adottare è quella di tipo sequenziale.
Infatti, si va a calcolare la COMPLESSITÀ MEDIA tenendo conto della lunghezza media della lista di collisione quindi *circa O(C)* dove *C* è la lunghezza.

Si va quindi a calcolare il fattore di carico della tabella, cioè il rapporto tra:

> Fc = n / nCelle

dove *n* è il numero di dati da memorizzare. Se questo fattore è *circa 0,9*, allora il tempo medio di accesso e di ricerca è pari a *1,45* (OTTIMO, *circa O(1)*) e ci indica che il ridimensionamento della nostra tabella è ottimo.

## Funzioni hash buone e complessità

Alcuni classici esempi di funzioni hash sono i seguenti:

- **metodo del quadrato centrale:** viene determinato k² e vengono estratti m bit al centro del risultato, dove m = ⌊log p⌋; il numero risultante è ovviamente inferiore a *p* e viene utilizzato per indirizzare la tabella — per evitare di avere indirizzi non utilizzati conviene dimensionare *p* uguale esattamente a 2^m

- **metodo dell'avvolgimento:** la rappresentazione binaria *k* viene spezzata in segmenti di *m* bit (aggiungendo eventuali 0 all'ultimo segmento nel caso esso abbia meno di *m* bit), vengono sommati i vari segmenti e vengono prelevati gli *m* bit meno significativi della somma — *m* è definito come nel metodo del quadrato centrale

- **metodo del modulo:** come indirizzo viene utilizzato il resto della divisione di *k* per *p*; l'indirizzo assume valori tra *0* e *p-1* — si noti che in questo caso è opportuno che *p* sia un numero dispari al fine di ridurre la probabilità di collisioni; ancora meglio se *p* è pari a un numero primo

Si basano sulla rappresentazione binaria della chiave. Queste funzioni devono avere complessità *O(1)* ed indipendenti dall'input. Una volta applicata si devono organizzare i dati in tabelle hash e quindi riorganizzare i dati (*O(n)*).

### Modellazione

È utile usare una classe *template* affinché si possa andare a preservare l'integrità dei nostri dati.
Quest'ultima deve prevedere metodi quali:

- *insert(elem e, chiave k)*
- *delete(chiave k)*
- *search(chiave k)* e restituisce elem

Se vogliamo organizzare una lista già esistente (non ordinata) dobbiamo specificare quale attributo vogliamo usare come chiave e per ognuna di esse verrà creata una tabella hash.

### Classi C++

```cpp
map<chiave, valore> // ordinati per chiave e non per hash
set // la chiave è il valore
multimap // piu' chiavi uguali
unordered_map
unordered_set
```
