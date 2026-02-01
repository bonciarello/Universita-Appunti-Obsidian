## Package

I package sono il meccanismo attraverso il quale si possono creare librerie di classi correlate (come le librerie C++). Ad esempio **java.lang** o **java.io** visti precedentemente.

Tutte le classi facenti parte di un package iniziano con la direttiva **package <nome>**, dove **<nome>** è l'identificativo del package.

Per utilizzare le classi di un package all'interno di altre classi, è necessario specificare direttive **import** (simile a *include* del C++) nell'intestazione delle classi utilizzatrici.

È possibile che i package contengano sotto-package.

Ad esempio:
- **import java.util.Scanner** importa la classe **Scanner**, del package **java.util** (sottopackage di *java*);
- **import java.io.\*** importa tutte le classi e gli eventuali sotto-package di **java.io**;
- se usiamo una classe che è all'interno dello stesso package non è necessario importarla.

## Moduli

A partire da Java 9 è stato introdotto un nuovo livello di astrazione superiore ai package, conosciuto come **Java Platform Module System (JPMS)**, o semplicemente **moduli**.

I **moduli** sono gruppi di package legati tra di loro e gruppi di risorse. Orientativamente è come se fossero *"package di package"*.

Ogni modulo è responsabile delle proprie risorse, come ad esempio immagini, eventuali file, ecc.

Per utilizzare i moduli si deve realizzare un file descrittivo che definisce diversi aspetti del modulo che stiamo creando, come il nome del modulo, le dipendenze (altri moduli da cui il modulo dipende), una lista di package pubblici (cioè, package che vogliamo rendere visibili da altri moduli), ecc.

I package all'interno di un modulo sono di default privati, questo significa che dobbiamo esplicitamente renderli pubblici (nel file descrittivo) se vogliamo renderli disponibili anche fuori dal modulo che stiamo creando.

## Input da console

```java
import java.util.Scanner;

public class EsempioLetturaInput {
    public static void main (String[] args) {
        Scanner in = new Scanner(System.in);
        System.out.println("Primo numero");
        int m = in.nextInt();
        System.out.println("Secondo numero");
        int n = in.nextInt();
        in.close();
        int z = m + n;
        System.out.println("Somma: " + z);
    }
}
```

La classe *Scanner* della libreria *java.util* mette a disposizione metodi per **iterare** oggetti di tipo *InputStream* tra cui anche *System.in*.

Esiste un metodo *next<...>* per **quasi** tutti i tipi primitivi, ad esempio *nextInt*, *nextLong*, ecc.

Per le stringhe ci sono i metodi *next* (prossima stringa) e *nextLine* (prossima riga).

## Classi

```java
public class Esempio {
    public Esempio() {
    }

    public Esempio(int c) {
        campo = c;
    }
    public int getCampo ( ) {
        return campo;
    }
    public void setCampo (int c) {
        this.campo = c;
    }

    int campo;
    public int campoPubblico;
    private int campoPrivato;
    protected int campoProtected;
}
```

Abbiamo già visto che ogni file .java contiene, solitamente, una sola dichiarazione e definizione di classe.

La classe si dichiara con l'istruzione **public class NomeClasse**.

Gli specificatori di accesso (*public, private, protected*) vanno inseriti prima della dichiarazione del campo o del metodo.

Se un campo o un metodo non ha uno specificatore di accesso, *sarà visibile nella sua classe e nelle classi appartenenti allo stesso package*.

Gli oggetti di una classe si creano con l'istruzione:

```java
    NomeClasse oggetto = new NomeClasse();
    Esempio e1 = new Esempio();
    Esempio e2 = new Esempio(4);
```

## Riferimenti
Facciamo un esempio, creiamo una stringa:

> *String mystr = "questa e' una prova";*

Quando creiamo una stringa succede quello che segue:

![](riferimento.PNG)

*Cosa sono i riferimenti?* Concettualmente sono simili ai puntatori del C++: un riferimento *rappresenta* un indirizzo di memoria dove è stato allocato un oggetto ma a differenza del puntatore non è un indirizzo di memoria.

Importante: **non vanno deallocati!**

Facciamo un altro esempio, creiamo un oggetto:

> *Esempio e1 = new Esempio();*

Qual è il riferimento di e1? *Esempio@6d06d69c*

A sinistra del simbolo @ c'è il tipo dell'oggetto, nel nostro caso *Esempio*.
A destra del simbolo @ c'è un valore esadecimale che rappresenta l'indirizzo effettivo dell'oggetto.

**Se si prova a stampare e1, si ottiene in output il riferimento di e1.**

## Assegnamento degli oggetti
Ecco un esempio di assegnamento degli oggetti:

