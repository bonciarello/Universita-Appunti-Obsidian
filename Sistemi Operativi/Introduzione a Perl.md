---
aliases: [Introduzione Perl, Perl, IP]
tags: [sistemi-operativi]
---
**Perl** è un linguaggio di programmazione/scripting generico e, quindi, è un linguaggio interpretato (il codice sorgente viene tradotto da un interprete in linguaggio macchina al momento dell'esecuzione senza necessità di compilatore). Originariamente usato per la manipolazione di testi ed è oggi impiegato per una ampia gamma di attività come, per esempio, system administration, web development, network programming, GUI development, ecc.

## Vantaggi e svantaggi

I vantaggi di Perl sono:
- **pratico**;
- **efficiente**: integra delle macro istruzioni già pronte per essere utilizzate;
- **completo**: fornisce array, liste, mappe, RegExp, manipolazione di stringhe, pattern matching, ecc.

Lo svantaggio è che principalmente è minimale e conciso.

## Espressività

Imparare Perl è come imparare un qualsiasi linguaggio parlato. L'espressività di Perl consente a chi ha poca esperienza di scrivere buoni programmi senza avere una completa conoscenza di tutto il linguaggio. Vediamo i livelli di competenza per **triplicare una lista di interi**:
- Se sei all'inizio dell'utilizzo di Perl:
```perl
my @tripled;
for (my $i = 0; $i < scalar @numbers; $i++) {
    $tripled[$i] = $numbers[$i] * 3;
}
```
- Se invece sei già abile con Perl:
```perl
my @tripled;
for my $num (@numbers) {
    push @tripled, $num * 3;
}
```
- Se invece siamo molto esperti di Perl:
```perl
my @tripled = map { $_ * 3 } @numbers;
```

## Le basi di Perl

### Primo Script

Per vedere la versione di Perl, scriviamo sul terminale:

> *perl -v*

Per vedere il percorso dell'interprete di Perl, scriviamo sul terminale:

> *which perl*

Inseriamo il "path" corretto per l'interprete Perl, sulla prima riga del file:
```perl
#!/usr/bin/perl
```
Utilizziamo la funzione "print" per stampare del testo su standard output:
```perl
print "Hello World!";
```
Ci sono due modi per lanciare uno script Perl:
- Richiamando direttamente l'interprete Perl sullo script:
> *perl nome_file.pl*
- Rendendo eseguibile il file ed eseguendolo successivamente:
> *chmod u+x nome_file.pl*
>
> *./nome_file.pl*

È possibile nel terminale utilizzare l'opzione ***-w*** per ottenere più informazioni in caso di errori:

> *perl -w nome_file.pl*

È possibile lanciare nel terminale lo script in debug mode usando l'opzione ***-d***:

> *perl -d nome_file.pl*

È possibile includere *strict* e *warnings* per ricevere "suggerimenti" su possibili errori comuni durante la scrittura di script in Perl:
```perl
#!/usr/bin/perl
use strict;
use warnings;
print "Hello World !";
```

### Nomi delle variabili

I *nomi delle variabili* sono sempre preceduti da un identificatore (un simbolo) che indica il tipo della variabile:
```perl
my $variabile_scalare = "Ciao"; # scalari
my @array = (23, 42, 69); # array
my %array_associativo = (1 => "A", 2 => "B"); # mappe
```
Tutti i **nomi di variabili/strutture dati** in Perl iniziano con una lettera o con un underscore *"_"*. Successivamente possono includere qualsiasi combinazione di lettere, numeri e underscores. Di seguito di identificatori validi in Perl:
```perl
my $name;
my @_private_names;
my %Names_to_Addresses;
sub function;
```
Di seguito, invece, un esempio di identificatori non validi in Perl:
```perl
my $invalid name; # gli spazi non sono ammessi nei nomi
my @3; # non si inizia con un numero
my %~flags; # i simboli non sono ammessi
```

### Tipi di variabili in Perl

Le variabili in Perl sono contenitori per dei valori Values:
- **Stringhe**:
```perl
my $name = 'Francesco, Giovanni';
my $address = "Corso Mazzini, Cosenza (CS), 87100";
my $reminder = 'Laboratorio Lab 31b' . 'Lun. Mattina 8:30';
```
- **Numeri**:
```perl
my $integer = 42;
my $float = 0.007;
my $sci_float = 1.02e14;
my $binary = 0b101010;
my $octal = 052;
my $hex = 0x20;
```
- **Undef**:
```perl
my $name = undef; # non necessario
my $rank; # contiene anch'esso undef
```
- **List**:
```perl
my @fibonacci = (1, 1, 2, 3, 5, 8, 13);
```

Le **variabili scalari** contengono un singolo valore discreto, che può essere *string*, *integer or floating-point* e *filehandle*.
Sono preceduti dal simbolo $.
Ecco alcuni esempi di *definizione* e *inizializzazione*:
```perl
my $value;

$value = 123.456;
print "$value\n";

$value = 77;
print "$value\n";

$value = "I am Chuck's big toe.";
print "$value\n";
```
Gli **array** contengono uno o più scalari, rappresentano una lista di valori, la numerazione parte da 0 (sono *zero-indexed*) e La variabile speciale *$#array* ritorna l'indice dell'ultimo elemento di un array. **Sono preceduti dal simbolo *@*.**

Ecco abbiamo due tipologie di assegnamento:
-
```perl
my @gatti;
$gatti[3] = 'Jack';
$gatti[2] = 'Tuxedo';
$gatti[0] = 'Daisy';
$gatti[1] = 'Petunia';
$gatti[4] = 'Brad';
```

-
```perl
my @gatti = ('Daisy','Petunia','Tuxedo', ...);
```

Se vogliamo stampare, in modo semplice, un array:
```perl
print "@gatti\n";
```
Se vogliamo impostare un array vuoto:
```perl
@gatti = ();
```
Se vogliamo aggiungere e rimuovere elementi dalla fine di un array:
```perl
my @meals;
push @meals, ( hamburgers', 'spinach');
pop @meals;
```
Se vogliamo aggiungere e rimuovere elementi dall'inizio di un array:
```perl
unshift @meals, ('tofu', 'taquitos');
shift @meals;
```
Gli **hash (array associativi)** rappresenta un insieme di coppie chiave/valore, è una struttura dati che associa una chiave (string) ad una variabile scalare ed è possibile usare l'operatore *=>* per associare un valore ad una chiave in maniera più chiara. **Sono preceduti dal simbolo *%*.**

Ecco abbiamo due tipologie di dichiarazione:
-
```perl
my %cibo_preferito;
$cibo_preferito{Francesco} = 'Pizza';
$cibo_preferito{Giovanni} = 'Ciambella';
```

-
```perl
my %cibo_preferito = (
    'Francesco', 'Pizza',
    'Giovanni', 'Ciambella',
);
```

-
```perl
my %cibo_preferito = (
    Francesco => 'Pizza',
    Giovanni => 'Ciambella',
);
```

Se vogliamo impostare un array associativo vuoto:
```perl
%cibo_preferito = ();
```

## Operatori

Le caratteristiche principale sono:
- **Precedenza:** la *precedenza* di un operatore decide l'ordine in cui valutare una espressione;
- **Associatività:** la proprietà *associativa* di un operatore decide se valutare una espressione da sinistra verso destra o da destra verso sinistra;
- **Arietà:** l'*arietà* di un operatore indica il numero di operandi sul quale esso opera:
    - un operatore *"nullary"* opera su zero operandi;
    - un operatore *"unary"* opera su un operandi;
    - un operatore *"binary"* opera su due operandi;
    - un operatore *"trinary"* opera su tre operandi;
    - un operatore *"listary"* opera su lista di zero o più operandi.
- **Fixity:** gli operatori *infissi* appaiono al centro tra 2 operandi come, per esempio:
```perl
$length * $width;
```
Gli operatori *prefissi* precedono gli operandi:
```perl
-$x # negazione matematica
!$y # negazione booleana
```
Gli operatori *postfissi* seguono i loro operandi:
```perl
$z++ # incremento postfisso
```
Gli operatori *postcircumfix* seguono determinati operandi e ne racchiudono altri:
```perl
$hash{$x} # accesso agli elementi di un hash
$array[$y] # accesso agli elementi di un array
```

### Operatori numerici

Gli operatori numerici impongono il contesto numeric sui loro operandi:
- operatori aritmetici standard *+*, *-*, *\**, */*, *\*\**, *%*;
- varianti degli operatori aritmetici (*+=*, *-=*, *\*=*, */=*, \*\*=, %=);
- operatori di comparazione eguaglianza numerica (*==*), ineguaglianza numerica (*!=*), maggiore/minore di (*>*, *<*), maggiore/minore o uguale (*>=*, *<=*);
- operatore di comparazione per ordinamento (*<=>*).

