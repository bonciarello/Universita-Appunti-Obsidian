## 1. Scambio delle Chiavi Diffie-Hellman
Il primo algoritmo a chiave pubblica è lo **scambio di chiavi Diffie-Hellman**. Lo scopo dell'algoritmo è consentire a due utenti di scambiarsi in modo sicuro una chiave che può quindi essere utilizzata per la successiva crittografia simmetrica dei messaggi. L'algoritmo stesso è limitato allo scambio di valori segreti.

L’efficienza di questo algoritmo dipende dal fatto che è computazionalmente complesso realizzare un logaritmo discreto (logaritmo + modulo). Nello specifico si identifica un numero primo $q$ e un valore $alpha$ tale per cui $alpha < q$ e $alpha$ è una radice primitiva di $q$. Successivamente, si identifica un esponente $Xa$ (chiave privata) tale per cui possiamo trovare un numero $Ya$ (chiave pubblica) tale che $Y = alpha^X \mod q$.

Questi calcoli vengono effettuati da entrambi gli interlocutori, successivamente si scambiano le corrispondenti chiavi pubbliche e si calcolano la chiave segreta con cui crittografare il messaggio. Purtroppo, questo algoritmo è particolarmente debole in caso di attacchi **man-in-the-middle** attivi ed è vulnerabile a un simile attacco perché non autentica i partecipanti: questa vulnerabilità può essere superata con l'uso di firme digitali e certificati a chiave pubblica.

## 2. Algoritmi di Crittografia a Chiave Pubblica
Gli algoritmi di crittografia a chiave pubblica si basano su funzioni matematiche piuttosto che su sostituzione e permutazione. Ancora più importante, la crittografia a chiave pubblica è **asimmetrica**, comportando l'uso di due chiavi separate, a differenza della crittografia simmetrica, che utilizza una sola chiave. L'uso di due chiavi ha profonde conseguenze nelle aree di riservatezza, distribuzione delle chiavi e autenticazione.

Gli algoritmi asimmetrici si basano su una chiave per la crittografia e una chiave diversa ma correlata per la decrittografia. Questi algoritmi hanno una importante caratteristica: è computazionalmente impossibile determinare la chiave di decrittografia data solo la chiave crittografica e l'algoritmo.

Una delle due chiavi correlate può essere utilizzata per la crittografia, mentre l'altra può essere utilizzata per la decrittografia. Ciò consente di implementare uno schema crittografico piuttosto diverso:

*   **Cifratura:** $Y = E(PR, X)$;
*   **Decifratura:** $X = D(PU, Y)$;

In questo caso, A prepara un messaggio per B e lo crittografa utilizzando la chiave privata di A prima di trasmetterlo. B può decifrare il messaggio utilizzando la chiave pubblica di A. Poiché il messaggio è stato crittografato utilizzando la chiave privata di A, solo A avrebbe potuto preparare il messaggio. Pertanto, l'intero messaggio crittografato funge da **firma digitale**. Inoltre, è impossibile alterare il messaggio senza l'accesso alla chiave privata di A; quindi, il messaggio viene autenticato sia in termini di origine che in termini di integrità dei dati. Una copia deve inoltre essere conservata in testo cifrato in modo che l'origine e il contenuto possano essere verificati in caso di controversia.

Un modo più efficiente per ottenere gli stessi risultati è crittografare un piccolo blocco di bit che è una funzione del documento: tale blocco, chiamato **autenticatore**, deve avere la proprietà che è impossibile modificare il documento senza cambiare l'autenticatore. Se l'autenticatore è crittografato con la chiave privata del mittente, funge da firma che verifica l'origine, il contenuto e la sequenza. È importante sottolineare che il processo di crittografia non fornisce riservatezza: il messaggio inviato è al sicuro da alterazioni ma non da intercettazioni e, quindi, non c'è protezione della riservatezza perché qualsiasi osservatore può decrittografare il messaggio utilizzando la chiave pubblica del mittente.

## 3. Doppio Utilizzo dello Schema a Chiave Pubblica
È tuttavia possibile fornire sia la funzione di autenticazione che di riservatezza mediante un doppio utilizzo dello schema a chiave pubblica. Lo svantaggio di questo approccio è che l'algoritmo a chiave pubblica, che è complesso, deve essere esercitato quattro volte anziché due in ogni comunicazione.

### Utilizzo di Firma Digitale
Nel caso della firma digitale, l’hash value di un messaggio viene crittografato con la chiave privata di un utente. Chiunque conosca la chiave pubblica dell'utente può verificare l'integrità del messaggio.

*   Il digest è crittografato, utilizzando la crittografia a chiave pubblica e utilizzando la chiave privata del mittente: ciò fornisce l'autenticazione. Fornisce anche una firma digitale, perché solo il mittente potrebbe aver prodotto il codice hash crittografato;
*   Se si desidera la riservatezza e una firma digitale, il messaggio e il codice hash crittografato con chiave privata possono essere crittografati utilizzando una chiave segreta simmetrica.

## 4. Attacchi alla Crittografia a Chiave Pubblica
Come per la crittografia simmetrica, uno schema di crittografia a chiave pubblica è vulnerabile a un attacco bruteforce. La contromisura è la stessa: utilizzare testi grandi. Tuttavia, c'è un compromesso da considerare: i sistemi a chiave pubblica dipendono dall'uso di una sorta di funzione matematica invertibile. La complessità del calcolo di queste funzioni potrebbe non scalare linearmente con il numero di bit nella chiave, ma crescere più rapidamente di così. Pertanto, la dimensione della chiave deve essere sufficientemente grande da rendere impraticabile l'attacco bruteforce ma sufficientemente piccola per la crittografia e la decrittografia pratiche. La crittografia a chiave pubblica è attualmente limitata alla gestione delle chiavi e alle applicazioni di firma.

La crittografia a chiave pubblica è anche vulnerabile al **man-in-the-middle attivo**: se l'attaccante intercetta la chiave pubblica e la cambia con la propria, può fingere di essere Bob senza essere realmente lui. Quindi, il problema della distribuzione della chiave pubblica è ancora irrisolto: può esserci uno scambio di chiave pubblica. Un altro problema è presente: se un utente malintenzionato registra il nostro messaggio (digest e così via) può eseguire un attacco di replay utilizzando il tuo vecchio messaggio. Ovviamente, se c'è un livello di crittografia, sta usando il nostro messaggio ma non sa cosa c'è dentro. È per questo motivo che usiamo qualche **Nonce** o **timestamp** nel messaggio in modo da prevenire questo tipo di attacco.
