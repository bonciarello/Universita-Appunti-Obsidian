## Strutture dati in Java

Quando parliamo di strutture dati in Java, parliamo di **Collections**.

Una collezione è costituita da un gruppo di oggetti che sono tra loro collegati. Una collezione è anche definita utilizzando il termine contenitore. All'interno delle collezioni è possibile effettuare ricerche, aggiungere/rimuovere elementi ecc. Java fornisce una libreria/framework per la gestione delle strutture dati più comuni.

Quando parliamo di collections ci riferiamo a *List* (Vector, ArrayList, LinkedList), *Stack*, *Queue* e *Set*.

## I metodi principali

```java
// Aggiunge un elemento
boolean add (T t);

// Rimuove tutti gli elementi
void clear();

// Restituisce true se obj e' nella collection, false altrimenti
boolean contains(Object obj);

// Restituisce true se la collection e' vuota
boolean isEmpty();

// Rimuove obj , restituisce true se presente, false altrimenti
boolean remove(Object obj);

// Restituisce la dimensione della collection
int size();
```

## List

L'interfaccia List rappresenta una sequenza di elementi.
List è un'interfaccia che poi è implementata da diverse classi.
- Vector e ArrayList:
    - a livello concettuale sono molto simili all'implementazione di vector in C++;
    - Vector è tipicamente più lento perché è thread-safe;
    - ArrayList è più veloce ma non è thread-safe.
- LinkedList
    - rappresenta un'implementazione di una lista doppiamente concatenata, simile a list in C++.

### Uso pratico

```java
Vector<String> v = new Vector<String>();
// OPPURE List<String> v = new Vector<String>();
// OPPURE List<String> v = new ArrayList<String>();
// OPPURE List<String> v = new LinkedList<String>();
// QUALSIASI STRUTTURA DATI SUPPORTA I METODI SEGUENTI
v.add("Marco");
v.add("Maria");
v.add("Simone");
v.add("Chiara");
for(String s : v) {
    System.out.println(s);
}
System.out.println(v.get(2)); // Output: Simone
v.set(0, "Mauro"); // Modifica Marco in Mauro
System.out.println(v); // Output: [ Mauro , Maria , Simone , Chiara ]
v.remove(2); // Rimuove l'elemento in posizione 2 e restituisce l'elemento eliminato
v.remove("Chiara"); // Rimuove la prima occorrenza, restituisce true se presente
System.out.println(v); // Output: [ Mauro , Maria ]
v.clear(); // Svuota il vettore
System.out.println(v.size()); // Output: 0
```

## Metodo contains

*File Persona.java*
```java
public class Persona {
	private String cf;
	public Persona(String cf) { this.cf = cf; }
}
```
*File ProvaLista.java*
```java
import java.util.ArrayList;
public class ProvaLista {
	public static void main(String[] args) {
		ArrayList<Persona> persone = new ArrayList<Persona>();
		Persona p1 = new Persona("MRARSS80A01H501Z");
		Persona p2 = new Persona("MRARSS80A01H501Z");
		persone.add(p1);
		if(persone.contains(p2))
			System.out.println("Trovato!");
		else
			System.out.println("Non trovato!");
	}
}
```

Nell'esempio precedente il metodo *contains* non riesce a capire che **p1** e **p2** rappresentano la stessa persona. Per implementare il corretto comportamento è necessario ridefinire il metodo *equals*.

## Metodo equals

```java
public boolean equals(Object obj);
```

Restituisce *true* se l'oggetto obj è "uguale" all'oggetto con cui è confrontato.
Implementa una **relazione di equivalenza** su oggetti non nulli.
L'implementazione di default di Object prevede il controllo sui riferimenti, quindi restituisce *true* se e solo se si sta confrontando lo stesso riferimento.
Ogni volta che ridefiniamo equals dovremmo anche ridefinire il metodo **hashCode** (che vedremo più avanti).

### Regole da rispettare nella ridefinizione

1. **riflessività:** per ogni riferimento non nullo x, x.equals(x) deve restituire true;
2. **simmetria:** per ogni coppia di riferimenti non nulli x e y, x.equals(y) deve restituire true se e solo se y.equals(x) restituisce true;
3. **transitività:** per ogni tripla di riferimenti non nulli x, y e z, se x.equals(y) restituisce true e y.equals(z) restituisce true, allora x.equals(z) deve restituire true;
4. **consistenza:** per ogni coppia di riferimenti non nulli x e y, invocare più volte x.equals(y) deve ottenere sempre lo stesso risultato (posto che gli elementi usati per il confronto non siano stati modicati);

### Implementazione

```java
public boolean equals(Object o) {
	// regola 1 (riflessivita')
	if(this == o) return true;
	// regola 5
	if(o == null) return false;
	// se le classi sono diverse i due oggetti sono diversi
	if(this.getClass() != o.getClass()) return false;
	// qui siamo sicuri che o sia un oggetto della classe Persona
	Persona p = (Persona) o;
	// qui facciamo il controllo che le due persone siano uguali
	return codiceFiscale.equals(p.codiceFiscale);
}
```

## Classe Stack

La **pila** (o **stack**) rappresenta un gruppo di elementi disposti secondo un criterio LIFO (Last In First Out), in cui l'ultimo elemento inserito è il primo ad essere processato.
Le operazioni principali sono *aggiunta*, con cui un elemento è aggiunto alla pila, e *rimozione*, con cui un elemento è rimosso dalla pila.

## Interfaccia Queue

La **coda** (o **queue**) rappresenta un gruppo di elementi disposti secondo un criterio FIFO (First In First Out), in cui il primo elemento inserito è il primo ad essere processato.
Le operazioni principali sono aggiunta, con cui un elemento è *aggiunto* alla coda, e *rimozione*, con cui un elemento è rimosso dalla coda.
Un'implementazione concreta è *PriorityQueue*.

## Interfaccia Set

Set implementa una collection che non contiene duplicati.
Due implementazioni concrete: *HashSet* e *TreeSet*.
*HashSet:* è un'implementazione basata sul concetto di hash, non c'è garanzia che l'ordine degli elementi non cambi. **Attenzione:** per usare HashSet in modo opportuno con oggetti creati da noi dobbiamo ridefinire il metodo hashCode.
*TreeSet:* gli elementi sono ordinati seguendo il loro ordine naturale oppure usando un **Comparatore**.

### Esempio HashSet

```java
Set<String> set = new HashSet<String>();
set.add("aaa");
set.add("aaa");
set.add("aaa");
set.add("bbb");
set.add("ddd");
set.add("ccc");
System.out.println(set); // Possibile output: [ aaa , ccc , bbb , ddd ]
```

### Esempio TreeSet

```java
Set<String> set = new TreeSet<String>();
set.add("aaa");
set.add("aaa");
set.add("aaa");
set.add("bbb");
set.add("ddd");
set.add("ccc");
System.out.println(set); // Possibile output: [ aaa , bbb , ccc , ddd ]
```
