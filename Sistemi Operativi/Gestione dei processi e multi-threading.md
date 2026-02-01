Come un SO gestisce più processi che girano in contemporanea? Innanzitutto, abbiamo una sola CPU, una sola memoria RAM e tanti programmi che condividono la stessa memoria. L'*esigenza* è quella di **far girare più software "contemporaneamente"**

## Processi e Thread

Un **processo** è un entità dinamica caricata sulla memoria RAM da un programma, è associato a un singolo file binario eseguibile e ad un insieme di thread che condividono la stessa memoria. I processi NON vedono le strutture dati degli altri processi ed ha un suo stack e spazio di memoria.

I processi sono tutti quelli che appaiono nel Task Manager (ma non tutti, solo una parte, cioè quelli che hanno una controparte grafica). Il PID del processo è il codice identificativo del processo.

I **thread** è un filo logico, è relativo ad un processo e condividono le stesse risorse e strutture dati.

**Process Explorer** è una versione di terze parti più dettagliata di Task Manager: cliccando sul singolo processo, possiamo vedere i dettagli del processo visualizzando anche i thread. Il Context Switches permette di turnizzare i vari processi: ogni processo ha un numero di turno per essere eseguito, ha una sorta di ordine. Il Context Switches permette di turnizzare i vari processi: ogni processo ha un numero di turno per essere eseguito, ha una sorta di ordine.

## Composizione hardware di una macchina

All'avvio del computer, il processore avvia il **ciclo di fetch**: prelevo ed eseguo operazioni già definite dalla CPU ed incrementa anche il program counter. Nei moderni PC però la necessità di effettuare diversi processi simultaneamente ha reso questo modello parzialmente *obsoleto*. Infatti si viene ad introdurre il concetto di **"interruzione"**.

![](immagineDettagliataMacchina.png)

In questa figura ci sono più dettagli, è una struttura che si avvicina alla realtà: il processore comunica con alcuni bus. L'**address bus** comunica gli indirizzi tra le varie componenti con cui la CPU sta comunicando, il **data bus** rappresenta quante informazioni possono passare simultaneamente sulla RAM, il **control bus** servono a inviare segnali a componenti esterne alla CPU. La memoria e le periferiche comunicano con i bus. *La CPU come comunica con le periferiche?* Ci sono tante soluzioni tecniche: il controller **Memory Mapped IO** è la *migliore* e permette di collegarle virtualmente alla RAM attribuendogli degli indirizzi speciali ed, attraverso il **Chip Select (CS)**, la periferica capisce che il processore sta interagendo con essa.

Quando la CPU utilizza l'address bus, deve chiedere il permesso: può essere occupata dal controller DMA che avverte la CPU quando può utilizzarla.
*Come viene avvertita la CPU all'operazione completata?* Per notificare sull'avvenuta operazione si utilizza l'**interrupt controller**, utilizzato molto spesso per esempio quando premiamo un tasto sulla tastiera o semplicemente muoviamo il mouse.

La periferica stessa comunica il proprio stato al processore. In caso contrario si avrebbe un meccanismo chiamato **Busy waiting/Polling**. Pertanto la periferica stessa interrompe, attraverso un meccanismo di "interrupt".

Quando svolgiamo qualche azione con la periferica (o meglio quando noi notifichiamo), si utilizza il *ramo INTR* mentre quando la CPU comunica qualcosa alla periferica si utilizza il *ramo INTA*.

## DMA (Direct Memory Access)

Il **controller DMA (Direct Memory Access)** serve per gestire un grande flusso di dati proveniente da periferiche o memoria, può essere definito "arbitro" perchè regola il traffico sui bus e permette di definire chi può trasferire informazioni sui bus indirizzi e dati. Principalmente, mette in comunicazione qualsiasi componente: è come se affidasse un compito al controller DMA e quest'ultimo agisce come se fosse la CPU.
Qualora il processore volesse utilizzare l'address bus deve attivare il **flag HOLD** e chiedere quindi il permesso al DMA ed aspetta la risposta di quest'ultimo sul **flag HLDA**.
Un esempio di scrittuta attraverso il DMA è il seguente:

![](esempioDMA.png)

## Ciclo di fetch esteso

Il **ciclo di fetch esteso** è un ciclo di fetch ma con la presenza di *interrupt* che caratterizzano il ciclo di fetch con *istruzioni di selezione (IF)*. *Le aziende dei SO devono fornire, o meglio programmare, un sistema di gestione di interrupt.*

## Evento

Un **evento** è qualcosa che è successa e che va gestita e tipicamente viene segnalato con un interrupt.

## Coda eventi

Una **coda eventi** è un buffer FIFO di eventi che una certa applicazione deve gestire.

## *winproc*

**winproc** è una funzione dell'applicazione corrente che viene chiamata ogni volta che c'è da elaborare un evento.

## Multitasking collaborativo

Il multitasking collaboratico, detto anche multitasking cooperative, è uno scenario in cui *i programmi cedono volontariamente il controllo al sistema operativo una volta finita l'operazione in corso:* è il caso di Mac OS fino alla versione 9, o di Windows 3.0 e 3.1.

Il vantaggio maggiore di questo metodo è che non ha bisogno di supporto hardware e si può implementare facilmente su ogni tipo di architettura. Comodo per multitasking I/O bound, cioè per applicazioni che non consumano tanta CPU perchè stanno in attesa di una periferica.

Il *grave* svantaggio è che un singolo programma che si rifiuta di cedere il controllo, o che si ferma per qualche errore, può bloccare l'intero computer in quanto il sistema operativo non ha modo di riprendere il controllo da solo. Più precisamente lo scheduler senza prelazione attua un cambio di contesto solo in circostanze quali:
- il passaggio di un programma dallo stato di esecuzione allo stato di attesa;
- il termine del programma stesso.

