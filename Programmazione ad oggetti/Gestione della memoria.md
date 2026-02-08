In questa sezione viene illustrato come le variabili vengono allocate in memoria (indirizzi) e come gestirle tramite puntatori.
### Allocazione Statica e Indirizzi
Quando dichiariamo una variabile `int a = 5;`, il sistema scrive il valore `5` in uno specifico indirizzo di memoria (es. `0x3`).
- `cout << a;` stampa il valore (`5`).
- `cout << &a;` stampa l'indirizzo di memoria (`0x3`).

**Array Statici:** Dichiarando `int b[5] = {1, 2, 3, 4, 5};`, i valori vengono memorizzati in indirizzi contigui. Il nome dell'array `b` corrisponde all'indirizzo del primo elemento (`0x1`). Stampando `&b[i]` si ottengono gli indirizzi sequenziali degli elementi (es. `0x1`, `0x2`, `0x3`...).
### Allocazione Dinamica (Heap)
Per allocare memoria dinamicamente si usa l'operatore `new`.
- **Array Dinamico:** `int* b = new int[5];` alloca un blocco di 5 interi.
- **Variabile Singola:** `int* a = new int(1);` crea un puntatore a un singolo intero inizializzato a 1.

> **Nota:** La memoria allocata con `new` deve essere liberata manualmente con `delete[] b` (per array) o `delete a` (per variabili singole) per evitare memory leak.