### Operatori per stringhe

Gli operatori per le stringhe impongono il contesto string sui loro operandi:
- operatori specifici per le stringhe binding per espressioni regolari (*=~* and *!~*), concatenazione (*.*);
- operatori di comparazione eguaglianza su stringhe (*eq*), ineguaglianza su stringhe (*ne*), maggiore/minore di (*gt*, *lt*), maggiore/minore o uguale (*ge*, *le*);
- operatore di comparazione per ordinamento (*cmp*).

### Operatori logici

Gli operatori booleani impongono il contesto boolean sui loro operandi:
- operatori booleani standard: *&&*, *||*, *!*, *and*, *or*, *not*, *xor*;
- operatori booleani speciali operatori condizionali ternari (*? :*).

### Altri operatori

- **Operatori bitwise:** trattano i loro operandi numericamente a livello di bit left shift (<<), right shift (>>), bitwise and, bitwise or (|) and bitwise xor (^), e le loro varianti con assegnamento (<<=, >>=, &=, |=);
- **Operatore di ripetizione:**
```perl
my @test = ('nights') x 1001;
# conterra' ('nights', 'nights', ...)
my $calendar = 'nights' x 1001;
# conterra' "nightsnights..."
```
- **Operatore di range infisso:**
```perl
my @cards = ( 2 .. 10, 'J', 'Q', 'K', 'A' );
```