In sintesi, possiamo avere una sola *winproc* che occupa tempo o va in loop infinito blocca tutto il SO/applicazione/tab del browser.

Oggi si è evoluto in **programmazione asincrona** ed usato per la *gestione eventi in Java*, *gestione eventi in Javascript* e *Python Async I/O*.

> **ESEMPIO:** immaginiamo due coinquilini con un solo bagno. Il primo coinquilino va al bagno: fin quando non finisce, il secondo coinquilino dovrà aspettarlo e, quando finirà il primo coinquilino, potrà entrare.

![](multitaskingCollaborativo.png)

## Multitasking non collaborativo

Il multitasking non collaborativo, detto anche multitasking con prelazione (preemptive), è lo scenario in cui la prelazione è l'atto di *interrompere un programma a prescindere dalla volontà del programma stesso* e, quindi, non può essere implementato se la piattaforma hardware non mette a disposizione gli strumenti necessari, ma in compenso, grazie all'hardware, il cambio di contesto è molto più efficiente favorendo l'adozione di quanti di tempo regolari e una esecuzione più "fluida" dei vari processi. Il preemptive multitask è stato adottato dalla maggior parte dei sistemi operativi moderni.

> **ESEMPIO:** immaginiamo sempre due coinquilini con un solo bagno. Il primo coinquilino va al bagno e avrà un tempo prefissato per fare tutto quello che deve fare: alla fine di quel tempo, un "bodyguard del bagno" butterà fuori il primo coinquilino e farà entrare il secondo coinquilino.

![](multitaskingNonCollaborativo.png)

## Stato di ogni thread

![](statoThread.png)

Ogni thread può avere uno stato:
- **READY:** pronto ad essere eseguito;
- **RUNNING:** in esecuzione;
- **WAITING:** non può essere eseguito, in attesa di un evento esterno come, per esempio, l'input di una periferica.

## *load()* e *save()*

Un thread può compiere operazioni di:
- ***save()*:** scatta una "fotografia" del thread nel momento in cui si è sospeso, e la salva in memoria (TSS in Intel x86). Prima di essere sospeso, quando viene fatta questa "fotografia" e salvata in memoria, un esempio è il seguente:
![](saveThread.png)
- ***load()*:** carica da memoria il TSS di un thread.

## Che cosa avviene in *exec()*?

L'operazione di **exec** consiste nell'eseguire il thread e termina *volontariamente*, con un wait, oppure *brutalmente* quando scade il timer. *Programmare in questo contesto non collaborativo non è semplice.*

## Scheduler su Windows

In Windows le code dei thread pronti sono 32: ogni thread quando nasca ha una priorità rappresentato con un numero che va da 0 a 31. Si può settare manualmente dal *Task Manager*: cambiando la priorità, verrà cambiata la posizione nella coda. *Set Affinity* serve ad impostare quale processore deve gestire quel processo.

Lo scheduler di Windows, invece di fare una sola *take*, prende il *take* con la priorità più alta, non è presente un istruzione di *take* procede alla posizione precedente fino ad arrivare alla fine della coda.

**I processi con priorità più alta "mangia" più risorse e i processi con priorità più bassa "mangerà le briciole" dei processi più alti:** in sintesi, i processi più bassi, per eseguire il loro codice, devono sperare che i processi più alti si mettano in uno stato di *wait*.

![](32codeWindows.png)

Un processo con più thread ha la possibilità di prendere più risorse. Windows ha previsto un funzione di promozione: si conta quante volte il thread ha perso il turno nella coda e, se supera un certo numero, si alza la priorità per quel thread.

**Se modifichiamo la priorità, possiamo creare problemi nel sistema.** Ogni thread ha un punteggio che decide quanto tempo assegnare al thread stesso, con un massimo di 6 punti: questa assegnazione viene decisa internamente da Windows.

## Scheduler su Linux

Le priorità assegnate in Linux sono **dinamiche** (Windows è *statica*). La priorità rispetto a Windows è al contrario: 1 massima priorità e via via scendendo. La priorità funziona che se c'è un processo che si mangia le risorse, poco a poco viene abbassata la priorità per dare spazio ad altri processi. Abbiamo tre tipi di scheduling: **SCHED_FIFO**, **SCHED_RR** e **SCHED_NORMAL**

## Problemi frequenti con la programmazione multithread

Partiamo da un esempio di codice:
```c
int posto[100];

int allocaposto(int p, int codiceutente) {
	if (!posto[p]) return posto[p] = codiceutente;
	else return 0;
}
```
Questo codice è bug-free se, però, eseguito da un thread per volta. Di seguito, puoi vedere l'esecuzione del programma con Assembly ARM:

![](inconsistenza1.PNG)
![](inconsistenza2.PNG)
![](inconsistenza3.PNG)
![](inconsistenza4.PNG)

In questo caso si parla di **race condition** in una situazione in cui due o più thread "competono" senza controllo o disciplina nel modificare o leggere contemporaneamente gli stessi dati. *Un software che consente race condition incontrollate non è "thread-safe".*

Un esempio di race condition è il seguente:
```python
def bonifico(A : conto, B : conto, s : int):
    A.saldo += s
    B.saldo -= s
```
Potrebbe essere che si blocca sulla prima istruzione e rimaniamo fregati perché come banca ci rimettiamo il doppio dei soldi.

Per ovviare a questi problemi esistono dei costrutti fondamentali per la sincronizzazione tra i thread:
- **Spinlock**;
- **Test & Set**;
- **Monitor**;
- **Semaphore**;

## Lock e blocchi synchronized

