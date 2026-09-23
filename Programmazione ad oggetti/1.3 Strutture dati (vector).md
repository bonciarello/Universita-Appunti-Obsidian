---
aliases: [vector, Strutture dati]
tags: [programmazione-ad-oggetti]
---
Il documento analizza la struttura dati `Vector`, che rappresenta un array dinamico ridimensionabile.
### Implementazione Interna
Un Vector si basa su 3 elementi:
1. **Array dinamico interno:** Puntatore ai dati (`int *vec`).
2. **Size:** Numero di elementi effettivamente presenti (`sz`).
3. **Capacity:** Dimensione massima allocata in memoria prima di dover ridimensionare (`cap`).
### Metodi Chiave (`VectorInt`)
- **push_back(e):** Inserisce un elemento in coda. Se `size == capacity`, raddoppia la capacità.
- **reserve(n):** Aumenta la capacità allocando un nuovo array più grande, copiando i dati e cancellando il vecchio array.
- **Costruttore di Copia:** Necessario per effettuare la **Deep Copy** (copia profonda) dei dati, altrimenti due oggetti punterebbero alla stessa memoria.
- **Operatore di Assegnamento (`operator=`):** Gestisce la copia tra oggetti già esistenti, evitando memory leak e auto-assegnamenti.