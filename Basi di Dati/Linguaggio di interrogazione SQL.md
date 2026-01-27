**SQL (Structured Query Language)** è un linguaggio standardizzato per database basati sul modello relazionale (RDBMS), progettato per le seguenti operazioni:

- creare e modificare schemi di database *(DDL = Data Definition Language)*;
- inserire, modificare e gestire dati memorizzati *(DML = Data Manipulation Language)*;
- interrogare i dati memorizzati *(DQL = Data Query Language)*;
- creare e gestire strumenti di controllo e accesso ai dati *(DCL = Data Control Language)*.
A dispetto del nome, non si tratta perciò di un semplice linguaggio di interrogazione: alcuni suoi sottoinsiemi, infatti, permettono di creare, gestire e amministrare database.

## Costrutto di base

```sql
SELECT A1, ..., An
FROM R1, ..., Rk
WHERE condizione
```
Equivale a dire:

$
\pi_{A1, ..., An}(\sigma_{condizione}(R1 \bowtie ... \bowtie Rk))
$
Attraverso questo costrutto è possibile rappresentare le query algebriche (positive) basate su proiezione, selezione, join (prodotto cartesiano).

Se due attributi presenti nelle relazioni *R1, ..., Rk* hanno lo stesso nome, essi vengono considerati diversi in virtù del fatto di appartenere a relazioni diverse. Per cui l’espressione $ R1 \bowtie ... \bowtie Rk $ equivale al prodotto cartesiano tra le relazioni che appaiono nella clausola FROM.

Ciò implica che, per effettuare un join, è necessario esplicitare la condizione di join nella clausola WHERE.

**NOTA:** gli esempi di seguito riportati fanno riferimento alla seguente base di dati:
- **F(<u>codF**, nome, età , città)</u>
- **P(<u>codP**, nome, colore)</u>
- **FP(<u>codF***, \underline{codP*</u>, prezzo, quantità)}

### Selezione

```sql
SELECT *
FROM F
WHERE citta = Roma
```
Equivale a:

$
\sigma_{citta' = Roma} F
$

### Proiezione

```sql
SELECT codF, nome
FROM F
```
Equivale a:

$
\pi_{codF, nome} F
$

### Prodotto Cartesiano

```sql
SELECT codP, codF, citta
FROM F, P
```
Come sopra accennato, tra le relazioni che appaiono nella parte FROM viene fatto il prodotto cartesiano.
La relazione risultante ha quindi tutti gli attributi di F e di P.

Per disambiguare eventuali attributi con lo stesso nome, si usa la *dot notation*.

```sql
SELECT codP, F.nome, codP
FROM F, P
```
La notazione F.nome consente di disambiguare il significato di “nome”: nome del Fornitore o del Prodotto?

### Join
Per realizzare un join è necessario specificare la condizione di join nella clausola WHERE.

```sql
SELECT F.nome, FP.codP, FP.prezzo
FROM F, FP
WHERE F.codF = FP.codF      /* condizione di join */
```

La suddetta interrogazione fornisce i nomi dei fornitori, nonché i codici e i prezzi dei prodotti da essi forniti.
Equivale a:

$
\pi_{nome, codP, prezzo}(\sigma_{F.codF = FP.codF}(F \bowtie FP))
$
e:

$
\pi_{nome, codP, prezzo}((F \bowtie_{F.codF = FP.codF} FP))
$

### Selezione, Proiezione e Join

Nella clausola WHERE, oltre alle condizioni di join, vengono specificate altre eventuali condizioni di selezione.

**ESEMPIO:** codici e nome dei fornitori di Roma, con età inferiore ai 40 anni, che forniscono qualche prodotto:
```sql
SELECT F.codf, F.nome
FROM F, FP
WHERE F.codF = FP.codF AND citta = Roma AND eta < 40
```
Equivale a:

$
\pi_{codF, nome}(\sigma_{citta' = Roma AND eta' < 40}(F \bowtie_{F.codF = FP.codF} FP))
$
**NOTA:** il join tra F e FP è necessario per restringere l’insieme dei fornitori solo a quelli che forniscono almeno un prodotto.

### Eliminazione di duplicati

A differenza dell’AR, la risposta generata da una query SQL può contenere duplicati, a meno che non venga specificata la parola chiave DISTINCT:

```sql
SELECT DISTINCT citta
FROM F
```

### Ridenominazione
È possibile ridenominare gli attributi di selezione come di seguito:

```sql
SELECT codf AS codiceForn, nome AS nomeForn
FROM F
```

È anche possibile ridenominare le relazioni attraverso l’uso di variabili. La ridenominazione delle relazioni comporta la ridenominazione implicita dei relativi attributi.

**ESEMPIO:** coppie di nomi di fornitori (X,Y) tali per cui X è più anziano di Y:
```sql
SELECT X.nome, Y.nome
FROM F AS X, F AS Y
WHERE X.eta > Y.eta
```

![[ridenominazione.PNG]]

Senza l’uso delle variabili non c’è possibilità di disambiguare i nomi degli attributi.
Equivale a:

$
\pi_{X.nome, Y.nome}(\sigma_{X.età > Y.età}(\rho_{X.codF \leftarrow codF, X.nome \leftarrow nome, X.citta' \leftarrow citta'} F \bowtie \rho_{Y.codF \leftarrow codF, Y.nome \leftarrow nome, Y.eta' \leftarrow eta', Y.citta' \leftarrow citta'}))
$

**ESEMPIO:** codici e nomi dei fornitori più giovani del fornitore f1:
```sql
SELECT Y.codf AS codiceGiovane, Y.nome AS nomeGiovane
FROM F AS X, F AS Y
WHERE X.codF = f1 AND X.eta > Y.eta
```
**ESEMPIO:** nome e cognome di fornitori che hanno lo stesso cognome, ma nome diverso, di fornitori di
Roma:
```sql
SELECT F2.nome AS Nome, F2.Cognome AS Cognome
FROM F AS F1, F AS F2
WHERE F1.citta = Roma AND F1.cognome = F2.cognome AND F1.nome != F2.nome
```

## Operatori insiemistici

Si ottiene l’equivalenza con l’AR.

### Unione

**ESEMPIO:** nomi dei fornitori che forniscono p1 o p2:
```sql
SELECT Nome
FROM F, FP
WHERE F.codF = FP.codF AND FP.codP = p1
    UNION
SELECT Nome
FROM F, FP
WHERE F.codF = FP.codF AND FP.codP = p2
```
Alternativamente:
```sql
SELECT Nome
FROM F, FP
WHERE F.codF = FP.codF AND (FP.codP = p1 OR FP.codP = p2)
```

### Intersezione

**ESEMPIO:** nomi dei fornitori che forniscono sia p1 che p2:
```sql
SELECT Nome
FROM F, FP
WHERE F.codF = FP.codF AND FP.codP = p1
    INTERSECT
SELECT Nome
FROM F, FP
WHERE F.codF = FP.codF AND FP.codP = p2
```

### Differenza

**ESEMPIO:** codici dei fornitori che non forniscono prodotti:
```sql
SELECT codF
FROM Fornitori
    EXCEPT (oppure MINUS)
SELECT codF
FROM FP
```

## Sottoquery

Non aggiungono potere espressivo, ma rendono l’uso del linguaggio più semplice e naturale.

### Operatore IN

Operatore insiemistico di appartenenza di un elemento: $ \in $

```sql
SELECT nome
FROM F
WHERE codF IN (SELECT codF
                FROM FP) 
```
La sottoquery restituisce l’insieme S dei codici dei fornitori che forniscono almeno un prodotto. La query esterna a sua volta restituisce i nomi dei fornitori i cui codici appartengono ad S.
La suddetta query si presta quindi alla seguente lettura: calcola i nomi dei fornitori i cui codici appartengono all’insieme dei codici dei fornitori che forniscono qualche prodotto.

```sql
SELECT nome
FROM F, FP
WHERE F.codF = FP.codF
```
```sql
SELECT codF
FROM F 
WHERE cognome IN (SELECT cognome
                    FROM F
                    WHERE citta' = Roma)
```
```sql
SELECT F.nome
FROM F
WHERE codF IN (SELECT codF 
                FROM FP
                WHERE codP IN (SELECT codP
                                FROM P
                                WHERE colore = rosso)
```
Con riferimento alla base di dati in appendice, la sottoquery di secondo livello restituisce l’insieme *S = [p1, p2]* – i codici dei prodotti di colore rosso. La sottoquery di primo livello restituisce quindi l’insieme T dei codici dei fornitori in FP che forniscono prodotti i cui codici sono in S, cioè, *T = \{f1, f2, f3\*}. Infine, la query esterna restituisce i nomi dei fornitori i cui codici sono in T, quindi: [chiara, aldo, piero].

*QUERY NIDIFICATE CORRELATE:* Si basano sull’uso di variabili che legano query e sottoquery.
```sql
SELECT codF
FROM F AS F1
WHERE cognome IN (SELECT cognome
                    FROM F
                    WHERE citta' = Roma AND F1.nome != nome)
```
Nella suddetta query, l’interrogazione nidificata fa riferimento alla query esterna attraverso la  variabile F1. Da un punto di vista operazionale, essa può essere interpretata come segue: per ogni tupla F1 di Fornitore (query esterna) vengono selezionate tutte le tuple di Fornitore (query interna) tali che la città sia Roma ed il nome sia diverso da quello di F1. Se il cognome di F1 è nell’insieme calcolato dalla query nidificata, allora la query è soddisfatta e, per il fornitore F1, vengono forniti nome e cognome. Pertanto, la query nidificata viene valutata una volta per ogni tupla di Fornitori.

**NOTA:** la suddetta interrogazione può essere risolta con un prodotto cartesiano.

Si noti che la variabile F1, definita nella query esterna, è visibile nella sottoquery. In quest’ultima non è necessario (ma non è vietato) l’uso di altre variabili, in quanto gli attributi “città” e “nome” che appaiono nella clausola WHERE fanno riferimento alla istanza di F che appare nella clausola FROM della sottoquery.

In generale, una variabile definita in una (sotto)query è visibile in tutte le sottoquery più interne –
vale la regola di visibilità delle variabili nei linguaggi di programmazione.

### Operatore NOT IN

Operatore insiemistico di non appartenenza di un elemento: $ \notin $

```sql
SELECT nome
FROM F
WHERE codF NOT IN (SELECT codF
                    FROM FP)
```
Equivalente all’uso dell’operatore MINUS.
```sql
SELECT codF
FROM F AS F1
WHERE nome NOT IN (SELECT nome
                    FROM F AS F2
                    WHERE F1.codF != F2.codF)
```
La sottoquery restituisce, per ogni fornitore F1, i nomi di tutti gli altri fornitori.

**NOTA:** l’uso di F2 nella sottoquery non è necessario.

```sql
SELECT codF
FROM F 
WHERE codF NOT IN (SELECT F1.codF
                    FROM F AS F1, F AS F2
                    WHERE F1.codF != F2.codF AND F1.nome = F2.nome)
```
La sottoquery restituisce l’insieme dei codici dei fornitori che hanno qualche omonimo.

**NOTA:** in generale, una query correlata può essere trasformata in una query non correlata tramite il prodotto cartesiano.

### Operatore EXISTS

L’operatore EXISTS viene usato per controllare se il risultato di una **sottoquery correlata** (nei casi significativi) è vuoto oppure no. Si ispira all’operatore $ \exists $ del calcolo dei predicati.

```sql
SELECT nome 
FROM F AS F1
WHERE EXISTS (SELECT *
                FROM F AS F2
                WHERE F1.codF != F2.codF AND F1.nome = F2.nome)
```
```sql
SELECT codF
FROM F AS F1
WHERE EXISTS (SELECT *
                FROM F AS F2
                WHERE F2.eta < F1.eta)
```
Le suddette query si possono riformulare, in maniera meno naturale, attraverso il prodotto
cartesiano.

### Operatore NOT EXISTS

L’operatore NOT EXISTS ($ \nexists $) viene usato per controllare se il risultato di una **sottoquery correlata** è vuoto oppure no.

```sql
SELECT codF
FROM F AS F1
WHERE NOT EXISTS (SELECT *
                    FROM F AS F2
                    WHERE F1.codF != F2.codF AND F1.nome = F2.nome)
```
Il meccanismo di valutazione è sempre quello delle query correlate in generale (come per l’EXISTS): per ogni fornitore F1 viene calcolato l’insieme S delle tuple di F che rappresentano fornitori diversi da F1 e che hanno lo stesso nome. Se S è l’insieme vuoto, allora F1 è un fornitore che non ha omonimi e, quindi, fa parte della risposta.
```sql
SELECT codF
FROM F AS F1
WHERE NOT EXISTS (SELECT *
                    FROM F AS F2
                    WHERE F2.eta < F1.eta)
```
Attraverso il NOT EXISTS (o NOT IN) è possibile calcolare il MIN e il MAX di un insieme – il min (risp. max) di un insieme è un elemento tale che NON ESISTE un altro elemento minore (risp. maggiore).

```sql
SELECT codF
FROM FP AS F1
WHERE codP = p1 AND NOT EXISTS (SELECT *
                                FROM FP
                                WHERE F1.codF = codF AND codP != p1)
```

### Operatori di confronto

Si tratta degli usuali operatori di confronto $ \{=, >, < , ...\} $ che vengono utilizzati per collegare una query ad una sotto-query.

```sql
SELECT nome
FROM F
WHERE eta < (SELECT eta
                FROM F
                WHERE codF = f1)
```
**NOTA:** la sotto-query deve restituire un unico valore.
```sql
SELECT codF
FROM FP
WHERE codP = p2 AND prezzo < (SELECT prezzo
                                FROM FP
                                WHERE codF = f1 AND codP = p2)
```

### Operatori ALL e ANY

È possibile usare operatori aritmetici con sotto-query che restituiscono più valori usando ALL e ANY.

```sql
SELECT nome
FROM F
WHERE eta >= ALL (SELECT eta
                    FROM F)
```
La sotto-query restituisce l’insieme delle età dei fornitori $ S = {30, 40, 50} $. La query esterna quindi restituisce il nome dei fornitori la cui età è maggiore o uguale ad ogni età in S.

Attraverso le condizioni $ >= $ ALL e $ <= $ ALL è possibile calcolare il MAX e il MIN di un insieme.
```sql
SELECT codF
FROM FP
WHERE codP = p1 AND prezzo <= ALL (SELECT prezzo
                                    FROM FP
                                    WHERE codP = p1)
```
```sql
SELECT nome
FROM F
WHERE eta > ANY (SELECT eta
                    FROM F)
```

## Funzioni di aggregazione

Sono funzioni non rappresentabili in Algebra Relazionale, quindi rendono SQL più espressivo dell’AR. Sono: *COUNT*, *SUM*, *MIN*, *MAX*, *AVG*.

### Funzione COUNT

```sql
SELECT COUNT(*) AS #forn 
FROM F
WHERE citta = Roma
```
```sql
SELECT COUNT(ALL citta)
FROM F
```

### Funzioni MAX e MIN

```sql
SELECT codP
FROM FP
WHERE prezzo = (SELECT MAX(prezzo)
                FROM FP)
```

La sotto-query restituisce il prezzo massimo Q che appare nella relazione FP. La query esterna a sua volta restituisce i codici dei prodotti il cui prezzo è pari a Q.

**NOTA:** se la clausola *SELECT* contiene funzioni aggregate, tutti gli attributi nella clausola *SELECT* devono essere contenuti nelle funzioni aggregate.

```sql
SELECT COUNT(*)
FROM FP
WHERE prezzo = (SELECT MIN(prezzo)
                    FROM FP)
```

Con riferimento alla base di dati di esempio, la sotto-query restituisce il prezzo minimo, che è 20.
La query esterna quindi seleziona le tuple di FP in cui il prezzo è pari a 20 (le ultime 2), e conta il numero di tali tuple.

### Funzione SUM

```sql
SELECT SUM(quantita)
FROM FP
WHERE codP = p2 AND codf IN (SELECT codF
                                FROM F
                                WHERE citta = milano)
```

### Clausola GROUP BY

È utilizzata in query con funzioni di aggregazione con lo scopo di limitare la loro applicazione a sottoinsiemi di tuple.

```sql
SELECT citta, COUNT(*) AS #forn
FROM F
GROUP BY citta
```
*PARTENZA:* 

![[groupBy1.PNG]]

*RISULTATO:* 

![[groupBy2.PNG]]

**NOTA:** viene creato un raggruppamento di tuple per ogni valore di città (attributo presente nel GROUP BY). Il conteggio dei fornitori avviene all’interno di ogni singolo raggruppamento.

In generale, ogni raggruppamento è formato da tutte le tuple della relazione che hanno gli stessi valori degli attributi di raggruppamento – quelli che appaiono nel *GROUP BY*.

```sql
SELECT codF, COUNT(*) AS #prod, SUM(quantita) AS quanTot
FROM FP
GROUP BY codF
```
*PARTENZA:* 

![[groupBy3.PNG]]

*RISULTATO:* 

![[groupBy4.PNG]]

Gli attributi della *SELECT* devono apparire nel *GROUP BY*.

```sql
SELECT codF, nome, COUNT(*) AS #prod, SUM(quantita) AS quanTot
FROM F, FP
WHERE F.codF = FP.codF
GROUP BY codF, nome
```
*PARTENZA:* 

![[groupBy5.PNG]]

*RISULTATO:* 

![[groupBy6.PNG]]

Siccome il nome è richiesto nella clausola *SELECT*, deve anche apparire tra gli attributi del *GROUP BY* – si noti che la presenza del nome non modifica i raggruppamenti in quanto ad un codice fornitore corrisponde esattamente un nome.

```sql
SELECT nome, COUNT(*) AS #prod, SUM(quantita) AS quanTot
FROM F, FP
WHERE F.codF = FP.codF 
GROUP BY codF, nome
```

*RISULTATO:* 

![[groupBy7.PNG]]

Siccome il *nome* non è chiave primaria di *Fornitore* – ad esempio, sia f2 che f3 si chiamano entrambi Piero, se vogliamo mantenere la distinzione tra fornitori diversi, nel *GROUP BY* va comunque inserito il codice, anche se non viene usato nella clausola *SELECT*.

Usando solo il *nome* nel *GROUP BY*, si ottiene un risultato diverso, che risponde alla interrogazione:

```sql
SELECT nome, COUNT(*) AS #prod, SUM(quantita) AS quanTot
FROM F, FP
WHERE F.codF = FP.codF 
GROUP BY nome
```

*PARTENZA:* 

![[groupBy8.PNG]]

*RISULTATO:* 

![[groupBy9.PNG]]

### Clausola HAVING

Consente di selezionare gruppi creati con il *GROUP BY* – a differenza della clausola *WHERE* che consente di selezionare singole tuple.

```sql
SELECT codF
FROM FP
GROUP BY codF 
HAVING COUNT(*) > 1
```
Grazie al *GROUP BY*, il *COUNT* viene calcolato all’interno di ogni singolo raggruppamento. 

![[having1.PNG]]

La clausola *HAVING* restituisce quindi i raggruppamenti che soddisfano la condizione *COUNT $>$ 1* (in rosso).

![[having2.PNG]]

```sql
SELECT codF
FROM FP
GROUP BY codF
HAVING SUM(quantita) > 100
```

```sql
SELECT codF
FROM FP
GROUP BY codF
HAVING SUM(quantita) > = ALL (SELECT SUM(quantita)
                                FROM FP 
                                GROUP BY codF)
```

Dalla relazione FP:

![[having3.PNG]]

la sotto-query calcola il seguente insieme $S = \{150, 30, 300\}$, i cui elementi sono le somme delle quantità fornite da ogni fornitore. La query esterna quindi calcola, per ogni fornitore, la quantità totale *SUM(quantità)* fornita e restituisce il codice del fornitore presente nel raggruppamento che soddisfa la condizione *SUM(quantità) $>$= ALL*, cioè, in cui la quantità totale è maggiore o uguale ad ogni elemento di *S*.