Un lock può essere posseduto da un thread alla volta e ogni lock può essere *occupato* o *libero*. ***acquire()*:** se il lock è libero, acquisisce il lock e lo marca come *occupato*. Se un thread T cerca di prendere il possesso di un lock gia occupato, T viene posto in stato di *wait*. ***release()*:** quando un lock viene liberato, uno tra i thread in *wait* sullo stesso lock viene svegliato e posto in stato di *ready* (prenderà probabilmente il possesso del lock).
Ci sono due tipi di lock:
- **lock rientrante** con cui un thread che possiede un lock *rientrante* può riacquisirlo quante volte vuole senza bloccarsi (e cioè può invocare *acquire()* tante volte di fila);
- **lock non rientrante** con cui un thread che possiede un lock entra in ciclo di attesa infinito se prova ad acquisire un lock che già possiede (e cioè se invoca *acquire()* due volte di fila).

### Esempio del gioco delle *n* sedie

Nel gioco delle ***n* sedie**, ci sono ***n+1* partecipanti** che si contendono gli *n* posti a sedere. Perde il **partecipante** che non riesce ad occupare nessuna sedia (resta in piedi).

- Si scriva l'opportuno codice che simula il gioco, modellando ciascun partecipante con un thread che cerca di occupare un posto libero, e che termina quando trova un posto oppure quando non ci sono più posti liberi;
- Realizzare un thread **DISPLAY** che stampa periodicamente a video lo stato di occupazione delle sedie, ad esempio stampa una stringa di "0" e "*", dove lo "0" rappresenta una sedia libera e "*" rappresenta una sedia occupata.

```python
#!/usr/bin/python3
from multiprocessing import RLock
from threading import Thread
from time import sleep, time

class Posto:
    def __init__(self):
        self.occupato = False
        self.lock = RLock()

    def isEmpty(self):
        return not self.occupato

    def notEmptyMore(self):
        if (self.occupato):
            return False
        else:
            self.lock.acquire()
            self.occupato = True
            self.lock.release()
            return True

class Display(Thread):
    def __init__(self,gioco):
        super().__init__()
        self.gioco = gioco

    def run(self):
        for i in range(0,len(self.gioco)):
            if self.gioco[i].isEmpty():
                print("-", end='', flush=True)
            else:
                print("o", end='', flush=True)
        print('')

class Partecipante(Thread):
    def __init__(self, gioco):
        super().__init__()
        self.gioco = gioco

    def run(self):
        sleep(3)
        for i in range(0,len(self.gioco)):
            if self.gioco[i].notEmptyMore():
                print( "%s - Posto %d" %(self.getName(), i))
                return
        print("%s - Nessun posto" % self.getName())


postiDisponibili = [Posto() for i in range(0,10)]

lg = Display(postiDisponibili)
lg.start()

for t in range(0, 11):
    t = Partecipante(postiDisponibili)
    t.start()
```

### Esempio del gatto e del topo

Un topo e un gatto sono rappresentati all'interno di una stringa S di spazi, lunga L caratteri, rispettivamente con il simbolo *.* e *\**. Ad esempio:

> *S = " \* . "*

Si progetti la struttura dati Striscia che gestisce opportunamente: un stringa S, modellata come sopra, e 3 tipologie di thread:
- **Display:** visualizza periodicamente il contenuto di S, finchè il gatto non si sovrappone al topo;
- **Gatto:** muove periodicamente di una posizione il gatto. Il gatto si muove da sinistra a destra fino al bordo destro della striscia, per poi alternativamente cominciare a muoversi da destra fino al bordo sinistro, finchè non si sovrappone al topo;
- **Topo:** muove periodicamente di una posizione il topo. Il topo decide casualmente di stare fermo, o di muoversi di una casella a destra, a sinistra, finchè non viene raggiunto dal gatto (il topo potrebbe anche finire addosso al gatto).

Si rediga un programma che crei una istanza della classe, crei ed esegua un istanza di *Display*, di *Gatto* e di *Topo* che abbiano accesso alla stringa S. La posizione iniziale di gatto e topo è determinata casualmente.
```python
#!/usr/bin/python3
from multiprocessing import Lock
import random, time
from threading import Thread

class Striscia:
    LUNG = 20
    def __init__(self):
        self.striscia = list()
        self.lock = Lock()
        self.fine = False
        self.dirGatto = 1
        self.gatto = random.randint(0,self.LUNG-1)
        self.topo = random.randint(0, self.LUNG-1)
        for i in range (0,self.LUNG):
            self.striscia.append(' ')
        self.striscia[self.topo] = '.'
        self.striscia[self.gatto] = '*'

    def printStriscia(self):
        with self.lock: # e' come se il blocco fosse circondato da acquire e release
            print("|%s|" % ''.join(self.striscia))
            return self.fine


    def muoviGatto(self):
        with self.lock:
            if self.fine: return self.fine
            self.striscia[self.gatto] = ' '
            self.gatto += self.dirGatto
            if self.gatto > self.LUNG -1 or self.gatto < 0:
                self.dirGatto = -self.dirGatto
                self.gatto += 2 * self.dirGatto
            self.striscia[self.gatto] = '*'
            if self.gatto == self.topo:
                self.fine = True
                self.striscia[self.gatto] = '@'
                return True
            return False

    def muoviTopo(self):
        with self.lock:
            if self.fine: return self.fine
            self.striscia[self.topo] = ' '
            self.salto = random.randint(-1,1)
            if self.topo + self.salto >= 0 and self.topo + self.salto < self.LUNG:
                self.topo += self.salto
            self.striscia[self.topo] = '.'
            if self.gatto == self.topo:
                self.fine = True
                self.striscia[self.gatto] = '@'
                return True
            return False

class Display(Thread):
    def __init__(self,s):
        Thread.__init__(self)
        self.striscia = s

    def run(self):
        print ("First run Display")
        while not self.striscia.printStriscia():
            pass

class Gatto(Thread):
    def __init__(self,s):
        Thread.__init__(self)
        self.striscia = s

    def run(self):
        print ("First run Gatto")
        while not self.striscia.muoviGatto():
            time.sleep(0.100)

class Topo(Thread):
    def __init__(self,s):
        Thread.__init__(self)
        self.striscia = s

    def run(self):
        print ("First run Gatto")
        while not self.striscia.muoviTopo():
            time.sleep(0.050)

striscia = Striscia()

tom = Gatto(striscia)
jerry = Topo(striscia)
philips = Display(striscia)

philips.start()
jerry.start()
tom.start()
```

