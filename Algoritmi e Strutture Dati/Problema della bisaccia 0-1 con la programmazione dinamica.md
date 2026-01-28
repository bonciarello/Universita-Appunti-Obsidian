Affrontiamo nuovamente il problema della bisaccia 0-1, per il quale abbiamo visto precedentemente che la tecnica golosa fornisce solo una soluzione approssimata. Mostriamo ora come sia possibile ottenere una soluzione esatta utilizzando la tecnica della programmazione dinamica.

Abbiamo n oggetti, ciascuno con valore vᵢ e costo cᵢ, e abbiamo una disponibilità massima C per coprire i costi. Dobbiamo:

```cpp
void migliorOrdineDinamico(Vettore<int> r, Matrice<int>& s, Matrice<int>& m) {
    int n = r.n() - 1;
    for(int i = 1; i <= n; i++)
        m(i, j) = 0;
    for(int l = 2; l <= n; l++)
        for(int i = 1; i <= n - l + 1; i++) {
            int j = i + l - 1;
            /*
            prova a tutti i possibili valori di k da i fino a j-1
            la prima iterazione viene fatta fuori dal for, per avere un primo valore
            minimo anziche' impostare m(i, j) = +infinito
            */
            int k = i;
            m(i, j) = m(i, k) + m(k+1, j) + r[i] * r[k+1] * r[j+1];
            s(i, j) = k;
            for(k = i + 1; k <= j-1; k++) {
                int q = m(i, k) + m(k+1, j) + r[i] * r[k+1] * r[j+1];
                if(q < m(i,j)) {
                    m(i, j) = q;
                    s(i, j) = k;
                }
            } // for k
        } // for i, for l
}

template<class T>
Matrice<T> produttoria(const Vettore<Matrice<T>> v) {
    Matrice<int> s(1, v.n(), 1, v.n());
    Matrice<int> m(1, v.n(), 1, v.n());

    /*
    riempie il vettore p con le dimensioni su cui calcolare l'ordine ottimale
    */
    Vettore<int> p(1, v.n() + 1);
    p[1] = v[1].n();
    for(int i = 2; i <= v.n() + 1; i++)
        p[i] = v[i].m();
    migliorOrdineDinamico(p, s, m);
    return moltiplicaTanteMatrici(v, s, 1, v.n());
}

template<class T>
Matrice<T> moltiplicaTanteMatrici(const Vettore<Matrice<T>>& v, const Matrice<int>& s, const int i, const int j) {
    if(j > i) {
        Matrice<T> X = moltiplicaTanteMatrici(v, s, i, s(i, j));
        Matrice<T> Y = moltiplicaTanteMatrici(v, s, s(i, j) + 1, j);
        return X*Y;
    }
    else
        return v[i];
}
```
