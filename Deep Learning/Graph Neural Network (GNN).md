# Capitolo 24: Graph Neural Network (GNN)

Molti dati nel mondo reale non hanno una struttura a griglia (immagini) o a sequenza (testo), ma una struttura a **Grafo**:
*   **Social Network:** Utenti (nodi) e amicizie (archi).
*   **Chimica:** Atomi (nodi) e legami chimici (archi).
*   **Web:** Pagine (nodi) e link (archi).

Le **Graph Neural Networks (GNN)** sono progettate per apprendere da questi dati.

## Il Paradigma del Message Passing
Il cuore delle GNN è il **Neural Message Passing**.
L'obiettivo è calcolare un vettore di stato (embedding) $h_v$ per ogni nodo $v$, che codifichi sia le sue caratteristiche locali sia la struttura del grafo attorno a esso.

Il processo avviene in $K$ iterazioni (layer):
1.  **Message:** Ogni nodo $u$ invia un messaggio ai suoi vicini $v$. Il messaggio dipende dallo stato di $u$ ($h_u$).
2.  **Aggregation:** Ogni nodo $v$ raccoglie i messaggi dai suoi vicini $\mathcal{N}(v)$. Poiché il numero di vicini varia, la funzione di aggregazione deve essere invariante alle permutazioni (es. Somma, Media, Max).
    $$ m_v^{(k)} = \text{AGGREGATE}^{(k)}(\{ h_u^{(k-1)} : u \in \mathcal{N}(v) \}) $$
3.  **Update:** Il nodo $v$ aggiorna il suo stato combinando il suo stato precedente $h_v^{(k-1)}$ con il messaggio aggregato $m_v^{(k)}$.
    $$ h_v^{(k)} = \text{UPDATE}^{(k)}(h_v^{(k-1)}, m_v^{(k)}) $$

Dopo $K$ layer, ogni nodo "conosce" informazioni sui nodi distanti $K$ salti (il suo campo ricettivo).

## Architetture Principali

### 1. GCN (Graph Convolutional Networks)
Kipf & Welling (2017). È l'analogo della convoluzione per i grafi.
L'aggregazione è una **media pesata** dei vicini. I pesi sono fissi e dipendono dal grado dei nodi (normalizzazione laplaciana): i nodi molto connessi (hub) hanno meno influenza sul singolo vicino.
$$ H^{(l+1)} = \sigma(\tilde{D}^{-1/2}\tilde{A}\tilde{D}^{-1/2}H^{(l)}W^{(l)}) $$

### 2. GraphSAGE (Graph Sample and Aggregate)
Hamilton et al. (2017). Risolve il problema della scalabilità su grafi enormi.
Invece di usare *tutti* i vicini (che potrebbero essere milioni), ne campiona un numero fisso in modo casuale.
Inoltre, è un modello **Induttivo**: impara una funzione per generare embedding, quindi può gestire nodi mai visti prima (es. nuovi utenti iscritti al social network) senza riaddestrare tutto.

### 3. GAT (Graph Attention Networks)
Veličković et al. (2018). Introduce l'**Attenzione** nei grafi.
Nella GCN, tutti i vicini contribuiscono ugualmente (o in base al grado). Nella GAT, il modello impara dei **pesi di attenzione** $\alpha_{vu}$ per ogni arco.
Il nodo $v$ può decidere di ascoltare molto il vicino $u_1$ e ignorare il vicino $u_2$, basandosi sulle loro caratteristiche.

## Applicazioni GNN
*   **Node Classification:** Classificare gli utenti (es. Bot vs Umano).
*   **Link Prediction:** Prevedere se due nodi si collegheranno (es. "Persone che potresti conoscere", Raccomandazione prodotti).
*   **Graph Classification:** Classificare l'intero grafo (es. Predire se una molecola è tossica o se una proteina è un enzima).