## Blocking queues

Le **blocking queue (code bloccanti)** sono code LIFO (buffer) pensate per rendere Thread-Safe il loro accesso: permettono di mettere thread e/o processi in comunicazione, distribuirsi compiti tra thread, delegare compiti ad altri thread e compensare velocità di elebaorazione diverse.

In generale, a noi serve imparare queste robe perchè capita in un sacco di contesti come mail server, comunicazioni tra interlocutori in rete o, semplicemente, le pizzerie. Un esempio è il buffer della tastiera che può contenere nel suo buffer massimo 10 caratteri: se vengono inviati 11 caratteri, l'undicesimo carattere inviato muore.

Un esempio di codice sorgente è il seguente:
```python
#!/usr/bin/python3
import random, time
from threading import Thread, Lock, Condition

class BlockingQueue:
    def __init__(self,dim):
        self.lock = Lock()
        self.full_condition = Condition(self.lock)
        self.empty_condition = Condition(self.lock)
        self.ins = 0
        self.out = 0
        self.slotPieni = 0
        self.dim = dim
        self.thebuffer = [None] * dim

    def put(self,c):
        with self.lock:
            while self.slotPieni == len(self.thebuffer):
                self.full_condition.wait()

            self.thebuffer[self.ins] = c
            self.ins = (self.ins + 1) % len(self.thebuffer)

            self.empty_condition.notifyAll()

            self.slotPieni += 1

    def show(self):
        with self.lock:
            val = [None] * self.dim

            for i in range(0,self.slotPieni):
                val[(self.out + i) % len(self.thebuffer)] = '*'


            for i in range(0,len(self.thebuffer) - self.slotPieni):
                val[(self.ins + i) % len(self.thebuffer)] = '-'

            print("In: %d Out: %d C: %d" % (self.ins,self.out,self.slotPieni))
            print("".join(val))


    def get(self):
        with self.lock:
            while self.slotPieni == 0:
                self.empty_condition.wait()

            returnValue = self.thebuffer[self.out]
            self.out = (self.out + 1) % len(self.thebuffer)

            self.full_condition.notifyAll()

            self.slotPieni -= 1
            return returnValue

class Consumer(Thread):
    def __init__(self,buffer):
        self.queue = buffer
        Thread.__init__(self)

    def run(self):
        while True:
            time.sleep(random.random()*2)
            self.queue.get()
            self.queue.show()


class Producer(Thread):
    def __init__(self,buffer):
        self.queue = buffer
        Thread.__init__(self)

    def run(self):
        while True:
            time.sleep(random.random() * 2)
            self.queue.put(self.name)
            self.queue.show()

# Main
buffer = BlockingQueue(10)

producers = [Producer(buffer) for x in range(5)]
consumers = [Consumer(buffer) for x in range(3)]

for p in producers:
    p.start()

for c in consumers:
    c.start()
```

Prendiamo da esempio una possibile implementazione del metodo *put* della classe *BlockingQueue*:
```python
def put(self,c):
    with self.lock:
        while self.slotPieni == self.dim:
            pass
        self.thebuffer[self.ins] = c
        self.ins = (self.ins + 1) % self.dim
        self.slotPieni += 1
```
Con il *while* abbiamo reso la struttura dati ingestibile, altro problema è che potrebbe svolgere il lavoro su un unico processore. Utilizzando uno *sleep*, possiamo risolvere il problema ma il problema è che fissiamo una latenza. Potremmo quindi applicare il *lock.release()*, poi lo *sleep* e poi il *lock.acquire*: risulterà comunque macchinosa come procedura.

Ci vengono in aiuto le **condition**: per ogni *lock* L, esiste un insieme di thread in attesa di acquisire L (*WAIT-L*) e per ogni *condition* C esiste un insieme di thread in attesa su tale *condition* (*WAIT-C*). **Ogni condition C ha un lock padre (uno solo).**
Le istruzioni delle *condition* sono:
- ***C.wait()*:** libera il *lock* di appartenenza e pone il thread chiamante in stato di attesa su *WAIT-C*. **Non è possibile chiamare *wait()* se non si possiede il lock corrispondente**;
- ***C.notify()*:** prende un thread scelto in maniera impredicibile da *WAIT-C* e lo sposta in *WAIT-L*;
- ***C.notifyAll()*:** prende tutti i thread presenti in *WAIT-C* e li sposta in *WAIT-L*.

Nel dettaglio, con la *wait()* viene liberato il *lock* di appartenenza e pone il thread chiamante in stato di attesa: se non si ha il lock corrispondente, non sarà possibile chiamare *wait()*. Un thread che chiama *notify()* cambia sala di attesa (da *WAIT-C* a *WAIT-L*).
**Non dobbiamo dare per scontato che l'ordine di risveglio è predicibile e di tipo FIFO.**

