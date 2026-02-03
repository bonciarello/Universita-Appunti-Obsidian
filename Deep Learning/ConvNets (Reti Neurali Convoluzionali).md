Le **ConvNets** (o CNN) sono un tipo di architettura di rete neurale progettata specificamente per elaborare dati che hanno una struttura a griglia, come immagini (griglia 2D di pixel) o serie temporali (griglia 1D).
Sono state fondamentali per la rivoluzione del Deep Learning nella Computer Vision, alimentando applicazioni come il riconoscimento facciale, le auto a guida autonoma e la diagnostica medica.

## Differenza Fondamentale con le Reti Dense
Nelle reti densamente connesse (Dense/Fully Connected), ogni neurone è collegato a tutti i neuroni dello strato precedente. Queste reti apprendono **pattern globali** nel loro spazio di input.
Al contrario, i livelli convoluzionali apprendono **pattern locali**: nel caso delle immagini, pattern presenti in piccole finestre 2D degli input (es. 3x3 pixel).

Questa caratteristica conferisce alle ConvNets due proprietà cruciali:
1.  **Invarianza per Traslazione:** Dopo aver appreso un determinato pattern (es. un bordo verticale o un occhio) in una certa posizione dell'immagine (es. angolo in basso a destra), una ConvNet è in grado di riconoscerlo ovunque (es. angolo in alto a sinistra). Una rete densa dovrebbe imparare di nuovo lo schema se apparisse in una nuova posizione. Questo rende le ConvNets estremamente efficienti nell'uso dei dati: hanno bisogno di meno campioni per generalizzare.
2.  **Gerarchie Spaziali di Caratteristiche:** I primi strati apprendono pattern locali semplici (bordi, linee, texture). I livelli successivi combinano questi pattern semplici per formare pattern più complessi (occhi, orecchie). Gli strati finali combinano questi per riconoscere oggetti interi (gatti, cani). Questo rispecchia il modo in cui noi umani percepiamo il mondo visivo.

## Il Funzionamento della Convoluzione

Le convoluzioni operano su **tensori 3D**, chiamati **Mappe delle Caratteristiche (Feature Maps)**, con due assi spaziali (altezza e larghezza) e un asse di profondità (canali).
*   Per un'immagine RGB in input: Profondità = 3 (Rosso, Verde, Blu).
*   Per un'immagine in scala di grigi (es. MNIST): Profondità = 1.

L'operazione di convoluzione consiste nel far scorrere una piccola finestra (il **Kernel** o Filtro) sopra la mappa di input, fermandosi in ogni posizione possibile, ed eseguendo un prodotto scalare tra i pesi del filtro e la patch dell'input sottostante.
Questo produce una **Mappa delle Caratteristiche di Output**, che è ancora un tensore 3D.
La profondità dell'output non corrisponde più ai canali colore (RGB), ma al numero di **filtri** che abbiamo deciso di usare. Ogni canale nell'output rappresenta la risposta dell'immagine a uno specifico filtro (es. "quanto è presente un bordo verticale in questo punto?").

### Parametri Chiave
Una convoluzione è definita da due parametri principali:
1.  **Dimensione della Patch (Kernel Size):** Tipicamente $3 \times 3$ o $5 \times 5$.
2.  **Profondità dell'Output:** Il numero di filtri calcolati dalla convoluzione (es. 32, 64, 128).

Inoltre, la geometria dell'output è influenzata da:

#### Padding (Riempimento)
Se facciamo scorrere una finestra $3 \times 3$ su una griglia $5 \times 5$, possiamo centrarla solo in 9 posizioni (griglia interna $3 \times 3$). L'immagine si rimpicciolisce.
Per mantenere le stesse dimensioni spaziali (spesso utile per costruire reti profonde), usiamo il **Padding**: aggiungiamo righe e colonne di zeri attorno all'input.
*   `padding='valid'`: Nessun padding. L'output è più piccolo dell'input.
*   `padding='same'`: Padding tale che l'output abbia la stessa altezza/larghezza dell'input.

#### Stride (Passo)
Lo **Stride** è la distanza tra due finestre consecutive. Di default è 1 (finestre sovrapposte).
Se usiamo uno stride di 2 (o più), saltiamo delle posizioni. Questo ha l'effetto di **sottocampionare** (downsampling) l'immagine, dimezzandone le dimensioni. È usato raramente nei layer convoluzionali moderni (si preferisce il Max Pooling), ma è un concetto importante.

## Max Pooling
Oltre alla convoluzione, le ConvNets usano operazioni di **Pooling** per ridurre progressivamente le dimensioni spaziali delle feature maps.
Il **Max Pooling** consiste nell'estrarre finestre (solitamente $2 \times 2$) e emettere solo il **valore massimo** di ciascuna finestra.
*   **Perché farlo?**
    1.  Ridurre il numero di parametri e calcoli (meno pixel da processare negli strati successivi).
    2.  Indurre una certa invarianza a piccole traslazioni e distorsioni (se l'occhio si sposta di un pixel, il max nella finestra 2x2 resta probabilmente lo stesso).
    3.  Aumentare il campo ricettivo dei neuroni successivi (guardano aree più grandi dell'immagine originale).

---

## Esempio di Architettura Completa (CIFAR-10)

Ecco come appare una ConvNet tipica in codice. Notate lo schema ripetitivo: `Conv2D` -> `MaxPooling2D`.

```python
from tensorflow.keras import layers, models

def build_convnet(input_shape=(32, 32, 3), num_classes=10):
    model = models.Sequential()
    
    # 1. Blocco Convoluzionale (Estrae feature di basso livello)
    # Input: 32x32x3 -> Output: 32x32x32 (32 filtri, padding same)
    model.add(layers.Conv2D(32, (3, 3), activation='relu', padding='same', 
                            input_shape=input_shape))
    # Downsampling: 32x32x32 -> 16x16x32
    model.add(layers.MaxPooling2D((2, 2)))
    
    # 2. Blocco Convoluzionale (Estrae feature di medio livello)
    # Input: 16x16x32 -> Output: 14x14x64 (padding valid di default riduce dimensione)
    model.add(layers.Conv2D(64, (3, 3), activation='relu'))
    # Downsampling: 14x14x64 -> 7x7x64
    model.add(layers.MaxPooling2D((2, 2)))
    
    # 3. Blocco Convoluzionale (Estrae feature di alto livello)
    # Input: 7x7x64 -> Output: 5x5x64
    model.add(layers.Conv2D(64, (3, 3), activation='relu'))
    
    # 4. Classificatore (Top della rete)
    # Appiattiamo il volume 3D in un vettore 1D: 5x5x64 -> 1600
    model.add(layers.Flatten())
    # Strati densi per la classificazione finale
    model.add(layers.Dense(64, activation='relu'))
    model.add(layers.Dense(num_classes, activation='softmax'))
    
    return model
```
In questo esempio, le dimensioni spaziali si riducono (32 -> 16 -> 7 -> 5) mentre la profondità (numero di filtri) aumenta (3 -> 32 -> 64). Questo è un pattern standard.
