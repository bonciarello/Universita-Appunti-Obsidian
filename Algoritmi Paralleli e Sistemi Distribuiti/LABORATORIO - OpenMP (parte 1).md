In base alle direttive del compilatore, può utilizzare il codice seriale. Definito da un gruppo di società di hardware/software. L'API Fortran è stata rilasciata il 28 ottobre 1997. L'API C/C++ è stata rilasciata nel 1998. Dal rilascio di OpenMP 4, è supportata anche la GPGPU! Portatile / multipiattaforma, include Unix e Windows NT. Disponibile in C/C++ e Fortran. Può essere molto facile e semplice da usare. Consente un approccio incrementale al parallelismo (cioè la capacità di "convertire" un programma sequenziale in parallelo, "a poco a poco").

## Perché OpenMP?

- **Modelli di parallelismo incrementale:** è necessario proiettare il parallelismo all'intero programma, ma è possibile applicare direttive di condivisione del lavoro, ecc. Per estendere porzioni di codice e diverse strategie possono essere applicate nel codice;
- **Modello di parallelismo locale:** applicazioni di direttive di condivisione del lavoro che distribuiscono iterazioni di loop tra thread che sono computazionalmente costosi e funzioni che possono essere eseguite in parallelo (decomposizione di dati/funzioni).

Un parallelismo significativo può essere implementato utilizzando solo 3 o 4 direttive.

## Definizioni di base OpenMP: stack di soluzioni

![[stackSoluzioni.PNG]]

## OpenMP vs. MPI

| **Caratteristiche** | **OpenMP** | **MPI** |
| :--- | :---: | :---: |
| Adatto per multiprocessori | *SI* | *SI* |
| Adatto per multicomputer | *NO* | *SI* |
| Supporta la parallelizzazione incrementale | *SI* | *NO* |
| Codice extra minimo | *SI* | *NO* |
| Controllo esplicito della gerarchia della memoria | *NO* | *SI* |

## Modello concettuale di esecuzione

Il modello concettuale di OpenMP consiste in un'esecuzione alternata seriale e parallela, chiamata *"fork/join"*. I passi sono:

- all'inizio e alla fine c'è un processo seriale (processo master);
- in alcune parti (regioni parallele) vengono lanciati alcuni thread indipendenti (**fork**);
- ogni thread può eseguire operazioni simultanee;
- alla fine della parte parallela, il thread principale attende tutti i thread (**join**);
- il thread principale continua in sequenza.

## Modello fork/join

Costruzione regione parallela. Lo scopo è che una regione parallela è un blocco di codice che verrà eseguito da più thread. **Questo è il costrutto fondamentale di OpenMP!**

![[modelloForkJoin1.PNG]]

![[modelloForkJoin2.PNG]]

## Modello di memoria OpenMP

Tutti i thread hanno accesso alla stessa memoria, condivisa a livello globale. I dati possono essere condivisi o privati. I dati condivisi sono accessibili da tutti i thread. È possibile accedere ai dati privati solo dal thread che li possiede. Il trasferimento dei dati è trasparente per il programmatore. La sincronizzazione avviene, ma è per lo più implicita.

![[modelloMemoriaOpenMP.PNG]]

## Presentazione generale

- **direttive del compilatore:** considerato come commenti per le lingue supportate, per mantenere versioni seriali e compilatori OpenMP non compatibili;
- **funzioni di libreria:** permette di collegare il codice a OpenMP (i prototipi di funzione devono essere inclusi) e permette di determinare/modificare il numero di thread adottati;
- **variabili ambientali**.

## Componenti

![[componentiOpenMP.PNG]]

## Sintassi principale

La maggior parte dei costrutti in OpenMP sono direttive del compilatore.

```c
#pragma omp construct [clause[clause]...]
```

Ad esempio:

```c
#pragma omp parallel num_threads(4)
```

Prototipi e tipi di funzioni nel file:

