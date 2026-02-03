# Capitolo 4: Laboratorio - Creare Rete Neurale da Zero

## Costruzione di una Rete Neurale (Feedforward)

Implementiamo una semplice rete neurale feedforward con uno strato nascosto utilizzando Python e NumPy.

### Classe `SimpleNeuralNetwork`

```python
import matplotlib.pyplot as plt
import numpy as np

class SimpleNeuralNetwork:
    def __init__(self, n_input=2, n_hidden=3, n_output=1):
        # Inizializzazione pesi e bias
        self.W1 = np.random.rand(n_input, n_hidden)
        self.W2 = np.random.rand(n_hidden, n_output)
        self.B1 = np.zeros(n_hidden)
        self.B2 = np.zeros(n_output)

    def loss(self, out, out_pred):
        # Mean Absolute Error (o simile)
        return np.mean(np.sqrt(np.abs(out - out_pred)))

    def activationFunction(self, A):
        # Sigmoide
        return 1. / (1. + np.exp(-A))

    def gradActFuction(self, out):
        # Derivata della Sigmoide
        return out * (1 - out)

    def forward(self, input_data):
        self.Z0 = input_data
        
        # Layer 1
        self.A1 = np.dot(self.Z0, self.W1) + self.B1
        self.Z1 = self.activationFunction(self.A1)
        
        # Layer 2 (Output)
        self.A2 = np.dot(self.Z1, self.W2) + self.B2
        self.Z2 = self.activationFunction(self.A2)
        
        return self.Z2

    def grad(self, input_data, output, output_pred, learning_rate):
        # Backpropagation
        m = input_data.shape[0] # o shape[1] a seconda di come sono passati i dati
        
        # Calcolo gradienti Output Layer
        self.delta2 = (output_pred - output) * self.gradActFuction(output_pred)
        self.dW2 = np.matmul(self.Z1.T, self.delta2)
        self.dB2 = np.sum(self.delta2, axis=0)

        # Calcolo gradienti Hidden Layer
        self.delta1 = np.matmul(self.delta2, self.W2.T) * self.gradActFuction(self.Z1)
        self.dW1 = np.matmul(self.Z0.T, self.delta1)
        self.dB1 = np.sum(self.delta1, axis=0)

        # Aggiornamento pesi
        self.W1 -= learning_rate * self.dW1
        self.W2 -= learning_rate * self.dW2
        self.B1 -= learning_rate * self.dB1
        self.B2 -= learning_rate * self.dB2

    def fit(self, input_data, output, epochs=1, learning_rate=0.05):
        history = []
        for epoch in range(epochs):
            output_pred = self.forward(input_data)
            loss_val = self.loss(output, output_pred)
            history.append(loss_val)
            
            self.grad(input_data, output, output_pred, learning_rate)
            
        self.plot_loss(history)

    def predict(self, input_data):
        return np.array(self.forward(input_data))

    def plot_loss(self, loss):
        plt.plot(loss)
        plt.xlabel('Epochs')
        plt.ylabel('Loss')
        plt.show()
```

### Esempio di Utilizzo (Classificazione Malattia)

Supponiamo di voler prevedere se una persona è malata basandoci su età e reddito.

```python
import pandas as pd
from sklearn.metrics import accuracy_score
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import scale

# Caricamento e preparazione dati (ipotetico)
# df = pd.read_csv("dataset.csv")
# df.Illness = pd.Categorical(df.Illness).codes
# data = np.array(df.drop(columns='Illness'))
# labels = np.array(df['Illness']).reshape(-1, 1)

# data = scale(data)
# x_train, x_val, y_train, y_val = train_test_split(data, labels, random_state=0)

# Training
# net = SimpleNeuralNetwork()
# net.fit(x_train, y_train, epochs=2000)

# Valutazione
# y_pred_val = net.predict(x_val)
# y_pred_bin_val = (y_pred_val >= 0.5).astype("int")
# print("Validation accuracy", accuracy_score(y_pred_bin_val, y_val))
```

## Visualizzazione Funzioni di Attivazione

Ecco come visualizzare le principali funzioni di attivazione con Python.

### Sigmoid
```python
def sigmoid(x):
    return 1. / (1. + np.exp(-x))

x = np.linspace(-10, 10, 100)
plt.plot(x, sigmoid(x))
plt.title('Sigmoid Function')
plt.show()
```

### Tanh
```python
def tanh(x):
    return (np.exp(x) - np.exp(-x)) / (np.exp(x) + np.exp(-x))

plt.plot(x, tanh(x))
plt.title('Tanh Function')
plt.show()
```

### ReLU
```python
def relu(x):
    return np.maximum(0, x)

plt.plot(x, relu(x))
plt.title('ReLU Function')
plt.show()
```