### Stringhe (`std::string`)
La classe `string` semplifica la gestione del testo rispetto agli array di char del C.
- **Concatenazione:** `test += "casa";`.
- **Sottostringa:** `test.substr(5, 10);`.
- **Ricerca:** `test.find("abc");`.
- **Input:** `getline(cin, test);` per leggere intere righe inclusi gli spazi.
### Template
I Template permettono di scrivere codice generico che funziona con qualsiasi tipo di dato. Esempio: Classe `Pair<T>` per gestire una coppia di valori di tipo `T`.
```c++
template <class T>
class Pair {
    T first;
    T second;
public:
    Pair(T f, T s) { first = f; second = s; }
    T getFirst() const { return first; }
};
```

Questo permette di creare `Pair<int>`, `Pair<float>`, `Pair<string>`, ecc., riutilizzando lo stesso codice .