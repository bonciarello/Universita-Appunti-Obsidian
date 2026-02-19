---
aliases: [entropia]
tags: [machine-learning]
---
## Entropia nella teoria dell'informazione
L'**entropia** è un concetto utilizzato come misura del disordine. Ha origine dalla termodinamica (*Boltzmann*) e più recentemente utilizzato nella teoria dell'informazione (*Shannon*). Ovviamente, trova applicazione anche nel data mining.

L'informazione associata a un evento *E* è **proporzionale alla sorpresa** generata da *E*, che è inversamente proporzionale alla sua probabilità: quando si osserva un esito improbabile di un evento (*variabile casuale*), lo associamo a un'elevata quantità di informazioni.
Al contrario, quando si osserva un risultato più probabile, lo associamo a una minore quantità di informazioni.

Una **variabile casuale** $X = \{x_1, ..., x_n\}$ è una variabile che può assumere un insieme di possibili valori diversi, ciascuno con una probabilità associata $p(x_i)$ con cui indichiamo la probabilità che si verifichi il risultato $x_i$: minore è $p(x_i)$, maggiore è la sorpresa e, quindi, l'informazione, associata all'occorrenza di $x_i$.

L'***entropia* (informativa)** del risultato $x_i$ è definita come:

> $S(x_i) = log_2 \frac{1}{p(x_i)} = - log_2 p(x_i)$

Per un evento *x*, *S(x) = -log p(x)*:
- se *p(x)=1* allora *S(x)=0-x* è un certo evento;
- se *p(x)=0*, allora *S(x)=infinito–x* è un evento impossibile che fornisce una quantità infinita di informazioni;
- se *p(x)=0.5*, allora *S(x)=1*.

L'entropia attesa di $X = \{x_1, ..., x_n\}$ (cioè l'informazione media associata ai valori di *X*) è:

> $S(X) = \sum p(x_i) S(x_i) = \sum -p(x_i)*\log{p(x_i)}$

Quando i risultati hanno tutti la stessa probabilità $p(x) = \frac{1}{n}$ (ovvero *X* è una variabile completamente casuale):

> $S(X) = \sum -p(x_i)*\log{p(x_i)} = \log_{2}{n}$

Si può dimostrare che $S(X) \leq \log_{2}{n}$, cioè l'entropia di *X* è massima quando *X* ha una distribuzione di probabilità uniforme.

Riassumendo:
- l'entropia *S(X)* di *X* è massima quando *X* ha una distribuzione uniforme: 
    > $S(X) \leq log_{2}{n}$
- maggiore è la casualità di *X*, maggiore è la sua **imprevedibilità**, maggiore è la sua entropia;
- possiamo prendere l'entropia come misura della casualità o imprevedibilità di una variabile casuale.

### Esempio partita tra Juventus e Cosenza
Consideriamo la variabile aleatoria $X = {juv, cos, draw}$ i cui valori sono i possibili esiti di una partita di calcio tra **Juventus** e **Cosenza**:
- *juv:* vince la Juventus, con probabilità *p(juv)=0.85*;
- *cos:* vince il Cosenza, con probabilità *p(cos)=0.05*;
- *draw:* il risultato è un pareggio, con probabilità *p(draw)=0.1*.

Le entropie informative sono:
- *S(juv) = -log_2 p(juv) = 0.23*;
- *S(cos) = -log_2 p(cos) = 4.32*;
- *S(draw) = -log_2 p(draw) = 3.32*.

### Esempio corretto lancio dei dadi
L'entropia *S(X)* di un corretto lancio di dadi è il seguente:

> $S(X) = 6*(-\frac{1}{6}\log{\frac{1}{6}}) = \log_{2}{6} = 2,58$

Assumere la seguente distribuzione per un dado scorretto:
- $p(X=1) = \frac{1}{10}$;
- $p(X=2) = \frac{1}{4}$;
- $p(X=3) = \frac{1}{3}$;
- $p(X=4) = \frac{1}{20}$;
- $p(X=5) = \frac{1}{6}$;
- $p(X=6) = \frac{1}{10}$;

Quindi:

> $S(X) = \sum -p(x_i)*\log{p(x_i)} = 2,34$

Ancora una volta, per un dado ingiusto l'entropia è *inferiore*.

## Entropia e compressione del testo

Shannon (1949) ebbe l'intuizione che più un'informazione è prevedibile, cioè più bassa è la sua entropia, meno spazio (bit) è necessario per codificarla.

In particolare, per codificare in modo ottimale un simbolo *x*, utilizzare un numero di bit *n(x)* pari all'entropia informativa *S(x)*:

> $n(x)=S(x)=-\log_{2}{p(x)}$

ovvero, meno bit per simboli più probabili; ciò porta a ridurre al minimo la lunghezza prevista del messaggio.

## Entropia nel data mining
L'entropia è usata come misura del grado di disordine (*impurità*) di un set di dati:
- se tutti gli esempi sono nella stessa classe, l'entropia è zero (certe informazioni);
- se gli esempi sono distribuiti uniformemente tra le classi, l'entropia è massima (massima incertezza).

Considera un set di dati *D* e sia *X* una variabile casuale i cui valori sono le etichette di classe di *D*, ovvero $X = {c_1,...,c_n}$.
L'entropia di *D* è definita come:

> $S(D) = S(X) = \sum -p(c_i)*\log{p(c_i)}$

dove $p(c_i)$ è la frazione di *D* con etichetta $c_i$.

### Esempio dataset binario
Il dataset binario *D* è composto da 14 esempi, 9 positivi e 5 negativi che possiamo scrivere come $D=[9+,5-]$ quindi:

> $S(D) = - p_+ \log_{+}{p_+} - p_- \log_{-}{p_-} = -\frac{9}{14} \log{\frac{9}{14}} - \frac{5}{14} \log{\frac{5}{14}} = 0,94$

Se $D=[7+,7-]$:

> $S(D) = -\frac{1}{2} \log{\frac{1}{2}} - \frac{1}{2} \log{\frac{1}{2}} = 1$

Se $D=[14+,0-]$:

> $S(D) = -1 \log{1} - 0 \log{0} = 0$
