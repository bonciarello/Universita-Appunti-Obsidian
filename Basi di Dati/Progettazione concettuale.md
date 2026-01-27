## Modello concettuale

Offre una serie di costrutti per descrivere la realtà di interesse in modo facile da comprendere (rappresentazione grafica: schema E-R) e indipendente dal DBMS.

Il modello concettuale si basa su un livello di **astrazione** molto elevato.
L'astrazione è un procedimento mentale che si adotta quando si concentra l'attenzione su alcune caratteristiche di un insieme di entità, trascurando le altre giudicate non rilevanti.
I quattro tipi di astrazione sono:

- astrazione di classificazione;
- astrazione di aggregazione;
- astrazione di generalizzazione;
- astrazione di associazione.

### Astrazione di classificazione

L’**astrazione di classificazione** consente la definizione di una classe a partire da un insieme di oggetti aventi proprietà comuni.

Facciamo un esempio:

![[astrazioneClassificazione.PNG]]

In questo esempio, F500 è un'istanza della classe *Automobile* e si ha una relazione di *instance\_of*.

### Astrazione di aggregazione

L’**astrazione di aggregazione** consente la definizione di una classe a partire da un insieme di classi che costituiscono le sue componenti.

Facciamo un esempio:

![[astrazioneAggregazione.PNG]]

In questo esempio, il telaio è una parte dell'automobile e si ha una relazione di *part\_of*.

### Astrazione di generalizzazione

L’**astrazione di generalizzazione** consente la definizione di una classe (superclasse) a partire da un insieme di classi aventi proprietà comuni (sottoclassi).

Facciamo un esempio:

![[astrazioneGeneralizzazione.PNG]]

In questo esempio, si ha una relazione di *subset\_of* o *is\_a*.
Si nota che:

- le istanze di Automobile sono un sottoinsieme delle istanze di *Veicolo*;
- equivalentemente, ogni automobile è un veicolo;
- le sottoclassi possono possedere caratteristiche proprie.

### Astrazione di associazione

L’astrazione di associazione consente la definizione di un collegamento fra due o più classi.

![[astrazioneAssociazione.PNG]]

Le associazioni possono avere proprietà. Per esempio, data (in cui si è presa la residenza) può essere una proprietà dell'associazione *Risiede*.

## Modello ER

Uno dei modelli più utilizzati nell'ambito della progettazione concettuale.
La rappresentazione grafica può essere fatta tramite diagramma ER.
I concetti fondamentali sono:

- entità (istanze di entità);
- associazioni (istanze di associazione);
- attributi

### Entità

L'entità è un insieme di oggetti della realtà che possiedono caratteristiche comuni (es. persone, automobili, ...).
Le istanze di entità sono oggetti appartenenti a una certa entità (es. io, la mia auto, ...).
Graficamente:

![[entita.PNG]]

### Associazione

Le associazioni rappresentano un legame logico tra entità.
Le istanze di associazione sono combinazioni delle istanze delle entità che prendono parte ad una associazione.
Graficamente:

![[astrazioneAssociazione.PNG]]

In questo esempio, *p* istanza di *Persona*, *c* istanza di *Città* e *(p, c)* istanza di *Risiede*.

Il grado è il numero di classi che partecipano ad un'associazione.

L'associazione unaria ha grado 1.

![[grado1.PNG]]

L'associazione binaria ha grado 2.

![[grado2.PNG]]

Il ruolo è una funzione che un'istanza di entità esercita nell'ambito di un'associazione.

![[ruolo.PNG]]

Nel caso di associazione unaria, il ruolo è obbligatorio.

### Attributi

L'attributo è una proprietà elementare possedute da un'entità o da un'associazione.
Graficamente:

![[attributi.PNG]]

In questo esempio, *nome*, *cognome*, *cod\_fiscale* sono attributi di Persona.

Il dominio di un attributo è un insieme dei valori legali per l'attributo.
I domini possibili sono:

- interi, reali, booleani, caratteri;
- intervalli di interi e di caratteri;
- stringhe di caratteri;
- domini definiti dall'utente.

La notazione si può descrivere:

- *vi, vj* intervallo compreso fra *vi* e *vj*;
- *(vi, ..., vj)* insieme di valori possibili *vi, ..., vj*.

### Attributi sulle associazioni

Anche le associazioni possono avere attributi. Per esempio:

![[attrAssociazioni.PNG]]

In questo esempio, *data* e *voto* non sono proprietà né di uno *Studente* né di un *Corso*, ma del l*egame Studente-Corso* che si crea in occasione di un *Esame*.

Gli attributi possono essere visti come funzioni che associano un valore ad un'istanza di entità o associazione.

### Attributi compositi

Un attributo è composito quando possiede dei sotto-attributi.
Per esempio, data\_nascita con sotto-attributi giorno, mese, anno.

\includegraphics{img/sezione10/attributiCompositi.png}

I domini si possono distinguere in:

- *semplici:* domini degli attributi non compositi;
- *compositi:* domini degli attributi compositi ovvero prodotto cartesiano degli insiemi di valori associati ai domini componenti;

## Vincoli di integrità

Sono di due tipi:

- *impliciti:* automaticamente verificati dal sistema. Ogni occorrenza di una base di dati relativa ad uno schema ER li deve verificare;
    - vincoli di cardinalità (per associazioni e attributi);
    - vincoli di identificazione.
Anche i domini degli attributi sono dei vincoli di integrità.

### Vincoli di integrità impliciti

