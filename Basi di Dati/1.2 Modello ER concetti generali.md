---
aliases: [MECG]
tags: [basi-di-dati]
---
È un modello concettuale per la rappresentazione dei dati del dominio di interesse ad un elevato livello di astrazione. È un modello grafico molto semplice da rappresentare ed ha una sua complessità. I suoi costrutti di base sono:
- *entità*
- *attributi*
- *relazione (associazione)*
## Entità
Una entità E rappresenta un insieme (classe) di oggetti omogenei del mondo reale, cioè, conformi ad un unico schema. Una entità è uguale ad uno schema più un insieme di istanze.
*Un esempio di entità sarebbe un insieme degli studenti*
Lo schema descrive la struttura delle istanze - *intensione di E*. L'insieme delle istanze di E è detta *estensione di E*. L'entità è diverso dal concetto matematico di insieme.
## Attributi
Uno schema è composto da un nome e da attributi. Gli attributi descrivono le proprietà elementari di una entità. Ad esempio, l'entità Persona è caratterizzata dai seguenti attributi: *CF (chiave primaria), nome, età, sesso*.
Una istanza di Persona è una quadrupla del tipo:

$<$ XY32Z, clara, 24, F $>$
L'estensione di Persona è un insieme di istanze aventi questa struttura. La **chiave primaria** è un insieme di attributi che identificano univocamente le istanze di una entità (nel modello la chiave primaria viene sottolineata come in figura qui sotto).

![[schemaPersona.PNG]]

## Relazione (associazione)
Una relazione (associazione) è un legame logico tra entità. Un esempio pratico sarebbe:

![[relazione.PNG]]

- La relazione R associa ad ogni istanza di A qualche istanza di B, e viceversa - *relazione binaria*;
- Le *istanze* di r sono coppie del tipo *$<$a,b$>$*, con *a* istanza di A e *b* istanza di B - *a* è associata a *b* secondo R;
- L'estensione di R è l'insieme delle istanze di R, quindi, un sottoinsieme del prodotto cartesiano A x B.
Entriamo nella pratica della relazione e parliamo delle varie rappresentazioni con un esempio:

![[relazione.PNG]]

- A = {a, b, c}, B = {m, n}
- A x B = {$<$a,m$>$, $<$a,n$>$, $<$b,m$>$, …, $<$c,n$>$}
- R = {$<$a,m$>$, $<$b,n$>$}
- *a* è associato a *n* e *b* è associato a *m* (secondo R)

![[insiemeRelazione.PNG]]

![[rappresentazioneTabellare.PNG]]

 Rappresentazione tabellare della estensione di R
## Vincoli di cardinalità
Ogni entità partecipa a R con due vincoli di cardinalità: *massima e minima*. I vincoli di cardinalità pongono dei limiti alla estensione di R. A seconda dei vincoli, una relazione può essere:
- Molti-a-Molti *(n-m)*
- Uno-a-Molti *(1-n)*
- Uno-a-Uno *(1-1)*

## Esempi di modello ER (Entità-Relazione)
### Esempio Molti-a-Molti (n-m) (num. 1)
- Il concetto **Esame** è modellato attraverso una relazione;
- La relazione **Esame** è una relazione binaria tra **Studente** e **Corso** che associa ad ogni studente zero o più corsi e ad ogni corso zero o più studenti;
- Il tipo di relazione *molti-a-molti (n-m)*, con partecipazione opzionale da ambo i lati.

![[esempio5.PNG]]

Una istanza di E è una coppia, ad esempio <S1, C1> vale a dire che lo studente S1 ha sostenuto l'esame del corso C1.

La sua estensione è un insieme di coppie $<$studente, corso$>$ e quindi:

E $ \subseteq $ S x C
Ad esempio:

E = {$<$S1, C1$>$, $<$S2, C1$>$, $<$S2, C2$>$, … $<$S5,C3$>$}
**NOTA:** ad uno studente non possono essere associati più esami dello stesso corso.

E = {$<$S1, C1$>$, ~~$<$S1, C1$>$~~, $<$S2, C1$>$, $<$S2, C2$>$, …, $<$S5,C3$>$}
Ovviamente nella realtà, questo modello ER non è corretto essendo che uno studente può essere bocciato più volte e quindi la coppia si ripete più volte.

### Esempio Molti-a-Molti (n-m) (num. 2)

![[esempio1.PNG]]

- Associazione molti-a-molti
- Partecipazione di Persona (A) *opzionale*
- Partecipazione di Birra (B) *obbligatoria*
- Ad ogni persona piacciono zero o più birre, una birra piace ad almeno una persona
- Persona = {a, b, c, d}, Birra = {m, n, o, p, q}
- Piace $ \subseteq $ A x B
- Piace = { $<$a,m$>$, $<$a,n$>$, $<$b,o$>$, $<$b,q$>$, $<$c,n$>$, $<$c,p$>$ }

![[insieme1.PNG]]

### Esempio Uno-a-Molti (1-n)
Viene riportato l'esempio precedente ma con diversi vincoli:

![[esempio2.PNG]]

- Associazione uno-a-molti
- Partecipazione di Persona (A) *obbligatoria*
- Partecipazione di Birra (B) *obbligatoria*
- Ad ogni persona piace esattamente una birra
- Ogni birra piace ad almeno una persona
- Piace $ \subseteq $ A x B è una *funzione suriettiva* - in virtù dei punti 2 e 5

![[insieme2.PNG]]

### Esempio Uno-a-Uno (1-1) (num. 1)
Viene riportato l'esempio precedente ma con diversi vincoli:

![[esempio3.PNG]]

- Associazione uno-a-uno
- Partecipazione di Persona (A) *obbligatoria*
- Partecipazione di Birra (B) *opzionale*
- Ad ogni persona piace una e una sola birra
- Ogni birra piace al più ad una persona
- Piace $ \subseteq $ A x B - *funzione iniettiva*

![[insieme3.PNG]]

### Esempio Uno-a-Uno (1-1) (num. 2)
Viene riportato l'esempio precedente ma con diversi vincoli:

![[esempio4.PNG]]

- Associazione uno-a-uno
- Partecipazione di Persona (A) *obbligatoria*
- Partecipazione di Birra (B) *obbligatoria*
- Ad ogni persona piace una e una sola birra
- Ogni birra piace ad una e una sola persona
- R $ \subseteq $ A x B - *funzione biunivoca* - iniettiva + suriettiva

![[insieme4.PNG]]