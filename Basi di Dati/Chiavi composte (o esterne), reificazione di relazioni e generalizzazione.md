---
aliases: ["Chiavi composte , reificazione di relazioni e generalizzazione", o esterne]
tags: [basi-di-dati]
---
## Chiavi composte (o esterne)

Ogni entità E ha una chiave primaria
Questa può essere:
- **interna:** è formata solo da attributi di E;
- **composta (o esterna):** comprende attributi di altre entità.

### Esempio

![[chiaveComposta.png]]

La matricola di uno studente non è chiave primaria in quanto studenti di università diverse possono avere la stessa matricola. Tuttavia, la combinazione (*Matr + Nome*) è chiave primaria, dove *Nome* è chiave primaria di Università. Studente è detta entità *debole*.

La combinazione (*Matr, Nome*), dove *Nome* è la chiave primaria di Università, è una chiave composta per *Stud*: essa consente di identificare univocamente ogni istanza di *Stud*.

**NOTA:** la relazione **Iscrizione** è di tipo 1:n e *Stud* sta dalla parte 1 con partecipazione obbligatoria. Questa è una condizione necessaria per poter definire una chiave composta.

Possibili istanze di *Stud* sono:
- $<$252, Unical, Perri$>$ lo studente dell’Unical con matr 252 si chiama Perri
- $<$363, Bocconi, Tucci$>$

## Reificazione di relazioni

Si consideri ancora lo schema:

![[reificazione1.png]]

Possibili istanze della relazione *Esame* sono:

{$<$s1,c1,d1,v1$>$, $<$s2,c1,d2,v2$>$, …, $<$s3,c2,d3,v2$>$}
Ricordiamo che non sono ammessi esami duplicati, ad esempio, entrambe le istanze $<$s1,c1,d1,v1$>$ e $<$s1,c1,d2,v2$>$ non possono coesistere.

Il precedente schema può essere trasformato nel seguente schema equivalente attraverso la reificazione della relazione *Esame*:

![[reificazione2.png]]

In questo schema la relazione n:m *Esame* è stata sostituita dalla entità *Esame*, e da due relazioni 1:n.
Si noti che l’entità *Esame* ha come chiave primaria la chiave composta (*Matr, Cod*), per cui ogni istanza di *Esame* è univocamente individuata da una coppia *<studente, corso>*.

Possibili istanze della entità *Esame* sono (in rosso i valori della chiave):

\{$<$s1,c1,d1,v1$>$, $<$s2,c1,d2,v1$>$, …, $<$s3,c2,d3,v2$>$\}
Anche in questo caso non sono quindi ammessi esami duplicati, ad esempio:

$<$s1,c1,d1,v1$>$ e $<$s1,c1,d2,v2$>$
L’entità *Esame* e la relazione *Esame* ammettono le stesse istanze.

![[reificazione3.png]]

Aggiungendo l’attributo **data** alla chiave esterna, è possibile rappresentare il fatto che uno studente ha sostenuto più volte l’esame dello stesso corso in date diverse: infatti la chiave primaria è *$<$Matr, Cod, Data$>$*.
Possibili istanze di *Esame* sono:

\{$<$s1,c1,d1,v1$>$, $<$s1,c1,d2,v2$>$, $<$s2,c1,d1,v2$>$, …, $<$s3,c2,d3,v2$>$\}

Un modo alternativo per ottenere un effetto simile è quello di aggiungere un codice univoco nell’entità *Esame*, senza chiave composta.

![[reificazione4.png]]

L’informazione è distribuita così (in rosso valori della chiave primaria):
- **istanze di Esame**: \{$<$e1,d1,v1$>$, $<$e2,d2,v2$>$, $<$e3,v2,d1$>$, ...\}
- **istanze di S-E**: \{$<$s1,e1$>$, $<$s1,e2$>$, $<$s2,e3$>$, ...\}
- **istanze di E-C**: \{$<$e1,c1$>$, $<$e2,c1$>$, $<$e3,c2$>$, ...\}
Lo studente *s1* ha sostenuto l’esame *e1* relativo al corso *c1* nelle date *d1* e *d2* con voti *v1* e *v2*, rispettivamente, ecc.

**NOTA:** lo stesso esame può essere sostenuto più volte, anche nella stessa data.

## Generalizzazione

È un legame logico tra:
- una entità E, detta **genitore**;
- un insieme di entità E1, …, En, dette **figlie**.
L’entità E è una generalizzazione di E1, …, En, che sono specializzazioni di E, in quanto ogni istanza di Ei è una istanza di E, cioè:

Ei $ \subseteq $ E, per i = 1,n

![[generalizzazione1.PNG]]

La generalizzazione è un costrutto derivato!

### Esempio (num. 1) (Persona, Donna e Uomo)

![[generalizzazione2.PNG]]

*Persona* è una generalizzazione di *Uomo* e *Donna*.
Infatti ogni *Uomo* è una *Persona*, ed ogni *Donna* è una *Persona*.

Uomo $ \subseteq $ Pers, Donna $ \subseteq $ Pers
Siccome *Uomo* $ \cup $ *Donna* = *Pers*, tale generalizzazione è detta **totale**.
Inoltre, siccome ogni persona o è un uomo o è una donna (**OR esclusivo**), cioè, *Uomo* $ \cap $ *Donna* = $ \Phi $, la generalizzazione è detta **disgiunta**.

### Esempio (num. 2) (Persona, Avvocato e Professore)

![[generalizzazione3.PNG]]

Una generalizzazione non totale è detta **parziale**.
Ad esempio, la seguente generalizzazione è parziale, in quanto ci sono persone che non sono nè avvocati nè professori. Possiamo riassumere ciò con:

Avv $ \cup $ Prof $ \subset $ Pers
Inoltre, è **sovrapposta** (**non disgiunta**) in quanto ci sono avvocati che sono anche professori, Avv $ \cap $ Prof $ \neq \Phi $

## Generalizzazione a più livelli

Una generalizzazione può essere a più livelli.

![[generalizzazionePiuLivelli.png]]

Per esempio, questo modello ER in figura rappresenta una generalizzazione a due livelli.

Per quanto riguarda gli attributi delle varie entità generalizzate, gli attributi si propagano dall'alto verso il basso. Nel nostro caso:
- *tutte le entità hanno un CF* (persone, avvocati, professori, ecc.)
- *tutti i prof hanno uno stipendio* (prof di scuola media, prof universitario)

### Esempio

- Un professionista è un avvocato, un ingegnere o un informatico
- Un avvocato è un civilista o un penalista
- Un ingegnere è civile o industriale, e un ingegnere industriale è meccanico o chimico
- Un informatico può essere un programmatore
- Un programmatore conosce un certo numero di linguaggi di programmazione
- Ad ogni professionista è associata la città in cui esercita la professione

![[esempioGeneralizzazione.PNG]]