## Control Flow

Il *control flow* identifica l'ordine in cui le istruzioni o le funzioni di un programma imperativo sono eseguite o valutate.
I programmi imperativi sono eseguiti a partire dalla prima istruzione fino ad arrivare all'ultima, ad esempio:
```perl
print ("Inizio\n");
print ("Centro\n");
print ("Fine\n");
```
Alcune direttive in Perl cambiano l'ordine di ciò che potrebbe accadere più in là nel programma e prendono il nome di **Perl control flow**.

### If statement

Può essere:
- **postfix**:
```perl
print $cont if $cont > 10;
```
- **prefix**:
```perl
if ($cont > 10) {
    print $cont;
}
```

### Espressioni (expression) condizionali

Può essere:
- **postfix**:
```perl
greet_bob() if ($name eq 'Bob' && not greeted_bob());
```
- **prefix**:
```perl
if ($name eq 'Bob' && not greeted_bob()) {
    print 'Hello, Bob!';
    found_bob();
}
```

### Unless statement

È un *if* con il *not*. Può essere:
- **postfix**:
```perl
print "You're not Bob!" unless $name eq 'Bob';
```
- **prefix**:
```perl
unless ($name eq 'Bob') {
    print "You're not Bob!"
}
```

### If-else statement

Un esempio di implementazione **postfix** è la seguente:
```perl
if ($name eq 'Bob') {
    print 'Hi, Bob!';
} else {
    print "I don't know you.";
}
```

### Unless-else statement

Un esempio di implementazione è la seguente:
```perl
unless ($name eq 'Bob') {
    print "I don't know you.";
} else {
    print 'Hi, Bob!';
}
```

### If-elsif-else statement

Un esempio di implementazione è la seguente:
```perl
if ($name eq 'Robert') {
    print 'Hi, Robert !';
} elsif ($name eq 'James') # Attenzione "else if" NON e' corretto
{
    print 'Hi, James !';
} else {
    print "You're not my friend.";
}
```

### Operatore ternario condizionale

Un esempio di implementazione è la seguente:
```perl
my $time_suffix = after_noon($time)
                    ? 'afternoon'
                    : 'morning';
```
Equivale a:
```perl
my $time_suffix;
if (after_noon($time)) {
    $time_suffix = 'afternoon';
} else {
    $time_suffix = 'morning';
}
```

### Variabili di default

In Perl le variabili ***$_*** e ***@_*** sono variabili globali di default:
- ***$_*** è usata per le variabili scalari;
- ***@_*** è usata per gli array.