Tornando all'esempio del metodo *put*, si importa il package *Condition* dei *Thread* e bisogna dichiarare il lock padre: tramite *Condition(self.lock)* possiamo utilizzare le condition e quindi *notify()*. Nell'*init* andiamo a creare *full_condition*, condition per addormentare i produttori che non trovano posto, e *empty_condition*, condition per addormentare i consumatori. Di conseguenza:
```python
...
def __init__(self,dim):
    self.lock = Lock()
    self.full_condition = Condition(self.lock)
    self.empty_condition = Condition(self.lock)
    self.ins = 0
    self.out = 0
    self.slotPieni = 0
    self.dim = dim
    self.thebuffer = [None] * dim
...
```
Nel famoso *while*, andiamo a mettere in *wait()* la condition *full_condition*. Al di fuori del *while*, alla fine del metodo *put*, andiamo a notificare singolarmente *empty_condition*. Di conseguenza:
```python
def put(self,c):
    with self.lock:
        while self.slotPieni == len(self.thebuffer):
            self.full_condition.wait()

        self.thebuffer[self.ins] = c
        self.ins = (self.ins + 1) % len(self.thebuffer)

        self.empty_condition.notifyAll()

        self.slotPieni += 1
```
Viceversa per il metodo *get* (*empty_condition* in *wait()*, *full_condition* in *notifyAll()*). Di conseguenza:
```python
def get(self):
    with self.lock:
        while self.slotPieni == 0:
            self.empty_condition.wait()

        returnValue = self.thebuffer[self.out]
        self.out = (self.out + 1) % len(self.thebuffer)

        self.full_condition.notifyAll()

        self.slotPieni -= 1
        return returnValue
```
**L'istruzione *wait()* bisogna sempre inserirli nei cicli come il *while*, mai strutture come l'*if*. Se non siamo sicuri di quale thread prendere, si consiglia di utilizzare *notifyAll()*; se vogliamo rischiare, utilizziamo *notify()*.** Nell'esempio del codice precedente, il *notify()* è più economico dal punto di vista delle risorse perchè vengono effettuate delle operazioni a vuoto dal punto di vista del produttore.

Una **spurious wake-up** sono i cosidetti risvegli impredicibili: capita che si esce da un *wait()* senza una *notify()*. Si utilizza il *waitFor()* per colmare questo problema.

Nello specifico, una *spurious wake-up* si verifica quando un thread si sveglia dall'attesa di una variabile di condizione che è stata segnalata, solo per scoprire che la condizione che stava aspettando non è soddisfatta. Si chiama spurio perché il thread è stato apparentemente risvegliato senza motivo.

### Esempio dei filosofi

L'esercizio prevede di far mangiare 5 filosofi con 2 bacchette avendo sul tavolo solo 5 bacchette. Bisogna organizzare quindi un programma che permette di suddividere le posate in modo tale che tutti possono mangiare. Il codice inizialmente dovrebbe essere il seguente:
```python
from threading import Thread,Lock,Condition
from time import sleep
from random import randrange,random

class Bacchetta:
    def __init__(self):
        self.lock = Lock()

    def lasciaBacchetta(self):
        self.lock.release()

    def prendiBacchetta(self):
        self.lock.acquire()

class Tavolo:
    def __init__(self):
        self.bacchetta = [Bacchetta() for _ in range(5)]

class Filosofo(Thread):
    def __init__(self,tavolo,pos):
        super().__init__()
        self.posizione = pos
        self.t = tavolo
        self.name = "Philip %s" % pos

    def attesaCasuale(self,msec):
        sleep(randrange(msec)/1000.0)

    def mangia(self):
        print(f"Il filosofo {self.getName()} vuole mangiare")

        self.t.bacchetta[self.posizione].prendiBacchetta()
        print(f"Il filosofo {self.getName()} prende prima bacchetta")

        self.t.bacchetta[(self.posizione + 1) % 5].prendiBacchetta()
        print(f"Il filosofo {self.getName()} prende seconda bacchetta e comincia a mangiare.")

        print(f"Il filosofo {self.getName()}  termina di mangiare.")

        self.t.bacchetta[self.posizione].lasciaBacchetta()
        print(f"Il filosofo {self.getName()}  lascia prima bacchetta.")

        self.t.bacchetta[(self.posizione + 1) % 5].lasciaBacchetta()
        print(f"Il filosofo {self.getName()} lascia seconda bacchetta.")

    def pensa(self):
        print(f"Il filosofo {self.getName()} pensa.")

        print(f"Il filosofo {self.getName()} smette di pensare.")

    def run(self):
        while True:
            self.pensa()
            self.mangia()

if __name__ == "__main__":
    tavolo = Tavolo()
    filosofi = [Filosofo(tavolo,i) for i in range(5)]
    for f in filosofi:
        f.start()
```
Se proviamo ad eseguire il programma, ci accorgiamo che dopo qualche passo il programma si blocca. Questo blocco si chiama **deadlock** (prossima sezione). Il codice senza deadlock è il seguente:
```python
#!/usr/bin/python3
from time import sleep
from random import randrange
from threading import Thread,Lock,Condition

class Bacchetta:
    def __init__(self):
        self.occupata = False

    def checkOccupata(self):
        return self.occupata

    def prendiBacchetta(self):
        self.occupata = True

    def lasciaBacchetta(self):
        self.occupata = False

class Tavolo:
    def __init__(self):
        self.bacchetta = [Bacchetta() for _ in range(5)]
        self.lock = Lock()
        self.cond = Condition(self.lock)

    #  Questo metodo (non usato), consentirebbe di prendere le bacchette, mangiare e lasciare
    #  le bacchette in un solo colpo
    #  Tuttavia non consente a piu' di un filosofo per volta di mangiare, poiche' self.lock rimane acquisito
    #  per tutta la durata del pasto.
    def prendiMangiaESmetti(self,posizione):
        self.lock.acquire()
        while( self.bacchetta[posizione].checkOccupata() or self.bacchetta[(posizione+1) % 5].checkOccupata() ):
            self.cond.wait()
        self.bacchetta[posizione].prendiBacchetta()
        self.bacchetta[(posizione+1) % 5].prendiBacchetta()

        sleep(1)

        self.bacchetta[posizione].lasciaBacchetta()
        self.bacchetta[(posizione+1) % 5].lasciaBacchetta()
        self.cond.notifyAll()
        self.lock.release()

    def prendiLockSimultaneo(self,posizione):
        with self.lock:
            while( self.bacchetta[posizione].checkOccupata() or self.bacchetta[(posizione+1) % 5].checkOccupata() ):
                self.cond.wait()
            self.bacchetta[posizione].prendiBacchetta()
            self.bacchetta[(posizione+1) % 5].prendiBacchetta()

    def mollaLockSimultaneo(self,posizione):
        with self.lock:
            self.bacchetta[posizione].lasciaBacchetta()
            self.bacchetta[(posizione+1) % 5].lasciaBacchetta()
            self.cond.notifyAll()

class Filosofo(Thread):
    def __init__(self,tavolo,pos):
        super().__init__()
        self.posizione = pos
        self.t = tavolo
        self.name = "Philip %s" % pos

    def attesaCasuale(self,msec):
        sleep(randrange(msec)/1000.0)

    def pensa(self):
        print(f"Il filosofo {self.getName()} pensa.")
        print(f"Il filosofo {self.getName()} smette di pensare.")

    def mangia(self):
        print(f"Il filosofo {self.getName()} vuole mangiare.")

        # Acquire di entrambe le bacchette
        self.t.prendiLockSimultaneo(self.posizione)
        print(f"Il filosofo {self.getName()} ha le sue bacchette e mangia.")

        self.attesaCasuale(1)

        # Release di entrambe le bacchette
        print(f"Il filosofo {self.getName()} sta per lasciare le sue bacchette.")
        self.t.mollaLockSimultaneo(self.posizione)

        print(f"Il filosofo {self.getName()} termina di mangiare.")

    def run(self):
        while True:
            self.pensa()
            self.mangia()

tavolo = Tavolo()

filosofi = [Filosofo(tavolo,i) for i in range(5)]
for f in filosofi:
    f.start()
```

