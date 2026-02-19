---
aliases: [Expectation Massimization, EM]
tags: [machine-learning]
---
Abbiamo un insieme di dati da *raggruppare in dei cluster secondo la loro similarità*, distribuiti secondo un modello probabilistico. Non si conoscono i parametri, l'obiettivo è proprio quello di stimarli secondo il **Maximum Likelihood Principle**.

## Algoritmo EM
I passi sono i seguenti:
1. inizialmente ha un'ipotesi **TETA randomica**: $\mu$ e $\sigma$ sono i parametri di una distribuzione Gaussiana, in cui $\mu$ indica il valore atteso e $\sigma$ la deviazione standard;
2. calcoliamo le expectation, ovvero la probabilità (**Likelihood**) che il data point è stato generato dalle varie ipotesi (come la probabilità di $R_i(RED)$ dato $X_i$), e in seguito calcoliamo la media;
3. dato che conosciamo la nuova probabilità di $R$ e di $B$, eseguimo la **Maximization**, ovvero aggiustiamo i parametri delle ipotesi, ricalcolando $\mu$ e $\sigma$.

**L'algoritmo continua fino a quando non convergono le due curve**, i parametri delle ipotesi non cambieranno più. L'output che restituisce è l'ipotesi **Maximum Likelihood Principle**.

La particolarità di EM è quello di assegnare una probabilità per ogni cluster, chiamato **Fuzzy Cluster** dove, per ogni istanza, abbiamo un grado di appartenenza ad ogni cluster (una probabilità).

## EM vs K-means

In EM abbiamo la scelta randomica iniziale dell’insieme dei TETA, mentre in *K-means* scegliamo in maniera randomica i centroidi.
EM è definito **soft clustering** perché ha un grado di incertezza nel definire un data point, mentre *K-means* è **hard clustering** perché l’assegnazione al cluster è netta.
