---
aliases: [Classe]
tags: [machine-learning]
---
La **classificazione del testo (TC)** ha lo scopo di assegnare dei documenti a diverse classi tematiche (i.e. economia, sport, scienza).

È un compito difficile perché il linguaggio naturale è ricco e complesso. Infatti, sono ambigui a causa di
- **sinonimia:** due fonemi diversi, lo stesso significato (ad esempio, numero e cifra);
- **polisemia:** un unico fonema, diversi significati (ad esempio, azione può essere sport o economia).

Le fasi del processo della classificazione del testo sono:
- **rappresentazione del documento;**
- **pre-elaborazione del documento;**
- **classificazione;**
- **apprendimento del modello.**    

## Rappresentazione del documento
Abbiamo vari tipi di rappresentazione: 
- **rappresentazione bag of words:** un documento viene considerato come un sacchetto di parole, indipendentemente dall’ordine delle parole e della grammatica (ma tuttavia mantenendo la molteplicità);
- **rappresentazione binaria:** 0 o 1, per indicare l’assenza o la presenza di una parola nel documento. I documenti sono visti come istanze, mentre le parole sono [[Viste|viste]] come attributi;
- **rappresentazione di frequenza:** numero di volte in cui una parola (o N-gram) appare in un documento. Anche in questa rappresentazione, i documenti sono visti come istanze, mentre le parole sono [[Viste|viste]] come attributi.

A differenza della classificazione dei dati, la classificazione del testo è:
- **Multi-Label**, un documento può appartenere infatti a diverse categorie;
- **High Dimensional**, abbiamo a che fare con migliaia di attributi.

## Pre-elaborazione del documento
*Quali parole dovrebbero essere selezionate come caratteristiche rappresentative?* Le fasi principali della pre-elaborazione sono: 
1. **_N-gram_ extraction:** l’_N-gram_ è una sequenza di n parole consecutive (ad esempio, disturbo ossessivo compulsivo). Vengono estratti gli _N-gram_ perché sono molto più significativi delle singole parole;
2. **stop-words removal:** in questo step vengono ignorate le parole comuni come: ‘il’, ‘un’, ‘a’, ‘che’, ecc;
3. **Lemmatizzazione:** è la riduzione alla forma base di una parola (*Lemma*).
4. **Features Selection (FS):** vengono selezionati gli attributi con il più alto potere discriminante, quelli che appaiono solo nei documenti di una particolare classe (bassa [[Entropia|entropia]]). È benefica in quanto riduce il rumore e riducendo la dimension incrementa l’efficienza.

## Modello di induzione
Si usano sia classificatori tradizionali come Naive Bayes oppure classificatori specifici per il testo

## Conclusione
Riassumendo, abbiamo le seguenti fasi: 
1. **pre-elaborazione:** ogni documento è rappresentato nei termini delle sue parole più rappresentative. L'insieme di tutte le parole rimanenti dopo la pre-elaborazione è chiamato *vocabolario*;
2. **apprendimento:** per ogni classe $c$ stimare le probabilità a priori: 
    - $p(c)$ è una frazione di documenti con etichetta di classe $c$;
    - $p(w|c)$, per ogni parola $w$ che ricorre nei documenti di $c$ (usa *m*-stima).
3. **classificazione:** restituisce $c_{NB} = argmax_{c \in C}p(w_1|c_j)...p(w_n|c)$
