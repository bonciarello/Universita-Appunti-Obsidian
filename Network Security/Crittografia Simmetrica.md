---
aliases: [Grafo]
tags: [network-security]
---
La **crittografia simmetrica**, nota anche come crittografia a chiave privata, era l'unico tipo di crittografia in uso prima dello sviluppo della crittografia a chiave pubblica negli anni '70.

Per raggiungere il loro obiettivo, hanno bisogno di due diversi algoritmi conoscendo $K$ come chiave segreta, $X$ come testo in chiaro, $Y$ come testo cifrato:

*   **Codifica:** $Y = E(K,X)$ dove $E$ codifica $X$ grazie a $K$, il risultato è $Y$;
*   **Decodifica:** $X = D(K,Y)$ dove $D$ decodifica $Y$ grazie a $K$, il risultato è $X$.

## 1. Requisiti per l'Uso Sicuro
Due requisiti per l'uso sicuro della crittografia simmetrica:

1.  **Algoritmo Potente:** abbiamo bisogno di un potente algoritmo di crittografia: vorremmo che l'algoritmo fosse tale che un avversario che conosce l'algoritmo e ha accesso a uno o più testi cifrati non sia in grado di decifrare il testo cifrato o capire la chiave;
2.  **[[Sicurezza]] della Chiave:** il mittente e il destinatario devono aver ottenuto copie della chiave segreta in modo sicuro e devono conservare la chiave al sicuro: se qualcuno può scoprire la chiave e conosce l'algoritmo, tutte le comunicazioni che utilizzano questa chiave sono leggibili.

Non abbiamo bisogno di mantenere segreto l'algoritmo, dobbiamo mantenere segreta solo la chiave. Questa caratteristica della crittografia simmetrica è ciò che la rende fattibile per un uso diffuso. Il fatto che l'algoritmo non debba essere tenuto segreto significa che i produttori possono e hanno sviluppato implementazioni a basso costo di algoritmi di crittografia dei dati.

Con l'uso della crittografia simmetrica, il principale problema di [[Sicurezza|sicurezza]] è mantenere la segretezza della chiave. Se la chiave viene generata all'origine del messaggio, deve essere fornita anche alla destinazione tramite un canale sicuro. In alternativa, una terza parte potrebbe generare la chiave e consegnarla in modo sicuro sia all'origine che alla destinazione.

### Security Through Obscurity
Creare un nuovo algoritmo di crittografia può sembrare una buona soluzione ma è importante riflettere su tutti gli aspetti: un nuovo algoritmo non è esposto al mondo open source; quindi, le persone non ne conoscono la vulnerabilità; tuttavia, è possibile che alcune vulnerabilità non siano ancora state scoperte e che qualche aggressore le trovi per te.

La **Security Through Obscurity (STO)** fa affidamento sulla segretezza nello sviluppo del software per ridurre al minimo la possibilità che i punti deboli possano essere rilevati e presi di mira.

## 2. Superficie d’Attacco
In genere, l'obiettivo dell'attacco a un sistema di crittografia è recuperare la chiave in uso piuttosto che recuperare semplicemente il testo in chiaro di un singolo testo cifrato. Esistono due approcci generali per attaccare uno schema di crittografia convenzionale:

*   **Crittoanalisi (attacco statistico):** si basano sulla natura dell'algoritmo e su una certa conoscenza delle caratteristiche generali del testo in chiaro o anche alcune coppie campione testo in chiaro-testo cifrato. Questo tipo di attacco sfrutta le caratteristiche dell'algoritmo per tentare di dedurre uno specifico testo in chiaro o per dedurre la chiave utilizzata;
*   **Attacco di bruteforce:** l'attaccante prova ogni chiave possibile su un pezzo di testo cifrato fino a ottenere una traduzione comprensibile in testo in chiaro. In media, la metà di tutte le chiavi possibili deve essere provata per raggiungere il successo. Utilizzare una chiave molto lunga è molto utile per difendersi, in questo modo il tempo impiegato per trovare la chiave corretta può essere molto lungo e rendere impossibile la ricerca.

## 3. Tecnica di Sostituzione
Una tecnica di sostituzione è quella in cui le lettere del testo in chiaro sono sostituite da altre lettere o da numeri o simboli. Se il testo in chiaro è visto come una sequenza di bit, la sostituzione comporta la sostituzione di schemi di bit di testo in chiaro con schemi di bit di testo cifrato.

