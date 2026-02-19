---
aliases: [ER]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Un'espressione regolare è una stringa di testo che può essere utilizzata per trovare dei pattern all'interno di un'altra stringa.

La classe di riferimento per le espressioni regolari in Java è la classe **Pattern** che si trova nel package *java.util.regex.Pattern*.
La classe Pattern si può usare in due modi:
- usando il metodo statico **matches**, che restituisce true se input soddisfa l'espressione regolare, false altrimenti;
- usando il metodo statico **compile**, che permette di compilare un'espressione regolare e poi utilizzarla più volte.

## Metodo matches

Come si usa?

```java
public boolean test(String regex, String input) {
    boolean res = Pattern.matches(regex, input);
    if(res)
        System.out.println("Espressione regolare soddisfatta");
    else
        System.out.println("Espressione regolare non soddisfatta");
}
```

## Metodo compile

Come si usa?

```java
public boolean test(String regex, String input) {
    Pattern pattern = Pattern.compile(regex);
    Matcher matcher = pattern.matcher(input);
    boolean res = matcher.matches();
    if(res)
        System.out.println("Espressione regolare soddisfatta");
    else
        System.out.println("Espressione regolare non soddisfatta");
}
```

## Costrutti delle espressioni regolari

### Caratteri

| Comando | Significato |
|---------|-------------|
| \n | il carattere per indicare una nuova linea |
| \t | il carattere tab |
| \\ | il carattere per indicare il backslash |
| . | qualunque carattere tranne il terminatore di linea |
| \d | una cifra (da 0 a 9) |
| \D | tutti i caratteri che non rappresentano una cifra |
| \s | uno spazio bianco |
| \S | un carattere che non è uno spazio bianco |

### Classi di caratteri

| Comando | Significato |
|---------|-------------|
| [abc] | il carattere a, b, oppure c |
| [^abc] | tutti i caratteri tranne a, b, oppure c |
| [a-zA-Z] | tutti i caratteri da a fino a z, sia minuscolo che maiuscolo |
| [a-d[m-p]] | tutti i caratteri da a fino a d, o i caratteri da m fino a p (unione) |
| [a-z&&[def]] | d, e, oppure f (intersezione) |
| [a-z&&[^bc]] | da a fino a z, eccetto b e c (sottrazione) |
| [a-z&&[^m-p]] | da a fino a z, eccetto i caratteri da m a p (sottrazione) |

### Quantificatori

| Comando | Significato |
|---------|-------------|
| X* | X si ripete zero o più volte |
| X+ | X si ripete una o più volte |
| X? | X è presente zero o una volta |
| X{n} | X si ripete esattamente n volte |
| X{n,} | X si ripete almeno n volte |
| X{n,m} | X si ripete almeno n volte e al massimo m volte |

### Gruppi

L'uso dei gruppi permette di trattare un insieme di caratteri come se fossero un'unità. I gruppi si possono specificare mettendo tra parentesi tonda un insieme di caratteri da raggruppare.

I gruppi catturati si possono enumerare contando le parentesi aperte da sinistra a destra. Ad esempio, consideriamo l'espressione ((X1)(X2(X3))). In questo caso abbiamo 4 gruppi:

- ((X1)(X2(X3)))
- (X1)
- (X2(X3))
- (X3)

Il metodo *groupCount()* della classe Matcher ci restituisce il numero di gruppi, mentre il metodo *group(int n)* ci restituisce la stringa associata all'ennesimo gruppo catturato.
All'interno dei gruppi si possono usare anche espressioni come | per indicare l'OR tra gli elementi del gruppo.

## Esempi

Controlla se la stringa in input contiene una sequenza (non vuota) di a seguita da una b (es. aaaaaab).

```java
public boolean test(String input) {
    return Pattern.matches("a+b", input);
}
```

Controllla se la stringa in input contiene una sequenza (anche vuota) di a seguita da una b (es. aaaaaab).

```java
public boolean test(String input) {
    return Pattern.matches("a*b", input);
}
```

Controlla se la stringa in input contiene una sequenza (non vuota) di a seguita da una sequenza (non vuota) di b (es. aaabbbbb).

```java
public boolean test(String input) {
    return Pattern.matches("a+b+", input);
}
```

Controlla se la stringa in input contiene una sequenza (non vuota) di ab (es. ababab).

```java
public boolean test(String input) {
    return Pattern.matches("(ab)+", input);
}
```

Controlla se la stringa in input contiene una sequenza (non vuota) di a seguita da una sequenza (non vuota) di b e c (es. aaabbbcccbcbcbcb).

