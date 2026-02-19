---
aliases: [SDM]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
## Interfaccia Map

La **mappa** è un oggetto che collega *keys* (chiavi) a *values* (valori).
Una mappa non può contenere chiavi duplicate.

Implemenazioni di Map sono *HashMap* e *TreeMap*.

## Metodi principali di Map

```java
// Svuota la mappa
void clear();

// Restituisce true se key e' usato come chiave
boolean containsKey(Object key);

// Restituisce true se esiste almeno una chiave che mappa sul valore value
boolean contain sValue(Object value);

// Restituisce il valore associato alla chiave key, o null se la chiave non e' nella mappa
V get(Object key);

// Restituisce se la mappa e' vuota
boolean isEmpty();

// Restituisce un Set contenente tutte le chiavi
Set<K> keySet();

// Associa il valore value alla chiave key. Restituisce il precedente valore associato a key, oppure null se nessun valore era associato a key
V put(K key, V value);

// Restituisce una Collection di tutti i valori contenuti nella mappa
Collection<V> values();
```

## Esempi di implementazione di Map

Dato un array di marche, vogliamo stampare il numero di occorrenze di ogni marca all'interno dell'array.

```java
String[] marche = {"fiat", "fiat", "toyota", "mercedes", "toyota", "ferrari"};
Map<String, Integer> marcheOcc = new TreeMap<String, Integer>();
for(String marca : marche) {
    if(!marcheOcc.containsKey(marca))
        marcheOcc.put(marca, 0);
    marcheOcc.put(marca, marcheOcc.get(marca)+1);
}
System.out.println(marcheOcc);
```

## HashMap

Le HashMap sono un'implementazione dell'interfaccia Map.
Si basano sul concetto di *hash*.
L'implementazione di HashSet usa internamente una HashMap.

![](hashMap.png)

L'implementazione si basa su un array di liste.

Ogni stringa è convertita in un intero positivo, detto *hash*, che rappresenta la posizione nell'array.

L'efficienza dell'HashMap dipende da due parametri: la capacità iniziale dell'array (default 16) e il load factor (default 0.75). Quando il numero di elementi inseriti eccede il prodotto tra load factor e la capacità attuale dell'array, viene fatto un ridimensionamento dell'array in cui viene orientativamente raddoppiato.

## HashMap in pratica
Consideriamo l'implementazione della classe Persona con il metodo **equals**.

```java
import java.util.HashMap;

public class ProvaHashMap {
    public static void main(String[] args) {
        HashMap<Persona, String> m = new HashMap<Persona, String>();
        Persona p1 = new Persona("MRARSS80A01H501Z");
        Persona p2 = new Persona("MRARSS80A01H501Z");
        m.put(p1, "Roma");
        if(m.containsKey(p2))
            System.out.println("Trovato nella map!");
        else
            System.out.println("Non trovato nella map!");
    }
}
```

In questo caso il metodo **containsKey** non riesce a capire che *p1* e *p2* rappresentano la stessa persona, nonostante il metodo *equals*.

### Il metodo HashMap

Per implementare il corretto funzionamento va ridefinito *anche* il metodo **int hashCode();** dove l' intero restituito è il codice hash per l'oggetto.

Il metodo hashCode() deve rispettare queste regole generali:

1. quando è eseguito sullo stesso oggetto più volte deve restituire sempre lo stesso intero;
2. se due oggetti sono uguali eseguendo il metodo equals, allora il metodo hashCode dovrebbe restituire lo stesso intero;
3. se due oggetti non sono uguali eseguendo il metodo equals, allora hashCode non deve per forza restituire numeri diversi;
4. comunque è preferibile che due oggetti diversi tra di loro restituiscano interi diversi, questo garantisce performance migliori.

```java
public int hashCode() {
	return cf.hashCode();
}
```
