---
aliases: [AR]
tags: [basi-di-dati]
---
È un linguaggio di interrogazione costituito da un insieme di operatori definiti su relazioni che producono relazioni.
Le interrogazioni vengono formulate attraverso espressioni algebriche.
Gli operatori sono:
- insiemistici;
- specifici unari;
- specifici tipo join.

## Operatori insiemistici

Le relazioni sono insiemi (di tuple) e per questo possono essere manipolate attraverso operatori mutuati dalla teoria degli insiemi.
A differenza degli insiemi, le relazioni hanno uno schema e, relazioni che hanno lo stesso schema, hanno elementi omogenei.
Le operazioni di unione, sottrazione e intersezione possono essere fatte a parità di schema: ad esempio, si può fare l'unione di un insieme di persone con un altro insieme di persone ma non con un insieme di macchine.

Date due relazioni con gli stessi attributi *R(X)* e *S(X)*, dove *X=\{A1, ..., An\*}, la loro unione, differenza e intersezione è una relazione *T(X)*, che ha gli stessi attributi, e la cui estensione è la seguente:

$
T = R \cup S = \{t | t \in R$ or $t \in S\}
$

$
T = R - S = \{t | t \in R$ and $t \notin S\}
$

$
T = R \cap S = \{t | t \in R$ and $t \in S\}
$

![[operatoriInsiemistici.PNG]]

## Operatori specifici unari

***Selezione:*** $\sigma_F(R) $ è un operatore unario che restituisce una relazione S le cui tuple sono tutte e solo le tuple di R che soddisfano il predicato F. Matematicamente:

$
\sigma_F(R) = \{t \in R | t $ soddisfa $F\}
$

F è un predicato definito sugli attributi di R che utilizza operatori logici e operatori di confronto. Si noti che gli attributi di S coincidono con quelli di R.

**Esempio:** data la relazione *R(A, B, C)*:

$\sigma_{A = a \wedge C = c}(R) $
restituisce una relazione *S(A, B, C)* che ha gli stessi attributi di R e la cui estensione consiste di tutte le tuple di R che soddisfano la condizione A = a $
\wedge
$ C = c.

![[esempioOpSpecificiUnari1.PNG]]

***Proiezione:*** $\pi_Y(R) $ è un operatore unario che, data una relazione con schema *R(X)* e un sottoinsieme di attributi Y di X, restituisce una relazione *S(Y)* le cui tuple si ottengono da quelle di R considerando solo i valori sugli attributi in Y. Matematicamente:

$
\pi_Y(R) = \{t[Y]|t \in R\}
$

**Esempio:** data la proiezione *R(A, B, C)*:

$\pi_{A, B}(R) $
restituisce una relazione *S(A, B)* che ha gli attributi A, B specificati come pedice dell'operatore e la cui estensione consiste di tute e solo le tuple di R proiettate sugli stessi attributi:

![[esempioOpSpecificiUnari2.PNG]]

***Ridenominazione:*** operatore unario che cambia il nome degli attributi di una relazione:

$
\rho_{A1...An \leftarrow B1...Bn}
$

**Esempio:** date le relazioni *Padre(padre, figlio)* e *Madre(madre, figlio)*, generare la relazione *Genitore(genitore, figlio)*:

$ Genitore(genitore, figlio) = \rho_{genitore \leftarrow padre}(Padre) \cup \rho_{genitore \leftarrow madre}(Madre) $
**NOTA:** $ \rho{genitore \leftarrow padre}(Padre) $ modifica lo schema di *Padre* come segue: *Padre(genitore, figlio)*.

## Join

L'operatore join è l'operatore che concente di connettere informazioni contenute in relazioni diverse.

- join naturale
- equi-join
- theta-join
- join esterni

### Join naturale

Correla tuple in relazioni diverse sulla base di valori uguali di attributi con lo stesso nome.
Il join naturale di *R(X) $\bowtie$ S(Y)* produce una relazione *T(Z)* con *Z = X $\cup$ Y* ed estensione *E* definita come segue:

*E = \{t su X $\cup$ t.c. t[X] $\in$ R e t[Y] $\in$ S\*}
dove *t[X]* è la tupla *t* proiettata sugli attributi in X.