### Construtti per il loop

Può essere:
- **prefix**:
```perl
# fare il quadrato dei primi 10 numeri
foreach (1 .. 10) {
    print '$_ * $_ = ' . $_ * $_ . "\n";
}
```
- **postfix**:
```perl
print '$_ * $_ = ' . $_ * $_ . "\n" for 1 .. 10;
```
```perl
for my $i (1 .. 10) {
    print '$i * $i = ' . $i * $i . "\n";
}
```

### Iterare su un array

Abbiamo tre versioni possibili per iterare su un array:
- **versione 1**:
```perl
my @nums = 1 .. 10;
$_ **= 2 for @nums;
print join(", ", @nums);
```
- **versione 2**:
```perl
for my $num (@nums) {
    $num **= 2;
}
print join(", ", @nums);
for (@nums) {
    $_ **= 2;
}
print join(", ", @nums);
```
- **versione 3**:
```perl
for (my $i = 0; $i <= 10; $i += 2) {
    print '$i * $i = ' . $i * $i . "\n";
}
```

### Loop

Possiamo utilizzare vari tipi di loop:
- *while loop*:
```perl
while (@values)
{
    my $value = shift @values; # Attenzione: senza shift diventa un loop infinito
    print $value;
}
```
- *until loop*:
```perl
until ($finished_running) { ... }
```
- *postfix until loop*:
```perl
print "Hello, world!" until !$stop;
```
- *infinite for loop*:
```perl
for (;;) { ... }
```
- *infinite while loop*:
```perl
while (1) { ... }
```
- *postfix infinite while loop*:
```perl
print "Hello, world!" while 1;
```

### Controllo sui loop

- **next:** salta una iterazione del loop (simile al continue in C);
- **last:** esce immediatamente dal ciclo più interno (simile al break in C);
- **redo:** restart del loop senza valutare nuovamente la condizione.

## Files

Molti programmi interagiscono con il mondo reale per lo più leggendo, scrivendo e naturalmente manipolando i files.

Perl era inizialmente usato come un tool di programmazione per gli amministratori di sistemi e continua ad essere ancora oggi un ottimo linguaggio utilizzato per la manipolazione di testi.

### Filehandle

Il *filehandle* rappresenta lo stato corrente di uno specifico canale di input o output. Ci sono 3 canali standard (default) per il filehandle:
- **STDIN** - Input
- **STDOUT** - Output
- **STDERR** - Errore

Un esempio di lettura da STDIN è il seguente:
```perl
print "Inserisci una frase \n";
$line = <STDIN>; # Legge la successiva riga inserita da standard input
print "linea ricevuta:\n$line";
print "inserisci le righe successive!\n";
@lines = <STDIN>; # Legge tutte le righe inserite da standard input fino all'inserimento di ^D
print "restanti linee ricevute:\n@lines";
```

### Input e Output

Se vogliamo aprire un file in *lettura*:
```perl
open(my $fh, "<", "input.txt") or die "Can't open < input.txt: $!";
```
Se vogliamo aprire un file in *scrittura*:
```perl
open(my $fh, ">", "ouput.txt") or die "Can't open > output.txt: $!";
```
Se vogliamo chiudere un file sia in **lettura** che in **scrittura**:
```perl
close $fh;
```
Le principali modalità di apertura dei files sono:
- *<*: apre un file in modalità solo **lettura**;
- *>*: apre un file in modalità solo scrittura cancellandone il contenuto se il file era già esistente, altrimenti ne crea uno nuovo;
- *>>*: apre un file in modalità solo **scrittura** appendendo il nuovo testo a quello esistente; se il file non era presente ne viene creato uno nuovo;
- *+<*: apre il file sia in **lettura** che in **scrittura**;
- *-|*: legge l'output prodotto dalla shell pipeline;
- *|-*: scrive sulla shell pipeline.

Se vogliamo leggere un file:
```perl
while (my $line = <$fh>) {
    chomp $line; # remuove i newlines
    ...
```
Se vogliamo leggere un file usando la variabile di default *$_*:
```perl
while (<$fh>) {
    chomp;
    ...
```
**Perchè si usa il *while* e NON il *for*?** *for* impone il contesto *list* sugli operandi. Quando si è in *list* context, la funzione *readline* leggerà l'intero file prima di processarlo. *while* effettua l'iterazione e legge una linea per volta. Quando è importante risparmiare memoria, è preferibile usare il *while*.

