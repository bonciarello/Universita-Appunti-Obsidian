# Capitolo 17: Laboratorio - Ottimizzazione VAE

Il **Variational Autoencoder (VAE)** è un modello generativo potente. A differenza degli autoencoder classici, che imparano una mappatura deterministica (punto $A \to$ punto $B$), i VAE imparano una mappatura probabilistica: input $\to$ **distribuzione normale**.

## Concetti Chiave dell'Ottimizzazione VAE

### 1. Spazio Latente Probabilistico
Vogliamo che lo spazio latente sia continuo (punti vicini = immagini simili) e completo (ogni punto genera un'immagine valida).
Per ottenere questo, forziamo l'encoder a predire due vettori per ogni input:
*   `z_mean` ($\mu$): Il centro della distribuzione.
*   `z_log_var` ($\log(\sigma^2)$): Il logaritmo della varianza (usiamo il logaritmo per stabilità numerica, così la rete può predire valori negativi che mappano a varianze piccole tra 0 e 1).

### 2. The Reparameterization Trick
Dobbiamo campionare $z$ da questa distribuzione per passarlo al decoder. Ma l'operazione di campionamento casuale non è differenziabile: non possiamo calcolare il gradiente attraverso di essa per la backpropagation.
**Soluzione:** Spostiamo la casualità su una variabile ausiliaria $\epsilon$.
$$ z = \mu + \sigma \cdot \epsilon $$
dove $\epsilon \sim \mathcal{N}(0, 1)$ (rumore bianco standard).
Ora $z$ è una funzione deterministica dei parametri della rete ($\mu, \sigma$) e di una costante esterna ($\epsilon$), quindi possiamo derivare rispetto a $\mu$ e $\sigma$.

### 3. La Loss Function (ELBO)
La funzione di perdita da minimizzare è il "negativo dell'Evidence Lower Bound" (ELBO), composto da:
*   **Reconstruction Loss:** Misura quanto l'immagine generata somiglia all'originale (es. Binary Crossentropy).
*   **KL Divergence:** Misura quanto la distribuzione predetta ($\mu, \sigma$) diverge da una normale standard $\mathcal{N}(0, 1)$. Agisce come regolarizzatore, tenendo le distribuzioni "compatte" e centrate.

## Implementazione in Keras

### Layer di Campionamento Custom
```python
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

class Sampling(layers.Layer):
    """Usa (z_mean, z_log_var) per campionare z."""
    def call(self, inputs):
        z_mean, z_log_var = inputs
        batch = tf.shape(z_mean)[0]
        dim = tf.shape(z_mean)[1]
        
        # Generiamo il rumore epsilon
        epsilon = tf.keras.backend.random_normal(shape=(batch, dim))
        
        # Applichiamo la formula: z = mu + sigma * epsilon
        # Nota: exp(0.5 * log_var) equivale alla radice quadrata della varianza (deviazione standard)
        return z_mean + tf.exp(0.5 * z_log_var) * epsilon
```

### Encoder e Decoder
```python
latent_dim = 2 # Spazio latente 2D per poterlo visualizzare facilmente

# --- ENCODER ---
encoder_inputs = keras.Input(shape=(28, 28, 1))
x = layers.Conv2D(32, 3, activation="relu", strides=2, padding="same")(encoder_inputs)
x = layers.Conv2D(64, 3, activation="relu", strides=2, padding="same")(x)
x = layers.Flatten()(x)
x = layers.Dense(16, activation="relu")(x)

# Due output separati: media e log-varianza
z_mean = layers.Dense(latent_dim, name="z_mean")(x)
z_log_var = layers.Dense(latent_dim, name="z_log_var")(x)

# Campionamento
z = Sampling()([z_mean, z_log_var])

encoder = keras.Model(encoder_inputs, [z_mean, z_log_var, z], name="encoder")

# --- DECODER ---
latent_inputs = keras.Input(shape=(latent_dim,))
x = layers.Dense(7 * 7 * 64, activation="relu")(latent_inputs)
x = layers.Reshape((7, 7, 64))(x)
x = layers.Conv2DTranspose(64, 3, activation="relu", strides=2, padding="same")(x)
x = layers.Conv2DTranspose(32, 3, activation="relu", strides=2, padding="same")(x)
decoder_outputs = layers.Conv2DTranspose(1, 3, activation="sigmoid", padding="same")(x)

decoder = keras.Model(latent_inputs, decoder_outputs, name="decoder")
```

### Classe VAE Completa
Dobbiamo sovrascrivere `train_step` per implementare la loss complessa.

```python
class VAE(keras.Model):
    def __init__(self, encoder, decoder, **kwargs):
        super(VAE, self).__init__(**kwargs)
        self.encoder = encoder
        self.decoder = decoder
        # Tracker per monitorare le metriche durante il training
        self.total_loss_tracker = keras.metrics.Mean(name="total_loss")
        self.reconstruction_loss_tracker = keras.metrics.Mean(name="reconstruction_loss")
        self.kl_loss_tracker = keras.metrics.Mean(name="kl_loss")

    @property
    def metrics(self):
        return [self.total_loss_tracker, self.reconstruction_loss_tracker, self.kl_loss_tracker]

    def train_step(self, data):
        with tf.GradientTape() as tape:
            # Forward pass
            z_mean, z_log_var, z = self.encoder(data)
            reconstruction = self.decoder(z)
            
            # 1. Reconstruction Loss
            # Sommiamo l'errore su tutti i pixel
            reconstruction_loss = tf.reduce_mean(
                tf.reduce_sum(
                    keras.losses.binary_crossentropy(data, reconstruction), axis=(1, 2)
                )
            )
            
            # 2. KL Divergence Loss
            # Formula analitica per KL(N(mu, sigma) || N(0, 1))
            kl_loss = -0.5 * (1 + z_log_var - tf.square(z_mean) - tf.exp(z_log_var))
            kl_loss = tf.reduce_mean(tf.reduce_sum(kl_loss, axis=1))
            
            total_loss = reconstruction_loss + kl_loss

        # Backpropagation
        grads = tape.gradient(total_loss, self.trainable_weights)
        self.optimizer.apply_gradients(zip(grads, self.trainable_weights))
        
        # Aggiornamento metriche
        self.total_loss_tracker.update_state(total_loss)
        self.reconstruction_loss_tracker.update_state(reconstruction_loss)
        self.kl_loss_tracker.update_state(kl_loss)
        
        return {
            "loss": self.total_loss_tracker.result(),
            "reconstruction_loss": self.reconstruction_loss_tracker.result(),
            "kl_loss": self.kl_loss_tracker.result(),
        }
```
