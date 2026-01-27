Lo schema concettuale ER viene trasformato in uno schema relazionale equivalente.
Si tratta di rappresentare attraverso l’unico costrutto del modello dei dati relazionale i vari costrutti del modello ER.
La trasformazione si basa su semplici regole di “sintattiche”.

## Associazioni n:m (molti-a-molti)

Facciamo un esempio:

![[nm1.PNG]]

Il suo schema relazionale sarà:

- **Stud(<u>matr**, cognome)</u>
- **Mat(<u>cod**, nome)</u>
- **Esame(<u>matr***, \underline{mat*</u>, data, voto)}

Una relazione per ogni entità, più una relazione per l’associazione.
Gli attributi *matr* e *mat* di **Esame** sono chiavi secondarie che si riferiscono alle chiavi primarie *matr* (**Stud**) e *Cod* (**Mat**).
La coppia di attributi (*matr*, *mat*) di **Esame** è chiave primaria: quindi non possono esistere più registrazioni dello stesso esame.

![[nm2.PNG]]

Facciamo un altro esempio:

![[nm3.PNG]]

Il suo schema relazionale sarà:

- **Forn(<u>CodF**, città, nome)</u>
- **Dip(<u>CodD**, nome)</u>
- **Prod(<u>CodP**, nome)</u>
- **Fornitura(<u>CodF***, \underline{CodD*</u>, <u>CodP*</u>, quant, data)}

Lo schema di una relazione R che rappresenta una associazione n-aria molti-a-molti è composto:

- dalle n chiavi primarie delle entità connesse; ognuna di queste è una chiave secondaria, e tutte assieme formano la chiave primaria di R;
- dagli attributi della associazione.

## Associazioni 1:n (uno-a-molti)

Facciamo un esempio:

![[1n1.png]]

Il suo schema relazionale sarà:

- **Pers(<u>CF**, cognome, residenza*, data)</u>
- **Città(<u>nome**, regione)</u>

L'associazione *residenza* si rappresenta con l'attributo *residenza* di **Pers** che è chiave secondaria che si riferisce alla chiave primaria *nome* di **Città**: esso consente quindi di associare ad ogni istanza di persona una istanza di città.
L'attributo data della relazione *residenza* viene inserito nella relazione **Pers** in cui è presente la chiave secondaria *residenza*.

![[1n2.png]]

Facciamo un altro esempio:

![[1n3.png]]

Il suo schema relazionale sarà:

- Pers(<u>CF</u>, cognome, $<$nome, provincia$>$*, data)
- Comune(<u>nome</u>, <u>provincia</u>, regione)

La chiave secondaria in **Pers** è *$<$nome, provincia$>$*: essa consente di associare ad ogni istanza di **Pers** una istanza di **Comune**.

Una associazione 1:n tra due entità A e B, dove A sta dalla parte 1 dell'associazione, si rappresenta introducendo nella relazione che rappresenta A:

- una chiave secondaria S definita sulla chiave primaria della relazione che rappresenta B
- gli eventuali attributi dell'associazione

**NOTA:** si può sempre utilizzare lo schema usato per le associazioni n:m, usando un vincolo di unicità su S per garantire il vincolo di cardinalità 1:1 su A.

## Associazioni 1:1 (uno-a-uno)

![[111.png]]

Schema relazionale:

- Prof(<u>codP</u>, nome, età)
- Dip(<u>codD</u>, nome, ha\_dir*, data)

oppure:

- Prof(<u>codP</u>, nome, età, è\_dir*, data)
- Dip(<u>codD</u>, nome)

Come nel caso generale, l'associazione si rappresenta tramite chiave secondaria. Essendo l'associazione simmetrica (1:1) si può scegliere dove piazzarla: in **}{Prof** o in **}{Dip**.

I due schemi così ottenuti sono equivalenti. Quello di sopra, però, è preferibile per via dei valori nulli, meno valori nulli.

![[112.png]]

Consideriamo la sezione a sinistra: per garantire che un **Prof** non sia direttore di più di un **Dip** si introduce un vincolo di unicità sull'attributo *ha\_dir*.

Si tratta di una associazione simmetrica. Una associazione 1:1 tra due entità A e B si rappresenta introducendo nella relazione che rappresenta A (oppure B):

- una chiave secondaria definita sulla chiave primaria della relazione che rappresenta B (oppure A);
- gli eventuali attributi della associazione.

Se, ad es. su A, l’associazione è opzionale, si preferisce introdurre la chiave secondaria sulla relazione che rappresenta B.

## Associazioni ricorsive

![[ricorsive1.PNG]]

**NOTA:** si tratta di una relazione 1:n.

Lo schema relazionale sarà: Imp(<u>matr</u>, nome ha\_capo*)
Nella relazione **Imp**, l’attributo *ha\_capo* è una chiave secondaria che ammette come valori le matricole degli impiegati.
Quindi, una chiave secondaria definita sulla chiave primaria della stessa relazione di appartenenza.

## Chiavi esterne (o composte)

Facciamo l'esempio dell'entità **Squadra** e **Partita**:

![[composte1.PNG]]

Il suo schema relazionale sarà:
- **Squadra(<u>nome**, città)</u>
- **Partita(<u>casa***, \underline{ospite*</u>, data)}
La chiave primaria di Partita è la coppia di nomi delle due squadre.
*casa* e *ospite* sono anche chiavi secondarie che consentono di rappresentare le due associazioni 1:n.

![[composte2.PNG]]

Facciamo un altro esempio:

![[composte3.PNG]]

Il suo schema relazionale sarà:
- **Forn(<u>codF**, città, nome)</u>
- **Dip(<u>codD**, nome)</u>
- **Prod(<u>codP**, nome)</u>
- **Fornitura(<u>codF***, \underline{codP*</u>, <u>codD*</u>, quant, data)}

## Esempio di traduzione

![[esempioTraduzione.PNG]]

## Quadro riassuntivo

![[quadro1.PNG]]

![[quadro2.PNG]]

## Eliminazione delle generalizzazioni

Prima di tradurre uno schema ER in uno schema relazionale, è necessario eliminare eventuali generalizzazioni, riconducendole ai costrutti di base del modello ER. Solo successivamente si procedere alla traduzione seguendo le regole sopra esposte.

![[eliminazione1.PNG]]

1. *accorpamento delle figlie nel genitore:* scompaiono le entità figlie le cui istanze vengono immerse nel genitore e l’associazione con **Materia** diventa opzionale. 

![[eliminazione2.PNG]]

1. *accorpamento del genitore nelle figlie:* possibile solo se la generalizzazione è totale 

![[eliminazione3.PNG]]

1. *sostituzione generalizzazione con associazioni:* rimangono tutte le entità dello schema iniziale 

![[eliminazione4.PNG]]