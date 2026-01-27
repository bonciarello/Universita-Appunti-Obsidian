## Molteplici relazioni
Tra due identità è possibile definire più relazioni.

![[moltepliciRelazioni1.PNG]]

Entrambe le relazioni R e SdL sono di tipo *uno-a-molti (1:n)*. R e SdL sono due funzioni con dominio su Persona e codominio su Città. Esempi di estensioni delle due relazioni sono:
- R = {$<$I1, C1$>$, $<$I2, C1$>$, …, $<$I5, C3$>$}
- SdL = {$<$I1, C2$>$, $<$I2, C1$>$, …, $<$I5, C2$>$}
### Esempio
Viene riportato l'esempio di due entità Professore e Dipartimento per raggruppare le afferenze e le direzioni dei vari dipartimenti.

![[moltepliciRelazioni2.PNG]]

- La relazione Afferenza è di tipo *1:n*;
- La relazione Direzione è di tipo *1:1*, con partecipazione di Professore opzionale: un professore può essere direttore ma può anche non esserlo.

## Relazione n-aria
Una **relazione n-aria** è una relazione definita su *n* entità.

*E1, …, En*
L’estensione di una relazione R definita sulle entità *E1, …, En* è un sottoinsieme del prodotto cartesiano:

R $ \subseteq $ *E1* x ... x *En*
Le istanze di R sono n-uple del tipo $<$*e1*, …,*en*$>$.
### Esempio di relazione ternaria
Il seguente schema rappresenta la relazione ternaria Fornitura (F) tra Fornitore, Prodotto e Dipartimento.

![[relazioneTernaria.PNG]]

La sua estensione è F $ \subseteq $ Fornitore x Prodotto x Dipartimento. Una istanza di F è quindi una tripla *$<$f,p,d$>$*, dove:
- *f* è un fornitore;
- *p* è un prodotto;
- *d* è un dipartimento.

Il significato di una fornitura $<$f,p,d$>$ sarebbe che il fornitore *f* ha fornito il prodotto *p* al dipartimento *d*.

Dati i vincoli di cardinalità, ogni Fornitore appare in almeno una istanza di F. Analogamente per Prodotto e Dipartimento. Quindi, ogni fornitore può fornire qualsiasi prodotto a qualsiasi dipartimento.

Il seguente insieme, per esempio, è una possibile estensione di F:

F = {$<$f1, p1, d1$>$, $<$f1, p1, d2$>$, $<$f2, p1, d1$>$, $<$f2, p2, d1$>$, $<$f2, p2, d2$>$}
**NOTA:** lo stesso fornitore non può fornire più di una volta lo stesso prodotto allo stesso dipartimento.

### Esempi di relazione ternaria con modifiche ai vincoli di cardinalità

![[vincoliTernaria1.PNG]]

Ogni istanza di *Prodotto* partecipa esattamente ad una istanza di fornitura: un *Prodotto* può essere fornito da un unico *Fornitore* ad un unico *Dipendente*. Quindi si passa della rappresentazione tabellare con i vincoli originali:

F = {$<$f1, p1, d1$>$, $<$f1, p1, d2$>$, $<$f2, p1, d1$>$, $<$f2, p2, d1$>$, $<$f2, p2, d2$>$}
alla rappresentazione tabellare con il vincolo modificato:

F = {$<$f1, p1, d1$>$, ~~$<$f1, p1, d2$>$~~, ~~$<$f2, p1, d1$>$~~, $<$f2, p2, d1$>$, ~~$<$f2, p2, d2$>$~~}
Modifichiamo un altro vincolo di cardinalità per vedere i cambiamenti:

![[vincoliTernaria2.PNG]]

Ogni istanza di *Dipartimento* partecipa esattamente ad una istanza di fornitura: un *Dipartimento* può essere fornito da un unico *Fornitore* di un unico *Prodotto*. Quindi si passa della rappresentazione tabellare con i vincoli originali:

F = {$<$f1, p1, d1$>$, $<$f1, p1, d2$>$, $<$f2, p1, d1$>$, $<$f2, p2, d1$>$, $<$f2, p2, d2$>$}
alla rappresentazione tabellare con il vincolo modificato:

F = {$<$f1, p1, d1$>$, $<$f1, p1, d2$>$, ~~$<$f2, p1, d1$>$~~, ~~$<$f2, p2, d1$>$~~, ~~$<$f2, p2, d2$>$~~}
 Modifichiamo tutti e due i vincolo di cardinalità per vedere i cambiamenti:

![[vincoliTernaria3.PNG]]

Ogni istanza di *Dipartimento* e ogni istanza di *Prodotto* partecipano esattamente ad una istanza di *Fornitura*: ogni *Prodotto* può essere fornito ad un unico *Dipartimento*, ed ogni *Dipartimento* ha un’unica *Fornitura*. Quindi si passa della rappresentazione tabellare con i vincoli originali:

F = {$<$f1, p1, d1$>$, $<$f1, p1, d2$>$, $<$f2, p1, d1$>$, $<$f2, p2, d1$>$, $<$f2, p2, d2$>$}
alla rappresentazione tabellare con il vincolo modificato:

F = {$<$f1, p1, d1$>$, ~~$<$f1, p1, d2$>$~~, ~~$<$f2, p1, d1$>$~~, ~~$<$f2, p2, d1$>$~~, $<$f2, p2, d2$>$}
## Attributi nelle relazioni
Anche le relazioni possono avere gli attributi. Facciamo un esempio:

![[attributiRelazioni1.PNG]]

Esempi di istanze:
- *Studente:* $<$252, Piero$>$
- *Corso:* $<$B12, Basi di Dati$>$
- *Esame:* $<$252, B12, 23, 3/5/2019$>$

**NOTA:** due esami dello stesso Studente per lo stesso Corso non sono ammessi, indipendentemente dai valori di voto e data.

E = {$<$**252**, **B12**, 23, 3/5/2019$>$, ~~$<$**252~~, \dashuline{B12**, 30, 8/8/2019$>$}, …}
La relazione ternaria Fornitura ha 3 attributi. Una Fornitura è una sestupla del tipo:

*$<$f1, p1, d2, 100, 2, 3/3/2020$>$*
**NOTA:** lo stesso fornitore non può fornire più di una volta lo stesso prodotto allo stesso dipartimento: ciò potrebbe costituire un limite.

![[attributiRelazioni2.PNG]]