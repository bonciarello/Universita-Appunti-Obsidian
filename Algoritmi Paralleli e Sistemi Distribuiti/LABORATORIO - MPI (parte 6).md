## Raggruppamento dei dati per la comunicazione

Meno messaggi inviamo, meglio è! Vedremo tre metodi per raggruppare i dati in singoli messaggi:
- Parametri *count*;
- Tipi di dati derivati;
- *MPI_Pack*/*MPI_Unpack*.

Ricordiamo che i tipi di dati primitivi sono contigui (es. array in C): i metodi sopra elencati consentono di "costruire" messaggi con dati contigui o di costruire nuovi tipi in modo efficiente e facile da dati non contigui.

## Trasmissione dati

Il **problema** è che è possibile utilizzare meccanismi di comunicazione MPI di base per inviare o ricevere una sequenza di elementi dello stesso tipo e contigui in memoria. Solitamente è opportuno inviare dati di diverso tipo, non omogenei e anche non contigui. *Come possiamo specificare l'invio di dati non contigui dello stesso tipo? Oppure dati contigui di diverso tipo? Oppure dati non contigui di tipo misto?*

Le possibili soluzioni sono:
- utilizzo di più chiamate alle routine MPI per inviare e ricevere ogni elemento di dati;
- utilizzo di *MPI_PACKED* e *MPI_UNPACKED*;
- utilizzo di *MPI_BYTE*;

Queste soluzioni sono però molto lente, poco pratiche e utilizzano molta memoria! Il modo ottimale per evitare tutti i problemi di cui sopra è per mezzo di **tipi di dati derivati**.

![[derivatedDatatypes.png]]

## Tipi di dati derivati

I tipi di dati derivati sono nuovi tipi di dati a partire dai tipi MPI predefiniti fondamentali o da altri tipi derivati. Per definire un nuovo tipo di dati, è necessario:
- una sequenza di tipi di dati fondamentali;
- una sequenza di numeri interi (byte) per gli spostamenti.

Un modo semplice per immaginarlo è rappresentato da una sequenza di coppie: **tipo di dati fondamentale - spostamento**. In MPI, questo è chiamato *typemap*.

![[tipiDatiDerivati.png]]

## Salvataggio di un tipo di dati

Prima che un handle del tipo di dati venga utilizzato nella comunicazione con passaggio di messaggi, è necessario eseguirne il commit con *MPI_TYPE_COMMIT*. Questo deve essere fatto solo una volta.

```c
int MPI_Type_commit(MPI_Datatype *datatype);
```

## Esempio impacchettamento dati

![[esempioImpacchettamentoDati.png]]

Se dobbiamo inviare un unico messaggio alle tre variabili, sono richieste le seguenti informazioni:

- Ci sono tre elementi da trasmettere;
-
    - Il primo elemento è un *float*;
    - Il secondo elemento è un *float*;
    - Il terzo elemento è un *int*;
-
    - Il primo elemento ha indirizzo *&a*;
    - Il secondo elemento ha indirizzo *&b*;
    - Il terzo elemento ha indirizzo *&n*;

## Giocare con il parametro count

C garantisce che gli elementi dell'array siano contigui in memoria (per riga). Ad esempio possiamo sfruttare questa caratteristica per inviare "la seconda metà di un array di 100 elementi dal processo 0 al processo 1.

![[giocareParametroCount.png]]

## Dati non contigui (tipi di dati derivati da MPI)

Il metodo sopra descritto è valido solo per dati contigui (es. vettori in C). Se dichiariamo tre variabili (es. *float a, b, int n*), sappiamo che il compilatore non le mette in memoria contigua - sicuro al 99,99%. Potrei costruire una struttura (tramite typedef struct in C), ma MPI accetta solo tipi MPI (primitivi o derivati)!

**Primo metodo:** usa *MPI_Type_struct*. Rappresenta il costruttore di tipi di dati più generale in MPI, ma necessita di una descrizione completa di ogni elemento del tipo di dati (cioè tipo, spostamenti, indirizzi di ogni elemento, ecc.).

![[datiNonContigui.png]]

## Costruttori di tipi di dati

Quando ti occupi principalmente di vettori e array, il *MPI_Type_struct* è troppo "pesante" per specificare il nuovo tipo di dati. Puoi utilizzare altri tre costruttori:

- *MPI_Type_contiguous* (usato per costruire dataype derivato da elementi adiacenti di un array);
- *MPI_Type_vector* (usato per costruire dataype derivato da elementi equidistanti in un array);
- *MPI_Type_indexed* (usato per costruire dataype derivato da elementi arbitrari di un array)

## Dati contigui e MPI_Type_contigous

Il tipo di dati derivato più semplice. È costituito da una serie di elementi contigui dello stesso tipo di dati.

```c
int MPI_Type_contiguous8int count, MPI_Datatype oldtype, MPI_Datattype *newtype)
```

![[datiContigui.PNG]]

Creazione di un tipo di dati che rappresenta una riga di un array e distribuzione delle righe ai processi.

![[codiceDatiContigui.PNG]]

## Tipi di dati vettoriali

![[tipiDatiVettoriali1.PNG]]

```c
int MPI_Type_vector(int count, int blocklength, int stride, mPI_Datatype oldtype, MPI_Datatype *newtype)
```

![[tipiDatiVettoriali2.PNG]]

Creazione di un tipo di dati che rappresenta la colonna di un array e invio ai processi.

![[tipiDatiVettoriali3.PNG]]

## MPI_Type_indexed

![[indexed1.PNG]]

![[indexed2.PNG]]

## Dettagli sui tipi di dati

Sebbene si tratti di un tipo reale, per dichiarare una variabile del nuovo tipo di dati è necessario utilizzare una dichiarazione a *MPI_Datatype* e NON al nuovo tipo.

> **ESEMPIO:** inviare al processo 1 una variabile di tipo *new_type*:
>
```c
...
MPI_Type_commit(&new_type);
MPI_Datatype a; // or pointer if
		         // vector/array!
...
MPI_Send(&a, 1, new_type, 1, tag, MPI_COMM_WORLD);
...
```

## Pack/UnPack

*MPI_Pack* e *MPI_Unpack* consentono di "comprimere" e "dividere" esplicitamente i dati da posizioni di memoria contigue/non contigue. Non consente di definire un nuovo tipo di dati. Usato come "ultima risorsa" (overhead più elevato per il raggruppamento, sebbene facile da usare). Evita che MPI utilizzi il buffering.

![[packUnpack.png]]