Il primo uso noto e il più semplice di un cifrario a sostituzione fu di **Giulio Cesare**. Il cifrario di Cesare prevede la sostituzione di ogni lettera dell'alfabeto con la lettera che si trova tre posizioni più in basso nell'alfabeto. Ad esempio:

*   **plain:** meet me after the toga party
*   **cipher:** PHHW PH DIWHU WKH WRJD SDUWB

Per ogni lettera di testo in chiaro $p$, sostituire la lettera di testo cifrato $C$: $C = E(3,p) = (p + 3) mod26$.

Uno spostamento può essere di qualsiasi entità, così come il cifrario di Cesare, è $C = E(k,p) = (p + k) mod26$.

Se è noto che un dato testo cifrato è un cifrario di Cesare, allora è facile eseguire un attacco brutoforce: basta provare tutte le 25 chiavi possibili. Con solo 25 possibili chiavi, il cifrario di Cesare è tutt'altro che sicuro.

Se invece la parte “cipher” può essere una qualsiasi permutazione dei 26 caratteri alfabetici, allora sono $26!$ o maggiore di $4 * 10^{26}$ possibili chiavi. Questo è 10 ordini di grandezza maggiore dello spazio chiave per DES (algoritmo di cifratura il cui spazio è pari a 64 bit) e sembrerebbe eliminare le tecniche di bruteforce per la crittoanalisi. Tale approccio viene definito **cifrario a sostituzione monoalfabetica**, poiché per messaggio viene utilizzato un singolo alfabeto cifrato.

C'è, tuttavia, un'altra linea di attacco: se il crittoanalista conosce la natura del testo in chiaro (ad esempio, testo inglese non compresso), allora l'analista può sfruttare le regolarità della lingua. I cifrari monoalfabetici sono facili da decifrare perché riflettono i dati di frequenza dell'alfabeto originale. Una contromisura consiste nel fornire più sostituti, noti come **omofoni**, per una singola lettera.

## 4. One-Time Pad
Joseph Mauborgne ha suggerito di utilizzare una chiave casuale lunga quanto il messaggio, in modo che la chiave non debba essere ripetuta. La chiave deve essere utilizzata per crittografare e decrittografare un singolo messaggio, quindi viene eliminata. Ogni nuovo messaggio richiede una nuova chiave della stessa lunghezza del nuovo messaggio. Tale schema, noto come **one-time pad**, è infrangibile: produce un output casuale che non ha alcuna relazione statistica con il testo in chiaro. Poiché il testo cifrato non contiene alcuna informazione sul testo in chiaro, non c'è modo di rompere il codice.

La [[Sicurezza|sicurezza]] del one-time pad è interamente dovuta alla casualità della chiave. Quindi, non ci sono modelli o regolarità che un crittoanalista può utilizzare per attaccare il testo cifrato. Il one-time pad offre una [[Sicurezza|sicurezza]] completa ma, in pratica, presenta due difficoltà fondamentali:

1.  **Problema pratico di creare grandi quantità di chiavi casuali:** qualsiasi sistema molto utilizzato potrebbe richiedere milioni di caratteri casuali su base regolare;
2.  **Problema della distribuzione e della protezione delle chiavi:** per ogni messaggio da inviare, sia il mittente che il destinatario necessitano di una chiave di uguale lunghezza. Pertanto, esiste un gigantesco problema di distribuzione delle chiavi.

Non puoi limitare la dimensione della chiave perché in questo modo otterrai una chiave statica che, sicuramente, riutilizzerai in futuro.

## 5. DES (Data Encryption Standard) e AES (Advanced Encryption Standard)
Sebbene dall'introduzione del **DES (Data Encryption Standard)** siano stati sviluppati numerosi cifrari simmetrici, e sebbene sia destinato a essere sostituito dall'**AES (Advanced Encryption Standard)**, il DES rimane l'algoritmo di questo tipo più importante.

L'algoritmo **AES** è un cifrario a blocchi simmetrico in grado di crittografare (cifrare) e decrittografare (decifrare) le informazioni. Questo standard specifica l'algoritmo Rijndael, un cifrario a blocchi simmetrico in grado di elaborare blocchi di dati di 128 bit, utilizzando chiavi di cifratura con lunghezze di 128, 192 e 256 bit. AES viene solitamente selezionato perché è implementato con un particolare hardware dedicato al fine di evitare un'elevata latenza a causa di operazioni crittografiche.

