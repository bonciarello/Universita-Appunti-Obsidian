---
aliases: [Introduzione, parte 1]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
## Programmare in Java
Cosa serve?
- Java Development Kite (JDK), a lezione utilizzeremo la open JDK versione 15
- Un editor di testo o un IDE di sviluppo (Eclipse)

## Compilatore e macchina virtuale

![](compilatore.PNG)

- Codice sorgente: file .java
- Solitamente, il file .java contiene una sola dichiarazione e definizione di classe "esterna".
- Bytecode: file .class La Java virtual machine (JVM) interpreta il bytecode ed esegue il programma. La JVM è specifica per la piattaforma, ma l'ambiente di esecuzione è uniforme.
- Il file .java è compilato, il file .class è interpretato.

## Hello World in Java
Create il file HelloWorld.java:
```java
public class HelloWorld {
	public static void main(String[] args) {
		System.out.println("hello world!");
	}
}
```

Compilare ed eseguire un programma Java:
- Compilazione: *javac HelloWorld.java*
- Esecuzione: *java HelloWorld, java -cp. HelloWorld (windows)*
- La compilazione produce un file *HelloWorld.class*
- Il file *.class* contiene le istruzioni in un linguaggio intermedio, detto **bytecode**, che successivamente verrà interpretato.

Regole:
- Ogni file deve contenere una classe con esattamente lo stesso nome del file (anche le lettere maiuscole e minuscole)
- A differenza del C++, la keyword **public**, **private** o **protected** va inserita prima di ogni campo, metodo o classe.
- Il main riceve un array di stringhe che rappresentano i parametri da linea di comando del programma.

## Parametri da linea di comando
```java
> java AnApplication ciao mondo
ARGOMENTI: ciao e mondo

> java AnApplication
NESSUN ARGOMENTO DA LINEA DI COMANDO
```

- Ogni stringa di caratteri separata da spazio che segue l'invocazione di AnApplication è un **parametro da linea di comando**.
- Ogni parametro viene messo a disposizione come elemento dell'array *args* (nome arbitrario) passato al main.
- Se *args* è vuoto, nessun parametro è stato fornito.

## Stampa su standard output
```java
System.out.println("Questo e' un messaggio!")
```

- *System* è una **classe** della libreria *java.lang* (la libreria è inclusa automaticamente)
- *out* è un **campo pubblico** di System ed è uno oggetto della classe *PrintStream* (libreria *java.io*) collegato allo "standard output"
- *println* è un metodo della classe *PrintStream* per la stampa di vari tipi di dato
- Esiste anche il metodo *print* che è simile ma non va a capo dopo l'output
- Le stringhe sono oggetti della classe **String**

## Convenzione sui nomi
Per naming convention si intende la regola di scrittura da utilizzare per la scelta dei nomi all'interno del programma. È importante perché gli altri programmatori che leggono il vostro codice assumono la notazione e quindi migliora la leggibilità del codice!

Le regole principali sono:
- I nomi delle classi iniziano per lettera minuscola. Nel caso in cui il nome della classe sia composto da più parole si deve utilizzare la lettera maiuscola per ogni parola. Ad esempio: *class StudenteLavoratore*
- I nomi delle variabili e dei metodi iniziano per lettera minuscola. Nel caso in cui il nome sia composto da più parole si deve utilizzare la lettera maiuscola per le parole successive alla prima. Ad esempio: *int numeroIscritti*

## I tipi base
In Java abbiamo 8 tipi base:
- byte : 8 bit
- short: 16 bit
- int: 32 bit
- long. 64 bit
- float: 32 bit
- double: 64 bit
- boolean: 8 bit e può essere *true* o *false*
- char: 16 bit

Come in C++ le variabili si dichiarano *tipo nome;*.
Esempi:
```java
int a;
char c;
int a = 10;
```

## Array
- È possibile creare un array **A** di un determinato tipo e di lunghezza **N** con l'istruzione: *tipo[] A = new tipo[N];*. Esempio,
```java
int[] A = new int[10];
```
- Un array può essere creato assegnando dei valori
```java
int[] A = {6,2,4,8};
```
- Per conoscere la size dell'array si può utilizzare il campo *length*. Nell'esempio precedente, *A.length* è uguale a **4**.
- Come in C++, gli indici partono da 0 e arrivano alla lunghezza -1.
- Si può accedere ad una posizione usando le parentesi quadre. Esempio:
```java
a[2] = 5;
```

## Matrici
- È possibile creare una matrice **M** di un determinato tipo, **R** righe e **C** colonne, con l'istruzione: *tipo[][] M = new tipo[R][C];*. Esempio:
```java
int[][] M = new int[2][3];
```
- Una matrice può anche essere creata assegnando dei valori
```java
int[][] M = {{1,2,3},{4,5,6}};
```
- Per conoscere il numero di righe di una matrice si può utilizzare *length*. Nell'esempio precedente, *M.length* è uguale a **2**. L'istruzione *M[0].length* restituisce **3**.
- Si può accedere ad una posizione usando le parentesi quadre, *m[0][1] = 7;*.
- È possibile creare matrici dove ogni riga ha una dimensione diversa:
```java
int[][] M = new int[2][];
m[0] = new int[3];
m[1] = new int[4];
```

## If / else
Create il file ProvaIfElse.java
```java
public class ProvaIfElse {
    public staic void main(String[] args) {
        int a = 0;
        if(args.length > 2) {
            a++;
            System.out.println(a);
        } else {
            a--;
        }

        if(a == -1)
            System.out.println(a);
    }
}
```

## Switch
Create il file ProvaSwitch.java
```java
public class ProvaSwitch {
    public staic void main(String[] args) {
        switch(args.length) {
            case 0:
                System.out.println("Nessun parametro");
                break;
            case 1:
                System.out.println("1 parametro");
                break;
            case 2:
                System.out.println("2 parametri");
                break;
            default:
                System.out.println(">2 parametri");
                break;
        }
    }
}
```

## Cicli FOR
Create il file ProvaFor.java
```java
public class ProvaFor {
    public staic void main(String[] args) {
        String[] automobili = {"panda", "golf", "corsa"};

        //FOR classico
        for(int i = 0; i < automobili.length; i++) {
            System.out.println("automobili[i]");
        }

        //FOR "enhanced"
        for(String auto : automobili) {
            System.out.println(auto);
        }
    }
}
```

## Cicli WHILE
Create il file ProvaWhile.java
```java
public class ProvaWhile {
    public staic void main(String[] args) {
        String[] automobili = {"panda", "golf", "corsa"};

        //WHILE
        int i = 0;
        while(i < automobili.length) {
            System.out.println(automobili[i]);
            i++;
        }
    }
}
```
