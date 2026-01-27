Git è un software per il controllo della versione.
Consente a gruppi di sviluppatori (client) di lavorare contemporaneamente su più file.
I file vengono archiviati in un repository condiviso (in genere online) e le copie vengono archiviate localmente sui client.

Alcune operazioni del cliente:
- crea una copia di lavoro dei file così come sono nel repository online;
- modifica la copia locale dei file offline;
- conferma le modifiche con un messaggio di commit che specifica cosa è stato modificato;
- invia la sua copia al repository centrale.

I commit sono come istantanee del codice accompagnate da un messaggio. I commit possono essere ripristinati e gli utenti possono selezionare quali file eseguire il commit e da quali file devono essere esclusi.

## Ramificazione Git

Git è basato su un sistema di ramificazione:

![[branching.png]]

- ogni riga è un *branch*;
- i cerchi rappresentano i *commit*;
- è possibile creare un nuovo *branch* come copia di un altro *branch*;
- le frecce che tornano a un *branch* più vecchio sono unioni;
- *master* è il *branch* iniziale e predefinito in git.

## Architettura

![[architettura.png]]

## Branch e comandi

I branch possono essere *remoti* e *locali*. I client lavorano su filiali locali e possono sincronizzarli con i branch remoti:

- **git pull:** aggiorna la tua copia di lavoro (ramo di lavoro) con eventuali modifiche dal repository remoto;
- **git add:** aggiunge uno o più file che vuoi condividere;
- **git commit:** conferma le modifiche alla copia di lavoro locale;
- **git push:** invia le modifiche locali confermate al repository remoto. *Nota:* se qualcun altro ha inviato il suo codice al repository remoto dopo che abbiamo eseguito l'ultimo *pull*, allora dobbiamo eseguire un altro *pull*, altrimenti non possiamo eseguire il *push*;
- **git status:** confronta il *branch* locale (copia di lavoro) con la copia del corrispondente *branch* remoto;
- **git branch:** elenca, crea o elimina rami;
- **git checkout name:** si sposta in un altro *branch* o in un *commit* specifico. Può anche essere usato per creare e passare a un nuovo *branch* contemporaneamente. Con *git checkout*, le modifiche non salvate vengono trasferite e il comando fallirà se le modifiche non salvate sono in conflitto con il *branch* di destinazione;
- **git merge name:** unisce un determinato *branch* al *branch* corrente (cioè unisce tutte le modifiche) creando un *commit* di unione. Quando i *commit* divergenti nei due *branch* non modificano lo stesso file nella stessa area allora il comando *merge* fonderàle modifiche automaticamente. In caso contrario, allo sviluppatore verrà chiesto di risolvere i conflitti. I conflitti sono evidenziati nel file in conflitto da speciali tag git;
- **git init:** crea e inizializza un nuovo repository git locale;
- **git clone remote_url:** crea un repository git locale clonando un repository remoto esistente;
- **git stash;**
- **git reset;**
- ...