Se vogliamo scrivere su un file:
```perl
print $out_fh "Questa linea sara' scritta sul file\n";
```

## [[Espressioni regolari|Espressioni Regolari]] e Matching

Perl è talvolta chiamato *Practical Extraction and Reporting Language*. Una **espressione regolare** (chiamata anche *regex* o *regexp*) è un pattern che descrive le caratteristiche di un particolare pezzo di testo. Il *regular expression engine* di Perl applica questo pattern al fine di trovare un matching tra determinate porzioni di testo. Una espressione regolare è racchiusa tra 2 slash *'/'*:

> */ ESPRESSIONE REGOLARE /*

Gli elementi principale di una espressione regolare sono:
- **elementi (literals):** rappresentano una parte di testo;
- **quantificatori (quantifiers):** indicano quante volte ci si aspetta che un certo elemento (o gruppo di elementi) possa essere ripetuto.

Gli elementi più semplici sono i caratteri alfanumerici. Ad esempio, l'espressione regolare ***/a/*** indica che una stringa soddisfa il pattern se contiene almeno una ***a***.

### Pattern Matching

Un pattern matching si definisce mediate l'operatore di ricerca *m*, del tipo:
```perl
# m/REGEXP/
```
L'operatore *=~* verifica se esiste una corrispondenza tra una stringa ed una certa espressione regolare, avverte Perl che la parte successiva è una espressione regolare. Questo operatore si chiama operatore di binding, per il fatto che effettua una connessione (binding) fra la variabile ***$testo*** e l'espressione regolare ***/la/***.

In sostanza, può essere letto come "applica l'espressione regolare a destra sul testo della variabile a sinistra".

**Non si tratta di un assegnamento!**

### Modificatori

I modificatori di una REGEXP ne determinano il comportamento:
- ***/REGEXP[/SUBST]/g*:** *g* è il modificatore **globale**: sarà ricercata o sostituita ogni evenienza;
- ***/REGEXP[/SUBST]/i*:** *i* è il modificatore **case-insensitive**: l'espressione regolare sarà case-insensitive.

Facciamo un esempio:
```perl
$testo = "Oggi e' una bella giornata!";
print "$testo verifica il match /LA/ case-insensitive. \n" if $testo !~ m/LA/i;
```
**Come avviene la verifica del match?** Viene cercata la posizione più a sinistra in cui si riscontra l'intera espressione regolare. La stringa viene esaminata da sinistra a destra finché non viene trovato un riscontro di *regexp* o finché il confronto fallisce.

### Metacaratteri

-
```perl
\
```
annulla gli effetti del metacarattere successivo;
-
```perl
^
```
identifica l'inizio di una riga; inoltre all'inizio di un gruppo nega il gruppo stesso;
-
```perl
.
```
qualsiasi carattere ad eccezione di quelli che identificano una riga nuova;
-
```perl
$
```
identifica la fine di una riga;
-
```perl
|
```
indica una condizione OR;
-
```perl
()
```
indicano un gruppo di caratteri;
-
```perl
[]
```
indicano intervalli e classi di caratteri;

### Quantificatori

- ***\*:** indica 0 o tante occorrenze;
- ***+:** indica 1 o tante occorrenze;
- ***?:** indica al massimo 1 occorrenza;
- ***{n}:** ricerca esattamente *n* occorrenze;
- ***{n,}:** ricerca minimo *n* occorrenze;
- ***{n,m}:** ricerca minimo *n* e massimo *m* occorrenze;

### Tabulazioni e newlines

- ***\t:** tab (HT, TAB);
- ***\n:** newline (LF, NL);
- ***\r:** return (CR).

### Altri metacaratteri

- ***\d:** ricerca un numero (d sta per digit);
- ***\D:** opposto di *\d*, ricerca qualsiasi cosa che non sia un numero;
- ***\w:** ricerca un carattere *"parola"* (*w* sta per word), ovvero lettere, numeri e "_" -> [a-zA-Z0-9_];
- ***\W:** ricerca un carattere che non sia *\w*, ovvero tutto quello che non sia lettere, numeri o "_";
- ***\s:** ricerca uno spazio, comprese tabulazioni e caratteri di fine riga;
- ***\S:** opposto di *\s*. Ricerca qualsiasi cosa che non sia uno spazio, una tabulazione o dei caratteri di fine riga;
- ***\N:** ricerca un carattere che non sia newline.

