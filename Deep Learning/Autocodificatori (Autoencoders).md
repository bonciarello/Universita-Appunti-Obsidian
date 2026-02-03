Un **Autocodificatore** è una rete neurale non supervisionata addestrata a produrre in output ($r$) una copia esatta del suo input ($x$).
L'obiettivo, paradossalmente, non è copiare perfettamente l'input (sarebbe inutile, come la funzione identità), ma costringere la rete a imparare una rappresentazione **compressa** e **significativa** dei dati nel processo.

La rete ha una forma a clessidra:
1.  **Encoder:** Comprime l'input $x$ in un codice latente $z = f(x)$ di dimensione ridotta.
2.  **Bottleneck:** Il livello centrale a bassa dimensionalità.
3.  **Decoder:** Cerca di ricostruire l'input dal codice: $r = g(z)$.

La funzione di perdita è solitamente l'Errore Quadratico Medio (MSE) tra input e output: $L = ||x - r||^2$.

## Tipologie di Autocodificatori

### 1. Under-complete Autoencoder
È la forma base: la dimensione del codice $z$ è minore della dimensione dell'input.
Costringe la rete a scartare il rumore e catturare solo le correlazioni più forti (le caratteristiche principali).
*   Se il decoder è lineare, impara lo stesso sottospazio della **PCA (Analisi delle Componenti Principali)**.
*   Se non lineare (rete profonda), impara compressioni molto più potenti.

### 2. Sparse Autoencoder
Qui la dimensione del codice può essere anche maggiore dell'input (over-complete), ma aggiungiamo un vincolo di **scarsità** alla funzione di perdita.
Costringiamo la maggior parte dei neuroni del livello latente a essere inattivi (zero) per un dato input.
$$ L = MSE + \lambda \cdot \Omega(z) $$
Questo costringe ogni neurone a specializzarsi nel riconoscere una caratteristica molto specifica e rara.

### 3. Denoising Autoencoder (DAE)
Invece di limitare la dimensione della rete, cambiamo il compito per renderlo più difficile.
1.  Prendiamo l'input pulito $x$.
2.  Lo corrompiamo aggiungendo rumore (es. gaussiano o spegnendo pixel a caso) -> $x'$.
3.  Diamo $x'$ alla rete e le chiediamo di ricostruire $x$ (l'originale pulito).
La rete non può più limitarsi a copiare l'input. Deve imparare la struttura intrinseca dei dati (es. la forma di una cifra scritta a mano) per poter "riempire i buchi" e rimuovere il rumore.

---

## 4. Autocodificatori Variazionali (VAE)
Gli autoencoder classici mappano ogni input in un singolo punto fisso dello spazio latente. Questo spazio è spesso discontinuo: se prendiamo un punto a metà strada tra il codice di un "1" e il codice di un "7", il decoder potrebbe produrre spazzatura. Non sono buoni per generare nuovi dati.

I **VAE** (Kingma & Welling, 2013) combinano deep learning e inferenza statistica.
Invece di imparare un codice fisso $z$, l'encoder impara i parametri di una **distribuzione di probabilità** (una Gaussiana):
*   **Media ($\mu$):** Dove si trova il codice.
*   **Varianza ($\sigma^2$):** L'area di incertezza attorno al codice.

Il processo diventa stocastico:
1.  Encoder: $x \to (\mu, \sigma)$.
2.  Campionamento: Estraiamo un punto $z$ casuale dalla distribuzione $N(\mu, \sigma)$.
3.  Decoder: $z \to x_{ricostruito}$.

### La Loss Function del VAE
La funzione di perdita ha due termini in competizione:
1.  **Reconstruction Loss:** Fai in modo che l'output somigli all'input (minimizza l'errore).
2.  **KL Divergence (Regolarizzazione):** Fai in modo che le distribuzioni latenti somiglino a una Normale Standard $N(0, 1)$.

Questo forza lo spazio latente a essere:
*   **Continuo:** Punti vicini producono output simili.
*   **Completo:** Qualsiasi punto campionato dallo spazio latente produce un output plausibile.
Questo rende i VAE ottimi modelli **Generativi**: possiamo generare volti nuovi semplicemente campionando numeri casuali e passandoli al decoder.
