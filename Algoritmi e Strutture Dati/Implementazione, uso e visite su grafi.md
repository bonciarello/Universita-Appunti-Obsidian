---
aliases: [Grafo]
tags: [algoritmi-e-strutture-dati]
---
Un problema di base nei [[Grafi|grafi]] è visitare tutti i nodi raggiungibili da un dato nodo *v*, cioè determinare tutti i nodi *w* per cui esiste un cammino da *v* a *w*.

Ci sono due modalità fondamentali:

1. **Visita a ventaglio/ampiezza (BFS):** vengono visitati inizialmente tutti i nodi adiacenti al nodo di partenza e, successivamente, sono visitati gli adiacenti al nodo che è stato visitato per prima (visita per livelli)
2. **Visita a scandaglio/profondità (DFS):** in cui è visitato un nodo alla volta scegliendo tra gli adiacenti al nodo visitato per ultimo (visita anticipata)

![[ img/sezione13/visiteGrafi.PNG]]

## Algoritmo di visita in profondità

```cpp
void dfs(const Grafo& g, const unsigned& a, vector<bool>& visitati) {
	visitati[a] = true;
	// valuta a
	for(unsigned i = 0; i < g.n(); ++i) {
		if(a != i && g(a, i) && !visitati[i])
			dfs(g, i, visitati);
	}
}
```

---

## Algoritmo di visita in ampiezza

```cpp
void bfs(const Grafo& g, const unsigned& a, vector<bool>& visitati) {
	visitati[a] = true;
	list<unsigned> q;
	q.push_back(a);
	while(!q.empty()) {
		unsigned i = q.front();
		q.pop_front();
		// valuta il nodo
		for(unsigned j = 0; j < g.n(); ++j) {
			if(i != j && g(i, j) && !visitati[j]) {
				q.push_back(j);
				visitati[j] = true;
			}
		}
	}
}
```

Con le dovute accortezze esso può rappresentare anche il numero minimo di archi necessari per arrivare ad un determinato nodo (in caso di grafo non pesato).

---

## Esercizi

### Dato un grafo g ed un nodo start, stampare i nodi raggiungibili di start

```cpp
void reachability(const Grafo& g, const unsigned& start) {
	vector<bool> nodiRaggiungibili(g.n(), false);
	bfs(g, start, nodiRaggiungibili);
	for(unsigned i = 0; i < nodiRaggiungibili.size(); ++i) {
		if(nodiRaggiungibili[i] && i != start)
			cout<<"Il nodo "<<i<<" e' raggiunto.\n";
	}
}
```

### Dato un grafo g, determinare un cammino da un nodo a ad un nodo b

```cpp
bool trovaCammino(const Grafo& g, const unsigned& s, const unsigned& e, vector<bool>& visitati, list<unsigned>& cammino) {
	if(s == e) {
		visitati[e] = true;
		return true;
	} else {
		visitati[s] = true;
		bool trovato = false;
		for(unsigned j = 0; j < g.n() && !trovato; ++j) {
			if(j != s && g(s, j) & !visitati[j]) {
				cammino.push_back(j);
				trovato = trovaCammino(g, i, e, visitati, cammino);
				if(!trovato) {
					cammino.pop_back();
				}
			}
		}
		return trovato;
	}
}
```

### Determinare se esiste una clique di grado k in un grafo orientato

Il **clique** è un insieme di nodi tale per cui per ogni coppia di nodi appartenente a questo insieme esiste un arco che li congiunge.

> G = <V, A>
>
> W ⊆ V tale che |W| = k e ∀_{W_i, W_j} ∈ W esiste (W_i, W_j)

Un singolo nodo rappresenta una clique! *BRUTE FORCE*

- genera tutti i sottoinsiemi di V
- verifica se il sottoinsieme soddisfa la condizione

```cpp
bool esisteClique(const Grafo& g, unsigned k) {
    vector<bool> insieme(G.n, false);
    return verifica(G, k, insieme, O);
}

bool verifica(const Grafo& G, unsigned k, vector<bool>& insieme, unsigned S) {
    bool esito;

    for(int i = 0; i <= 1; i++) {
        insieme[S] = i;
        if(S < G.n() - 1) {
            // genera tutti i numeri binari - O(n^2)
            esito = verifica(G, k, insieme, S+1);
        } else {
            /*
            qui ho generato una possibile combinazione di nodi
            la variabile insieme codifica questa combinazione
            */
            int cont = 0;
            for(int j = 0; j < G.n(); j++)
                if(insieme[j]) cont++;

            if(cont == k)
                esito = false;
            else {
                bool tuttiConnessi = true;
                for(int x = 0; x < G.n(); x++)
                    for(int y = 0; y < G.n(); y++)
                        if(x != y && insieme[x] && insieme[y] && !G(x, y))
                            tuttiConnessi = false;
                esito = tuttiConnessi;
            }

            if(esito == true)
                return esito;
        }

        return false;
    }
}
```
