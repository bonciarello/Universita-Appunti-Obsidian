## Puntatori
Le variabili puntatore contengono **indirizzi di memoria** di altre variabili. Mentre le variabili normali contengono un valore diretto (Riferimento Diretto), i puntatori contengono l'indirizzo di una variabile che ha uno specifico valore (Riferimento Indiretto) .

**Dichiarazione:**
Si usa il simbolo `*` tra il tipo e il nome.
- `int *ptr;` (Puntatore a un intero).
- Attenzione: in dichiarazioni multiple l'asterisco va ripetuto: `int *ptr1, *ptr2;`.

**Operatori Fondamentali:**
1. **Indirizzo (`&`):** Restituisce l'indirizzo di memoria della variabile.
    - Es. `ptr = &x;` (Assegna a `ptr` l'indirizzo di `x`).
2. **Dereferenziazione (`*`):** Restituisce il valore dell'oggetto puntato (accede al contenuto all'indirizzo).
    - Es. `cout << *ptr;` (Stampa il valore della variabile puntata da `ptr`).
    - Nota: `*` e `&` sono operatori inversi: `*&y == y`.

**Puntatori Nulli:** Possono essere inizializzati a `0`, `NULL` o `nullptr` (C++11). Dereferenziare un puntatore nullo causa un errore di **Segmentation Fault** (crash del programma).
**Puntatori Pendenti (Dangling Pointers):** Un puntatore che punta a un'area di memoria non più valida (es. una variabile locale uscita dallo scope). Dereferenziarlo causa comportamento indefinito.
## Puntatori e Costanti (`const`)
Il qualificatore `const` modifica il comportamento del puntatore:
1. **Puntatore a Costante:** Il valore puntato non può cambiare, ma il puntatore può cambiare indirizzo.
    - `const int *ptr = &x;`
    - `*ptr = 5;` -> **ERRORE**
    - `ptr = &y;` -> **OK**.
        
2. **Puntatore Costante:** L'indirizzo contenuto nel puntatore non può cambiare, ma il valore puntato sì. Deve essere inizializzato subito.
    - `int * const ptr = &x;`
    - `ptr = &y;` -> **ERRORE**
    - `*ptr = 5;` -> **OK**.
        
3. **Puntatore Costante a Costante:** Né l'indirizzo né il valore possono cambiare.
    - `const int * const ptr = &x;`.
## Riferimenti (References)
Un riferimento è un **alias** (un altro nome) per una variabile esistente.
- **Sintassi:** `int &ref = x;`.
- Qualsiasi operazione su `ref` ha effetto su `x`.

**Regole e Differenze con i Puntatori:**
- Deve essere inizializzato alla dichiarazione.
- Non può essere `NULL`.
- Non può essere cambiato per riferirsi a un'altra variabile dopo l'inizializzazione (non si può "reseatare").
## Aritmetica dei Puntatori
Operazioni valide sui puntatori (utili soprattutto con gli array):
1. **Incremento/Decremento (`++`, `--`):** Sposta il puntatore alla locazione di memoria successiva/precedente in base alla dimensione del tipo di dato.
    - Es. Se `int` occupa 4 byte e `ptr` è `3000`, `ptr++` diventa `3004`.    
2. **Somma/Sottrazione Intero (`+`, `-`):** Sposta il puntatore di N posizioni.
3. **Differenza tra Puntatori:** Restituisce il numero di elementi tra due indirizzi.
4. **Relazione Array-Puntatori:** Il nome di un array è un **puntatore costante** al primo elemento. `V[i]` è equivalente a `*(V + i)`.
## Memoria Dinamica (Heap)
La memoria allocata staticamente (es. `int A[10]`) è decisa a tempo di compilazione. Per decidere la quantità di memoria a tempo di esecuzione, si usa lo **Heap** (Free Store).
### Operatore `new`
Alloca memoria nello Heap e restituisce un puntatore.
- Variabile singola: `int *p = new int(5);` (alloca e inizializza a 5).
- Array dinamico: `int *arr = new int[dimensione];`.
### Operatore `delete`
Dealloca la memoria per evitare **Memory Leaks** (perdita di memoria).
- Variabile singola: `delete p;`.
- Array dinamico: `delete[] arr;` (fondamentale le parentesi quadre!).

**Errori Comuni:**
1. **Memory Leak:** Dimenticare `delete` (la memoria rimane occupata fino al termine del programma).
2. **Double Free:** Applicare `delete` due volte sullo stesso puntatore.
3. **Accesso dopo Delete:** Usare un puntatore dopo aver liberato la memoria (il puntatore è _dangling_).
### Matrici Dinamiche
Si realizzano come array di puntatori (puntatore a puntatore).
1. Allocazione array di puntatori (righe): `int **M = new int*[righe];`
2. Allocazione di ogni riga: `M[i] = new int[colonne];` .
3. Deallocazione inversa: prima le righe (`delete[] M[i]`), poi l'array di puntatori (`delete[] M`) .