---
aliases: [SRS, "Strutture dati, ricorsione stringhe"]
tags: [fondamenti-di-informatica]
---
## Array (Vettori)
Un array è una struttura dati complessa, statica e omogenea. È una sequenza di elementi dello stesso tipo immagazzinata in locazioni di memoria consecutive. Il primo elemento ha indice **0**, l'ultimo ha indice **N-1** (dove N è la dimensione) .

**Dichiarazione:**
- Senza inizializzare: `<tipo> <nome>[<dimensione>];` (es. `int V[5];`) .
- Con inizializzazione: `<tipo> <nome>[] = {el1, el2, ...};` (es. `int V[] = {2, 4, 6, 8, 10};`) .

**Accesso:**
- Si usa l'operatore parentesi quadre: `<nome>[indice]` (es. `V[3] = 12;`) .
## Matrici
Una matrice è un array bidimensionale (array di array).

**Dichiarazione:**
- `int M[4][3];` (4 righe, 3 colonne) .
- Inizializzazione: `int M[2][3] = {{1, 2, 3}, {4, 5, 6}};` .

**Accesso e Scorrimento:**
Per scorrere una matrice si usano due cicli annidati (solitamente `for`):
- Il ciclo esterno scorre le righe (indice `i`).
- Il ciclo interno scorre le colonne (indice `j`).
- Accesso: `M[i][j]` .

**Esempi di Algoritmi su Matrici:**
- **Diagonale Principale:** Somma elementi dove `i == j` (`M[k][k]`) .
- **Diagonale Secondaria:** Somma elementi dove la somma degli indici è pari alla dimensione - 1 (`M[k][N-(k+1)]`) .
## Ricorsione
Tecnica di programmazione in cui un problema viene suddiviso in sotto-problemi simili all'originale ma più semplici, fino ad arrivare ai **Casi Base** (risolvibili immediatamente) .

**Funzione Ricorsiva:** Una funzione che richiama se stessa (direttamente o indirettamente) .

**Esempi Classici:**
1. **Fattoriale ($N!$):**
    - Definizione: $N! = N \times (N-1)!$
    - Caso base: $0! = 1$
    - Codice:
        ```c++
        int fatt(int n) {
            if (n <= 1) return 1;       // Caso Base
            else return n * fatt(n-1);  // Chiamata Ricorsiva
        }
        ```    
2. **Fibonacci:**
    - Ogni numero è la somma dei due precedenti: $0, 1, 1, 2, 3, 5, 8...$
    - $Fib(n) = Fib(n-1) + Fib(n-2)$
    - Casi base: $Fib(0)=0, Fib(1)=1$ .

**Iterazione vs Ricorsione:**
- **Iterazione:** Ciclo esplicito, termina quando la condizione fallisce. Più performante.
- **Ricorsione:** Chiamate ripetute, termina col caso base. Codice più pulito/elegante ma rischio _Stack Overflow_ se troppe chiamate .
## Stringhe (C-Style)
In C++, le stringhe "vecchio stile" (ereditate dal C) sono array di caratteri terminati dal carattere speciale nullo `\0` .

**Dichiarazione:**
- `char str[7] = "ABCDEF";` (Serve dimensione N+1 per il terminatore `\0`) .
- `char str[] = "testo";` (Dimensione calcolata automaticamente) .

**Input/Output:**
- `cout << str;` stampa tutta la stringa .
- `cin >> str;` legge fino al primo spazio bianco (non legge intere frasi!) .
- **`cin.getline(array, dim, delimitatore);`**: Legge intere righe inclusi gli spazi.
    - Es. `cin.getline(frase, 80, '\n');` .
### Libreria `<cstring>`
Contiene funzioni utili per manipolare stringhe C-style :
- `strlen(s)`: Restituisce la lunghezza (escluso `\0`) .
- `strcpy(dest, source)`: Copia `source` in `dest` .
- `strcat(dest, source)`: Concatena (appende) `source` alla fine di `dest` .
- `strcmp(s1, s2)`: Confronta due stringhe.
    - Restituisce `0` se uguali.
    - `< 0` se s1 precede s2 alfabeticamente.
    - `> 0` se s1 segue s2 .
- **Tokenizzazione (`strtok`):** Spezza una stringa in "token" in base a dei delimitatori. La prima chiamata prende la stringa, le successive passano `NULL` per continuare sulla stessa stringa.