### Esempio del conto bancario

Si deve implementare una struttura dati per la gestione di transazioni tra più conti bancari, dove le transazioni possono essere simultanee. I metodi *getSaldo* e *trasferisci* devono essere Thread-safe. Si assuma di prevedere un volume di accessi alla struttura dati di circa 1000 thread in contemporanea.

In ordine di priorità, il codice deve essere implementato:
- garantendo la **mutua esclusione** nell'accesso ai dati condivisi **solo ove necessario**;
- garantendo l'assenza di situazioni di stallo permanente (**deadlock**);
- garantendo il **massimo grado di parallelismo ed efficienza**; Si assuma di prevedere un volume di accessi alla struttura dati di circa 1000 thread in contemporanea.

```python
#!/usr/bin/python3
from multiprocessing import  Lock, RLock
from random import randint, random
from threading import Thread, Condition
from time import sleep

class Cliente(Thread):
    def __init__(self, c, b):
        super().__init__()
        self.conto_associato = c
        self.banca = b

    def run(self):
        mioContoID = self.conto_associato.id
        contiIDs = self.banca.getContiIDs()
        while True:
            ammontare = randint(0,1000)
            while True:
                destinatario = contiIDs[randint(0,len(contiIDs)-1)]
                if destinatario != mioContoID:
                    break
            print(f"Il mio [{mioContoID}] saldo prima del trasferimento e' di {self.banca.getSaldo(mioContoID)} euro")

            if not self.banca.trasferisci(mioContoID, destinatario, ammontare):
                print(f"Non sono riuscito a trasferire i soldi perche' non ne ho abbastanza {mioContoID}")

            print(f"Il mio [{mioContoID}] saldo dopo il trasferimento e' di {self.banca.getSaldo(mioContoID)} euro")

            sleep(random())
        pass

class ContoBancario(Thread):
    def __init__(self, id):
        self.id = id
        self.saldo = 1000
        self.lock = Lock()
        self.cond = Condition(self.lock)
        self.transazioni = []
        self.occupato = False

    def checkOccupata(self):
        return self.occupato

    def prendi(self):
        self.occupato = True

    def lascia(self):
        self.occupato = False

    def aggiungi(self, n):
        self.saldo += n

    def rimuovi(self, n):
        if n <= self.saldo:
            self.saldo -= n
            return True
        else:
            return False

    def inserisci_transazione(self, a, b, n):
        tmp = Transazione(a, b, n)
        self.transazioni.append(tmp)

    def prendiLockSimultaneo(self):
        with self.lock:
            while self.checkOccupata():
                self.cond.wait()
            self.prendi()

    def mollaLockSimultaneo(self):
        with self.lock:
            self.lascia()
            self.cond.notifyAll()

class Transazione:
    def __init__(self, a, b, n):
        self.contoSogente = a
        self.contoDestinazione = b
        self.valore = n

class Banca:
    def __init__(self):
        self.conti = {}
        self.lock = RLock()
        self.cond = Condition(self.lock)

    def getContiIDs(self):
        return list(self.conti.keys())

    def aggiungi_contoCorrente(self, c):
        with self.lock:
            if c.id in self.conti:
                raise Exception("Conto non valido")
            else:
                self.conti[c.id]=c

    def getSaldo(self, id):
        with self.conti[id].lock:
            return self.conti[id].saldo

    def trasferisci(self, id_a, id_b, ammontare):
        sorgente = self.conti[id_a]
        destinazione = self.conti[id_b]

        sorgente.prendiLockSimultaneo()
        destinazione.prendiLockSimultaneo()
        try:
            print(f"Trasferimento di {ammontare} euro dal conto {id_a} al conto {id_b}")
            if sorgente.rimuovi(ammontare):
                destinazione.aggiungi(ammontare)
                sorgente.inserisci_transazione(sorgente, destinazione, ammontare)
                destinazione.inserisci_transazione(sorgente, destinazione, ammontare)
                return True
            else:
                return False
        finally:
            sorgente.mollaLockSimultaneo()
            destinazione.mollaLockSimultaneo()

def main():
    numero_clienti = 10
    clienti = []

    b = Banca()
    for i in range(numero_clienti):
        c = ContoBancario(i)
        b.aggiungi_contoCorrente(c)
        clienti.append(Cliente(c, b))

    for c in clienti: c.start()

if __name__  == '__main__':
    main()
```

