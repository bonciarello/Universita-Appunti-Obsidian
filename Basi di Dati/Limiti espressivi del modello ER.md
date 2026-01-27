## Concetto generale di progettazione concettuale

Si parte dall'analisi del dominio applicativo, questa analisi si mette nero su bianco, nel linguaggio naturale, che è informale. Per questo, bisogna utilizzare il modello ER. Quando si progetta uno schema concettuale si deve porre una serie di domande, cioè:

- un concetto deve essere modellato come una entità o come un attributo?
- un concetto deve essere modellato come una entità o come una relazione?

Un altro passo è trovare i vincoli di integrità: non tutti i vincoli possono essere rappresentati dai diagrammi ER.

## Entità vs. Attributi

La *città* di una persona, conviene modellarla come un attributo della entità *Pers* o come una entità?
Se della città si è interessati solo al nome, potrebbe bastare usare un **attributo**.
Se della città siamo interessati ad altri attributi, come, regione di appartenenza, popolazione, coordinate geografiche, ecc., allora bisogna introdurre una **entità**.

![[relazioniEntita1.png]]

Come già visto, se nel dominio di interesse gli esami non possono essere ripetuti, allora è possibile usare uno qualsiasi dei due seguenti schemi, in cui il concetto *Esame* viene modellato o come relazione o come entità.

![[relazioniEntita2.png]]

Se invece gli esami possono essere ripetuti, è necessario adottare una delle seguenti soluzioni, in cui il concetto *Esame* è rappresentato come entità.

![[relazioniEntita3.png]]

## Limiti espressivi del modello ER

I vincoli di integrità sono condizioni che devono essere verificate dai dati.
Esempi non rappresentabile nel modello ER in base allo schema qui sotto presente:

- il direttore di un dipartimento deve essere un prof che afferisce allo stesso dipartimento;
- lo stipendio di un direttore non può essere inferiore a quello di un qualsiasi altro prof dello stesso dipartimento.

![[relazioniEntita4.png]]