```c
#include <omp.h>
```

La maggior parte dei costrutti OpenMP si applica a un **blocco strutturato**: un blocco di una o più istruzioni con un punto di entrata in alto e un punto di uscita in basso. Va bene avere un *exit()* all'interno del blocco strutturato.

## Come interagiscono i thread?

OpenMP è un modello di indirizzi condivisi multi-threading, cioè i thread comunicano condividendo le variabili.

La condivisione involontaria dei dati causa condizioni di competizione cioè il risultato del programma cambia poiché i thread sono programmati in modo diverso.

Per controllare le condizioni di gara, si usa la sincronizzazione per proteggere i conflitti di dati.

La sincronizzazione è costosa, quindi si modifica la modalità di accesso ai dati per ridurre al minimo la necessità di sincronizzazione.

## Pragma: direttive di pre-elaborazione sovrascrivibili

Il pragma è una direttiva del compilatore in C o C++ e sta per "informazione pragmatica". È un modo per il programmatore di comunicare con il compilatore ed il compilatore è libero di ignorare i pragmi. La sintassi è la seguente:

```c
#pragma omp <rest of pragma>
```

In C/C++:

```c
#pragma omp directive [clause[[,] clause]...]
Structured block
```

## Mantieni la versione seriale!

Utilizzando le funzioni della libreria OpenMP, la versione seriale non viene preservata: nessuna compilazione ha luogo se il compilatore non supporta OpenMP.

Le soluzioni ampiamente utilizzate consistono nella cosiddetta compilazione condizionale: se il compilatore non supporta OpenMP verrà ignorato, diverso per C e per Fortran.

In C/C++:

```c
#ifdef_OPENMP
iam = omp_get_thread_num();
#endif
```

## Librerie runtime

- *OMP_GET_NUM_THREADS()* restituisce il numero corrente di thread;
- *OMP_GET_THREAD_NUM()* restituisce l'ID di questo thread;
- *OMP_SET_NUM_THREADS(n)* imposta il numero di thread desiderato;
- *OMP_IN_PARALLEL()* restituisce TRUE se all'interno della regione parallela;
- *OMP_GET_MAX_THREADS()* restituisce il numero di thread possibili.

## Quanti thread?
Il numero di thread in una regione parallela è determinato dai seguenti fattori:
1. utilizzo della clausola NUM_THREADS;
2. uso della funzione di libreria omp_set_num_threads();
3. impostazione della variabile d'ambiente OMP_NUM_THREADS;
4. l'impostazione predefinita dell'implementazione.

I thread sono numerati da 0 (thread principale) a N-1.

## Ottenere i tempi

Restituisce il numero di processori fisici (core?) disponibili per l'uso dal programma parallelo:

```c
int omp_get_num_procs(void);
```

Per ottenere i tempi (in secondi da un punto fisso nel passato):

```c
double omp_get_wtime();
```

## Esempio: omp_get_wtime

```c
#include "omp.h"
#include <stdio.h>

int main(){
	double start = omp_get_wtime();
	sleep(10);
	double end = omp_get_wtime();

	printf("start: %.16g\n end: %.16g\n Time:
		%.16g\n", start, end, end-start);

	return 0;
}
```

## Identificazione dei thread

La funzione OpenMP per identificare i thread è *omp_get_thread_num*. In C/C++:

```c
int omp_get_thread_num(void)
```

Ogni thread riceve un valore restituito diverso. Il thread master riceve 0 mentre gli altri thread ottengono: 1, 2, 3,... N-1 con thread.

## omp_get_thread_num

Come si usa? Si include l'intestazione del file:

```c
#include "omp.h"
```

In una regione parallela per consentire a ogni thread di lavorare su dati diversi. Cosa succede fuori dalla regione parallela? Restituisce 0.

## Modifica del numero di thread

