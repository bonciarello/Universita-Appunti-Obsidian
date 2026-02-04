## 1. Introduzione ai Cifrari a Blocchi
Un **cifrario a blocchi** prende un blocco di testo di lunghezza fissa di $b$ bit e una chiave come input e produce un blocco di $b$ bit di testo cifrato. Se la quantità di testo in chiaro da crittografare è maggiore di $b$ bit, è comunque possibile utilizzare la cifratura a blocchi suddividendo il testo in chiaro in blocchi di $b$ bit. Quando più blocchi di testo in chiaro vengono crittografati utilizzando la stessa chiave, sorgono una serie di problemi di sicurezza.

Per applicare un cifrario a blocchi in una varietà di applicazioni, il NIST ha definito cinque **modalità di funzionamento**: è una tecnica per migliorare l'effetto di un algoritmo crittografico o adattare l'algoritmo per un'applicazione. Le cinque modalità hanno lo scopo di coprire un'ampia varietà di applicazioni di crittografia per le quali potrebbe essere utilizzato un cifrario a blocchi. Queste modalità sono destinate all'uso con qualsiasi cifrario a blocchi simmetrico, inclusi triple DES e AES.

## 2. Electronic Code Book (ECB)
La modalità più semplice è la modalità **ECB (Electronic Code Book)**, in cui il testo in chiaro viene gestito un blocco alla volta e ogni blocco di testo in chiaro viene crittografato utilizzando la stessa chiave.

Questa modalità deve essere utilizzata solo per proteggere i messaggi più brevi di un singolo blocco di cifratura sottostante (ad esempio, 64 bit per 3DES e 128 bit per AES), ad esempio per crittografare una chiave segreta.

La caratteristica più significativa di ECB è che se lo stesso blocco di $b$ bit di testo in chiaro compare più di una volta nel messaggio, produce sempre lo stesso testo cifrato. Per messaggi lunghi, questa modalità potrebbe non essere sicura. Se il messaggio fosse altamente strutturato, potrebbe essere possibile sfruttare queste regolarità. Ad esempio, se è noto che il messaggio inizia sempre con determinati campi predefiniti, allora l’attaccante potrebbe avere un numero di coppie note di testo in chiaro e testo cifrato con cui lavorare.

## 3. Cipher Block Chaining (CBC)
Possiamo superare i problemi di sicurezza dell'ECB utilizzando la modalità **CBC (Cipher Block Chaining)** in cui vogliamo crittografare lo stesso testo in chiaro con la stessa chiave ma ottenere un output diverso. Nella modalità CBC, l'input dell'algoritmo di crittografia è il risultato dell'XOR tra il blocco di testo in chiaro corrente e il blocco di testo cifrato precedente; viene utilizzata la stessa chiave per ogni blocco.

*   **Decrittazione:** ogni blocco di cifratura viene fatto passare attraverso l'algoritmo di decrittazione. Il risultato è sottoposto a XOR con il blocco di testo cifrato precedente per produrre il blocco di testo in chiaro.

Per produrre il primo blocco di testo cifrato, viene eseguito uno XOR con un vettore di inizializzazione (**IV**) e il primo blocco di testo in chiaro. In decrittazione, l'IV viene sottoposto a XOR con l'output dell'algoritmo di decrittazione per recuperare il primo blocco di testo in chiaro. L'IV è un valore che deve essere noto sia al mittente che al destinatario ma, inoltre, deve essere imprevedibile da una terza parte. In particolare, per ogni messaggio deve essere generato un IV casuale, che definisce i limiti della sessione.

## 4. Cipher Feedback (CFB)
È possibile convertire un cifrario a blocchi in un cifrario a flusso utilizzando la modalità **CFB (Cipher Feedback)**. Uno schema di crittografia a flusso elimina la necessità di riempire un blocco ed inoltre può operare in tempo reale.

*   **Funzionamento:** l'input della funzione di crittografia è uno shift register a $b$ bit che viene inizialmente impostato su un vettore di inizializzazione (IV). I bit più significativi dell'output della funzione di crittografia vengono sottoposti a XOR con i bit più significativi del testo in chiaro per produrre l'unità di testo cifrato.
*   **Decrittazione:** arriva l'unità di testo cifrato.

## 5. Output Feedback (OFB)
La modalità **OFB (Output Feedback)** è simile alla modalità CFB, tranne per il fatto che l'output della funzione di crittografia viene inviato allo shift register.

La modalità OFB richiede un vettore di inizializzazione (IV): nel caso di OFB, l'IV deve essere un **Nonce** ovvero l'IV deve essere univoco per ciascuna esecuzione dell'operazione di crittografia. La ragione di ciò è che la sequenza dei blocchi di output della crittografia, $Oi$, dipende solo dalla chiave e dall'IV e non dipende dal testo in chiaro: pertanto, per una data chiave e IV, il flusso di bit di output utilizzato per XOR con il flusso di bit di testo in chiaro è fisso. Se due messaggi diversi avevano un blocco identico di testo in chiaro nella stessa posizione, un utente malintenzionato sarebbe in grado di determinare quella porzione del flusso $Oi$.

OFB ha la struttura di un tipico cifrario a flusso e quel flusso di bit viene sottoposto a XOR con i bit di testo in chiaro. Il flusso generato che viene sottoposto a XOR con il testo in chiaro è esso stesso indipendente dal testo in chiaro. Immaginiamo di utilizzare OFB per criptare un file e, dopo un po' di tempo, vogliamo decriptare solo una piccola parte di questo file: per raggiungere questo obiettivo dobbiamo criptare tutti i byte precedenti e poi possiamo arrivare allo specifico pezzo che vogliamo.

## 6. Counter (CTR)
La modalità **Counter (CTR)** viene utilizzato con le applicazioni per la sicurezza di rete ATM (modalità di trasferimento asincrono) e IPSec.

Il funzionamento è che viene utilizzato un contatore pari alla dimensione del blocco di testo in chiaro. L'unico requisito è che il valore del contatore deve essere diverso per ogni blocco di testo in chiaro crittografato. In genere, il contatore viene inizializzato su un valore e quindi incrementato di 1 per ogni blocco successivo.

*   **Crittografia:** il contatore viene crittografato e quindi sottoposto a XOR con il blocco di testo in chiaro per produrre il blocco di testo cifrato; non c'è concatenamento.
*   **Decrittazione:** viene utilizzata la stessa sequenza di valori contatore, con ogni contatore crittografato sottoposto a XOR con un blocco di testo cifrato per recuperare il blocco di testo in chiaro.
