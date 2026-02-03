# Capitolo 23: Laboratorio - Transformer per Traduzione

Costruiremo un modello Transformer completo per tradurre dal Portoghese all'Inglese. Keras fornisce `MultiHeadAttention`, ma dobbiamo assemblare noi l'architettura Encoder-Decoder.

## 1. Positional Encoding
Poiché non usiamo RNN, dobbiamo iniettare l'informazione sulla posizione.
Creiamo un layer che somma l'embedding delle parole a un vettore calcolato con seni e coseni.

```python
import tensorflow as tf
import numpy as np

def get_positional_encoding(seq_len, d_model):
    angles = np.arange(seq_len)[:, np.newaxis] / np.power(10000, (2 * (np.arange(d_model)[np.newaxis, :] // 2)) / np.float32(d_model))
    angles[:, 0::2] = np.sin(angles[:, 0::2]) # Indici pari: Seno
    angles[:, 1::2] = np.cos(angles[:, 1::2]) # Indici dispari: Coseno
    return tf.cast(angles[np.newaxis, ...], dtype=tf.float32)

class PositionalEmbedding(tf.keras.layers.Layer):
    def __init__(self, vocab_size, d_model):
        super().__init__()
        self.d_model = d_model
        self.embedding = tf.keras.layers.Embedding(vocab_size, d_model, mask_zero=True)
        self.pos_encoding = get_positional_encoding(2048, d_model)

    def call(self, x):
        length = tf.shape(x)[1]
        x = self.embedding(x)
        x *= tf.math.sqrt(tf.cast(self.d_model, tf.float32)) # Scaling
        x = x + self.pos_encoding[:, :length, :]
        return x
```

## 2. Blocchi Costitutivi (Encoder & Decoder Layers)
Ogni layer del Transformer segue uno schema standard:
`Input -> Attention -> Add & Norm -> FeedForward -> Add & Norm`

```python
class EncoderLayer(tf.keras.layers.Layer):
    def __init__(self, d_model, num_heads, dff, dropout_rate=0.1):
        super().__init__()
        self.mha = tf.keras.layers.MultiHeadAttention(num_heads=num_heads, key_dim=d_model)
        self.ffn = tf.keras.Sequential([
            tf.keras.layers.Dense(dff, activation='relu'),
            tf.keras.layers.Dense(d_model)
        ])
        
        self.layernorm1 = tf.keras.layers.LayerNormalization(epsilon=1e-6)
        self.layernorm2 = tf.keras.layers.LayerNormalization(epsilon=1e-6)
        self.dropout1 = tf.keras.layers.Dropout(dropout_rate)
        self.dropout2 = tf.keras.layers.Dropout(dropout_rate)

    def call(self, x, training):
        # 1. Self Attention
        attn_output = self.mha(x, x)
        attn_output = self.dropout1(attn_output, training=training)
        out1 = self.layernorm1(x + attn_output) # Residual Connection

        # 2. Feed Forward
        ffn_output = self.ffn(out1)
        ffn_output = self.dropout2(ffn_output, training=training)
        out2 = self.layernorm2(out1 + ffn_output) # Residual Connection
        
        return out2
```

Il `DecoderLayer` è simile, ma ha *due* blocchi di attenzione: uno `masked_mha` (per l'input del decoder) e uno `cross_mha` (che prende Key/Value dall'encoder).

## 3. Assemblaggio del Modello
```python
class Transformer(tf.keras.Model):
    def __init__(self, num_layers, d_model, num_heads, dff, input_vocab_size, target_vocab_size, dropout_rate=0.1):
        super().__init__()
        self.encoder = Encoder(num_layers, d_model, num_heads, dff, input_vocab_size, dropout_rate)
        self.decoder = Decoder(num_layers, d_model, num_heads, dff, target_vocab_size, dropout_rate)
        self.final_layer = tf.keras.layers.Dense(target_vocab_size)

    def call(self, inputs, training):
        # inputs = [inp_sentence, tar_sentence]
        inp, tar = inputs
        
        enc_output = self.encoder(inp, training)
        dec_output, attention_weights = self.decoder(tar, enc_output, training)
        
        final_output = self.final_layer(dec_output)
        return final_output
```

## 4. Training (Loss Masking)
Poiché usiamo il padding, la loss non deve considerare gli zeri alla fine delle frasi. Dobbiamo implementare una funzione di perdita personalizzata.

```python
loss_object = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True, reduction='none')

def masked_loss(real, pred):
    mask = tf.math.logical_not(tf.math.equal(real, 0)) # Maschera dove real != 0
    loss_ = loss_object(real, pred)
    
    mask = tf.cast(mask, dtype=loss_.dtype)
    loss_ *= mask # Azzera la loss sul padding
    
    return tf.reduce_sum(loss_) / tf.reduce_sum(mask) # Media solo sui token validi
```