## Deadlock

Due o più thread si bloccano in un **"abbraccio mortale"** formando un ciclo infinito per l'acquisizione di una risorsa. Si può evitare per esempio:
- **riprogettando l'accesso alle risorse con una disciplina diversa** (vedi il problema dei filosofi);
- **prendendo il lock alle risorse sempre nello stesso ordine:** questo lo si può osservare nell'esercizio del *Conto Bancario*. Si attribuisce ad ogni risorsa un ID (possibilmente sequenziale) e vi si impone la seguente regola d'accesso: le risorse vanno lockate dall'ID più basso a quello più alto (vale anche il viceversa);
- **utilizzando il grafo delle risorse:** è costituito da due tipologie di nodi: risorse *'[]'* (quadrato) e thread *'O'* (cerchietto). In particolare esiste un arco orientato da T ad R se il T sta aspettando la risorsa R. Esiste invece un arco da R a T se quest'ultimo possiede la risorsa.

## Starvation

Problema ricorrente nella programmazione concorrente che implica la possibilità che il tempo di attesa prima di accedere a una determinata risorsa sia anche infinito. Un esempio tipico è il non riuscire ad ottenere il controllo della CPU da parte di processi con priorità molto bassa, qualora vengano usati algoritmi di scheduling a priorità. In termini matematici: ∄ T ∣ ∀ t > T,P(t) = 0.

### Esempio del read/write locks

Un esempio, per quanto riguarda il codice che andremo a vedere, è quello dei file. Possiamo leggere un file con tante applicazioni ma possiamo scrivere quel file quando qualsiasi programma non lo utilizza per leggerlo. Applichiamo questo esempio ma con i thread e cerchiamo di evitare la *starvation*. Il codice è il seguente:
```python
#!/usr/bin/python3
from threading import Thread, RLock, Condition
from random import random
from time import sleep

plock = RLock()
def prints(s):
    plock.acquire()
    print(s)
    plock.release()

class DatoCondiviso():
    def __init__(self,v):
        self.dato = v
        self.numLettori = 0
        self.ceUnoScrittore = False
        self.lock = RLock()
        self.condition = Condition(self.lock)

    def getDato(self):
        return self.dato

    def setDato(self, i):
        self.dato = i

    def acquireReadLock(self):
        self.lock.acquire()
        while self.ceUnoScrittore:
            self.condition.wait()
        self.numLettori += 1
        self.lock.release()

    def releaseReadLock(self):
        self.lock.acquire()
        self.numLettori -= 1
        if self.numLettori == 0:
            self.condition.notify()
        self.lock.release()

    def acquireWriteLock(self):
        self.lock.acquire()
        while self.numLettori > 0 or self.ceUnoScrittore:
            self.condition.wait()
        self.ceUnoScrittore = True
        self.lock.release()

    def releaseWriteLock(self):
        self.lock.acquire()
        self.ceUnoScrittore = False
        self.condition.notify_all()
        self.lock.release()


class DatoCondivisoSenzaStarvation(DatoCondiviso):
    SOGLIAGIRI = 5

    def __init__(self,v):
        super().__init__(v)
        self.numScrittoriInAttesa = 0
        self.numGiriSenzaScrittori = 0

    def acquireReadLock(self):
        self.lock.acquire()
        while self.ceUnoScrittore or \
              (self.numScrittoriInAttesa > 0 and self.numGiriSenzaScrittori > self.SOGLIAGIRI):
            self.condition.wait()
        self.numLettori += 1
        # Il contatore viene incrementato solo se effettivamente ci sono
        # scrittori in attesa.
        if self.numScrittoriInAttesa > 0:
            self.numGiriSenzaScrittori += 1
        self.lock.release()

    def releaseReadLock(self):
        self.lock.acquire()
        self.numLettori -= 1
        # Nella versione senza starvation, possono esserci anche dei lettori in attesa.
        # E' necessario dunque svegliare tutti.
        if self.numLettori == 0:
            self.condition.notify_all()
        self.lock.release()

    def acquireWriteLock(self):
        self.lock.acquire()
        self.numScrittoriInAttesa += 1
        while self.numLettori > 0 or self.ceUnoScrittore:
            self.condition.wait()
        self.ceUnoScrittore = True
        self.numScrittoriInAttesa -= 1
        self.numGiriSenzaScrittori = 0
        self.lock.release()


class Scrittore(Thread):
    maxIterations = 1000

    def __init__(self, i, dc):
        super().__init__()
        self.id = i
        self.dc = dc
        self.iterations = 0

    def run(self):
        while self.iterations < self.maxIterations:
            prints("Lo scrittore %d chiede di scrivere." % self.id)
            self.dc.acquireWriteLock()
            prints("Lo scrittore %d comincia a scrivere." % self.id )
            sleep(random())
            self.dc.setDato(self.id)
            prints("Lo scrittore %d ha scritto." % self.id)
            self.dc.releaseWriteLock()
            prints("Lo scrittore %d termina di scrivere." % self.id)
            sleep(random() * 5)
            self.iterations += 1


class Lettore(Thread):
    maxIterations = 100

    def __init__(self, i, dc):
        super().__init__()
        self.id = i
        self.dc = dc
        self.iterations = 0

    def run(self):
        while self.iterations < self.maxIterations:
            prints("Il lettore %d Chiede di leggere." % self.id)
            self.dc.acquireReadLock()
            prints("Il lettore %d Comincia a leggere." % self.id)
            sleep(random())
            prints("Il lettore %d legge." % self.dc.getDato())
            self.dc.releaseReadLock()
            prints("Il lettore %d termina di leggere." % self.id)
            sleep(random() * 5)
            self.iterations += 1


dc = DatoCondivisoSenzaStarvation(999)

NUMS = 5
NUML = 5
scrittori = [Scrittore(i,dc) for i in range(NUMS)]
lettori = [Lettore(i,dc) for i in range(NUML)]
for s in scrittori:
    s.start()
for l in lettori:
    l.start()
```

