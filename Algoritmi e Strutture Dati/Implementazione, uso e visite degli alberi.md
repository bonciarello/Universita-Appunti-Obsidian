---
aliases: [Albero]
tags: [algoritmi-e-strutture-dati]
---
```cpp
template <class T>
class AlberoB {
    protected:
        SNodo* pradice;
    public:
        AlberoB():
        AlberoB(const T& value);
        bool nullo() // => radice == null
        bool figlio() // => se entrambi i figli sono nulli
        AlberoB figlio (Direzione d) const
        // ...
}
```

## Metodi utili implementati da noi

- Ci fornisce la profondità di un albero.
```cpp
int profondo(Albero<T> A) {
    if(A.nullo()) return 0;

    int p1 = profondo(A.figlio(DES));
    int p2 = profondo(A.figlio(SIN));

    return 1 + max(p1, p2);
}
```
Questo tipo di risoluzione è del tipo BOTTOM UP, cioè si risolve prima ciò che sta in basso (NODO FOGLIA) e poi ricostruisci.

- Fornisce se l'albero è bilanciato.
```cpp
bool bilanciato(Albero<T> A) {
    if(A.nullo()) return true;

    int p1 = profondo(A.figlio(DES));
    int p2 = profondo(A.figlio(SIN));

    return(abs(p1-p2) <= 1) && bilanciato(A.figlio(DES)) && bilanciato(A.figlio(SIN));
}
```
Poco efficiente, vado a ricontrollare nodi che ho già visto visitato (*circa O(esponenziale)*).
Sviluppiamola in maniera differente.
```cpp
bool bilanciato(Albero<T> A, int& prof) {
    if(A.nullo()) {
        prof = 0;
        return true;
    }

    int profD, profS;
    int s1 = bilanciato(A.figlio(DES), profD);
    int s2 = bilanciato(A.figlio(SIN), profS);

    return s1 && s2 && abs(profD+profS) <= 1;
    // complessita' O(n)
}
```

## Visita di [[Alberi binari|alberi binari]]

Possiamo individuare quattro tipi di algoritmi di visita:

![[ img/sezione9/visitaAlberiBinari.PNG]]

1. ***visita in preordine***, valuto il contenuto della radice e poi visito tutto il sotto-albero sinistro e poi il destro → A L E R B O
2. ***visita simmetrica***, visito tutto l'albero sinistro, valuto la radice e poi tutto il sotto-albero destro → E L R A B O
3. ***visita in postordine***, visito tutto sinistra poi tutta a destra e poi stampo → E R L O B A
4. ***visita per livelli***, visita tutti i nodi livello per livello → A L B E R O

I tre tipi di algoritmo si chiamano *deep first search (DFS)* che visitano in profondità mentre l'ultimo algoritmo si chiama *breadth first search (BFS)* che visitano in ampiezza.

Vediamo la codifica di un algoritmo *deep first search (DFS)*:

```cpp
void visitaDFS(AlberoB A) {
    if(A.nullo()) return;

    A.radice(); // la pos. di questa istruzione determina il tipo di DFS
    visitaDFS(A.figlio(SIN));
    visitaDFS(A.figlio(DES));
}
```

Attraverso le [[Viste|viste]] possiamo risolvere la maggior parte dei problemi degli alberi.

## Albero binario di ricerca

Non sono altro che alberi in cui vige una regola di ripartizione degli elementi. In particolare è detto albero binario se per ogni nodo *v* di esso, tutti i nodi nel sotto-albero sinistro di *v* precedono *v* e tutti i nodi del sotto-albero destro non precedono *v*.

Le operazioni fondamentali sono cercare/inserire/cancellare un oggetto. La ricerca in un albero binario, nel caso essa sia pure bilanciato, ha complessità pari a *O(log₂ n)*.

I vantaggi di usare un albero rispetto ad altre strutture dati sono:
- struttura dinamica → niente spreco di spazi
- velocità d'inserimento → *O(log₂ n)*

Stampa ordinata dei valori contenuti in un albero binario di ricerca avviene tramite vista simmetrica.

È utile avere metodi di manutenzione dell'albero, ad esempio l'auto-bilanciamento. Questo può avvenire creando un vettore temporaneo ordinato e poi andando a dividere gli elementi, inserendo l'elemento centrale all'interno dell'albero.
