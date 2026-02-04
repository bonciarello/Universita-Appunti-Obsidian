## 1. Strumenti di Sicurezza
Alcuni strumenti di sicurezza utilizzati per ottenere un livello basso/alto di crittografia:

*   **Chiave singola:** se mittente e destinatario utilizzano la stessa chiave, si parla di cifratura simmetrica, a chiave singola, a chiave segreta o convenzionale;
*   **Due chiavi:** se mittente e destinatario utilizzano chiavi diverse, si parla di cifratura asimmetrica, a due chiavi o a chiave pubblica;
*   **Senza chiave (keyless).**

Il nostro obiettivo è evitare l'intercettazione della conversazione da parte di qualcuno che ha il controllo del canale informativo. Per raggiungere questo obiettivo, dobbiamo codificare il nostro messaggio in modo da far conoscere ciò che viene detto solo dagli interlocutori. In questo modo cerchiamo di creare un canale sicuro dal punto di vista della riservatezza.

## 2. Terminologia Importante

*   **Testo in chiaro:** questo è il messaggio o i dati originali che vengono inseriti nell'algoritmo come input;
*   **Testo cifrato:** questo è il messaggio criptato prodotto come output. Dipende dal testo in chiaro e dalla chiave segreta. Per un dato messaggio, due chiavi diverse produrranno due diversi testi cifrati. Il testo cifrato è un flusso di dati apparentemente casuale e, così com'è, è incomprensibile;
*   **Cipher:** chiamato anche algoritmo di crittografia, esegue varie sostituzioni e trasformazioni sul testo in chiaro per trasformarlo in testo cifrato;
*   **Chiave:** la chiave segreta viene inserita anche nell'algoritmo di crittografia. La chiave è un valore indipendente dal testo in chiaro e dall'algoritmo. Si tratta di un'informazione utilizzata in cifratura nota solo al mittente/destinatario;
*   **Crittografia:** studio dei principi/metodi di crittografia;
*   **Crittoanalisi (codebreaking):** studio dei principi/metodi di decifrazione del testo cifrato senza conoscere le chiavi segrete.
