---
aliases: [SL]
tags: [sistemi-operativi]
---
## Le basi della shell Linux

- **Come si accede a *bash*?** i tasti utili sono *Ctrl-Alt-F1* oppure *Ctrl-Alt-F7*;
- *exit* → si esce dalla shell;
- *ls* → mostra contenuto della directory corrente;
- *ls -l* → mostra contenuto con file;
- *ls -a* → mostra anche contenuto nascosto;
- *pwd* → mostra percorso della directory attuale;
- **Come avere aiuto?** *man*, *info*, opzione *--help*.

## Path assoluti e relativi

I **path assoluti** specificano esattamente l'intero path del file/directory. I **path relativi** specificano solo una parte del path del file/directory. Da fare attenzione con i path assoluti da utilizzare nei nostri programmi perchè creano problemi e quindi si utilizza il path relativo: con *.* ci riferiamo alla directory corrente mentre con *..* ci riferiamo alla directory superiore.

## Directories

- */* → root directory;
- */bin, /sbin, /usr/bin, /usr/sbin* → eseguibili
- */dev* → dispositivi;
- */etc* → file di configurazione;
- */home* → home degli utenti;
- */lib* → librerie;
- */mnt, /media* → [[File System|file system]] esterni;
- */opt* → componenti opzionali;
- */tmp* → file temporanei;
- */usr* → molti eseguibili e tanto altro;
- */var* → file variaibili, soggetti a continue modifiche;
- */proc* → processi e varie.

## Come navigare le directory

In ogni momento la shell ha una sua directory corrente. Ogni utente possiede una sua cartella *$HOME*.
- *mkdir <nomedir>* → crea una nuova cartella dal nome <nomedir>;
- *cd <nomedir>* → ci si sposta nella cartella <nomedir>;
- *cd* → ci si sposta in *$HOME*;
- *rmdir <nomedir>* → rimuove la cartella dal nome <nomedir> ma SOLO se è vuota;
- *rm –rf <nomedir>* → rimuove la cartella dal nome <nomedir> e ricorsivamente tutto il suo contenuto (È PERICOLOSO, tutto ciò che è stato eliminato non potrà più essere recuperato);
- *pwd* → mostra il path in cui ci si trova;
- *ls <nomedir>* → lista il contenuto della cartella <nomedir>.

## Come gestire i file

- *cp file1 file2* → crea una copia del *file1* in *file2*;
- *cp file1 .. filex directory* → crea una copia di tutti i file specificati in *directory*;
- *mv file1 file2* → rinomina il *file1* in *file2*;
- *mv file1 .. filex directory* → sposta tutti i file specificati nella *directory*;
- *rm file1* → rimuove il *file1*;
- *less file1* → legge il contenuto del *file1* in modalità interattiva;
- *cat file1* → stampa su terminale il contenuto di *file1*;
- *nano file1* → modifica il *file1* da terminale (alias: *pico*) ;
- *file filename* → analizza il tipo di file di *filename*;
- *touch file1* → crea un nuovo file vuoto dal nome *file1*.

## Ricerca tra directory e file

- *find <nomedir> -name file1* → cerca il file di nome *file1* a partire dalla directory *<nomedir>*;
- *find <nomedir> -name \*.txt* → cerca i file che hanno estensione *.txt* a partire dalla directory *<nomedir>*;
- *grep parola <nomedir>* → cerca in tutti i file collocati nella directory *<nomedir>* la parola parola;
- *grep parola file1* → mostra solo le righe del *file1* che contengono la parola *parola*.

## Caratteri jolly

- **Asterisco "\*":** sta per 0 o più caratteri (ad esempio, *\*.txt*, *\*txt*, *\*txt\**);
- **Punto interrogativo "?":** sta per un qualsiasi singolo carattere (ad esempio, *x??*, *t?t*);
- **Parentesi quadre [] e "!":** ad esempio, *[AB]\** tutti i file che cominciano per *A* o *B*;
- I file nascosti vengono di solito ignorati (quelli che cominciano per *"."*).

## Navigare tra i comandi e shortcut

- **CTRL + R** → avvia la ricerca di comandi tra la *history* dei comandi eseguiti in passato;
- **Tasti freccia** → scorre i precedenti comandi;
- **Tab** → completa i nomi di file e dei comandi;
- **CTRL + L** → ripulisce il terminale (scorciatoia comando *clear*);
- **CTRL + C** → interrompe l'esecuzione di un comando;

## Programmi console e loro canali di input

Possiamo fornire ad un programma dei parametri in input direttamente dalla command line (il cosiddetto ARGV).

![](console1.png)

Lo standard error/output (STDERR/STDOUT) possono essere inseriti all'interno di un file di testo e quindi il loro contenuto non sarà messo direttamente nella command line ma bensì in un file. Questa procedura può essere fatta andando a specificare la seguente pipe (P1 rappresenta il nome del programma e F1 il nome del file):

![](console2.png)

Equivalentemente allo standard output possiamo decidere di dare in input (STDIN) un file al nostro programma nel seguente modo:

![](console3.png)

Possiamo decidere di immettere l'output di un programma P1 come input di un programma P2 nel seguente modo:

![](console4.png)

## Altri comandi per il terminale (utili)

- Per scaricare contenuto da internet: *wget <link>*;
- Per scaricare contenuto da internet (anche con protocolli di [[Sicurezza|sicurezza]]): *wget auto <link>*;
- Per decomprimere un archivio compresso: *unzip <path>*;
- Per spostare dei file con una certa estensione: *mv \*.<estensione> <directory>*;
- Per dividere le stringhe: *cut -f <campo 1>,<campo n> -d "<carattere>" <file origine>*;
- Per prendere certe righe: *sed -n <da riga>,<a riga>p <file origine>*;
- Per ordinare le righe di uno o più file, eliminando righe duplicate: *sort -u <file>..<file>*;
- Per prendere i file sopra una certa dimensione: *find . -size +<dimensione>*;
- Per ordinare in modo inverso: *sort -r <file>*.