### Esempi di REGEXP

-
```perl
o.a
```
"o" seguita da qualsiasi carattere seguito da "a";
-
```perl
^f
```
"f" all'inizio di una riga;
-
```perl
^yogi
```
yogi all'inizio di una riga;
-
```perl
e$
```
"e" alla fine di una riga;
-
```perl
notte$
```
notte a fine riga;
-
```perl
aiuto*
```
aiuto seguito da zero o piu' caratteri "o";
-
```perl
.*
```
qualsiasi stringa senza un newline;
-
```perl
^$
```
una riga vuota;
-
```perl
[qjk]
```
alternativamente q oppure j oppure k;
-
```perl
[^qjk]
```
ne' q, ne' j, ne' k;
-
```perl
[a-z]
```
qualsiasi carattere tra a e z inclusi;
-
```perl
[^a-z]
```
nessuna lettera minuscola;
-
```perl
[a-zA-Z]
```
qualsiasi lettera;
-
```perl
[a-z]+
```
qualsiasi sequenza non vuota di lettere minuscole;
-
```perl
arancia|limone
```
alternativamente arancia o limone;
-
```perl
cas(a|erma)
```
casa oppure caserma;
-
```perl
(la)+
```
la oppure lala oppure lalala oppure ...;

### Catturare parti del matching

Spesso è utile potersi ricordare dei pattern per cui c'è stato un match, così da poterli ri-utilizzare.

Ciò che avviene è che qualsiasi stringa che soddisfa un match è memorizzata da Perl nelle variabili speciali *$1,...,$9*.
In pratica, le variabili *$1, $2, etc.* funzionano all'esterno di un criterio di ricerca e contengono le sequenze effettivamente riscontrate nei vari raggruppamenti in *()* dell'espressione regolare e contate da sinistra a destra.

Questo meccanismo consente di "catturare" singole parti dell'espressione regolare per cui si verifica il match.

### Quantificatore "ingordo"

Il meccanismo dei quantificatori è "ingordo", ovvero esamina riscontrati. Se questa sequenza non soddisfa l'espressione regolare allora diminuisce il numero di atomi fino al minimo consentito finché non riesce a soddisfare la regex.
```perl
$testo = '"Ciao!", gli disse lei, "Ciao." rispose lui.';
$testo =~ /"(.+)"/;
print "$1\n"; # Ciao!", gli disse lei, "Ciao.
```

### Quantificatore "minimale"

Posticipando il carattere *?* ad un quantificatore lo si rende quantificatore minimale, ovvero verrà cercata la prima occorrenza possibile del carattere/gruppo, minimizzando il risultato.
```perl
$testo = '"Ciao!", gli disse lei, "Ciao." rispose lui.';
$testo =~ /"(.+?)"/;
print "$1\n"; # Ciao!
```

## Espressioni Regolari

### Sostituzioni

Oltre a poter trovare se una stringa soddisfa una particolare espressione regolare, Perl permette di fare sostituzioni basate sulle corrispondenze individuate. Il modo per fare questo è usare l'operatore *s*. Un esempio:
```perl
$frase = "Universita' degli Studi della Calabria";
print "PRIMA => $frase \n"; # Uni... degli ...
$frase =~ s/degli/Degli/g;
print "DOPO => $frase \n"; # Uni... Degli ...
```
Le variabili speciali *$1, $2, etc.* possono essere usate nelle espressioni regolari o nelle sostituzioni mediante i codici speciali *\1, ..., \9*.

Facciamo un esempio per racchiudere le lettere maiuscole tra i due punti ":":
```perl
$frase = "Universita' degli Studi della Calabria";
print "PRIMA => $frase \n";
$frase =~ s/([A-Z])/:\1:/g;
print "DOPO => $frase \n"; # :U:niversita' degli :S:tudi della :C:alabria
```
Facciamo un esempio per invertire l'ordine delle parole:
```perl
$frase = "Universita' degli Studi della Calabria";
print "PRIMA => $frase \n";
$frase =~ s/(.*) (.*) (.*) (.*) (.*)/\5 \4 \3 \2 \1/g;
print "DOPO => $frase \n"; # Calabria della Studi degli Universita'
```