A causa della sua vulnerabilità agli attacchi bruteforce, DES è stato ampiamente sostituito da schemi di crittografia più potenti. Sono stati adottati due approcci:

1.  Progettare un algoritmo completamente nuovo che sia resistente sia agli attacchi di crittoanalisi che a quelli di bruteforce, di cui AES (Advanced Encryption Standard) è un ottimo esempio;
2.  Consiste nell'utilizzare la crittografia multipla con DES e chiavi multiple, preservando l'investimento esistente in software e apparecchiature. La forma più semplice di crittografia multipla prevede due stadi di crittografia e due chiavi:
    *   **Codifica:** dato un testo in chiaro $P$ e due chiavi di cifratura $K1$ e $K2$, il testo cifrato $C$ viene generato come $C = E(K2, E(K1, P)$);
    *   **Decodifica:** richiede che le chiavi vengano applicate in ordine inverso $P = D(K1, D(K2, C))$.

Per DES, questo schema sembra comportare una lunghezza della chiave di $56 * 2 = 112 bit$ e dovrebbe comportare un notevole aumento della forza crittografica. Esiste un modo possibile per attaccare questo schema, uno che non dipende da alcuna particolare proprietà di DES ma che funzionerà contro qualsiasi algoritmo di crittografia a blocchi. Questo algoritmo è il **meet-in-the-middle** che si basa sull'osservazione che, se abbiamo $C = E(K2, E(K1, P))$, considerando $C = E(K2, X) 
ightarrow X = D(K2, C)$, allora $X = E(K1, P) = D(K2, C)$.

Data una coppia nota, $(P, C)$, il procedimento dell’algoritmo meet-in-the-middle come segue:
1.  Crittografa $P$ per tutti i $2^{56}$ possibili valori di $K1$;
2.  Memorizza questi risultati in una tabella e ordina la tabella in base ai valori di $X$;
3.  Decrittografa $C$ utilizzando tutti i $2^{56}$ possibili valori di $K2$;
4.  Ad ogni decrittazione, controlla il risultato rispetto alla tabella per una corrispondenza;
5.  Se si verifica una corrispondenza, testa le due chiavi risultanti rispetto a una nuova coppia nota di testo in chiaro e testo cifrato;
6.  Se le due chiavi producono il testo cifrato corretto, accettale come chiavi corrette.

Un ovvio contrasto all'attacco meet-in-the-middle consiste nell'utilizzare tre stadi di crittografia con tre chiavi diverse. Utilizzando DES come algoritmo sottostante, questo approccio è comunemente indicato come **3DES** o **Triple Data Encryption Algorithm (TDEA)** ed esistono due versioni di 3DES: uno con due chiavi e uno con tre chiavi.

## 6. Cifrari a Flusso e a Blocchi
Uno studio dettagliato di DES fornisce una comprensione dei principi utilizzati in altri cifrari simmetrici. Per questo motivo, è importante esaminare i principi di progettazione del cifrario Feistel. Iniziamo con un confronto tra cifrari a flusso e cifrari a blocchi:

*   **Un cifrario a flusso ([[Stream Cipher]])** crittografa e decrittografa i dati in modo continuo, bit per bit o byte per byte, man mano che fluiscono attraverso il sistema. Sono spesso preferiti quando è richiesta una crittografia in tempo reale, ad esempio nelle comunicazioni wireless o nelle videochiamate, in quanto possono crittografare i dati in modo continuo senza dover aspettare la creazione di un blocco completo.
*   **Un cifrario a blocchi ([[Block Cipher]])** crittografa i dati in blocchi di dimensioni fisse. I dati in chiaro vengono divisi in blocchi e ciascun blocco viene crittografato separatamente utilizzando una chiave segreta. Sono spesso utilizzati per crittografare dati a riposo o file di grandi dimensioni, in quanto consentono di lavorare su blocchi di dati più grandi.

Considerando la grandezza della chiave, il numero di possibili crittazioni è finito, ciò comporta la possibilità di ripetizioni delle chiavi. Nonostante ciò, è stata trovata una soluzione. Una peculiarità di cui devono godere le chiavi crittografiche è la proprietà di **avalanche** e cioè la modifica anche di un solo carattere del testo in chiaro dovrebbe implicare l’alterazione di tutto il testo cifrato (**diffusione**). Analogamente, la modifica di anche un solo carattere della chiave, dovrebbe implicare l’alterazione di tutto il testo cifrato (**confusione**).
