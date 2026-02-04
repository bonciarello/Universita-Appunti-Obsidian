Il **Control Hijacking** è una categoria di attacchi in cui un aggressore prende il controllo di un sistema o programma senza autorizzazione. L'obiettivo è ottenere un accesso non autorizzato per rubare dati, danneggiare il sistema o comprometterne la sicurezza.

## 1. Buffer Overflow
È uno degli attacchi più classici. Si verifica quando un input supera la dimensione del buffer allocato, sovrascrivendo le aree di memoria adiacenti.

### Esempio in C
```c
void func(char *str){
    char buf[128];
    strcpy(buf, str);
}
```
In questo esempio, `strcpy` non controlla la lunghezza della stringa. Se `str` è più lunga di 128 byte, sovrascriverà lo stack, inclusi lo **Stack Frame Pointer** e il **Return Address**.

### Obiettivo dell'attaccante
L'attaccante punta a sovrascrivere il *Return Address* con un indirizzo che punti a un codice malevolo (spesso una shell) inserito nello stack stesso.
*   **NOP Slide:** Poiché è difficile indovinare l'indirizzo esatto, si inserisce una serie di istruzioni nulle (`NOP`) prima del codice malevolo per aumentare l'area target (se l'esecuzione cade in un punto qualsiasi della slide, "scivolerà" fino al payload).

### Contesto e Storia
Il Buffer Overflow colpisce linguaggi a basso livello (C/C++) che espongono i puntatori e non effettuano controlli automatici sui limiti.
*   **Esempi famosi:**
    *   **Morris Worm (1988):** danni tra 10 e 100 milioni di dollari.
    *   **CodeRed (2001):** 300.000 macchine infette in 14 ore.
    *   **SQL Slammer (2003):** 75.000 macchine infette in 10 minuti.

### Perché persistono?
*   Presenza di codice **legacy** non orientato alla sicurezza.
*   Necessità di performance (uso di C per velocità).
*   Aritmetica dei puntatori e dereferenziazioni non controllate.
*   Il Sistema Operativo non interviene finché il processo non accede a pagine di memoria non assegnate (grazie alle tabelle delle pagine).

## 2. Contromisure: Stack Canaries
Si inserisce un valore casuale (**CANARY**) tra il buffer e il Return Address.
*   Prima di eseguire la `return`, il sistema controlla se il canarino è stato alterato.
*   Se l'alterazione è rilevata, l'esecuzione viene interrotta.
*   **Esempi:** StackGuard, SSP di gcc.
*   **Limitazioni:** Non protegge se i puntatori vengono sovrascritti e usati *prima* del controllo del canarino, o se l'attaccante riesce a leggere il valore casuale.

## 3. Bounds Checking
Tecnica per verificare che l'uso dei puntatori avvenga entro intervalli validi. In C è difficile perché i puntatori sono semplici numeri senza semantica.

### Approcci al Bounds Checking:
1.  **Fat Pointers:** Puntatori "grossi" che contengono `[indirizzo, base, fine]`.
    *   *Svantaggi:* Alti costi di CPU, incompatibilità con librerie esistenti, la scrittura non è atomica (più word).
2.  **Tagging (Low-fat pointers):** Informazioni sui limiti memorizzate nei bit inutilizzati dei puntatori a 64 bit.
3.  **Shadow Data Structures:** Per ogni oggetto allocato (malloc o variabili statiche), si memorizza la dimensione in una struttura separata (Shadow Object).
    *   **Aritmetica:** Traccia la provenienza dei puntatori.
    *   **Dereferenziazione:** Verifica se il puntatore è nel range.
    *   **Baggy Bounds:** Una tecnica che arrotonda le allocazioni a potenze di 2 per semplificare i controlli tramite prefissi di bit (se due puntatori $p$ e $p'$ hanno lo stesso prefisso, sono validi).

*Costi:* Overhead spaziale (padding extra), overhead di CPU, possibili falsi allarmi.

## 4. Memoria non eseguibile ($W \oplus X$)
Hardware moderno (DEP in Windows, NX bit in AMD) permette di marcare lo stack come non eseguibile.
*   Politica **Write XOR Execute**: una cella può essere scritta o eseguita, mai entrambe.
*   *Problema:* Difficoltà nel generare codice dinamico.

## 5. ASLR (Address Space Layout Randomization)
Randomizzazione degli indirizzi di memoria (stack, heap, librerie) per rendere imprevedibile la posizione del codice e del Return Address.

## 6. Tecniche Evolute: ROP e BROP

### Return-Oriented Programming (ROP)
Invece di iniettare codice, l'attaccante usa frammenti di codice esistente nella memoria (es. librerie standard) chiamati **gadget**.
*   Un gadget è una sequenza di 2-5 istruzioni che termina con un `ret`.
*   Unendo più gadget, l'attaccante può costruire un attacco Turing-completo.
*   La difesa principale è l'ASLR.

### Blind ROP (BROP)
Attacco contro macchine a 64 bit con ASLR. Se un server crasha e si riavvia senza cambiare la randomizzazione:
1.  Si legge lo stack per scoprire canarini e return address.
2.  Si trovano gadget a runtime per causare chiamate di sistema (es. `write`).
3.  Si ricostruisce l'exploit interamente da remoto.

## 7. Control Flow Integrity (CFI)
Approccio moderno che verifica se il flusso di esecuzione segue il **Control Flow Graph (CFG)** pre-calcolato.
*   **IRM (In-line Reference Monitor):** Inserisce etichette prima dei target di salti indiretti.
*   Se l'etichetta al momento del salto non corrisponde a quella prevista dal grafo, il programma viene bloccato.
*   **MCFI (Modular CFI):** Versione modulare per codice C con overhead ridotto (5-12%). Elimina il 95% dei gadget ROP e il 99% dei target di salto indiretti non autorizzati.
