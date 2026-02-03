L'Attenzione può essere descritta come un sistema di recupero informazioni "fuzzy" (sfocato).
Immaginiamo di avere un database di coppie (Chiave, Valore). Arriva una Query.
*   In un database classico, cerchiamo la Chiave esatta e restituiamo il Valore.
*   Nell'Attenzione, calcoliamo quanto la Query è *simile* a ciascuna Chiave, e restituiamo una media ponderata dei Valori.

## 1. La Formula Matematica
Ogni input $X$ viene proiettato tramite tre matrici di pesi apprendibili ($W^Q, W^K, W^V$) per produrre tre vettori:
*   **Query ($Q$):** Cosa sto cercando?
*   **Key ($K$):** Cosa offro? (Etichetta per il confronto).
*   **Value ($V$):** Il contenuto vero e proprio.

L'attenzione è calcolata come:
$$ Attention(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V $$

1.  **$QK^T$ (Prodotto Scalare):** Calcola la somiglianza tra la Query e tutte le Key. Se due vettori sono allineati, il prodotto è alto.
2.  **$\\sqrt{d_k}$ (Scaling):** Divide per la radice della dimensione dei vettori. Serve a evitare che il prodotto scalare diventi troppo grande (che spingerebbe la softmax in zone a gradiente zero).
3.  **Softmax:** Converte i punteggi grezzi in probabilità (pesi che sommano a 1).
4.  **$\cdot V$:** Calcola la somma pesata dei Valori.

## 2. Tipologie di Attenzione nel Transformer

### Self-Attention (Encoder e Decoder)
Qui $Q, K, V$ provengono tutti dalla stessa fonte (l'output del livello precedente).
Ogni parola presta attenzione a tutte le altre parole della frase. Serve per costruire una rappresentazione contestuale ricca.

### Masked Self-Attention (Decoder)
Nel Decoder, mentre generiamo la parola al tempo $t$, non dobbiamo poter sbirciare le parole future ($t+1, t+2...$).
Si applica una **Maschera** (valori $-\\infty$ prima della softmax) che azzera l'attenzione verso il futuro.

### Cross-Attention (Encoder-Decoder)
*   **Query:** Viene dal Decoder (ciò che voglio generare).
*   **Key & Value:** Vengono dall'output dell'Encoder (la frase originale).
Questo permette al Decoder di allinearsi con la frase di input.

## 3. Multi-Head Attention
Una singola attenzione potrebbe focalizzarsi solo su un aspetto (es. concordanza sintattica).
Usiamo **$h$ Teste di Attenzione** parallele. Ogni testa ha le sue matrici $W^Q, W^K, W^V$ e impara a guardare aspetti diversi (una guarda la sintassi, una la semantica, una le entità nominate).
Gli output delle teste vengono concatenati e passati per una proiezione lineare finale.