**Esempio:** Siano date due relazioni *R(X)* e *S(Y)*, con *X = \{A,B\*} e  *Y=\{B,C\*}. Il join naturale produce una relazione *T(Z)*, con *Z = X $\cup$ Y = \{A,B,C\*}, le cui tuple si ottengono concatenando le tuple di *R* e di *S* con lo stesso valore dell’attributo in comune *B*:

![[joinNaturale1.PNG]]

Si noti che se *X $\cap$ Y = $\Phi$*, cioè le due relazioni non hanno attributi in comune, il join naturale degenera nel prodotto cartesiano:

![[joinNaturale2.PNG]]

Si noti che se *X = Y*, cioè le due relazioni hanno gli stessi attributi, il join naturale coincide con l’intersezione:

![[joinNaturale3.PNG]]

### Equi-join

È un operatore che consente di eseguire join anche tra relazioni che non hanno attributi in comune, senza che questo degeneri nel prodotto cartesiano:

$
R \bowtie_F S
$
dove F è un predicato che esprime condizioni di uguaglianza tra attributi delle due relazioni.

Ad esempio, dati R(A,B,C) e S(X,Y,Z), F potrebbe essere:

*F = (A = X and B = Y)*
Il risultato è una relazione che ha gli attributi di *R* e di *S* prendendo una sola volta quelli che appaiono in *F* e come tuple la concatenazione delle tuple di *R* e di *S* che hanno gli stessi valori sugli attributi di join.

**Esempio:**
Prof(<u>codP</u>, nome, età, dip*)

Dip(<u>codD</u>, nomeD, univ, dir*)
Il risultato della seguente espressione:

*Prof $\bowtie_{dip=codD*$ Dip}
è una relazione che ha il seguente schema:

*ProfDip(codP, nome, età, dip, nomeD, univ, dir)*
in cui i due attributi dip e codD sono rappresentati solo da uno di essi.

![[equijoin.PNG]]

L’equi-join (così come il join naturale) è un operatore derivato, nel senso che può essere espresso attraverso altri operatori:

R $\bowtie_F$ S = $\pi_Z$ ($\sigma_F$ (R $\bowtie$ S))
dove $\pi_Z$ produce la proiezione su tutti gli attributi di R e S, prendendo una sola volta quelli che appaiono in F.

## Algebra relazionale per le interrogazioni

Usando gli operatori dell’algebra relazionale è possibile costruire delle espressioni che producono come risultato una relazione.
Tali espressioni possono essere utilizzate per formulare interrogazioni sulle [[Basi di Dati|basi di dati]] relazionali

### Base di dati di esempio
L'esempio di riferimento per le prossime spiegazioni saranno:

![[BDEsempio.PNG]]

Lo schema logico è il seguente:
- **Prodotto**(<u>codP</u>, colore, prezzo, forn*)
- **Fornitore**(<u>codF</u>, nome, città)
- **Composizione**(prod*, comp*)
Gli attributi *prod* e *comp* sono chiavi secondarie definite sul codice dei prodotti.

![[BDDati.PNG]]

## Interrogazioni

- **interrogazioni positive:** si basano su proiezione, selezione, join, unione
- **interrogazioni negative:** serve anche la differenza

### Proiezione-selezione

**Come scoprire i codici dei componenti del prodotto *p1**?*

$\pi_{comp}$ ($\sigma_{prod=p1}$ (Composizione))
Tutta l’informazione è racchiusa nella relazione Composizione, per cui è sufficiente utilizzare operatori unari.

![[proiezioneSelezione.PNG]]

### Proiezione-selezione-join

**Come scoprire il colore dei prodotti di cui è componente il prodotto *p3**?*

$\pi_{colore}$ ($\sigma_{comp = p3}$ (Composizione $\bowtie_{prod = codP}$ Prodotto))
**NOTA:** il join tra *Prodotto* e *Composizione* si basa su una condizione di uguaglianza tra la chiave primaria *codP* e la chiave secondaria *prod*.

![[proiezioneSelezioneJoin1.PNG]]

**Come scoprire i codici e i nomi dei Fornitori di prodotti che hanno il componente p2?**

