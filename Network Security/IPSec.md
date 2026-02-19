---
aliases: [ipsec]
tags: [network-security]
---
Implementando la [[Sicurezza|sicurezza]] a livello IP, un'organizzazione può garantire una rete sicura per le numerose applicazioni che ignorano la [[Sicurezza|sicurezza]]. La [[Sicurezza|sicurezza]] a livello IP (IPSec) comprende tre aree funzionali:

*   **Autenticazione:** garantisce che un pacchetto ricevuto sia stato effettivamente trasmesso dalla parte identificata come sorgente nell'intestazione del pacchetto;
*   **Riservatezza:** consente ai nodi comunicanti di crittografare i messaggi per impedire l'intercettazione da parte di terzi;
*   **[[Gestione delle Chiavi|Gestione delle chiavi]]:** si occupa dello scambio sicuro delle chiavi.

La caratteristica principale di IPsec che gli consente di supportare queste diverse applicazioni è che può crittografare e autenticare tutto il traffico a livello IP. Pertanto, tutte le applicazioni distribuite possono essere protette. Per garantire la [[Sicurezza|sicurezza]], vengono utilizzati due protocolli:
*   **Authentication Header (AH):** un protocollo di autenticazione designato dall'intestazione del protocollo;
*   **Encapsulating Security Payload (ESP):** un protocollo combinato di crittografia/autenticazione designato dal formato del pacchetto per quel protocollo.

Uno dei concetti fondamentali per il funzionamento di IPSec è il concetto di una politica di [[Sicurezza|sicurezza]] applicata a ciascun pacchetto IP che transita da una sorgente a una destinazione. La politica IPsec è determinata principalmente dall'interazione di due [[Database|database]], il **Security Association [[Database]] (SAD)** e il **Security Policy [[Database]] (SPD)**.

## 1. Security Association (SA)
Il **Security Association (SA)** è una connessione logica unidirezionale tra un mittente e un destinatario che offre servizi di [[Sicurezza|sicurezza]] al traffico trasportato su di essa. Se è necessaria una relazione peer-to-peer per lo scambio sicuro bidirezionale, sono necessarie due SA.

Una SA è identificata in modo univoco da tre parametri:
*   **Security Parameters Index (SPI):** un numero intero senza segno a 32 bit assegnato a questa SA e con significato solo locale. L'SPI viene trasportato nelle intestazioni AH ed ESP per consentire al sistema ricevente di selezionare la SA in base alla quale verrà elaborato un pacchetto ricevuto;
*   **IP Destination Address:** questo è l'indirizzo dell'endpoint di destinazione della SA;
*   **Security Protocol Identifier:** questo campo dell'intestazione IP esterna indica se l'associazione è un'associazione di [[Sicurezza|sicurezza]] AH o ESP.

In ogni implementazione IPsec, è presente un [[Database|database]] di SA nominali che definisce i parametri associati a ciascuna SA. Una SA è normalmente definita dai seguenti parametri:
*   **Security Parameter Index:** viene utilizzato per costruire l'intestazione AH o ESP del pacchetto e per mappare il traffico alla SA appropriata;
*   **Sequence Number Counter:** utilizzato per generare il campo Sequence Number nelle intestazioni ESP;
*   **Sequence Counter Overflow:** un flag che indica se l'overflow del Sequence Number Counter deve generare un evento controllabile e impedire l'ulteriore trasmissione di pacchetti su questa SA (richiesto per tutte le implementazioni);
*   **Anti-Replay Window:** utilizzata per determinare se un pacchetto AH o ESP in entrata è un replay (richiesto per tutte le implementazioni);
*   Informazioni AH;
*   Informazioni ESP;
*   **Durata della SA:** un intervallo di tempo o conteggio dei byte dopo il quale una SA deve essere sostituita con una nuova SA (e una nuova SPI) o terminata (richiesto per tutte le implementazioni);
*   Modalità protocollo IPsec;
*   Path MTU.

> **Commentato [CP15]:** Security Association [[Database]] (SAD) contiene le informazioni sulle Security Association (SA) attive, che definiscono i parametri di [[Sicurezza|sicurezza]] per le connessioni IPsec.

