---
aliases: [Oggetto, Accenni alla programmazione ad oggetti, Object Oriented, OOP]
tags: [fondamenti-di-informatica]
---
## Concetti Fondamentali
La Programmazione a Oggetti è un paradigma di programmazione dove un programma è visto come un insieme di oggetti che interagiscono tra loro.

**Modellare gli Oggetti:**
Ogni oggetto è caratterizzato da:
1. **Attributi:** Parametri che lo contraddistinguono (lo stato dell'oggetto). Es. Cilindrata, Colore.
2. **Comportamento:** Insieme di funzionalità (azioni) che specificano come si modifica lo stato a seguito di stimoli esterni. Es. Accelera, Frena.

**Classi:**
Attributi e comportamenti sono incapsulati in pacchetti di codice chiamati **Classi**.
- Una classe è un **modello** (template) per creare oggetti (definisce un nuovo tipo di dato).
- Un oggetto è un'**istanza** della classe .

**I 3 Principi della OOP:**
1. **Incapsulamento (Information Hiding):** I dettagli dell'implementazione sono nascosti. Si separa l'**Interfaccia** (metodi pubblici) dall'**Implementazione** (codice interno).
2. **Ereditarietà:** Permette di definire nuove classi a partire da altre già definite (Specializzazione/Generalizzazione) favorendo il riutilizzo del codice.
3. **[[Polimorfismo]]:** Permette di attivare a tempo di esecuzione comportamenti diversi su oggetti con un'interfaccia comune.
## Definizione di una Classe (Codice)
Le classi si definiscono usando la keyword `class`. Il corpo è racchiuso tra graffe e termina con `;`.

**Specificatori di Accesso:**
- **`public`:** Accessibili da tutti i punti del programma.
- **`private`:** Accessibili solo dalle funzioni membro della classe (default).
- **`protected`:** Simili a private ma legati all'ereditarietà .

**Metodi Principali:**
- **Costruttore:** Inizializza l'oggetto. Ha lo stesso nome della classe e nessun tipo di ritorno.
- **Getter:** Restituisce il valore di un attributo privato (Es. `getA()`).
- **Setter:** Modifica il valore di un attributo privato (Es. `setA(int val)`).

Esempio di implementazione esterna:
```c++
// Definizione
class A {
    int x;
public:
    void setX(int val);
};

// Implementazione fuori dalla classe (risolutore di scope ::)
void A::setX(int val) {
    x = val;
}
```
## [[Gestione della memoria|Gestione della Memoria]] nelle Classi (Esempio `VectorOfInt`)
Quando una classe gestisce memoria dinamica (puntatori), il costruttore di default non basta. Bisogna gestire manualmente l'allocazione e la deallocazione (Regola del Tre).
### Costruttore
Inizializza le variabili e alloca la memoria dinamica.
```c++
VectorOfInt() : size(0), capacity(10) {
    P = new int[capacity]; // Allocazione nello Heap
}
```
### Costruttore di Copia (Deep Copy)
Serve quando si crea un nuovo oggetto come copia di uno esistente. Se non definito, il compilatore fa una "copia superficiale" (copia solo il puntatore), causando errori (due oggetti puntano alla stessa memoria).

Bisogna fare una **Copia Profonda (Deep Copy)**:
```c++
VectorOfInt(const VectorOfInt& V) : size(V.size), capacity(V.capacity) {
    P = new int[capacity]; // Nuova allocazione
    for(unsigned k=0; k < size; k++) {
        P[k] = V.P[k]; // Copia dei valori
    }
}
```
### Distruttore
Viene chiamato quando l'oggetto viene distrutto. Serve a liberare la memoria allocata con `new`.
Nome: `~NomeClasse()`.
```c++
~VectorOfInt() {
    delete[] P; // Evita Memory Leak
}
```
### Operatore di Assegnamento (`operator=`)
Gestisce l'assegnamento tra oggetti già esistenti (`A = B`). Deve:
1. Controllare l'auto-assegnamento (`this != &V`).
2. Liberare la vecchia memoria (`delete[] P`).
3. Allocare nuova memoria e copiare i dati.
4. Restituire `*this`.
### Operatore Indice (`operator[]`)
Permette di accedere agli elementi come un array normale.
```c++
int& operator[](unsigned i) {
    return P[i]; // Ritorna un riferimento (permette lettura e scrittura)
}
```
## Organizzazione dei File
Per una migliore organizzazione, il codice viene diviso in:
1. **Header File (`.h` o `.hpp`):** Contiene lo "scheletro" (dichiarazioni) della classe.
    - Deve contenere le **Inclusion Guards** per evitare inclusioni multiple:
        ```c++
        #ifndef NOME_H
        #define NOME_H
        // ... Codice Classe ...
        #endif
        ```
2. **Source File (`.cpp`):** Contiene l'implementazione dei metodi. Include l'header (`#include "Nome.h"`).

**Compilazione:** Si compilano solo i file `.cpp`: `g++ main.cpp Classe.cpp -o programma`. Non si includono mai i `.cpp` negli header!.
## Enum Class
Tipo definito dall'utente con valori costanti (enumeratori).
- Sintassi: `enum class Stagione { PRI, EST, AUT, INV };`
- Accesso: `Stagione::PRI`.
- Sono fortemente tipizzati (non sono interi impliciti). Per ottenere il valore numerico serve un cast: `static_cast<int>(Stagione::PRI)`.