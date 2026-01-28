## Edit Distance

La traccia del problema è la seguente:

> *Date due stringhe x e y, la **edit distance** tra x e y è definita come il numero minimo di edit operation da effettuare per trasformare x in y.*
>
> **ESEMPIO:** stringa x *"astro"*, stringa y *"arto"*, *editDistance(x, y) = 2*

Le edit operation sono:

- **INSERTION:** inserire un simbolo in una qualunque posizione;
- **DELETION:** cancellare un simbolo da una qualunque posizione;
- **SUBSTITUTION:** sostituire un simbolo c con un simbolo c'.

I **sottoproblemi** sono rappresentati dalla edit distance tra i prefissi delle stringhe.
Con il **bottom-up** risolviamo prima i sottoproblemi semplici (la edit distance tra due simboli), per poi usarli nella risoluzione dei sottoproblemi più difficili (la edit distance tra due prefissi).

*Come definiamo le relazioni tra i sottoproblemi (come uso la soluzione di un sottoproblema per un altro?)?* Siano i (risp. j) una posizione nelle stringhe x (risp. y), allora:

![[ img/sezione20/editDistance.PNG]]

```cpp
#include <iostream>
#include <string>

unsigned min(unsigned src1, unsigned src2, unsigned src3) {
  if(src1 > src2) {
    if(src2 > src3) {
      return src3;
    } else if(src2 < src3) {
      return src2;
    }
  } else if(src1 < src2) {
    if(src1 > src3) {
      return src3;
    } else if(src1 < src3) {
      return src1;
    }
  }
}

unsigned editDistance(const std::string & x, const std::string& y) {
  // d è una matrice x.size() + 1 * y.size() + 1
  unsigned** d = new unsigned*[x.size() + 1];
  for(unsigned i = 0; i <= x.size(); ++i)
    d[i] = new unsigned[y.size() + 1];

  // numero di operazioni per trasformare la stringa vuota in x
  // esempio: d[4][0] = 4 -> ho bisogno di 4 edit operation per trasformare
  // la stringa vuota in x (inserimento di 4 simboli)
  for(unsigned i = 0; i <= x.size(); ++i)
    d[i][0] = i;

  // numero di operazioni per trasformare la stringa vuota in y
  for(unsigned j = 0; j <= y.size(); ++j)
    d[0][j] = j;

  for(unsigned i = 1; i <= x.size(); ++i)
    for(unsigned j = 1; j <= y.size(); ++j) {
      int s = 0;
      if(x[i-1] != y[j-1])
        s = 1;

      d[i][j] = min(
        d[i-1][j] + 1,
        d[i][j-1] + 1,
        d[i-1][j-1] + s
      )
    }

  unsigned distance = d[x.size()][y.size()];

  for(unsigned i = 0; i <= x.size(); ++i)
    delete[] d[i];
  delete[] d;

  return distance;
}

int main() {
  unsigned d = editDistance("astro", "arto");
  std::cout<<"La edit distance tra astro e arto e' "<<d<<'\n';
  return 0;
}
```

## DivCoins

La traccia del problema è la seguente:

> *Dato un sacchetto contenente n monete, dove ogni moneta ha un valore positivo, determinare **la più equa divisione** delle monete tra **due persone**.*
>
> Con equa divisione si intende la **differenza** tra la somma delle monete date alla prima persona e la somma delle monete date alla seconda persona deve essere la **minima** possibile (tra tutte le possibili divisioni).
>
> **ESEMPIO:** *{10, 4, 6, 3, 8, 2}*, una equa divisione è *{10, 6}, {8, 4, 3, 2}* perché |16 - 17| = 1

I possibili approcci sono:

- **BRUTEFORCE:** provare tutte le possibili divisioni. Il *punto positivo* è soluzione ottima assicurata, il *punto negativo* è complessità temporale enorme.
- **GREEDY:** uno stage dell'algoritmo greedy sceglie se inserire o meno la monete corrente in una partizione e il criterio della scelta è *locale* (si basa solo sulle monete inserite fino a questo punto). Il *punto positivo* è complessità lineare, il *punto negativo* è soluzione ottima non assicurata.
- **ALTRI APPROCCI?**
  - Variazione del problema **subset sum** (dato un insieme I di numeri positivi e una soglia T, stabilire se esiste un sottoinsieme *I'* tale che sum(I') ≤ T e sia la più grande possibile);
  - Variazione del problema **coin change** (dato un numero N e un insieme di monete M, in quanti modi possiamo ottenere N usando le monete di M?)

Dobbiamo affrontare due fasi:

- **FASE 1, determinare tutte le possibili somme:** quali sono le possibili somme che possiamo fare con tutte le monete? Enumerare tutte le possibili somme in un modo intelligente.
- **FASE 2, qual è la somma, tra tutte le possibili, che ci permette di ottenere la differenza minima?** Manteniamo una variabile min indicante la differenza minima possibile (inizialmente = T, dove T è la somma di tutte le monete), e aggiorniamo *min* se troviamo una somma che ci permette di avere una differenza più piccola di *min*.
