## Rappresentazione dell'Informazione
L'idea fondamentale alla base della rappresentazione dell'informazione è il **BIT** (Binary Digit) .

Il BIT costituisce l'unità minimale di rappresentazione in quanto può rappresentare uno di due valori: 0 oppure 1 .

Potendo assumere solo due valori, un BIT può essere associato alla presenza/assenza di corrente elettrica.

Possiamo combinare più Bit allo scopo di rappresentare informazioni più complesse . Ad esempio, per rappresentare le stagioni sono sufficienti 2 BIT:

|**Bit 1**|**Bit 2**|**Stagione**|
|---|---|---|
|0|0|PRIMAVERA|
|0|1|ESTATE|
|1|0|AUTUNNO|
|1|1|INVERNO|
In generale, $N$ BIT possono assumere $2^N$ combinazioni diverse, ognuna delle quali può essere associata a un valore che una specifica informazione può assumere .

Particolarmente importanti sono le sequenze di 8 BIT, comunemente chiamate **BYTE**, con cui possiamo rappresentare informazioni con dominio di al più $2^8 = 256$ elementi .

Generalmente, informazioni più complesse vengono rappresentate con successioni di BYTE .

I multipli del BYTE prendono nomi specifici:
- $1 \text{ KB (KiloByte)} = 2^{10} \times 1 \text{ Byte} = 1024 \times 1 \text{ Byte}$
- $1 \text{ MB (MegaByte)} = 2^{10} \times 1 \text{ KB} = 1024 \times 1 \text{ KB}$
- $1 \text{ GB (GigaByte)} = 2^{10} \times 1 \text{ MB} = 1024 \times 1 \text{ MB}$
- $1 \text{ TB (TeraByte)} = 2^{10} \times 1 \text{ GB} = 1024 \times 1 \text{ GB}$
## Sistemi di Numerazione
### Sistema Posizionale
È un sistema di numerazione che usa simboli (cifre) usati per scrivere i numeri e assumono valori diversi a seconda della posizione che occupano nella notazione .

Questa notazione si basa su due fattori:
1. **Alfabeto ($\alpha$):** l'insieme delle cifre disponibili per la rappresentazione.
2. **Base ($b$):** il numero degli elementi dell'alfabeto.
    - $\alpha = \{0, 1, 2, ..., p-1\}$ dove $b=p$ .
### Sistema Binario
È un sistema numerico posizionale in base 2. Utilizza due simboli, 0 e 1 .
- $\alpha = \{0, 1\}$ ; $b=2$
- Esempio: $11_2 = 1 \cdot 2^1 + 1 \cdot 2^0 = 2 + 1 = 3_{10}$
- Esempio: $1001_2 = 1 \cdot 2^3 + 0 \cdot 2^2 + 0 \cdot 2^1 + 1 \cdot 2^0 = 8 + 0 + 0 + 1 = 9_{10}$
### Codice Ottale
Sistema numerico posizionale in base 8.
- $\alpha = \{0, 1, 2, 3, 4, 5, 6, 7\}$ ; $b=8$ .
### Codice Esadecimale
Sistema numerico in base 16. Utilizza 16 simboli: da 0 a 9 per le prime dieci cifre e poi le lettere da A a F per le successive sei cifre.
- $\alpha = \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A, B, C, D, E, F\}$ ; $b=16$ .
## Conversioni di Base
### Da Decimale a Binario
Bisogna dividere il numero per 2 finché non diventa 0. Ad ogni passaggio bisogna conservare i resti ed infine scriverli al contrario .
- **Esempio:** Convertire $12_{10}$ in binario.
    - $12/2 = 6$ (R: 0)
    - $6/2 = 3$ (R: 0)
    - $3/2 = 1$ (R: 1)
    - $1/2 = 0$ (R: 1)
    - Risultato: $1100_2$ .
### Da Decimale a Esadecimale
Bisogna dividere il numero per 16 finché non diventa 0. Conservare i resti e scriverli al contrario .
### Da Binario a Ottale
Si ottiene raggruppando il numero binario in gruppi di tre cifre (bit) a partire dal punto in entrambe le direzioni. I gruppi estremi vanno completati con zeri .
- **Esempio:** $101100_2$ in Ottale.
    - $101 | 100$
    - $101_2 = 5_8$
    - $100_2 = 4_8$
    - Risultato: $54_8$ .