Ogni istanza di associazione deve riferirsi ad istanze di entità presenti nell'occorrenza della base di dati.

Istanze diverse della stessa associazione devono riferirsi a differenti combinazioni di istanze delle entità partecipanti all'associazione.

### Vincoli di integrità espliciti (associazioni)

È il numero minimo e massimo di istanze dell'associazione a cui un'istanza dell'entità può partecipare. I valori più comuni sono:

- *cardinalità minima (c\_min):* 0, 1
- *cardinalità massima (c\_max):* n, ovvero qualunque intero $>$ 1
Data una classe C e un'associazione A:

- **c\_min = 0** =$>$ esistono istanze di C che non partecipano ad alcuna istanza di A;
- **c\_min = 1** =$>$ ogni istanza di C partecipa almeno ad una istanza di A.
Data un'entità E e un'associazione A:

- **c\_max = 1** =$>$ ogni istanza di E può partecipare al più ad una istanza di A;
- **c\_max = n** =$>$ non esiste limite al numero massimo di istanze di A a cui ogni istanza di E può partecipare;
- **c\_max = c\_min = 1** =$>$ ogni istanza di E partecipa ad una ed una sola istanza di A;
- **c\_min = 0, c\_max = n** =$>$ ogni istanza di E può partecipare ad un numero qualsiasi di istanze di A.
Facciamo un esempio:

![[esempioVincoli.PNG]]

- *c\_min* di Automobile rispetto a Proprietario è 0: esistono automobili non possedute da alcuna persona;
- *c\_min* di Persona rispetto a Proprietario è 0: esistono persone che non posseggono alcuna automobile;
- *c\_max* di Persona rispetto a Proprietario è n: ogni persona può essere proprietaria di un numero arbitrario di automobili;
- *c\_max* di Automobile rispetto a Proprietario è 1: ogni automobile può avere al più un proprietario.
Data un’associazione A tra due entità E1 ed E2:

- A è un’associazione **uno a uno** se *c\_max* di E1 ed E2 rispetto ad A è 1;
- A è un’associazione **uno a molti** se *c\_max* di E1 rispetto ad A è n e *c\_max* di E2 rispetto ad A è 1;
- A è un’associazione **molti a uno** se *c\_max* di E1 rispetto ad A è 1 e *c\_max* di E2 rispetto ad A è n;
- A è un’associazione **molti a molti** se *c\_max* di E1 ed E2 rispetto ad A è n.
Inoltre, un'associazione è detta opzionale se *c\_min* è 0, è detta obbligatoria se *c\_min* è 1.

### Vincoli di integrità espliciti (attributi)

È il numero minimo e massimo di valori dell'attributo che possono essere associati ad un'istanza della corrispondente associazione od entità.
Nei diagrammi si può indicare la coppia *(c\_min, c\_max)* sulla linea che congiunge l'attributo all'associazione/entità.
Se non si indica niente il valore di default è *(1,1)*.

Si parla di attributi:

- **opzionali:** se la cardinalità minima è 0 (es. *cognome\_da\_nubile*);
- **monovalore:** se la cardinalità massima è 1 (es. *cod\_fiscale*);
- **multivalore:** se la cardinalità massima è n (es. *telefono*).
Un esempio di diagramma con vincoli di cardinalità sarebbe:

![[esempioAttrOMM.png]]

## Vincoli espliciti di identificazione

Gli **identificatori** (o chiavi) sono un insieme di attributi che identificano univocamente le istanze dell'entità e devono essere minimali cioè qualsiasi sottoinsieme proprio non è un identificatore.

Si noti che gli identificatori hanno senso solo per le entità e non per le associazioni.
Nell'insieme di istanze di un'associazione si hanno tutte tuple distinte quindi non c'è bisogno di identificatori.

A volte non è possibile identificare un'istanza di entità solo sulla base dei suoi attributi, cioè due istanze diverse possono coincidere su tutti gli attributi.
Si utilizza allora il fatto che tale istanza partecipi ad una particolare istanza di associazione con una data istanza di un'altra entità: l'entità viene detta **debole**.

Abbiamo vari tipi di identificatori:

- **interni:** uno o più attributi dell'entità;
- **esterni:** uno o più associazioni collegate all'entità a cui si riferiscono (identificazione esterna da tale entità attraverso tale associazione);
- **misti:** attributi o associazioni;
- **semplici:** un elemento;
- **compositi:** più di un elemento.

## Gerarchie di classe

Una entità *E* è una generalizzazione delle entità *E1, ..., En* se ogni istanza delle entità *E1, ..., En* è anche un'istanza di *E*. *E* è l'entità padre mentre *E1, ..., En* sono entità figlie. Vediamo un esempio:

![[gerarchie.png]]

Ogni generalizzazione può essere:

- **totale:** ogni istanza di *E* è istanza di almeno un'entità *Ei* (es. Persona - Uomo, Donna);
- **parziale:** esiste almeno un'istanza di *E* che non è istanza di alcuna entità *Ei* (es. Persona - Studente, Impiegato);

Ogni generalizzazione può essere inoltre:

- **esclusiva:** ogni istanza di *E* è istanza di al più un'entità *Ei* (es. Persona - Uomo, Donna);
- **sovrapposta:** esiste almeno un'istanza di *E* che è istanza di più di un'entità *Ei* (es. Persona - Studente, Impiegato);
Tali caratteristiche possono essere indicate come vincoli espliciti della gerarchia di generalizzazione.

## Rappresentazione grafica componenti modello ER

![[componenti.png]]