La variabile d'ambiente OMP_NUM_THREADS specifica il numero massimo di thread che verranno creati nella regione parallela: per ragioni di efficienza, l'implementazione può ignorare la dichiarazione.

Alla riga di comando:
- *setenv OMP_NUM_THREADS 5* for csh
- *export OMP_NUM_THREADS=5* for bash

## Threads: fork e join

La direttiva PARALLEL crea una regione parallela (**fork**), dove oltre a un **master thread** (che esegue il codice seriale) viene creato un **numero variabile di thread**. Da qui, tutti i thread eseguono il codice **contemporaneamente** e **indipendentemente**: ogni thread esegue lo stesso codice. Solo il thread principale continua alla fine della regione parallela (**join**).

```c
#pragma omp parallel [clause list]
/* blocco strutturato */
```

## Pthreads? No grazie!

![[pthreads.PNG]]

Confronto tra OpenMP e il programma corrispondente scritto con Pthreads.

## Regioni parallele

![[regioniParallele1.PNG]]

![[regioniParallele2.PNG]]

## Sviluppo di "Hello World"

```c
#include <omp.h>
#include <stdio.h>
int main() {
		int nthreads, tid;

	/* Fork a team of threads with each thread having a private tid variable */
	#pragma omp parallel private(tid)
	{
		/* Obtain and print thread id */
		tid = omp_get_thread_num();
		printf("Hello World from thread = %d\n", tid);
		/* Only master thread does this */
		if (tid == 0)
		{
			nthreads = omp_get_num_threads();
			printf("Number of threads = %d\n", nthreads);
		}
	} /* All threads join master thread and terminate */
}
```

Per compilare:

```bash
gcc -fopenmp hello.c -o hello
```

## Thread: contesto di esecuzione

Ogni thread ha il proprio **contesto di esecuzione**. Il contesto di esecuzione è lo spazio degli indirizzi contenente tutte le variabili a cui un thread può accedere. Contenuti del contesto di esecuzione:
- variabili statiche
- strutture dati allocate dinamicamente nell'heap
- variabili nello stack di runtime
- stack di run-time aggiuntivo per le funzioni invocate dal thread

## Variabili condivise e private

La **variabile condivisa** ha lo stesso indirizzo nel contesto di esecuzione di ogni thread. La **variabile privata** ha un indirizzo diverso nel contesto di esecuzione di ogni thread. Un thread non può accedere alle variabili private di un altro thread!

Privata (elenco):
- nessuna associazione di archiviazione con l'oggetto originale;
- tutti i riferimenti sono all'oggetto locale;
- i valori non sono definiti in entrata e in uscita.

Condivisa (elenco):
- i dati sono accessibili da tutti i thread del team;
- tutti i thread accedono allo stesso spazio degli indirizzi.

![[variabiliCP1.PNG]]

![[variabiliCP2.PNG]]

Per impostazione predefinita, le variabili sono di tipo condiviso, mentre le variabili di ciclo sono private.

## Clausola "private"

*private(var)* crea una copia locale di var per ogni thread: il valore non è inizializzato e la copia privata NON è associata all'archiviazione dell'originale.

![[clausolaPrivate.PNG]]

## Calcolo della divisione: costrutti di condivisione del lavoro

Divide l'esecuzione della regione chiusa tra i membri della squadra che la incontrano. I costrutti di condivisione del lavoro non avviano nuovi thread. Nessuna barriera implicita all'ingresso in un costrutto di condivisione del lavoro. Tuttavia, esiste una barriera implicita alla fine del costrutto di condivisione del lavoro (a meno che non venga utilizzato **nowait**).

## Costrutti di condivisione del lavoro: parallelismo del ciclo

Parallelismo a livello di loop: parallelizza solo i loop! È facile da implementare, il codice altamente leggibile, le prestazioni non ottimali (a volte) e molto spesso è utilizzato in OpenMP.

![[parallelismoCiclo.PNG]]

## parallel/for

