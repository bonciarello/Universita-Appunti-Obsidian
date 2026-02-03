Le reti neurali convoluzionali profonde (Deep ConvNets) richiedono enormi quantità di dati e risorse computazionali per essere addestrate da zero. Fortunatamente, nel Deep Learning è comune riutilizzare modelli già addestrati su dataset molto grandi: questa pratica si chiama **Transfer Learning** (Apprendimento per Trasferimento).

L'idea è semplice: un modello addestrato su un dataset vasto e generico (come **ImageNet**, che contiene 1.4 milioni di immagini divise in 1000 classi) ha imparato una gerarchia di caratteristiche visive (bordi, texture, forme, occhi, ruote...) che sono utili per *qualsiasi* problema di visione artificiale, non solo per classificare le 1000 classi originali.
Possiamo prendere queste "conoscenze visive" e trasferirle al nostro problema specifico (es. distinguere cani da gatti, o tipi di cellule al microscopio), anche se abbiamo pochi dati.

## Modelli Famosi
Keras include molti modelli pre-addestrati pronti all'uso (`keras.applications`):
*   **VGG-16 / VGG-19:** Architetture classiche, molto lineari e facili da capire, ma pesanti.
*   **ResNet:** Introduce le connessioni residue ("skip connections") per permettere l'addestramento di reti molto profonde (50, 101, 152 strati).
*   **Inception:** Usa moduli complessi che applicano filtri di diverse dimensioni in parallelo.
*   **MobileNet:** Ottimizzata per dispositivi mobili (bassa latenza, pochi parametri).

Noi useremo **VGG16** come esempio per la sua semplicità didattica.

## Strategie di Transfer Learning

Esistono due modi principali per usare una rete pre-addestrata:

### 1. Estrazione di Caratteristiche (Feature Extraction)
Consiste nell'utilizzare la **base convoluzionale** del modello pre-addestrato (la parte che estrae le feature map) per elaborare le nuove immagini, e addestrare un **nuovo classificatore** (strati densi) da zero sopra di essa.

*   **Perché scartiamo il classificatore originale?** Gli strati convoluzionali imparano mappe spaziali di concetti (es. "c'è un occhio qui"), che sono generiche. Gli strati densi finali, invece, distruggono l'informazione spaziale per produrre una probabilità per le classi specifiche di ImageNet. Se il nostro problema ha classi diverse, quella parte è inutile.
*   **Congelamento (Freezing):** È fondamentale "congelare" la base convoluzionale durante l'addestramento. Se non lo facessimo, i grandi gradienti generati dal classificatore inizializzato casualmente distruggerebbero i pesi pre-addestrati (le "conoscenze") della base.

#### Implementazione Pratica con VGG16

```python
from tensorflow.keras.applications import VGG16
from tensorflow.keras import models, layers, optimizers

# 1. Carichiamo la base convoluzionale VGG16
conv_base = VGG16(weights='imagenet',       # Pesi pre-addestrati
                  include_top=False,        # Escludiamo il classificatore finale (i 3 strati densi)
                  input_shape=(150, 150, 3))

# 2. Congeliamo la base
conv_base.trainable = False 
# Ora i pesi di conv_base non verranno aggiornati durante il fit()

# 3. Aggiungiamo il nostro classificatore
model = models.Sequential()
model.add(conv_base)
model.add(layers.Flatten())
model.add(layers.Dense(256, activation='relu'))
model.add(layers.Dropout(0.5))
model.add(layers.Dense(1, activation='sigmoid')) # Esempio binario (es. Cani vs Gatti)

# 4. Compilazione e Addestramento
model.compile(optimizer=optimizers.RMSprop(learning_rate=2e-5),
              loss='binary_crossentropy',
              metrics=['acc'])

# model.fit(...)
```

### 2. Fine-Tuning (Sintonizzazione Fine)
Il Fine-Tuning è un passaggio avanzato, da fare *solo dopo* aver addestrato il classificatore con la tecnica precedente.
Consiste nello "scongelare" (rendere trainables) alcuni degli strati superiori della base convoluzionale (quelli più vicini all'output) e addestrarli insieme al classificatore.

*   **Logica:** I primi strati della rete imparano feature molto generiche (bordi, colori). Gli strati superiori imparano feature più astratte e specifiche (orecchie di cane, ruote). Con il fine-tuning, adattiamo leggermente queste feature di alto livello per renderle più pertinenti al *nostro* dataset specifico.
*   **Attenzione:** Usare un learning rate molto basso (es. `1e-5`) per non stravolgere i pesi, ma solo "aggiustarli".

#### Implementazione del Fine-Tuning
```python
# Scongeliamo la base...
conv_base.trainable = True

# ...ma ricongeliamo tutti i layer tranne l'ultimo blocco
set_trainable = False
for layer in conv_base.layers:
    if layer.name == 'block5_conv1':
        set_trainable = True
    
    if set_trainable:
        layer.trainable = True
    else:
        layer.trainable = False

# È necessario ricompilare il modello affinché le modifiche abbiano effetto
model.compile(optimizer=optimizers.RMSprop(learning_rate=1e-5), # Learning rate bassissimo!
              loss='binary_crossentropy',
              metrics=['acc'])

# model.fit(...)
```

In sintesi:
1.  Addestra il classificatore sopra la base congelata.
2.  Scongela gli ultimi strati della base.
3.  Addestra tutto insieme con un learning rate basso.
