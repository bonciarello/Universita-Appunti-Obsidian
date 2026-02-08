### Concetti Fondamentali
La OOP è un paradigma basato sul concetto di **Oggetto**, che racchiude:
- **Attributi (Dati):** Caratteristiche dell'oggetto (es. cilindrata, colore).
- **Metodi (Comportamento):** Azioni che l'oggetto può compiere (es. accelerare, frenare).

**Classe vs Oggetto:**
- La **Classe** è il modello astratto (il progetto).
- L'**Oggetto** è l'istanza specifica creata a partire dalla classe.
### Incapsulamento e Data Hiding
I dati interni di un oggetto dovrebbero essere nascosti all'esterno (`private`) e accessibili solo tramite metodi pubblici (**Getters** e **Setters**).

**Esempio Classe `Prodotto`:**
- **File .h (Header):** Contiene la definizione della classe.
    ```c++
    class Prodotto {
    public:
        Prodotto();
        int getId() const;
        void setId(int);
    private:
        int id;
        float prezzo;
    };
    ```
- **File .cpp (Source):** Contiene l'implementazione dei metodi.
    ```c++
    int Prodotto::getId() const { return id; }
    void Prodotto::setId(int i) { id = i; }
    ```