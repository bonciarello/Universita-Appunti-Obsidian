L'**esecuzione simbolica** è una tecnica di analisi che utilizza variabili simboliche anziché valori concreti per rappresentare gli input di un programma. Questo permette di esplorare sistematicamente i diversi percorsi di esecuzione (**path**) per identificare bug o vulnerabilità senza testare manualmente ogni caso.

## 1. Obiettivi e Funzionamento
L'obiettivo è verificare il comportamento del software su un insieme potenzialmente infinito di input.
*   **Esempio:** Lo strumento **SAGE** di Microsoft genera sequenze di input che dimostrano vulnerabilità reali.
*   **Problemi rilevabili:** Crash, dereferenziazione di puntatori null, errori di indice (out-of-bounds), **double free**, variabili non inizializzate, overflow (heap/stack).

### Esempio logico
Data una funzione `foo(x, y)`:
1.  Inizializza `t = 0`.
2.  Se `x > y` allora `t = x`, altrimenti `t = y`.
3.  Se `t < x` esegui blocco `[A]`.

L'esecuzione simbolica analizza se esiste un input simbolico tale per cui il blocco `[A]` sia raggiungibile. In questo caso, analizzando i vincoli:
*   Percorso 1: `x > y` AND `x < x` (Impossibile).
*   Percorso 2: `x <= y` AND `y < x` (Contraddizione, dato che `x <= y` e `y < x` non possono essere entrambi veri).
*   **Risultato:** Il blocco `[A]` è irraggiungibile.

## 2. Confronto: Dinamico vs Simbolico
*   **Approccio Dinamico (Fuzzing):** Analizza risultati su input concreti. È utile per trovare bug ma non garantisce l'assenza di vulnerabilità (non è esaustivo).
*   **Analisi Statica (Simbolica):** Considera tutti i possibili input. Può individuare bug e, in certi casi, **dimostrare l'assenza** di determinate classi di errori.

## 3. SMT Solvers
Per risolvere meccanicamente i vincoli logici si utilizzano gli **SMT (Satisfiability Modulo Theories) solvers**.
*   **Input:** Una teoria o formula logica.
*   **Output:** Soluzione trovata, "Insolvibile" o "I don't know" (poiché molti problemi SMT sono NP-completi).
*   **Domini:** Numeri reali, interi, liste, formule non interpretate.

### Funzionamento dei SAT/SMT Solvers
Gli SMT sono costruiti sopra i risolutori **SAT** (che lavorano su variabili booleane):
1.  La formula viene convertita in **CNF** (congiunzione di clausole).
2.  Il risolutore SAT assegna valori di verità alle variabili.
3.  L'SMT verifica se tale assegnamento soddisfa i vincoli specifici della teoria (es. disequazioni lineari).
4.  Se c'è una contraddizione, l'SMT aggiunge un nuovo vincolo al SAT per evitare quel percorso nelle iterazioni successive (**Conflict-Driven Clause Learning**).

## 4. Limitazioni
L'esecuzione simbolica deve affrontare l'**esplosione dei percorsi**: il numero di path possibili cresce in modo esponenziale rispetto alla complessità del codice.
*   **Strategie di mitigazione:**
    *   Controllare la **path condition** ad ogni passo per interrompere percorsi impossibili.
    *   Evitare di esplorare percorsi simili a quelli già esclusi.

## 5. Conclusioni
L'analisi simbolica è uno strumento potente per la sicurezza perché permette di esplorare rami di codice difficili da raggiungere con i test tradizionali, fornendo una copertura molto più profonda rispetto al semplice testing dinamico.