$\pi_{codF, nome}$ ((Prod $\bowtie_{codP=prod}$ ($\sigma_{comp = p2}$ Comp)) $\bowtie_{forn=codF}$ Forn)

![[proiezioneSelezioneJoin2.PNG]]

**Come scoprire i nomi dei Fornitori dei componenti di prodotti di colore giallo?**

$\pi_{nomeF}$ ($\sigma_{col=giallo}$ Prod $\bowtie_{codP=prod}$ Comp $\bowtie_{comp=codP}$ Prod $\bowtie_{forn=codF}$ Forn)

![[proiezioneSelezioneJoin3.PNG]]

**Come scoprire le coppie di codici di prodotti con lo stesso prezzo?** La relazione coinvolta è Prodotto.

$\pi_{codP, codP'}$ ($\sigma_{codP != codP' AND prezzo = prezzo'}$ (Prodotto $\bowtie$ Prodotto'))

$Prodotto' = \rho_{codP' \leftarrow codP, colore' \leftarrow colore, prezzo' \leftarrow prezzo, forn' \leftarrow forn}Prodotto $
**NOTA:** per fare il prodotto cartesiano di Prodotto con se stesso abbiamo dovuto procedere ad una ridenominazione di Prodotto.

![[proiezioneSelezioneJoin4.PNG]]

### Uso della differenza

**Come scoprire i codici dei prodotti che non hanno componenti?** Le relazioni coinvolte sono Prodotto e Composizione.

$
\pi_{codP}(Prodotto) - \rho_{codP \leftarrow comp}(\pi_{comp}(Composizione))
$
**NOTA:** interrogazione negativa. In AR è necessario usare l’operatore “differenza”.

![[usoDifferenza1.PNG]]

**Come scoprire i codici dei fornitori che NON forniscono il prodotto p1?** Le relazioni coinvolte sono Prodotto e Fornitore.

$
\pi_{codF}(Forn) - \rho_{codF \leftarrow forn}(\pi_{forn}(\sigma_{codP=p1}Prod ))
$
**Come scoprire i codici dei prodotti che NON hanno componenti con un prezzo maggiore di 100?** Per semplicità, suddividiamo il problema in due sotto-problemi:
- calcolo dei codici dei prodotti che hanno qualche componente con un prezzo maggiore di 100;
- calcolo della differenza tra l’insieme dei codici di tutti i prodotti e quello calcolato al passo precedente.

$
R(prod) = \pi_{prod}(Composizione \bowtie_{comp=codP}(\sigma_{prezzo > 100}Prodotto))

\pi_{codP}Prodotto - \rho_{codP \leftarrow prod}R
$
**Come scoprire i codici dei prodotti i cui componenti sono *tutti** di colore giallo?* Le relazioni coinvolte sono Prodotto e Composizione.

La query può essere riformulata come segue: codici dei prodotti che NON hanno alcun componente di colore diverso dal giallo.

$
R(prod) = \pi_{prod}(Composizione \bowtie_{comp=codP}(\sigma_{colore != giallo}Prodotto))

\pi_{codP}Prodotto - \rho_{codP \leftarrow prod}R
$
**Come scoprire i codici dei prodotti che hanno *solo** il componente p3?* La relazione coinvolta è Prodotto.

La query può essere riformulata come segue: codici dei prodotti che hanno il componente p3 e NON hanno alcun altro componente.

$
\pi_{prod}(\sigma_{comp=p3}Comp)) - \pi_{prod}(\sigma_{comp!=p3}Comp))
$

![[usoDifferenza2.PNG]]

**Come scoprire i codici dei componenti di un *unico** prodotto?*

$
\pi_{comp}Comp - \pi_{comp}(\sigma_{comp=comp' AND prof != prod'}(Comp \bowtie Comp'))

Comp' = \rho_{prod' \leftarrow prod, comp' \leftarrow comp}(Comp)
$
La sotto-espressione di destra della prima espressione calcola, attraverso il prodotto cartesiano, i codici dei componenti di almeno due prodotti.
Con la differenza si individuano quindi i codici dei componenti di un unico prodotto.

![[usoDifferenza3.PNG]]