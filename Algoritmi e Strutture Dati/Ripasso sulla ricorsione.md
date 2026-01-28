Noi faremo un uso massiccio della ricorsione durante il corso. Quando andiamo a richiamare una funzione, dovreste sapere che in memoria viene generato il record di attivazione della funzione che contiene un elemento per ogni parametro e per ogni elemento locale, oltre a contenere una porzione speciale per la variabile di ritorno.

![[ img/sezione3/ricorsione.PNG]]

*Una funzione è ricorsiva se richiama se stessa!*

Come esempio, vediamo il calcolo del fattoriale di un numero:

```cpp
int fact(int val) {
	if(val <= 1) return 1; // CASO BASE
	else  // QUI L'ELSE PUO' ESSERE EVITATO

	return fact(val-1) * val;
}
```

Se, per esempio, scriviamo nel nostro *main* il comando *cout<<fact(5);*, succederà:

| **Chiamate** | **Return** | **Valori** |
|--------------|------------|------------|
| fact(5) | fact(4)*5 | 120 |
| fact(4) | fact(3)*5 | 24 |
| fact(3) | fact(2)*5 | 6 |
| fact(2) | fact(1)*5 | 2 |
| fact(1) | 1 | 1 |

Come altro esempio, possiamo scrivere una funzione che ci permetta di svolgere il calcolo del n-esimo numero della sequenza di Fibonacci:

```cpp
int fibRec(const int n) {
	if((n==0) || (n==1)) return n;
	else return fibRec(n-1) + fibRec(n-2);
}
```

---

Di seguito si può trovare il valore corrispondente ad ogni chiamata:

| **Chiamate** | **Valori** |
|--------------|------------|
| fibRec(0) | 0 |
| fibRec(1) | 1 |
| fibRec(2) | 1 |
| fibRec(3) | 2 |
| fibRec(4) | 3 |
| ... | ... |
