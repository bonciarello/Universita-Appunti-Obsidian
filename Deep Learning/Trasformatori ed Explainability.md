# Capitolo 21: Trasformatori ed Explainability

Il paper **"Attention is All You Need"** (Google Brain, 2017) ha segnato un punto di svolta nel Deep Learning. Ha introdotto l'architettura **Transformer**, che ha rapidamente reso obsolete le RNN per la maggior parte dei compiti di NLP (Natural Language Processing).

## 1. I Limiti delle RNN
Le RNN (LSTM/GRU) processano i dati in modo **sequenziale**: per calcolare lo stato al tempo $t$, devono aver calcolato lo stato al tempo $t-1$.
Questo comporta due grossi problemi:
1.  **Lentezza:** Non è possibile parallelizzare il calcolo su GPU. Se una frase ha 100 parole, servono 100 step sequenziali.
2.  **Memoria a lungo termine:** Nonostante i meccanismi di gating, l'informazione tende a degradarsi su sequenze molto lunghe.

## 2. L'Architettura Transformer
I Trasformatori abbandonano completamente la ricorrenza. Elaborano l'intera sequenza in parallelo.
Il segreto è il meccanismo di **Self-Attention** (Auto-Attenzione): permette a ogni parola della frase di "guardare" direttamente a tutte le altre parole, indipendentemente dalla loro distanza, per capire il contesto.

Il modello è composto da due pile di blocchi:
*   **Encoder (Sinistra):** Legge e comprende l'input. Composto da strati di *Self-Attention* e *Feed Forward*.
*   **Decoder (Destra):** Genera l'output. Composto da strati di *Masked Self-Attention*, *Cross-Attention* (guarda l'encoder) e *Feed Forward*.

### Positional Encoding
Poiché il modello non processa le parole in ordine, non ha idea della loro posizione relativa ("A ama B" è diverso da "B ama A").
Per risolvere questo, sommiamo al vettore di embedding di ogni parola un **Vettore Posizionale**.
Questi vettori seguono pattern sinusoidali a diverse frequenze, permettendo al modello di imparare relazioni relative (es. "parola a distanza 3").

## 3. Explainability (Spiegabilità)
Le reti neurali profonde sono spesso considerate "Black Box": input $\to$ magia $\to$ output. In settori critici (medicina, finanza), questo non è accettabile.
L'**Explainability (XAI)** è la branca dell'AI che cerca di rendere i modelli interpretabili.

I Trasformatori offrono un vantaggio unico qui: le **Mappe di Attenzione**.
Possiamo visualizzare la matrice dei pesi di attenzione per vedere esattamente su quali parole il modello si è "concentrato" mentre prendeva una decisione.
*   *Esempio:* Se il modello traduce la parola "bank" in "riva" (del fiume) invece che "banca", possiamo vedere che la sua attenzione era focalizzata sulla parola "river" nel contesto, spiegando la disambiguazione.