```java
public boolean test(String input) {
    return Pattern.matches("a+[bc]+", input);
    // oppure return Pattern.matches("a+(b|c)+", input);
}
```

Controlla se la stringa in input contiene una sequenza (non vuota) di a seguita da una sequenza (non vuota) di b oppure di c (es. aaabbbbbb oppure aaaaccccc).

```java
public boolean test(String input) {
    return Pattern.matches("a+(b+|c+)", input);
}
```

Restituiamo true se la stringa in input contiene una sequenza (non vuota) di qualunque carattere tranne a (es. bbbbcsd).

```java
public boolean test(String input) {
    return Pattern.matches("[^a]+", input);
}
```

Controlla se la stringa in input è composta da esattamente 5 a.

```java
public boolean test(String input) {
    return Pattern.matches("a{5}", input);
}
```

Controlla se la stringa in input è composta da 5 caratteri diversi da a.

```java
public boolean test(String input) {
    return Pattern.matches("[^a]{5}", input);
}
```

Controlla se la stringa in input è una sequenza (non vuota) di lettere minuscole.

```java
public boolean test(String input) {
    return Pattern.matches("[a-z]+", input);
}
```

Controlla se la stringa in input è una sequenza (non vuota) di lettere maiuscole.

```java
public boolean test(String input) {
    return Pattern.matches("[A-Z]+", input);
}
```

Controlla se la stringa in input è una sequenza (non vuota) di lettere minuscole e maiuscole.

```java
public boolean test(String input) {
    return Pattern.matches("[a-zA-Z]+", input);
}
```

Controlla se la stringa in input è una sequenza (non vuota) di lettere minuscole comprese tra la lettera a e la lettera d, oppure tra la lettera f e la lettera h.

```java
public boolean test(String input) {
    return Pattern.matches("[a-d[f-h]]+", input);
}
```

Controlla se la stringa in input è una sequenza (non vuota) di lettere minuscole tra a e z tranne p e q.

```java
public boolean test(String input) {
    return Pattern.matches("[a-z&&[^pq]]+", input);
}
```

Controlla se la stringa in input è una sequenza (non vuota) di lettere minuscole tra a e z tranne quelle tra p e t.

```java
public boolean test(String input) {
    return Pattern.matches("[a-z&&[^p-t]]+", input);
}
```

Controlla se la stringa è un indirizzo email di *mat.unical* (per semplicità assumiamo che gli indirizzi email possano contenere solo lettere).

```java
public boolean test(String input) {
    return Pattern.matches("[a-zA-Z]+@mat\\.unical\\.it", input);
}
```

Controlla se la stringa è una dichiarazione di una variabile intera in Java.

```java
public boolean test(String input) {
    return Pattern.matches("int [a-zA-Z_][a-zA-Z_0-9]*;" , input);
}
```

Controlla se il codice fiscale è valido.

```java
public boolean test(String input) {
    return Pattern.matches("[a-zA-Z]{3}[a-zA-Z]{3}\\d{2}[a-ehlmpr-
        tA-EHLMPR-T]\\d{2}[a-zA-Z]\\d{3}[a-zA-Z]", input);
}
```

## Esempio di gruppo

E se volessimo stampare i vari componenti del codice fiscale?

```java
Pattern pattern = Pattern.matches("[a-zA-Z]{3}[a-zA-Z]{3}\\d{2}[a-ehlmpr-
        tA-EHLMPR-T]\\d{2}[a-zA-Z]\\d{3}[a-zA-Z]", input);
Matcher matcher = pattern.matcher(codiceFiscale);
if(matcher.matches()) {
    System.out.println("cognome" + matcher.group(1));
    System.out.println("nome" + matcher.group(2));
    System.out.println("anno" + matcher.group(3));
    System.out.println("mese" + matcher.group(4));
    System.out.println("giorno" + matcher.group(5));
    System.out.println("codiceComune" + matcher.group(6));
    System.out.println("codiceControllo" + matcher.group(7));
} else
    System.out.println("Codice fiscale non valido");
```

## Espressioni regolari e stringhe

```java
String testo = "2021-03-12_18:22:30";
String data = testo.replaceAll("_.*", "");
System.out.println(data);

// Risultato della stampa: 2021-03-12

String testo = "1;nome;cognome;data di nascita";
String[] result = testo.split(";");
System.out.println(result[0] + " " + result[1] + " " + result[2] + " " + result[3]);

// Risultato della stampa: 1 nome cognome data di nascita
```
**Attenzione:** non tutti i metodi interpretano espressioni regolari! Ad esempio il metodo *replace* riceve come parametro una stringa.
