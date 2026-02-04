I dispositivi hardware appartenenti al livello 2 sono gli switch e gli hub: questi ultimi in particolare applicano politiche di smistamento insicure. Grazie, naturalmente, alla crittografia il problema viene mitigato ma purtroppo continuano a persistere numerosi problemi.

## Port Stealing
Il **Port Stealing** è una tecnica utilizzata per acquisire il controllo di una porta di comunicazione di un sistema informatico senza l’autorizzazione del proprietario legittimo della porta. Questa tecnica viene spesso utilizzata per sfruttare una connessione già stabilita tra due entità e interferire con essa.

## MAC Flooding
Il **MAC Flooding** consiste nell’“inondare” lo switch di numerosi pacchetti ARP in maniera tale da andare ad intasargli la memoria: infatti, quando quest’ultima si satura, lo switch si comporta esattamente come se fosse un hub. In questo modo, tutti i frame vengono inviati a raffica su ogni porta e quindi è possibile intercettarli.

## Contromisure
Le possibili contromisure a questa tipologia di attacchi sono:

*   **Port locking:** programmazione delle voci della tabella degli switch in modalità statici;
*   **Standard IEEE 802.1X:** la soluzione finale, fornisce il controllo di autenticità dell’indirizzo MAC. Funziona con i server di autenticazione in modo che la prima volta che una stazione desidera inviare un pacchetto, deve effettuare il login. Una volta effettuato il login, la porta viene assegnata a quello specifico indirizzo MAC. Ogni voce nelle tabelle di accesso viene salvata fino a quando la stazione effettua il logout.
