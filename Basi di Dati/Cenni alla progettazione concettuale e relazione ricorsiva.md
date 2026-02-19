---
aliases: [CPCRR, Cenni progettazione concettuale relazione ricorsiva]
tags: [basi-di-dati]
---
## Primo esempio di [[Progettazione concettuale|progettazione concettuale]]
Facciamo un esempio di [[Progettazione concettuale|progettazione concettuale]], una sorta di esercizio:
- una azienda è organizzata in dipartimenti (Dip);
- ogni Dip ha un codice (chiave primaria) e un nome;
- ad ogni Dip afferiscono più impiegati (Imp), mentre un Imp afferisce ad un unico Dip;
- ogni Imp ha un codice (chiave primaria), un nome e una età;
- un Imp può essere direttore di un Dip;
- ogni Imp partecipa ad uno o più Progetti, ad ognuno dei quali partecipano più Imp;
- ogni Dip è ubicato in una Città, di cui si riportano la regione di appartenenza e la popolazione.
Possiamo rappresentare questo esempio come segue:

![[primoProgConcettuale.PNG]]

## Relazione ricorsiva

Sono relazioni tra una entità E e se stessa e consentono di creare legami tra le istanze di E: le istanze di una relazione ricorsiva R sono coppie di istanze di E.

R $ \subseteq $ E x E

![[relazioneRicorsiva.PNG]]

### Esempio di relazione ricorsiva (successione)

![[ricorsivaES1.PNG]]

- *Successione:* relazione che associa ad ogni sovrano
    - al più un sovrano quale suo successore
    - al più un sovrano sovrano quale suo predecessore
- **Ruoli:** *predecessore* e *successore* sono i ruoli svolti da Sovrano nella relazione
- *Sovrano* = \{a, b, c\}

![[descrizioneES1.PNG]]

### Esempio di relazione ricorsiva (composizione)

![[ricorsivaES2.PNG]]

- *Composizione:* relazione che associa ad ogni prodotto
    - 0 o n prodotti che sono suoi componenti
    - 0 o n prodotti di cui è componente
- **Ruoli:**  *è\_comp* e *ha\_comp* sono i ruoli svolti da Prodotto nella relazione
- *Prodotto* = \{a, b, c, d, e\}

![[descrizioneES2.PNG]]