## Barrier

Oggetto utile per sincronizzare un gruppo di processi che eseguono parti di un compito frazionabile in parti uguali. Pertanto è utile qualora esistano porzioni di codice che vogliamo che vengano eseguite solamente dopo che tutti i thread abbiano finito di compiere i propri calcoli. Esistono diverse implementazioni utili come, per esempio, *CyclicBarrier* di Java e *Barrier* di Python.

### Esempio dei numeri primi

Si vuole ottimizzare una libreria per il calcolo dei numeri primi in maniera tale da funzionare al meglio su una CPU con N core indipendenti. È necessario scegliere adeguatamente quali siano i metodi e campi da sincronizzare in maniera tale da garantire *mutua esclusione tra gli i thread allocati, ove si ritenga necessario,* e *assenza di possibili situazioni di stallo (deadlock) e/o starvation*. Il codice è il seguente:
```python
#!/usr/bin/python3
import math
import multiprocessing
from threading import Condition, Lock, Thread
import time

class DistributoreNumeri:
    def __init__(self,min,max):
        self.min = min
        self.max = max
        self.numCorrente = min
        self.lock = Lock()

    def getNextNumber(self):
        with self.lock:
            if self.numCorrente > self.max:
                return -1
            num = self.numCorrente
            self.numCorrente += 1
            return num


class Barrier:
    def __init__(self,n):
        self.soglia = n
        self.threadArrivati = 0
        self.lock = Lock()
        self.condition = Condition(self.lock)

    def wait(self):
        with self.lock:
            self.threadArrivati += 1

            if self.threadArrivati == self.soglia:
                self.condition.notifyAll()

            while self.threadArrivati < self.soglia:
                self.condition.wait()


def eprimo(n):
    if n <= 3:
        return True
    if n % 2 == 0:
        return False
    for i in range(3,int(math.sqrt(n)+1),2):
        if n % i == 0:
            return False
    return True


def contaPrimiSequenziale(min,max):
    totale = 0
    for i in range(min,max+1):
        if eprimo(i):
            totale += 1
    return totale


class Macinatore(Thread):
    def __init__(self,d,b):
        super().__init__()
        self.min = min
        self.max = max
        self.totale = 0
        self.barrier = b
        self.distributore = d

    def getTotale(self):
        return self.totale

    def run(self):
        n = self.distributore.getNextNumber()
        quantiNeHoFatto = 0
        while(n != -1):
            if eprimo(n):
                self.totale += 1
            quantiNeHoFatto += 1
            n = self.distributore.getNextNumber()

        print(f"Il thread {self.getName()} ha finito e ha contato {quantiNeHoFatto} numeri")
        self.barrier.wait()

def contaPrimiMultiThread(min,max):
    nthread = multiprocessing.cpu_count()
    print(f"Trovato {nthread} processori" )
    ciucci = []

    fetta = (max - min + 1) // nthread

    while fetta == 0:
        nthread -= 1
        fetta = (max - min + 1) // nthread

    b = Barrier(nthread+1)
    d = DistributoreNumeri(min,max)

    for i in range(nthread):
        ciucci.append(Macinatore( d, b ))
        ciucci[i].start()

    b.wait()

    totale = 0
    for i in range(nthread):
        totale += ciucci[i].getTotale()
    return totale


min = 100000
max = 10000000
start = time.time()
nprimi = contaPrimiMultiThread(min,max)
elapsed = time.time() - start
print (f"Primi tra {min} e {max}: {nprimi}")
print (f"Tempo trascorso: {elapsed} secondi")
```

## Conclusioni generali

Prendiamo in esempio il codice dell'*esercizio dei numeri primi*: sequenzialmente, se facciamo girare il programma da 100000 a 1000000, ci metterà all'incirca 2 secondi, mentre, se facciamo girare il programma da 100000 a 10000000, ci metterà all'incirca 55 secondi. Rendendolo multi-threading le tempistiche per il calcolo del risultato non cambia.

*Perchè non cambia?* Python, per i thread, usa l'interprete **Global interpreter lock** ed è uno solo: questo significa che se abbiamo 12 processori, il multi-threading sarà inutile e quindi il risultato sarà molto simile (se non peggio) in termini di tempistiche al sequenziale. Una sorta di *race condition* continua dove cerca di eseguire tanti *acquire()* e *release()*.