### Da Binario a Esadecimale
Si raggruppa in gruppi di quattro cifre (bit) .
- **Esempio:** $101100_2$ in Esadecimale.
    - $0010 | 1100$ (aggiunti zeri a sinistra)
    - $0010_2 = 2_{16}$
    - $1100_2 = C_{16}$
    - Risultato: $2C_{16}$ .
### Da Binario a Decimale
Bisogna moltiplicare per due elevato alla posizione ogni cifra del numero (partendo da destra) e sommarle .
- Esempio $110_2$: $1 \cdot 2^2 + 1 \cdot 2^1 + 0 \cdot 2^0 = 4 + 2 = 6$ .
## Aritmetica Binaria
### Addizione
Somma dei due bit con riporto di 1 quando la somma è superiore a 1 .
- $0+0=0$
- $0+1=1$
- $1+0=1$
- $1+1=0$ con riporto di 1 .
### Sottrazione
Differenza con eventuale prestito di 1 .
- $0-0=0$
- $0-1=1$ con prestito di 1
- $1-0=1$
- $1-1=0$ .
### Modulo e Segno
Rappresentazione dei numeri relativi. Il bit più a sinistra rappresenta il segno:
- $0 = "+"$
- $1 = "-"$
    Con $N$ bit si rappresentano i valori da $-(2^{n-1}-1)$ a $2^{n-1}-1$ .
## Rappresentazione in Complemento
### Complemento alla Base
Dato un numero $X$ in base $b$ di $n$ cifre, è definito come: $b^n - X$ .
- Esempio (Decimale): Complemento a 10 di 64 ($n=2, b=10$) è $10^2 - 64 = 36$ .
- Esempio (Binario - Complemento a 2):
    - $X = 01011$
    - $2^5 - X = 100000 - 01011 = 10101$ .
    - Metodo rapido: Invertire tutti i bit e aggiungere 1 (oppure partire da destra, lasciare invariati i bit fino al primo 1 incluso, e invertire i successivi).
### Complemento alla Base - 1 (Complemento a 1)
Definito come $(2^n - 1) - X$ .
- Equivale a invertire tutti i bit (0 diventa 1, 1 diventa 0) .
### Range Complemento a 2
Un numero binario di $n$ cifre in complemento a 2 rappresenta i numeri compresi fra:
$-(2^{n-1})$ e $2^{n-1}-1$ .

**Esempi:**
- $15_{10}$ ($n=5$) $\rightarrow 01111_2$ .
- $-16_{10}$ ($n=5$) $\rightarrow 10000_2$ (Nota: -16 è il minimo rappresentabile) .
- $-34_{10}$ ($n=7$):
    - $34_{10} = 0100010$
    - Inversione: $1011101$
    - Aggiungo 1: $1011110$ .
## Codici di Caratteri
### Codice ASCII
Codice a 7 bit che rappresenta 128 caratteri. Mancano caratteri accentati e greci .
### Unicode (UTF-8)
Sistema di codifica che assegna un numero univoco ad ogni carattere (include caratteri internazionali) .
## Calcolo Proposizionale
Una **Proposizione** è una affermazione a cui è possibile ottenere un valore di verità .
- **Proposizione Semplice:** contiene soltanto un predicato .
- **Proposizione Composta:** unione di due o più proposizioni tramite connettivi logici .
### Connettivi Logici
1. **NOT (!):** Inverte il valore di verità.
    - $V \rightarrow F$, $F \rightarrow V$ .
2. **AND (&&):** Assume valore positivo solo se tutte e due le proposizioni sono vere .
    - V-V = V; V-F = F; F-V = F; F-F = F .
3. **OR (||):** Assume valore positivo se è presente almeno una proposizione vera .
    - V-V = V; V-F = V; F-V = V; F-F = F .
**Vuoi che proceda con la "Parte 2" riguardante Algoritmi e Introduzione al codice C++?**