---
aliases: [GE, Gestione errori]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Durante la fase di sviluppo del codice è normale avere errori software.

```java
import java.util.Scanner;
public class Calcolatrice {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int numero1 = in.nextInt();
        int numero2 = in.nextInt();
        System.out.println(numero1/numero2);
        in.close();
    }
}
```

## Gli assert

Come in C++, anche in java è presente la keyword **assert**:

```java
assert espressione;
assert espressione : espressione2;
```

Se un assert fallisce, il programma **termina**!

Per abilitare l'uso di assert è necessario aggiugere l'opzione -ea alla JVM. In Eclipse: *Menu Run -> Run Configurations*, a sinistra selezionare *Java Application -> Nome Progetto* e poi sulla destra selezionate *Arguments* e poi nel campo VM arguments aggiungere -ea.

```java
public class TestAssertion {
    public void test(int n) {
        assert n % 2 != 0 : "Mi aspetto un numero dispari";
        System.out.println(n);
    }
}
```

## Eccezioni

La gestione delle eccezioni è un meccanismo offerto da alcuni linguaggi di programmazione per intercettare e gestire potenziali errori software.

Attraverso la gestione delle eccezioni:

- si può evitare che un programma termini bruscamente in presenza di un errore software;
- si può cercare di riparare all'errore in modo opportuno;
- si può separare la parte di gestione degli errori dal codice di un programma.

## La classe Exception

La classe Exception e le sue sottoclassi estendono Throwable, ciò indica una condizione che un'applicazione dovrebbe prevedere.

Le eccezioni sono gestite attraverso le keyword:

- **try/catch:** con try si può specificare un blocco di codice in cui potrebbe verificarsi un'eccezione, mentre con catch si specifica quale eccezione si vuole gestire e inserire il codice per gestirle;
- **finally:** permette di specificare un blocco di codice le cui istruzioni saranno eseguite sempre, a prescindere che si sia verificata o meno un'eccezione;
- **throws:** si inserisce vicino la definizione di un metodo e permette di rimandare la gestione dell'eccezione nei punti in cui il metodo è chiamato;
- **throw:** consente di lanciare una determinata eccezione software.

### Try/Catch

```java
try {
	...
}
catch(ExceptionT1 | ExceptionT2 | ... | ExceptionTN) {
	...
}
```

Prova ad eseguire il codice che si trova nel blocco del *try*, se si verifica un'eccezione tra quelle indicate nel *catch*, allora esegui il blocco del codice che si trova nel catch.

```java
int numero1 = in.nextInt();
int numero2 = in.nextInt();
try {
    System.out.println(numero1/numero2);
} catch(ArithmeticException e) {
    e.printStackTrace(); // solo in fase di sviluppo
}
```

### Finally

```java
try { ... }
catch (ExceptionT1 | ExceptionT2 | . . . | ExceptionTN) {
	...
}
finally { ... }
```
oppure:
```java
try { ... }
finally { ... }
```
Il codice che si trova nel blocco finally è eseguito sempre, indipendentemente se si è verificata un'eccezione o meno.

```java
import java.util.Scanner;
public class Calcolatrice {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int numero1 = in.nextInt();
        int numero2 = in.nextInt();
        try {
            System.out.println(numero1/numero2);
        } catch(ArithmeticException e) {
            System.out.println("Divisione per 0!");
        } finally {
            in.close();
        }
    }
}
```

### Throws

```java
public void foo() throws ExceptionT1, ExceptionT2, ..., ExceptionTN { ... }
```

Rimanda la gestione delle eccezioni ai metodi che chiamano foo().

```java
public void div(int a, int b) throws ArithmeticException {
    System.out.println(a/b);
}

public void calcola() {
    try {
        div(4, 0);
    } catch(ArithmeticException e) {
        e.printStackTrace();
    }
}
```

### Throw

```java
throw new ExceptionType();
```
Lancia un'eccezione del tipo indicato.

```java
public void aggiungiVoto(int voto) throws Exception {
	if(voto < 0 || voto > 30) {
		throw new Exception("Voto atteso tra 0 e 30");
	}
	...
}
```

## Tipi di eccezioni

Java distingue due tipi di eccezioni:
- **eccezioni unchecked:** sono le eccezioni che non è obbligatorio gestire, ad esempio *ArrayOutOfBoundsException* e *NullPointerException*;
- **eccezioni checked:** sono le eccezioni che vanno sempre gestite, ad esempio *IOException*;

## Dichiarare nuovi eccezioni

Definizione di **eccezioni unchecked**:

```java
public class MyException extends RuntimeException
```
Definizione di **eccezioni checked**:

```java
public class MyException extends Exception
```

## Errori concettuale

In alcuni casi gli errori non sono individuabili (o lo sono in rari casi) attraverso il meccanismo delle eccezioni.
Spesso questi errori sono dovuti alla mancata lettura della documentazione di Java o alla mancata applicazione delle regole prescritte.
Un esempio è non rispettare il contratto del metodo *equals*.

```java
public class Persona { // Implementazione errata!
    private String cf;
    public Persona(String cf) { this.cf = cf; }
    public setCodiceFiscale(String cf) { this.cf = cf; }
    public boolean equals(Object o) {
        if(o instanceof Persona) {
            Persona p = (Persona) o;
            return cf.equals(p.cf);
        }
        return false;
    }
    public int hashCode() {
        return cf.hashCode();
    }
}
```

**Quali errori vedete?**
Cambiare il codice fiscale (attraverso il metodo set) potrebbe portare a dei problemi nel caso in cui la persona sia usata insieme a HashMap.
*instanceof* restituisce true anche se si usa una sottoclasse di Persona e questo
potrebbe portare ad errori. Ad esempio, se la classe Studente implementasse il
metodo equals allo stesso modo non si rispetterebbe la simmetria:

```java
Persona p1 = new Persona();
Persona p2 = new Studente();
```
*p1.equals(p2)* potrebbe essere diverso da *p2.equals(p1)*.
