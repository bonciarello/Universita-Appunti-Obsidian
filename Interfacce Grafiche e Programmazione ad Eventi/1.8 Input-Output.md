---
aliases: [input-output]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
## Stream

Uno stream è una connessione associabile ad una sorgente (**input stream**) e a una destinazione (**output stream**).
La sorgente e la destinazione possono essere rappresentate da diversi oggetti (file, socket, console, ecc.). Implementazione nel package *java.io*.

## Stream di byte

I programmi usano questa tipologia di stream per eseguire la lettura e la scrittura di **byte di dati**.
Tutte le classi che si occupano di effettuare stream di byte estendono InputStream o OutputStream.
Esempi di classi che estendono InputStream: *AudioInputStream, FileInputStream, ObjectInputStream, StringBufferInputStream*.

## FileInputStream e FileOutputStream

La classe FileInputStream ci permette di leggere dei byte da un file e FileOutputStream ci consente di scrivere dei byte su un file. Questo è il codice per copiare il contenuto di un file in un altro file:

```java
public void copia(String orig, String dest) throws IOException {
    FileInputStream in = null;
    FileOutputStream out = null;
    try {
        in = new FileInputStream(orig); // Crea l'oggetto che legge
        out = new FileOutputStream(dest); // Crea l'oggetto che scrive
        int c = in.read(); // Legge un byte di dati
        while(c != -1) { // alla fine della lettura c e' uguale a −1
            out.write(c); // Scrive il byte di dati letto prima
            c = in.read(); // Legge un nuovo byte di dati
        }
    } finally {
        if(in != null) in.close(); // Chiude l'input stream
        if(out != null) out.close(); // Chiude l'output stream
    }
}
```

## Stream di caratteri

I programmi usano questa tipologia di stream per eseguire la lettura e la scrittura di **caratteri**.
Tutte le classi che si occupano di effettuare stream di caratteri estendono *Reader* o *Writer*.
Esempi di classi che estendono Reader: *BufferedReader*, *InputStreamReader*, *StringReader*.

## FileReader e FileWriter

La classe FileReader ci permette di leggere dei caratteri da un file e FileWriter ci consente di scrivere dei caratteri su un file.
FileReader estende InputStreamReader, mentre FileWriter estende OutputStreamWriter.
Questo è il codice per copiare il contenuto di un file in un altro file:

```java
public void copia(String orig, String dest) throws IOException {
    FileReader in = null;
    FileWriter out = null;
    try {
        in = new FileReader(orig);
        out = new FileWriter(dest);
        int c = in.read(); // Legge un carattere
        while(c != -1) {
            System.out.println((char) c); // Stampa il carattere letto
            out.write(c); // Scrive il byte di dati letto prima
            c = in.read(); // Legge un nuovo byte di dati
        }
    } finally {
        if(in != null) in.close();
        if(out != null) out.close();
    }
}
```

## Unbuffered vs. Buffered

I tipi di lettura e scrittura visti finora sono **unbuffered**, cioè le operazioni sono effettuate dal sistema operativo che accede ai file al momento delle richieste, e quindi sono inefficienti.
Java mette a disposizione delle classi che permettono di usare stream **buffered**, che usano un'area di memoria in cui effettuano le operazioni prima che le stesse siano eseguite dal sistema operativo.
Funzionamento da *input*:
1. vengono letti dal file un certo numero di byte e memorizzati in un blocco di memoria (**buffer**);
2. quando il buffer è pieno viene letto e svuotato;
3. si ripete il passo 1.

Funzionamento da *output*:
1. il contenuto da salvare su file viene scritto in un blocco di memoria (**buffer**);
2. quando il buffer è pieno il contenuto del buffer è salvato su file e successivamente svuotato;
3. si ripete il passo 1.

## BufferedReader

BufferedReader è un'implementazione di stream buffered e permette di leggere un file di testo linea per linea:

```java
public void leggi(String orig) throws IOException {
    BufferedReader in = null;
    try {
        in = new BufferedReader(new FileReader(orig));
        while(in.ready()) {
            String line = in.readLine();
            System.out.println(line);
        }
    } finally {
        if(in != null) in.close();
    }
}
```

## BufferedWriter

BufferedWriter è anche buffered e permette di scrivere su un file di testo:

```java
public void scrivi(String dest) throws IOException {
    BufferedWriter out = null;
    try {
        out = new BufferedWriter(new FileWriter(dest));
        out.write("linea1");
        out.newLine();
        out.write("linea2");
        out.newLine();
    } finally {
        if(out != null) out.close();
    }
}
```

## Percorso dei file

```java
BufferedReader bln = new BufferedReader(new FileReader("input.txt"));
```

In eclipse, il file *"input.txt"* è cercato nella cartella home del progetto, cioè allo stesso livello di bin e src.
Se si volesse cercare il file in una cartella, si può utilizzare *File.separator*:

```java
BufferedReader bln = new BufferedReader(new FileReader("nome cartella" + File.separator + "input.txt");
```

In generale, va evitato l'utilizzo di percorsi direttamente nel codice! Il percorso deve essere scelto dall'utente, ad esempio tramite interfaccia grafica.

## Risorse

Come si gestiscono i file di configurazione, immagini del programma, oppure i file musicali? Questi file sono associati al programma, e non sono specifici dell'utente. Per gestirli si può creare un package (non una cartella) da cui leggere il file.

```java
public void readConfiguration() throws IOException {
    // Una risorsa chiamata configuration.txt che si trova nel package resources
    String filename = getClass().getResource("/resources/configuration.txt").getFile();
    BufferedReader in = null;
    try {
        in = new BufferedReader(new FileReader(filename));
        while(in.ready()) {
            String line = in.readLine();
            System.out.println(line);
        }
    } finally {
        if(in != null) in.close();
    }
}
```
