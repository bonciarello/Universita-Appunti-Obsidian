---
aliases: [MDR, Modello dati relazionale]
tags: [basi-di-dati]
---
Il modello relazione è un modello logico dei dati, direttamente supportato dai DBMS commerciali.
Si basa su un unico costrutto chiamato *relazione* (tabella).
Nella progettazione di una BD, lo *schema relazionale* si ottiene per trasformazione dello schema concettuale ER.

Una relazione ha uno **}{schema** e una **estensione**.
Lo *schema* si può spiegare matematicamente con *R(X)*, dove *R* è il nome della relazione e *X = \{A1, ..., An\*} è l'insieme dei valori che può assumere.
Ogni attributo $A_i$ ha associato un dominio $D_i$ che è l'insieme dei valori che può assumere.
L'estensione di *R(A1, ..., An)* è:

$
R \subseteq D1 * ... * Dn
$
Ogni elemento *(n-pla)* di *R* è detto **tupla**.

Per spiegare i prossimi argomenti utilizziamo l'esempio di *Professore* e *Dipartimento*.

**Schemi di relazione:**
- Professore(<u>CodP</u>, Nome, Età, Dip)
- Dipartimento(<u>CodD</u>, Nome, Univ, Dir)

**Estensioni di relazione**(o solo *relazioni*):

![[estensioniRelazioni.png]]

**Schemi BD relazionale:**

\{Prof(<u>CodP</u>, Nome, Età, Dip), Dip(<u>CodD</u>, Nome, Univ, Dir)\}

**Base di dati:**

![[estensioniRelazioni.png]]

## Chiavi primarie e secondarie
Gli attributi *CodP* e *CodD* sono **chiavi primarie** cioè non possono esistere due tuple con gli stessi valori di questi attributi.

Gli attributi *Dip* e *Dir* sono **chiavi secondarie**:
- *Dip* assume valori della chiave primaria *CodD* presenti nella relazione *Dipartimento*;
- *Dir* assume valori della chiave primaria *CodP* presenti nella relazione *Professore*.
Le chiavi secondarie consentono di rappresentare le relazioni *Afferenza* e *Direzione*.

![[primarieSecondarie.PNG]]

Quindi, una **chiave secondaria (o esterna)** è un attributo (o insieme di attributi) che assumono come valori i valori della chiave primaria presenti in qualche relazione e consente di rappresentare associazioni tra relazioni.

## Vincolo di integrità referenziale
Se una chiave secondaria *X* assume un valore *x*, allora *x* deve apparire come valore della chiave primaria cui *X* è riferita.

![[vincoliIntegritaReferenziale1.png]]

Supponiamo che modifichiamo una chiave esterna con una chiave primaria che non esiste:

![[vincoliIntegritaReferenziale2.png]]

## Valori nulli
Può succedere che il valore di un attributo sia **}{sconosciuto**, **inesistente** o **senza informazione**.

Per spiegare i vari tipi di valori nulli, utilizziamo un esempio:

![[valoriNulli1.PNG]]

- Mario non possiede il telefono (valore *inesistente*);
- di Gianni non conosciamo il numero telefonico (valore *sconosciuto*);
- di Chiara non sappiamo se abbia o meno il telefono (valore *senza informazione*).
Una chiave primaria non può assumere valori nulli! Un esempio di seguito:

![[valoriNulli2.PNG]]