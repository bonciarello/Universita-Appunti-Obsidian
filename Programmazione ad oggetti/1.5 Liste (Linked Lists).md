---
aliases: [Liste, Linked Lists]
tags: [programmazione-ad-oggetti]
---
### Lista Concatenata
Sequenza di elementi dove ogni **nodo** contiene il dato e un puntatore al nodo successivo .
- Vantaggio: Inserimento/Rimozione veloci.
- Svantaggio: Accesso lento (bisogna scorrere i nodi per arrivare all'i-esimo).
### Lista Doppiamente Concatenata (`std::list`)
Ogni nodo ha un puntatore al successivo (`next`) e uno al precedente (`prev`).
Per scorrere una lista non si usano gli indici, ma gli **iteratori**.
```c++
for(auto it = l.begin(); it != l.end(); ++it) {
    cout << *it << endl; // Accesso tramite dereferenziazione
}
```

Se la lista contiene puntatori a oggetti (`list<Prodotto*>`), l'accesso ai metodi avviene con `(*it)->metodo()`.