## 2. Security Policy [[Database]] (SPD)
Il mezzo con cui il traffico IP è correlato a SA specifiche (o nessuna SA nel caso di traffico autorizzato a bypassare IPsec) è il **Security Policy [[Database]] (SPD)** nominale. Un SPD contiene voci, ognuna delle quali definisce un sottoinsieme di traffico IP e punta a una SA per quel traffico. Ogni voce SPD è definita da un insieme di valori di campo IP e protocollo di livello superiore, chiamati **selettori**. Questi selettori vengono utilizzati per filtrare il traffico in uscita al fine di mapparlo in una particolare SA.

L'elaborazione in uscita obbedisce ad una sequenza generale per ciascun pacchetto IP:
1.  Confrontare i valori dei campi appropriati nel pacchetto (i campi del selettore) con l'SPD per trovare una voce SPD corrispondente;
2.  Determinare l'eventuale SA per questo pacchetto e il relativo SPI associato;
3.  Eseguire l'elaborazione IPsec richiesta.

I seguenti selettori determinano una voce SPD:
*   **Indirizzo IP locale e remoto:** può trattarsi di un singolo indirizzo IP, un elenco enumerato o un intervallo di indirizzi o un indirizzo jolly (maschera). Gli ultimi due sono necessari per supportare più di un sistema, rispettivamente, di origine o di destinazione che condivide la stessa SA (ad esempio dietro un firewall);
*   **Next Layer Protocol:** l'intestazione del protocollo IP include un campo che designa il protocollo che opera su IP. Se si utilizza AH o ESP, questa intestazione del protocollo IP precede immediatamente l'intestazione AH o ESP nel pacchetto;
*   **Porte locali e remote:** possono essere singoli valori di porta TCP o UDP, un elenco enumerato di porte o una porta jolly.

> **Commentato [CP16]:** Security Policy [[Database]] (SPD) contiene le politiche di [[Sicurezza|sicurezza]] che determinano come il traffico di rete deve essere protetto.

## 3. Modalità di Trasporto e Tunnel
Sia AH che ESP supportano due modalità di utilizzo:

*   **Modalità trasporto:** computazionalmente più leggera, crittografa solo il payload del pacchetto IP, mantenendo l’header IP originale. È comunemente utilizzata per proteggere le comunicazioni host-to-host all’interno di una stessa rete;
*   **Modalità tunnel:** computazionalmente più pesante, crittografa l’intero pacchetto IP. È comunemente utilizzata per proteggere le comunicazioni gateway-to-gateway.

## 4. AH (Authentication Header)
L'autenticazione nel protocollo AH è assicurata calcolando un hash sui campi del pacchetto: ovviamente alcuni campi non vengono utilizzati per il calcolo dell'hash, come il campo TTL e il checksum. L'hash viene quindi inserito nell'intestazione AH.

*   **Modalità trasporto:** Tutti i pacchetti AH non sono crittografati, questo può essere utile quando non si vuole un sovraccarico causato dalla crittografia e si preferisce semplicemente preservare l'integrità dei dati. In questo modo nessuno può modificare il payload, la sorgente e la destinazione: per questo motivo AH è incompatibile con il natting del router perché i router NAT vogliono cambiare sorgente e destinazione continuamente.
*   **Modalità tunnel:** Di solito, questa modalità viene utilizzata durante la connessione router-to-router perché i router inoltrano il traffico già firmato e crittografato quindi il controllo di integrità è già stato effettuato; siamo solo preoccupati per l'invio e la ricezione di pacchetti in una VPN. Dopo che il vero datagramma è stato generato e incorporato in un nuovo grande datagramma, viene aggiunta una firma AH e il pacchetto viene inviato su Internet. Di solito, non ci fidiamo di ciò che c'è tra i router coinvolti nella conversazione, quindi aggiungiamo un livello di integrità tra i due.

## 5. Encapsulating Security Payload (ESP)
ESP può essere utilizzato per fornire riservatezza, autenticazione dell'origine dei dati, integrità senza connessione e un servizio anti-replay. L'insieme dei servizi forniti dipende dalle opzioni selezionate al momento della costituzione di SA.

