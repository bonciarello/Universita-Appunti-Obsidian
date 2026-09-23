---
aliases: [AI, Algoritmi introduzione C++]
tags: [fondamenti-di-informatica]
---
## Concetti Base di Programmazione
La **Programmazione** è l'attività con cui si predispone l'elaboratore ad eseguire un particolare insieme di azioni su una particolare tipologia di dati, allo scopo di risolvere un problema.

**Problema:** È un quesito espresso attraverso un enunciato che, sulla base di dati noti, richiede di determinare dati non noti logicamente deducibili dai primi. La risoluzione di un problema avviene in 3 passaggi:
1. Individuazione dati conosciuti (**INPUT**).
2. Individuazione dati che si vogliono ottenere (**OUTPUT**).
3. Studio del metodo risolutivo (**ALGORITMO**).
### Algoritmo
È una sequenza finita di passi, eseguita secondo un ordine prefissato, che risolve in un tempo finito il problema .

**Proprietà degli Algoritmi:**
- **Efficienza:** perviene alla soluzione nel modo migliore possibile.
- **Finitezza:** il numero di istruzioni è finito e vengono eseguite un numero finito di volte.
- **Determinismo:** le istruzioni devono essere definite senza ambiguità. Se eseguito più volte con gli stessi dati, deve produrre gli stessi risultati.
- **Generalità:** risolve una famiglia di problemi, non solo un caso singolo.
- **Non Ambiguità:** costituito da regole interpretabili in modo univoco.

**Algoritmi Equivalenti:** Due algoritmi si dicono equivalenti se, in corrispondenza dello stesso input, calcolano lo stesso output.
## Linguaggi di Programmazione
Un **Linguaggio di Programmazione** è un linguaggio formale dotato di una sintassi ben definita utilizzato per scrivere programmi che realizzano algoritmi.
- **Sintassi:** Insieme delle regole che consentono di scrivere correttamente le frasi del linguaggio (errori rilevati dal compilatore).
- **Semantica:** Disciplina che studia il significato delle parole e delle frasi. Gli errori semantici causano comportamenti inaspettati e non vengono segnalati dal compilatore.

**Traduzione dei Programmi:** Per essere eseguito, un programma ad alto livello (es. C, C++, Java) deve essere tradotto in linguaggio macchina.
1. **Compilazione:** Il programma sorgente viene trasformato interamente in un eseguibile. Vantaggio: velocità di esecuzione.
2. **Interpretazione:** Traduzione riga per riga. Vantaggio: portabilità; Svantaggio: lentezza .

## Elementi di Base del C++
### Fasi di un programma C++
1. **Edit:** scrittura del programma.
2. **Preprocess:** gestione delle inclusioni.
3. **Compile:** compilazione.
4. **Link:** collegamento con le librerie.
5. **Load:** caricamento in memoria.
6. **Execute:** esecuzione.

**Compilazione (Linux/Terminale):**
- Comando: `g++ main.cpp` (genera `a.out`).
- Esecuzione: `./a.out`.
- Specificare nome output: `g++ main.cpp -o prova` $\rightarrow$ `./prova`.
### Struttura del Codice
Esempio di "Hello World":
```c++
#include <iostream>  // Direttiva al preprocessore per Input/Output [cite: 309-312]
using namespace std; // Namespace standard [cite: 298]

int main() {         // Funzione principale (entry point) [cite: 313]
    cout << "ciao";  // Istruzione di output [cite: 325]
    return 0;        // Termina il programma correttamente [cite: 328]
}
```
**Sequenze di Escape:** Caratteri speciali non stampati. `\n` (nuova linea), `\t` (tab) .
### Variabili e Tipi di Dato
Una variabile è una posizione di memoria in cui un valore può essere archiviato.
**Dichiarazione e Inizializzazione:**
- Classica: `int x = 0;`.
- C++11: `int x {0};`.
- _Nota:_ Bisogna sempre inizializzare le variabili.

**Tipi di dato principali:**
- `int`: Numeri interi.
- `float` / `double`: Numeri reali (virgola mobile).
- `char`: Singolo carattere (tra apici singoli `' '`).
- `bool`: Valori booleani (`true`/`false`).

**Limiti dei tipi:** Utilizzando la libreria `<climits>`, si possono conoscere i valori minimi e massimi (es. `INT_MAX`, `INT_MIN`).
### Input e Output
- **Output (Stampa):** `cout << variabile;`.
- **Input (Lettura):** `cin >> variabile;`.
    - Utilizza lo stream di input standard per ottenere un valore dalla tastiera.
## Strutture di Controllo (Flusso)
### Istruzione IF (Selezione)
Indica al calcolatore di eseguire istruzioni diverse al verificarsi di una condizione.
```c++
if (condizione) {
    // Istruzioni se VERO
} else {
    // Istruzioni se FALSO
}
```
La condizione è un'espressione logica (TRUE o FALSE).
### Ciclo WHILE (Iterazione)
Esegue le istruzioni all'interno del blocco finché la condizione è verificata.
```c++
while (condizione) {
    // Istruzioni
}
```
Il controllo avviene _prima_ di eseguire il blocco.
### Ciclo DO-WHILE
Simile al While, ma il blocco viene eseguito **almeno una volta**, poiché il controllo è alla fine.
```c++
do {
    // Istruzioni
} while (condizione);
```
### Ciclo FOR
Effettua un ciclo tramite tre azioni principali:
1. **Prologo:** Inizializzazione del contatore.
2. **Condizione:** Verifica per la continuazione.
3. **Continuazione:** Aggiornamento (incremento/decremento) a fine giro.
```c++
for (int i = 0; i < N; i++) {
    // Istruzioni
}
```
### Comandi di Branching
- **break:** Termina l'esecuzione del ciclo o dello switch (salta fuori).
- **continue:** Salta alla fine del blocco corrente ma non termina il ciclo (passa all'iterazione successiva).
## Esercizi ed Esempi Notabili
1. **Numeri Random:** Utilizzare `<cstdlib>` e `rand()`. Per generare numeri diversi ad ogni esecuzione usare il "seme": `srand(time(0))` con la libreria `<ctime>` .
2. **Switch Case:** Alternativa all'IF per scelte multiple su valori discreti/interi.