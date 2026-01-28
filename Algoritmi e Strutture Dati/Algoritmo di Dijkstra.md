```cpp
Dijkstra(G(V,E), w, x):
    for v in V:
        v.d = infinito
        v.pi = null
    x.d = 0

    S = {}
    Q = V

    while(!Q.empty):
        u = ExtractMin(Q)
        S = S UNIONE {u}

        for v in adj(u):
            if v.d > u.d + w(u,v):
                v.d = u.d + w(u,v)
                v.pi = u
```
Ogni nodo v ha due attributi:

- *v.d* = lunghezza del cammino minimo verso v
- *v.pi* = nodo predecessore di v

L'algoritmo mantiene due insiemi:

- *S* = insieme dei nodi visitati (per i quali il cammino minimo è stato calcolato)
- *Q* = insieme dei nodi non ancora visitati

*Funzioni di utilità:*

- *w(u,v)* = restituisce il peso dell'arco (u,v)
- *adj(u)* = restituisce i nodi adiacenti a u
- *ExtractMin(Q)* = estrae da Q il nodo u con valore u.d più piccolo

```cpp
for v in V:
    v.d = infinito
    v.pi = null
x.d = 0

S = {}
Q = V
```
*Fase di inizializzazione:*

- v.d = infinito (nessun nodo è stato ancora raggiunto) e v.pi = null (nessun nodo ha un predecessore)
- x.d = 0 (il nodo sorgente ha distanza 0 da sé stesso)
- S = {} (nessun nodo è stato visitato) e Q = V (tutti i nodi sono ancora da visitare)

```cpp
while(!Q.empty):
    u = ExtractMin(Q)
    S = S UNIONE {u}

    for v in adj(u):
        if v.d > u.d + w(u,v):
            v.d = u.d + w(u,v)
            v.pi = u
```

## Esempio

Trovare il cammino minimo dal nodo X al nodo Y.

![[img/sezione15/dijkstra.PNG]]

Una semplice BFS/DFS non basta.
Nel grafo non sono presenti archi con peso negativo: possiamo usare Dijkstra.

- fase di inizializzazione, creazione di S e Q:
  ![[img/sezione15/ris1.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: X
  - aggiungo X ad S
  - aggiorno *d* degli adiacenti di X
  - aggiorno *pi* degli adiacenti di X

  ![[img/sezione15/ris2.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: A
  - aggiungo A ad S
  - aggiorno *d* degli adiacenti di A
  - aggiorno *pi* degli adiacenti di A

  ![[img/sezione15/ris3.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: B
  - aggiungo B ad S
  - aggiorno *d* degli adiacenti di B
  - aggiorno *pi* degli adiacenti di B

  ![[ img/sezione15/ris4.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: D
  - aggiungo D ad S
  - aggiorno *d* degli adiacenti di D
  - aggiorno *pi* degli adiacenti di D

  ![[ img/sezione15/ris5.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: C
  - aggiungo C ad S
  - aggiorno *d* degli adiacenti di C
  - aggiorno *pi* degli adiacenti di C

  ![[ img/sezione15/ris6.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: E
  - aggiungo E ad S
  - aggiorno *d* degli adiacenti di E
  - aggiorno *pi* degli adiacenti di E

  ![[ img/sezione15/ris7.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: F
  - aggiungo F ad S
  - aggiorno *d* degli adiacenti di F
  - aggiorno *pi* degli adiacenti di F

  ![[ img/sezione15/ris8.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: G
  - aggiungo G ad S
  - aggiorno *d* degli adiacenti di G
  - aggiorno *pi* degli adiacenti di G

  ![[ img/sezione15/ris9.PNG]]

- iterazione:
  - nodo con *d* minimo in Q: Y
  - aggiungo Y ad S
  - aggiorno *d* degli adiacenti di Y
  - aggiorno *pi* degli adiacenti di Y

  ![[ img/sezione15/ris10.PNG]]

- **STOP!**
  - Q è adesso vuota
  - Il costo del cammino minimo da X a Y è pari a 6.

  ![[ img/sezione15/ris11.PNG]]