Il formato di primo livello di un pacchetto ESP contiene i seguenti campi:
*   **Security Parameters Index (32 bit):** identifica una SA;
*   **Sequence Number (32 bit):** un valore del contatore che aumenta in modo monotono; questo fornisce una funzione anti-replay;
*   **Payload Data (variabile):** si tratta di un segmento a livello di trasporto (modalità trasporto) o di un pacchetto IP (modalità tunnel) protetto da crittografia;
*   **Padding (0–255 byte);**
*   **Pad Length (8 bit):** indica il numero di byte del pad immediatamente precedenti a questo campo;
*   **Next Header (8 bit):** identifica il tipo di dati contenuti nel campo dati del payload identificando la prima intestazione in quel payload;
*   **Integrity Check Value (variabile):** un campo di lunghezza variabile (deve essere un numero intero di parole a 32 bit) che contiene il valore di Integrity Check calcolato sul pacchetto ESP. Per gli algoritmi in modalità combinata, l'ICV che normalmente apparirebbe alla fine del pacchetto ESP (quando è selezionata l'integrità) può essere omesso.

I campi Payload Data, Padding, Pad Length e Next Header sono crittografati dal servizio ESP. Se l'algoritmo utilizzato per crittografare il payload richiede un vettore di inizializzazione, questi dati possono essere riportati esplicitamente all'inizio del campo Payload Data. L'ICV viene calcolato dopo l'esecuzione della crittografia. Questo ordine di elaborazione facilita il rilevamento rapido e il rifiuto di pacchetti riprodotti o fasulli da parte del destinatario prima della decrittazione del pacchetto.

Il campo Padding ha diversi scopi:
*   Se un algoritmo di crittografia richiede che il testo in chiaro sia un multiplo di un certo numero di byte, il campo Padding viene utilizzato per espandere il testo in chiaro alla lunghezza richiesta;
*   Il formato ESP richiede che i campi Pad Length e Next Header siano allineati a destra all'interno di una parola a 32 bit. In modo equivalente, il testo cifrato deve essere un multiplo intero di 32 bit. Il campo Padding viene utilizzato per garantire questo allineamento;
*   È possibile aggiungere ulteriore riempimento per fornire una parziale riservatezza del flusso di traffico nascondendo la lunghezza effettiva del payload.

Le superfici di attacco su protocollo ESP sono:
*   L'hash calcolato non copre l'indirizzo IP di origine e di destinazione, quindi è vulnerabile allo IP Spoofing, motivo per cui può essere utilizzato dal router senza alcun problema di natting;
*   Il campo Next viene inserito alla fine della variabile Padding per evitare attacchi: se fosse stato nel campo Payload, un hacker saprebbe che il primo byte del payload è riservato al campo Next;
*   SA è un campo obbligatorio ma se un utente malintenzionato tentasse di iniettare un datagramma originale, verrebbe comunque ricevuto e memorizzato nel buffer, perché la SA nel datagramma va bene.

*   **Modalità trasporto:** Fornisce protezione principalmente per i protocolli di livello superiore. In genere, la modalità trasporto viene utilizzata per la comunicazione end-to-end tra due host. Viene utilizzato per crittografare e facoltativamente autenticare i dati trasportati dall'IP. Per questa modalità che utilizza IPv4, l'intestazione ESP viene inserita nel pacchetto IP immediatamente prima dell'intestazione del livello di trasporto e un trailer ESP viene inserito dopo il pacchetto IP. L'intero segmento a livello di trasporto più il trailer ESP sono crittografati.
*   **Modalità tunnel:** La modalità tunnel fornisce protezione all'intero pacchetto IP. Per ottenere ciò, dopo che i campi AH o ESP sono stati aggiunti al pacchetto IP, l'intero pacchetto più i campi di [[Sicurezza|sicurezza]] vengono trattati con una nuova intestazione IP esterna. L'intero pacchetto originale, interno, viaggia attraverso un tunnel da un punto all'altro di una rete IP; nessun router lungo il percorso è in grado di esaminare l'intestazione IP interna. Poiché il pacchetto originale è incapsulato, il nuovo pacchetto più grande potrebbe avere indirizzi di origine e destinazione completamente diversi. La modalità tunnel viene utilizzata quando una o entrambe le estremità di una SA sono un gateway di [[Sicurezza|sicurezza]].