### Traslitterazione

La funzione ***tr*** permette una traduzione (traslitterazione) carattere per carattere.
Il modo per fare questo è usare l'operatore ***tr***.

Un esempio di traslitterazione è il seguente:
```perl
$frase = "Universita' degli Studi della Calabria"; print "PRIMA => $frase \n";
$frase =~ tr/USC/OTZ/;
print "DOPO => $frase \n"; # Oniversita' degli Ttudi della Zalabria
```
Un altro esempio di traslitterazione è il seguente:
```perl
$frase = "Universita' degli Studi della Calabria"; print "PRIMA => $frase \n";
$frase =~ tr/a-z/A-Z/;
print "DOPO => $frase \n"; # UNIVERSITA' DEGLI STUDI DELLA CALABRIA
```

## Funzioni avanzate di Perl

### Esecuzione di comandi shell

Una stringa racchiusa tra apici inversi (backticks ' '), oppure indicata attraverso l'operatore ***qx***, viene interpolata ed il risultato viene fatto eseguire dal sistema operativo.

L'output del comando è il risultato della valutazione della stringa e il valore restituito dal comando può essere letto.

Un esempio d'implementazione è il seguente:
```perl
@output = 'ls -l';
# oppure
@output = qx(ls -l);
print "output del comando @output\n";
```

### Sorting di array associativi

Di seguito, un esempio d'implementazione per ordinare un array associativi:
```perl
%studenti = (
    "ale", 10567,
    "cla", 1789,
    "adri", 6443
);
# Stampa ordinata in base alla chiave (nome)
foreach $nome (sort keys %studenti) {
    print "$nome - $studenti{$nome}.\n";
}
```
Abbiamo vari tipi di metodi ***sort***:
-
```perl
sort {$a cmp $b}
```
ordinamento lessicografico;
-
```perl
sort {$b cmp $a}
```
come prima ma in ordine inverso;
-
```perl
sort {$a <=> $b}
```
ordinamento numerico ascendente;
-
```perl
sort {$b <=> $a}
```
ordinamento numerico discendente;
-
```perl
sort {($hash{$b} <=> $hash{$a}) || ($a cmp $b) }
```
ordinamento prima per valore e poi per chiave in array associativi;

Facciamo l'esempio di fare una stampa ordinata in base al valore (matricola), ascendente:
```perl
foreach $nome (sort { $studenti{$a} <=> $studenti{$b} } keys %studenti) {
    print "$nome - $studenti{$nome}.\n";
}
```

### Split su stringhe

La funzione split "spezza una stringa in pezzi mettendoli in un array". Per specificare cosa deve essere considerato come separatore si passa a questa funzione un'espressione regolare. Facciamo l'esempio di fare il sorting delle chiavi:
```perl
$info = "root:x:0:0:root:/root:/bin/bash";
@dati = split( /:/, $info );
print "i dati sono @dati\n";
```
L'array *@ARGV* contiente gli argomenti passati allo script tramite linea di comando. *$#ARGV* è pari al numero di argomenti meno 1 perchè in *$ARGV[0]* è contenuto il primo argomento.

Le operazioni su array in Perl (incluse ***shift*** e ***pop***) operano su *@ARGV* implicitamente al di fuori delle funzioni.

Facciamo l'esempio di fare la stampa di tutti gli argomenti:
```perl
foreach (@ARGV) {
    print "$_\n";
}
```
Facciamo l'esempio di prendere il primo argomento:
```perl
my $number = shift or die "Usage: $0 NUMBER\n";
```

### Caso Speciale

ARGV ha un caso speciale. Se si legge da un *null file handle* <>, Perl tratterà ogni elemento in @ARGV come se fosse il nome di un file da aprire in lettura.

Se *@ARGV* è vuoto, Perl leggerà da standard input *<STDIN>*. Nota che *$ARGV* contiene il nome del file corrente mentre si legge da <>.

Un esempio di processare il contenuto di ogni file passato come argomento è il seguente:
```perl
while (<>) {
    ...
}
```