```java
public class Main {
    public static void main(String[] args) {
        Esempio e1 = new Esempio (1);
        Esempio e2 = new Esempio (2);
        System.out.println(e1.getCampo()); // Risultato: 1
        System.out.println(e2.getCampo()); // Risultato: 2
        e2 = e1;
        System.out.println(e2.getCampo()); // Risultato: 1

        e2.setCampo(3) ;
        System.out.println(e2.getCampo()); // Risultato: 3
        System.out.println(e1.getCampo()); // Risultato: 3

        e1.setCampo(5) ;
        System.out.println(e1.getCampo()); // Risultato: 5
        System.out.println(e2.getCampo()); // Risultato: 5
    }
}
```

## Passaggio degli argomenti ai metodi

```java
public class Esempio {
    private int campo;

    public Esempio(int c) {
        campo = c;
    }

    public int getCampo() {
        return campo;
    }

    public void modifica(Esempio e1, Esempio e2) {
        campo = e1.campo + e2.campo;
        e1.campo=0;
        e2.campo=0;
        e1 = new Esempio(8); //Ha effetto solo nel metodo
        System.out.println(e1.getCampo()); // Risultato: 8
    }

    public static void main(String[] args) {
        Esempio e1 = new Esempio(1);
        Esempio e2 = new Esempio(2);
        Esempio e3 = new Esempio(5);
        System.out.println(e3.getCampo()); // Risultato: 5

        e3.modifica(e1, e2);
        System.out.println(e1.getCampo()); // Risultato: 0
        System.out.println(e2.getCampo()); // Risultato: 0
        System.out.println(e3.getCampo()); // Risultato: 3
    }
}
```

Se l'argomento è un tipo base (int, float, ecc.), allora viene fatta una copia dell'elemento.

Se l'argomento è una variabile riferimento, allora viene fatta una copia del suo riferimento.

## Metodi statici

```java
public class MyMath {
    public static int gcd(int m, int n) {
        if(n == 0)
            return m;
        else
            return gcd(n, m%n);
    }
}

public class TestMyMath {
    public static void main(String[] args) {
        int result = MyMath.gcd(60, 24);
        System.out.println(result);
    }
}
```

I metodi **static** possono essere invocati utilizzando il nome della classe.

Non possono utilizzare campi o invocare metodi che non siano **static**.

I campi **static** sono condivisi da tutte le istanze degli oggetti.

## Oggetti speciali: stringhe

Gli oggetti *String* rappresentano sequenze di caratteri alfanumerici e sono **immutabili**.

È possibile accedere ad un qualsiasi carattere con il metodo *charAt*, ma non è possibile modificarlo.

Data una stringa a, gli indici dei caratteri vanno da *0* a *a.length() - 1*. Esistono metodi per l'uguaglianza e per la concatenazione.

L'istruzione *String c = a + b* dove crea una **nuova** stringa con i contenuti di a concatenati a quelli di b.

### Esempio di stringhe

```java
import java.util.Scanner;

public class TestString {
    public static void main ( St ring [ ] args ) {
        Scanner in = new Scanner(System.in);
        String text1 = in.nextLine();
        String text2 = in.nextLine();

        // Confronto del riferimento
        if(text1 == text2)
            System.out.println("Confronto ==" );

        // Confronto del valore
        if(text1.equals(text2))
            System.out.println("Confronto equals");

        // Confronta ignorando maiuscole/minuscole
        if(text1.equalsIgnoreCase(text2))
            System.out.println("Confronto equalsIgnoreCase");

        in.close();
    }
}
```

### Classi StringBuffer e StringBuilder

```java
String x = "Testo"; // Un oggetto String
String y = x + "!"; // Un nuovo oggetto String

StringBuffer z = new StringBuffer(x);
z.append("!");
z.setCharAt(0 ,'t' );

StringBuilder w = new StringBuilder(x);
w.append("!");
w.setCharAt(0 ,'u' );
```

La classe *String* costruisce oggetti immutabili.

Ogni operazione su oggetti *String* che ha come risultato *String*, genera nuovi oggetti.

Le classi *StringBuffer* e *StringBuilder* mettono a disposizione **oggetti mutabili** per la gestione di testi.

*StringBuilder* è tipicamente più efficiente di *StringBuffer* ma non garantisce la sincronizzazione nella gestione multi-thread.

### Differenze di performance

```java
public class Main {
    private static String stampaConString() {
        String s = "";
        for(int i = 0; i < 100000; i++)
            s += i;
        return s;
    }

    private static String stampaConStringBuilder() {
        StringBuilder s = new StringBuilder();
        for(int i = 0; i < 100000; i++)
            s.append(i);
        return s.toString();
    }

    public static void main(String[] args) {
        long startTime = System.nanoTime();
        stampaConString();
        long stopTime = System.nanoTime();
        double duration = (double) (stopTimestartTime) / 1000000000;
        System.out.println("Durata primo metodo: " + duration + " secondi");
        startTime = System.nanoTime();
        stampaConStringBuilder();
        stopTime = System.nanoTime();
        duration = (double) (stopTimestartTime) / 1000000000;
        System.out.println("Durata secondo metodo: " + duration + " secondi");
    }
}
```
