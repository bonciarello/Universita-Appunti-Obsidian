---
aliases: [RT]
tags: [network-security]
---
Rimanere anonimi su Internet è molto importante non solo dal punto di vista dell'aggressore ma anche per l'utente comune. Un modo importante per farlo è mantenere anonimo l'indirizzo IP. Sfortunatamente, non è abbastanza e spesso vuoi fare di più che mascherare il tuo IP. Per fare ciò, puoi utilizzare la rete TOR.

Dal punto di vista dell'utente, utilizzare TOR è molto semplice: basta installarlo e aprire il browser TOR: incanala tutto il traffico nella rete TOR. L'IP assegnato alla postazione personale dalla rete TOR non è il solito; infatti, quando ci colleghiamo al browser, viene utilizzato il **circuito TOR**: è una catena composta da free-peer nella rete TOR che gestirà la connessione di rete in modo tale da preservare alcune proprietà di privacy.

Quando il client apre il browser TOR, il browser TOR si collegherà a un server TOR che ha il ruolo di un server di directory e ha l'elenco dei nodi TOR correnti. Il client otterrà l'elenco e selezionerà casualmente tre nodi che sono i cosiddetti membri del circuito TOR:
*   Il primo nodo è il **nodo di entrata (nodo di guardia o di entrata)**;
*   Il secondo è il **nodo intermedio**;
*   Il terzo è il **nodo di uscita (di uscita)**.

Il client parlerà con il nodo di entrata che passerà l'informazione a quello intermedio, il nodo intermedio passerà l'informazione al nodo di uscita e quest'ultimo sarà quello che dialogherà con il servizio esterno che il client vuole raggiungere. Su richiesta, il circuito può essere modificato. Il membro conosce solo il nodo successivo nel percorso.

## 1. Onion Routing
L'infrastruttura TOR si basa su un protocollo chiamato **Onion Routing** e si basa sulla crittografia pubblica. Tutti i nodi TOR depositano una chiave pubblica nel server di directory.

Quando il client produce traffico, il traffico viaggia incorporato in pacchetti governati dall'Onion Routing: prima di inviare i dati originali sulla rete, i dati vengono crittografati utilizzando la chiave pubblica del nodo di uscita, i dati vengono nuovamente crittografati utilizzando la chiave pubblica del nodo intermedio e quindi i dati vengono nuovamente crittografati utilizzando la chiave pubblica del nodo di ingresso.

Come viene costruito e poi decostruito il pacchetto?
All'inizio i pacchetti vengono cifrati tre volte usando la chiave pubblica in ordine. Successivamente, il nodo di ingresso decifra il primo livello e invia il pacchetto al nodo intermedio; quindi, il nodo intermedio decifra i nodi e sa chi è il nodo di uscita; quindi, invierà il pacchetto al nodo di uscita. Il nodo di uscita è in grado di decrittografare le informazioni senza sapere chi sia l'origine delle informazioni. Quindi:
*   **Crittografia:** nodo di uscita $\rightarrow$ nodo intermedio $\rightarrow$ nodo di ingresso;
*   **Decrittografia:** nodo di ingresso $\rightarrow$ nodo intermedio $\rightarrow$ nodo di uscita;

Il browser TOR non accede solo alla normale infrastruttura DNS ma anche all'infrastruttura Onion DNS che contiene siti web con dominio ".onion". Questo è un dominio di primo livello non ufficiale.

## 2. Disponibilità degli Hidden Server
Gli **hidden server** sono servizi anonimi e l'utente non conosce il vero indirizzo IP ma può raggiungerlo utilizzando la rete TOR. Il protocollo che governa l'hidden server utilizza due circuiti diversi, uno per il client e l'altro per il server e i due circuiti dialogano tra loro.

Quando vogliamo parlare con l’hidden server:
*   L’hidden server stabilirà un circuito TOR fino al punto di introduzione;
*   Allo stesso tempo, il client stabilirà un circuito TOR per parlare con lo stesso punto di introduzione.

Quindi abbiamo:
*   Il client;
*   Tre nodi dal client al punto di introduzione;
*   Il punto di introduzione;
*   Tre nodi dal punto di introduzione all’hidden server;
*   L’hidden server.

Un totale di 7 nodi intermedi coinvolti nella conversazione; il punto di introduzione vedrà solo il traffico proveniente dal client e che viene instradato all'hidden server e viceversa; il punto di introduzione non conosce l'identità del client, né l'identità dell’hidden server.

## 3. Attacchi e Vulnerabilità
Quali sono i problemi della rete TOR?
*   **Timing attack:** le persone possono trovare la tua vera identità correlando il tempo di attività tra il nodo di ingresso e il nodo di uscita. Per fare ciò, è necessario registrare il traffico su un nodo di ingresso e su un nodo di uscita. Quindi, puoi applicare questo attacco se riesci a controllare i dati in entrata e in uscita dalla rete TOR;
*   **Majority attack:** se controlli gran parte della rete, le probabilità che un client selezioni i tuoi nodi come parte del circuito sono molto alte. Per questo motivo si consiglia di non modificare molto spesso i nodi di ingresso perché ad un certo punto è molto probabile che si selezionerà un nodo che non dovrebbe essere selezionato.
