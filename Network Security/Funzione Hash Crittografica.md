---
aliases: [Grafo]
tags: [network-security]
---
Una **funzione hash** $H$ accetta un blocco di dati $M$ di lunghezza variabile come input e produce un risultato di dimensione fissa $h=H(M)$, denominato **digest** o **hash value** la cui dimensione è solitamente di 128 o 256 bit.

Una "buona" funzione hash ha la proprietà che i risultati dell'applicazione della funzione a un ampio insieme di input produrranno output uniformemente distribuiti e apparentemente casuali. In termini generali, l'obiettivo principale della funzione hash è l'**integrità dei dati**: una modifica a qualsiasi bit in $M$ risulta, con alta probabilità, in una modifica del digest.

## 1. Proprietà
Il tipo di funzione hash necessaria per le applicazioni di [[Sicurezza|sicurezza]] viene definita **funzione hash crittografica** per il quale è computazionalmente impossibile trovare:

*   Un oggetto che esegue il mapping a un risultato hash pre-specificato (**proprietà unidirezionale**);
*   Due oggetti mappati allo stesso risultato hash (**proprietà delle collisioni**).

A causa di queste caratteristiche, le funzioni hash vengono spesso utilizzate per determinare se i dati sono stati modificati o meno. In genere, il campo della lunghezza del messaggio è una misura di [[Sicurezza|sicurezza]] per aumentare la difficoltà per un utente malintenzionato di produrre un messaggio alternativo con lo stesso valore hash. Per evitare forti attacchi di collisione, è importante aumentare le dimensioni del messaggio di un numero enorme.

> **Commentato [CP3]:** La proprietà dell’unidirezionalità nelle funzioni hash significa che è facile calcolare l’hash per un dato input, ma estremamente difficile invertire il processo, ovvero risalire all’input originale a partire dall’hash.

> **Commentato [CP4]:** La proprietà delle collisioni nelle funzioni hash si riferisce alla possibilità che due diversi input producano lo stesso hash. Una buona funzione hash minimizza le collisioni, rendendole rare, poiché le collisioni possono compromettere la [[Sicurezza|sicurezza]] e l’integrità dei dati.

## 2. Message Authentication
Il **message authentication** (autenticazione del messaggio) è un meccanismo o un servizio utilizzato per verificare l'integrità di un messaggio e garantisce che i dati ricevuti corrispondano esattamente a quelli inviati.

Gli step necessari per l’uso di una funzione hash per l'integrità del messaggio sono:
1.  Il mittente calcola un valore hash in funzione dei bit nel messaggio e trasmette sia il valore hash che il messaggio;
2.  Il ricevitore esegue lo stesso calcolo hash sui bit del messaggio e confronta questo valore con il valore hash in arrivo;
3.  Se c'è una mancata corrispondenza, il destinatario sa che il messaggio (o forse il digest) è stato alterato. Il digest deve essere trasmesso in modo sicuro.

Esistono diversi modi in cui è possibile utilizzare un codice hash per fornire l'autenticazione dei messaggi:

1.  Il messaggio e il codice hash concatenato viene crittografato utilizzando la [[Crittografia Simmetrica|crittografia simmetrica]]: poiché solo A e B condividono la chiave segreta, il messaggio deve provenire da A e non è stato alterato;
2.  Solo il digest viene crittografato, utilizzando la [[Crittografia Simmetrica|crittografia simmetrica]];
3.  Utilizzare una funzione hash ma nessuna crittografia per l'autenticazione dei messaggi: la tecnica presuppone che le due parti comunicanti condividano un valore segreto comune $S$ e si procede come di seguito:
    *   A calcola il valore hash sulla concatenazione di $M$ e $S$ e aggiunge il valore hash risultante a $M$;
    *   Poiché B possiede $S$, può ricalcolare il valore hash per verificare;
    *   Poiché il valore segreto stesso non viene inviato, un avversario non può modificare un messaggio intercettato e non può generare un messaggio falso.
4.  La riservatezza può essere aggiunta all'approccio del metodo precedente crittografando l'intero messaggio e il codice hash.

C'è un problema con i metodi 2 e 3: un attaccante, che non conosce la chiave o il seme, può provare a rompere questi metodi usando il digest stesso. Ad esempio, invece di provare a decifrare la cifratura cercando la chiave, può provare a iniettare un messaggio $M'$ e con un po' di fortuna può trovare una collisione che abbia senso.

## 3. HMAC
L’**HMAC (Hash Message Authentication Code)** è una costruzione crittografica che combina una funzione di hash crittografica con una chiave segreta per fornire autenticazione e integrità dei dati.

L’HMAC opera nel seguente modo:
1.  Prende un messaggio di input;
2.  Applica una funzione di hash crittografica (come SHA-256 o MD5) al messaggio di input;
3.  Combina il risultato dell’hash con una chiave segreta utilizzando operazioni di XOR e concatenazione;
4.  Applica nuovamente la funzione di hash all’output combinato;
5.  Il risultato finale, chiamato HMAC, viene utilizzato per autenticare e verificare l’integrità dei dati.

L’HMAC garantisce che il messaggio non sia stato alterato e che provenga da una fonte autentica ed è ampiamente utilizzato in diversi protocolli e applicazioni che richiedono [[Sicurezza|sicurezza]] dei dati.

## 4. Attacchi alle Funzioni Hash
Come per gli algoritmi di crittografia, esistono due categorie di attacchi alle funzioni hash:

*   **Bruteforce:** dipende solo dalla lunghezza in bit del valore hash. Nello specifico, l’attaccante deve sperare di bypassare:
    *   La *preimage* o la *second preimage property* e quindi di trovare un valore $y$ tale per cui l’hash corrisponda al messaggio originale;
    *   La *resistenza alle collisioni* e quindi trovare una coppia di messaggi $x$ e $y$ tale per cui si verifica una collisione;
*   **Crittoanalisi:** basato sui punti deboli di un particolare algoritmo crittografico. In questa categoria appartengono due famosi attacchi:
    *   **Length Extension Attack:** l’attaccante sfrutta $H(M1)$ e la sua lunghezza per calcolare l’hash di $H(M1+M2)$ senza la necessità di conoscere il contenuto del messaggio $M1$;
    *   **Shatter Attack:** è possibile iniettare all’interno di un documento particolari elementi tali per cui è possibile prevedere il corrispondente hash.
