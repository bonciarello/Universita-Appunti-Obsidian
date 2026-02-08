Il polimorfismo permette di trattare oggetti di classi diverse (ma imparentate) in modo uniforme, invocando il metodo corretto a tempo di esecuzione.
### Override e Funzioni Virtuali
Per abilitare il polimorfismo, la classe base deve dichiarare i metodi come `virtual`.
```c++
class Persona {
public:
    virtual void stampa() const; // Metodo virtuale
};
```

Se `Studente` ridefinisce `stampa()`, allora:
```c++
Persona* p = new Studente();
p->stampa(); // Chiama Studente::stampa() grazie al polimorfismo
```
### Classi Astratte
Una classe è astratta se contiene almeno un **metodo virtuale puro** (`= 0`).
```c++
virtual unsigned int area() = 0;
```

Non si possono istanziare oggetti di classi astratte (`FiguraGeometrica`). Le classi derivate (`Quadrato`, `Rettangolo`) **devono** implementare i metodi puri per poter essere istanziate.    

> **Importante:** Il distruttore di una classe base polimorfica deve essere sempre `virtual` per garantire la corretta deallocazione della memoria .