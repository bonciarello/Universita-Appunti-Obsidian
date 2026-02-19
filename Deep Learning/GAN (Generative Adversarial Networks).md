---
aliases: [Generative Adversarial Networks, GAN]
tags: [deep-learning]
---
Le **GAN** (Goodfellow et al., 2014) rappresentano un approccio radicalmente diverso alla modellazione generativa. Invece di modellare esplicitamente una distribuzione di probabilità (come i VAE), le GAN imparano a generare dati attraverso un **gioco competitivo** tra due [[Reti|reti]] neurali.

## 1. I Giocatori
Immaginiamo una sfida tra un falsario d'arte e un critico d'arte.

### Il Generatore ($G$) - Il Falsario
*   **Input:** Un vettore di rumore casuale $z$ (vettore latente).
*   **Obiettivo:** Trasformare questo rumore in un'immagine $x_{fake}$ che sia indistinguibile da un'immagine reale del dataset di training.
*   Non vede mai le immagini reali! Impara solo dai feedback del discriminatore.

### Il Discriminatore ($D$) - Il Critico
*   **Input:** Un'immagine (che può essere reale $x$ o falsa $x_{fake}$). 
*   **Obiettivo:** Classificare correttamente l'immagine come "Reale" (1) o "Falsa" (0).
*   È un classico classificatore binario addestrato in modo supervisionato.

## 2. Il Gioco Minimax
L'addestramento è un gioco a somma zero.
*   $D$ cerca di massimizzare la sua accuratezza (riconoscere i falsi).
*   $G$ cerca di minimizzare l'accuratezza di $D$ (ingannarlo).

Matematicamente, cercano di ottimizzare la funzione valore $V(D, G)$:
$$ \min_G \max_D V(D, G) = \mathbb{E}_{x \sim p_{data}}[\log D(x)] + \mathbb{E}_{z \sim p_{z}}[\log(1 - D(G(z)))] $$
All'equilibrio (teorico), il generatore produce dati perfetti e il discriminatore non sa più distinguere (tira a indovinare con probabilità 0.5).

## 3. Sfide nell'Addestramento
Le GAN sono notoriamente difficili da addestrare a causa dell'instabilità dinamica.

### Mode Collapse (Collasso Modale)
Il generatore trova *una* immagine che inganna bene il discriminatore (es. una specifica scarpa) e inizia a generare *solo* quella, ignorando la varietà del dataset reale. Il discriminatore impara a bloccarla, il generatore si sposta su un'altra singola immagine, e così via, girando in tondo senza imparare la distribuzione completa.

### Vanishing Gradients
Se il discriminatore è troppo bravo (perfetto), non fornisce gradienti utili al generatore per migliorare (la curva di loss diventa piatta). Bisogna mantenere i due avversari bilanciati.

## 4. Varianti Famose

### DCGAN (Deep Convolutional GAN)
La prima [[Architettura|architettura]] stabile per generare immagini. Usa convoluzioni trasposte (Deconvolutions) nel generatore per up-campionare il rumore in un'immagine, e convoluzioni con stride nel discriminatore. Elimina i livelli di Pooling e Fully Connected.

### CycleGAN: Traduzione Image-to-Image senza Coppie
Supponiamo di voler trasformare foto di **cavalli** in **zebre**.
Con i metodi classici (Pix2Pix), servirebbero migliaia di foto esattamente accoppiate (stessa posa, stesso sfondo).
CycleGAN risolve il problema usando due generatori ($G: X \to Y$ e $F: Y \to X$) e due discriminatori.
Introduce la **Cycle Consistency Loss**:
*   Se traduco una frase in Inglese -> Francese -> Inglese, devo riottenere la frase originale.
*   Se trasformo Cavallo -> Zebra -> Cavallo, l'immagine finale deve essere identica all'originale.
Questo vincolo costringe la rete a preservare la struttura della scena mentre cambia solo lo stile (texture).
