---
aliases: [IP, Python, Introduzione Python]
tags: [sistemi-operativi]
---
Python è un linguaggio di *scripting pseudocompilato*, cioè un linguaggio interpretato. L'**interprete** è un normale programma che va installato sulla propria macchina, e si occuperà di interpretare il codice sorgente e di eseguirlo. Non c'è una fase di compilazione (linking che trasforma il sorgente in eseguibile) e si ha a disposizione solo il sorgente che viene direttamente eseguito dall'interprete. I vantaggi di Python sono:
- *portabilità*, lo stesso programma potrà girare su diverse piattaforme purché vi sia installato l'interprete;
- *Object Oriented*, supporta le classi, l'ereditarietà e si caratterizza per il binding dinamico;
- *Short Learning Curve*, chiunque nell'arco di un paio di giornate può imparare ad usarlo e a scrivere le sue prime applicazioni.

Gli svantaggi sono:
- uso dell'operatore *self*;
- indentazione, come per esempio:
```python
def main():
    one_to_ten = range(1,11)
    for x in one_to_ten:
        if x % 2 == 0:
            print("Il numero %d e' divisibile per 2" %(x))
            #print("Il numero {x} e' divisibile per 2".format(x=x))
            #print("Il numero" + str(x) + "e' divisibile per 2")

if __name__ == '__main__':
    main()
```

## Costrutti

I principali costrutti Python sono i seguenti:
- *if*:
```python
if x < 0:
    print("Negativo")
elif x == 0:
    print("Zero")
else:
    print("Positivo")
```
- *for*:
```python
for i in range(1,100):
    print(i)

words = ['cat', 'window', 'defenestrate']
for w in words:
    print(w, len(w))
```
- *while*:
```python
while True:
    print("Loop Infinito")

x = 0
while x != -1:
    print("Il valore di x e' %d" %(x))
x = int(input("Inserisci x:"))
```

## Esempi di primi programmi

- Esempio di *Hello World*:
```python
def main():
    print("Hello world!")

if __name__  == '__main__':
    main();
```
- Esempio di *definizione di una classe e dei suoi metodi*:
```python
class MyClass:
    def __init__(self, **kwargs):
        self.x = 0 # oppure self.x = ""
        if "x" in kwargs:
            self.x = kwargs["x"]
        print("Costruttore con: entrato")

    def print(self):
        print("%d" %(self.x))
```
- Esempio di *uso della classe e accesso ai campi*:
```python
from MyClass import MyClass

def main():
    myClassInstance = MyClass(3)
    myClassInstance.printX()
    myClassInstance.x = 4
    print(myClassInstance.x)

    mySecondClassInstance = MyClass()

if __name__ == '__main__':
    main()
```

## Esercizio con persona, esame e studente

- Si crei una classe **persona** che contenga tutti i dati relativi alla stessa (*codice fiscale*, *nome*, *cognome*, *data di nascita*, *...*);
- Si crei una classe **esame** che abbia i seguenti campi pubblici: *Nome insegnamento*, *codice insegnamento*, *votoP*;
- Si crei una classe **studente** che eredita da persona (campi richiesti: *matricola*, *lista esami superati*); la classe studente dovrà offrire i seguenti metodi pubblici:
    - *CalcolaMedia:* stampa in output la media degli esami dello studente;
    - *ControllaEsame:* Dato un esame in input , controlla se lo stesso è presente nella lista degli esami superati dello studente.
- Si crei un **main** di prova per testare il programma:

```python
class Persona:
    def __init__(self, **kwargs):
        self.cf = ""
        self.nome = ""
        self.cognome = ""
        self.eta = 0
        if "cf" in kwargs: self.cf = kwargs["cf"]
        if "nome" in kwargs: self.nome = kwargs["nome"]
        if "cognome" in kwargs: self.cognome = kwargs["cognome"]
        if "data" in kwargs: self.data = kwargs["data"]

class Esame:
    def __init__(self, **kwargs):
        self.nome = ""
        self.codice = 0
        self.voto = 0
        if "nome" in kwargs: self.nome = kwargs["nome"]
        if "codice" in kwargs: self.codice = kwargs["codice"]
        if "voto" in kwargs: self.voto = kwargs["voto"]

    def __eq__(self, other):
        return (self.nome == other.nome and self.cfu == other.cfu)

    def __ne__(self, other):
        return not (self == other)

class Studente(Persona):
    def __init__(self, **kwargs):
        self.matricola = 0
        self.esami = []
        if "matricola" in kwargs: self.matricola = kwargs["matricola"]
        if "esami" in kwargs: self.esami = kwargs["esami"]
        # Per richiamare la classe padre
        super(Studente, self).__init__(**kwargs)

    def calcolaMedia(self):
        somma = 0
        for e in esami:
            somma = somma + e.voto
        media = somma / lenght(esami)
        print("La media dello studente e' %f" %(media))

    def controllaEsame(self, esame):
        for e in esami:
            if e == esame:
                return true
        return false
        # piu' facilmente --> return esame in self.esami

    def addEsame(self, esame):
        self.esami.append(esame)

def main():
    # costruttore di Studente
    francesco = Studente(cf="pcnfnc93b06d086o", nome="Francesco", cognome="Pacenza", eta=27, matricola=182452)

    # stampa utilizzando il metodo ridefinito (override) dalla classe Studente
    francesco.print()

    print("")

    # aggiunge un esame
    francesco.addEsame(Esame("ING", 6, 18))

    # stampa la lista degli esami
    francesco.printListaEsami()

    print("")

    # "%.2f" tronca il numero float a 2 cifre decimali
    print ("La media ponderata e': %.2f" % (francesco.calcolaMedia()))

    # ricerca un esame in carriera
    if francesco.controllaEsame(Esame("SOR", 12)):
        print ("L'esame e' presente in carriera")
    else:
        print ("L'esame NON e' presente in carriera")

if __name__  == '__main__':
    main()
```

## Consigli generali

- Tipo di interprete utilizzato: *#!/usr/bin/python3*;
- Per trovarlo, si utilizza nel terminale: *which python3*;
- Per eseguire il programma Python nel terminale di Linux: *python3 test.py*;
- Se abbiamo inserito l'interprete: *./test.py*;
- **Non avremo bisogno di fare include perché è tutto incluso nell'interprete**;
- **Conversione in ASCII non presente**;
- Se si devono prendere dei parametri, si utilizza: *input("<eventuale messaggio>")*;
- Se si vuole un tipo specifico, si utilizza: *<tipo-dato>(input("<eventuale messaggio>"))*;
- Operatore *self* (in C o Java sarebbe *this*) indispensabile per le proprietà anche all'interno della classe stessa;
- Può esistere solo un costruttore all'interno di un classe. Per ovviare a questo problema, esiste una parola chiave chiamata ***kwargs** all'interno dei parametri del costruttore;
- **Gli attributi inserite all'interno della classe sono sempre pubblici**.