La differenza tra *parallel*, *parallel for* e *for* è la seguente: un team è il gruppo di thread che vengono eseguiti attualmente; all'inizio del programma, il team è composto da un unico thread; una direttiva parallela divide il thread corrente in un nuovo gruppo di thread per la durata del blocco/istruzione successivo, dopodiché il gruppo si fonde di nuovo in uno solo.
- *for* divide il lavoro del ciclo for tra i thread del **team corrente**. Non crea thread, divide solo il lavoro tra i thread del team attualmente in esecuzione;
- *parallel for* è un'abbreviazione per due comandi contemporaneamente: *parallel* e *for*;
- *parallel* crea una nuova squadra e, per le divisioni, quella squadra gestisce parti diverse del ciclo.

Quindi, se il tuo programma **non contiene mai una direttiva parallela**, non c'è mai più di un thread; il thread principale che avvia il programma e lo esegue, come nei programmi non threading.

## Costrutto combinato parallelo/condivisione del lavoro

Scorciatoia OpenMP mette la direttiva "parallela" e di condivisione del lavoro sulla stessa riga.

![[costruttoCombinatoParallelo.PNG]]

Per esempio:

![[esempioCCP.PNG]]

![[risultatoCCP.PNG]]

## Costrutti di condivisione del lavoro in loop

I costrutti di condivisione del lavoro in loop dividono le iterazioni del loop tra i thread di un team.

![[costruttiCondivisione.PNG]]

Un esempio:

![[costruttiCondivisioneEsempio.PNG]]

## Condivisione dei dati: clausola Firstprivate

*Firstprivate* è un caso speciale di private: inizializza ogni copia privata con il valore corrispondente dal thread principale.

![[firstprivate.PNG]]

## Condivisione dei dati: clausola Lastprivate

*Lastprivate* passa il valore di un private dall'ultima interazione a una variabile globale.

![[lastprivate.PNG]]

## Esempio: Matrix - prodotto vettoriale

![[prodottoVettoriale.png]]

## Clausola if

L'*if (espressione scalare)* esegue in parallelo solo se l'espressione è vera, altrimenti, esegue in serie.

![[clausolaIF.PNG]]

## Clausola barrier

Supponiamo di eseguire ciascuno di questi due loop in parallelo su i.

![[barrier1.PNG]]

Questo potrebbe darci una risposta sbagliata (un giorno): PERCHÉ?

Dobbiamo prima aver aggiornato tutto A[], prima di utilizzare A[].

![[barrier2.PNG]]

Tutti i thread attendono al punto di barriera e continuano solo quando TUTTI i thread hanno raggiunto il punto di barriera. Se c'è la garanzia che la mappatura delle iterazioni sui thread è identica per entrambi i cicli, non ci sarà alcuna corsa di dati.

La sintassi della barriera sarà:

![[barrier3.PNG]]

## Clausola nowait

Per ridurre al minimo la sincronizzazione, alcune direttive/pragma OpenMP supportano la clausola nowait opzionale: se presenti, i thread non si sincronizzano per quel particolare costrutto.

![[clausolaNoWait.PNG]]

### Esempio

![[esempioNoWaitBarrier.PNG]]

## Sincronizzazione: barrier

Barrier: ogni thread attende l'arrivo dei thread.

![[sincronizzazioneBarrier.PNG]]

## Quando usare le barriere?

Vengono usati se i dati vengono aggiornati in modo asincrono e l'integrità dei dati è a rischio. Esempi:
- tra le parti del codice che leggono e scrivono la stessa sezione di memoria;
- dopo un timestep / iterazione in un risolutore.

Sfortunatamente, le barriere tendono ad essere costose e potrebbero non essere scalabili a un numero elevato di processori. Le serrature che possono essere utilizzate sono:

```c
omp_init_lock(), omp_set_lock(), omp_unset_lock(), omp_test_lock(), omp_destroy_lock()
```

```
