Dato un insieme di numeri interi positivi S = {x₁, ..., xₙ} determinare se esiste un sottoinsieme R ⊆ S tale che la somma dei numeri contenuti in *R* sia esattamente uguale ad una costante data *W*.

Per esempio:

> S = {4, 12, 3, 88, 192}, W = 207

Una possibile soluzione è:

> R = {12, 3, 192}

```cpp
#include <iostream>
#include <vector>

struct Soluzione {
  // dentro sol inseriamo gli indici dei valori in S

  // la nostra sol e' un array rappresentante un sottoinsieme di R
  std::vector<int> sol;
  // l'insieme S letto da input
  std::vector<int> s;
  // il valore letto da input la cui somma di sol deve essere pari
  int w;

  Soluzione(const std::vector<int> s, const int& w):s(s), w(w) {};
};

void add(int& x, Soluzione& sol) {
  sol.sol.push_back(x);
}

void remove(Soluzione& sol) {
  sol.sol.pop_back();
}

bool isComplete(const Soluzione& sol) {
  int sum = 0;
  for(auto x: sol.sol) // itero sugli elementi di sol
    sum += sol.s[x]; // tramite l'indice x vado a prendermi il valore S[x]
  return sum == sol.w; // se sum e' esattamente W, allora sol e' completa
}

bool canAdd(const int& x, const Soluzione& sol) {
  // posso inserire x dentro alla sol se e solo se
  // - x non e' stato ancora inserito
  // - sum(sol) <= W + S[x]
  if(std::find(sol.sol.begin(), sol.sol.end(), x) != sol.sol.end())
    return false;
  int sum = 0;
  for(auto a: sol.sol)
    sum += sol.s[a];
  return sum <= sol.w + sol.S[x];
}

bool solve(Soluzione sol) {
  int x = 0;
  while(x <= sol.s.size()) {
    if(canAdd(x, sol)) {
      add(x, sol);

      if(isComplete(sol)) return true; // sol in questo caso e' un sottoinsieme la cui somma e' pari a W
      else if(solve(sol)) return true; // in una qualche successiva chiamata, ho costruito una solo completa

      remove(sol);
      x++;
    } else {
      x++;
    }
  }
  return false;
}

int main() {
  std::vector<int> s = {4, 12, 3, 88, 192};
  int w = 207;
  Soluzione sol(s, w);

  if(solve(sol)) {
    std::cout << "Il sottoinsieme con somma pari a " << w << "e'...\n";
    for(auto x: sol.sol)
      std::cout << sol.s[x] << ' ';
    std::cout << '\n';
  }
  else
    std::cout << "Non esiste un sottoinsieme di S con somma pari a " << w << ":(\n";
}
```
