Un **grafo** è costituito da una coppia *(N, A)* dove *N* è l'insieme dei nodi e *A* è l'insieme di coppie di nodi.

- **Grafo orientato** è un grafo in cui gli archi sono coppie di nodi ordinati:
    ![[ img/sezione12/grafoOrientato.PNG]]

- **Grafo non orientato** è un grafo in cui gli archi non sono rappresentati da coppie ordinate:
    ![[ img/sezione12/grafoNonOrientato.PNG]]

Il numero di archi possibili in un grafo sono:

- *MAX:* n²
- *MIN:* n(n-1)/2

che equivale come complessità a *circa* O(n²).

- **Grafo denso:** nel caso di grafi con n² archi si parla di grafo denso
- **Grafo sparso:** nel caso di grafi in cui ci sono circa *n* archi (*circa 1* per ogni nodo) si parla di grafo sparso

## Adiacenza (orientati)

Dato un arco *(v, w)* si dice che *w* è adiacente a *v* e che l'arco esce da *v* ed entra in *w*.

## Grado (orientati)

Si possono distinguere due tipi di gradi:

- **grado di entrata:** numero archi entranti da un nodo
- **grado di uscita:** numero archi uscenti da un nodo

## Cammino

Un cammino da *v* a *w* è una sequenza di archi distinti *(v1, v2) (v2, v3) ... (vK-1, vK)* tale che *v = v1* e *w = vK* ed ha lunghezza *K-1*.

## Ciclo

È un cammino attraverso cui, a partire da un nodo *x*, è possibile ritornare su di esso. Un grafo che contiene un ciclo viene definito **ciclico** sennò **aciclico**.

## Grafo non ordinato connesso

Se esiste un cammino che collega ogni coppia di nodi, una componente **connessa** è un insieme massimale di nodi tale che esiste un cammino che collega ogni coppia di nodi di essa.

## Grafo ordinato debolmente connesso e fortemente connesso

Si definisce fortemente connesso se per ogni coppia di nodi esiste un cammino orientato.
Si definisce debolmente connesso se il grafo non orientato ottenuto da esso è connesso.

## Grafi pesati

Una differenza importante tra grafo e albero è che in quest'ultimo è possibile definire un nodo di entrata. Inoltre il contenuto informativo dei grafi non è limitato solamente ai nodi ma anche gli archi.

## Rappresentazione dei grafi

Il problema principale nella rappresentazione dei grafi è che ogni nodo deve essere direttamente accessibile quindi è necessario avere una qualche struttura dati che lo permetta.

Inoltre è possibile creare un'altra struttura dati che conservi il contenuto informativo del nodo indipendente dalla strategia di rappresentazione degli archi.

Le più comuni modalità di rappresentazione di quest'ultimi:

- **Matrice di adiacenza:** più velocità nella lettura dell'esistenza di un arco ma spreco di memoria (se non è denso)
- **Liste di adiacenza:** più velocità nella lettura dei nodi adiacenti ma meno velocità nella lettura dell'esistenza di un arco

## Classi

```cpp
class Grafo {
    protected:
        n nodi, m archi...
        matrice/lista adiacenti
    public:
        Grafo(unsigned n)
        bool operator() (unsigned i, unsigned j) // verifica se l'arco (i, j) esiste
        void operator() (unsigned i, unsigned j, bool b) // se voglio aggiungere l'arco (i, j)
        int n() // numero nodi
}
```

## Esempio di utilizzo

*Scrivere una funzione che, ricevuto un grafo non orientato, restituisca, per ciascun nodo, il suo grado.*

```cpp
Vector<int> numAdiacenti(const Grafo& G) {
    Vector<int> grado(G.n());
    for(int i = 0; i < G.n(); i++) {
        grado[i] = 0;
        for(int j = 0; j < G.n(); j++) {
            if(i != j && G(i, j)) // O(1) oppure O(n)
                grado[i]++;
        }
    }
    return grado;
}
```

La funzione ha O(n²) in caso di matrice di adiacenza sennò O(n³).
Lo stesso algoritmo possiamo risolverlo in funzione dell'arco:

```cpp
Arco e;
grado[i] = 0;
for(e = G.primoAd(i); !e.nullo; e = G.succAd(e)) grado[i]++;
```

Con questo algoritmo possiamo avere una complessità costante di O(n²).
