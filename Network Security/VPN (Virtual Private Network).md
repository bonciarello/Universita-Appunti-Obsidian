---
aliases: [VPN, Virtual Private Network]
tags: [network-security]
---
Nel corso degli anni, diversi problemi ed esigenze sono sorti durante l'utilizzo delle [[Reti|reti]]:
*   I domini di collisione non sono sicuri;
*   I dipendenti hanno spesso bisogno di un accesso remoto alle risorse della rete aziendale: il traffico Internet è molto più vulnerabile alle intercettazioni rispetto a quello che circola in una rete aziendale interna (si pensi al Wi-Fi di un albergo);
*   Potrebbe essere necessario che più uffici remoti abbiano accesso alle stesse risorse;
*   Esigenze generali di privacy rispetto a ISP e governi.

La soluzione sarebbe quella di simulare un dominio di collisione utilizzando come mezzo trasmissivo Internet e, mediante un protocollo di tunneling, incapsulare i dati da trasmettere (ed eventualmente criptarli). Nasce così VPN.

Una **VPN (Virtual Private Network)** consente di simulare un dominio di collisione (LAN) utilizzando un collegamento virtuale che opera al di sotto del collegamento fisico (tunneling). In questo modo, la stazione connessa alla VPN riceverebbe un indirizzo della sottorete assegnata alla VPN.

Gli obiettivi principali di questa tecnologia sono:
*   Mantenere l'anonimato aggiungendo così un livello di [[Sicurezza|sicurezza]] e privacy;
*   **Exploiting Geographic proxying:** ovvero la possibilità di collegarsi ad indirizzi IP nazionali e di usufruire di tutti i vantaggi di questi ultimi;
*   Consentire alle aziende di creare canali sicuri per condividere le proprie risorse aziendali.

> **Commentato [CP14]:** Exploiting Geographic Proxying è una tecnica usata per ingannare sistemi di [[Sicurezza|sicurezza]] basati sulla localizzazione geografica.

## 1. Tipologie di VPN
Esistono diversi tipi di VPN:
*   **VPN per Roadwarriors:** questo tipo di VPN è utilizzato da utenti mobili (road warriors) che si connettono da diverse posizioni remote, come lavoratori in viaggio o da casa. Un uso tipico è consentire a singoli utenti di accedere alla rete aziendale da qualsiasi luogo, garantendo [[Sicurezza|sicurezza]] e privacy;
*   **VPN LAN-2-LAN:** questo tipo di VPN collega due o più [[Reti|reti]] locali (LAN) situate in posizioni diverse, creando un tunnel sicuro tra di esse. Un uso tipico è collegare uffici o sedi aziendali dislocate geograficamente in una rete aziendale unificata;
*   **VPN Many-To-Many:** questo tipo di VPN connette molte [[Reti|reti]] o nodi a molte altre [[Reti|reti]] o nodi, formando una rete complessa e interconnessa. Un uso tipico è in scenari in cui diverse [[Reti|reti]] aziendali o partner commerciali devono comunicare tra loro in modo sicuro e coordinato.

## 2. Funzionamento Tecnico
Cosa succede quando entriamo in una VPN?
Viene creato un nuovo collegamento tra la nostra postazione e il server ma è solo virtuale. Nella tabella di routing qualcosa cambia, invece di un gateway di default, ora abbiamo due gateway diversi ma con caratteristiche diverse: quello nuovo, infatti, è firmato come link migliore con una metrica più piccola; in questo modo il traffico viene sempre reindirizzato al nuovo gateway che è il server VPN. Un'altra voce viene aggiunta nella tabella di routing e specifica che ogni volta che un nuovo pacchetto deve raggiungere il server VPN, deve essere inoltrato verso il normale gateway predefinito.

Cosa succede quando proviamo a inviare o ricevere un pacchetto e utilizziamo una VPN?
Quando creiamo nuovo traffico, i pacchetti vengono inviati nel buffer di questa nuova scheda e hanno questo formato:
*   **Sender:** l'indirizzo IP della nuova scheda;
*   **Receiver:** l'indirizzo IP della stazione che stiamo cercando di raggiungere.

Il processo di uscita dei pacchetti originati viene gestito dal sistema operativo che ha improvvisamente riconosciuto che il collegamento che vogliamo utilizzare è virtuale quindi il pacchetto viene firmato, crittografato e incorporato in un pacchetto più grande che diventa il datagramma fisico da inviare con il precedente datagramma originato come payload. Il nuovo datagramma ha questo formato:
*   **Sender:** l'indirizzo IP della nostra scheda fisica;
*   **Receiver:** l'indirizzo IP del server VPN.

Successivamente, il pacchetto viene inoltrato al server VPN utilizzando il gateway predefinito in base alla nuova voce sulla tabella di routing. Il percorso tra la nostra postazione e il server VPN è un percorso sicuro: il traffico può essere intercettato ma nessuno può violarne la riservatezza e l'integrità perchè crittografato. Tuttavia, una volta nel server VPN, il pacchetto potrebbe essere compromesso.

Una volta nel server VPN, il traffico viene bufferizzato nella scheda virtuale assegnata alla conversazione con la nostra postazione: i server VPN sono speciali perché creano una nuova scheda per ogni nuova conversazione avviata. Quando il pacchetto raggiunge il server, viene decrittografato e la firma viene verificata. Se il sistema operativo lo ritiene integro, viene reindirizzato alla scheda corretta riservata alla conversazione. Successivamente, il pacchetto viene inoltrato alla corretta destinazione iniziale trovata nel payload del datagramma utilizzando come indirizzo IP di origine quello assegnato alla nostra stazione.

Dobbiamo stare attenti a questo punto perché, in base a quello che stiamo facendo e a quale protocollo stiamo usando, il pacchetto può essere in chiaro o cifrato; se stiamo comunicando con qualche stazione che non usa TLS, la conversazione è in chiaro e da quel momento, quando il pacchetto viene inoltrato alla destinazione corretta, il pacchetto può essere intercettato, compromesso e letto.

Quando l'altra stazione (un server o un altro client) risponde al pacchetto con un altro datagramma, raggiunge il server VPN che riconosce che l'indirizzo di destinazione utilizzato è quello assegnato alla nostra stazione; quindi, il pacchetto viene inviato alla scheda corretta che ri-incorpora, firma e crittografa il datagramma e lo invia alla nostra stazione. Il pacchetto che arriva alla nostra stazione ha questo formato:
*   **Sender:** l'indirizzo IP del server VPN;
*   **Receiver:** l'indirizzo IP della nostra stazione.

Una volta nella nostra stazione, il pacchetto viene riconosciuto e inserito nella scheda corretta, smembrato, decifrato e controllato con la firma.

### VPN Layer 7
L'utilizzo delle schede virtuali è importante perché uniformano il processo di inoltro e le applicazioni non devono modificare nulla nell'implementazione per consentire l'utilizzo della VPN. Tuttavia, ci sono alcune alternative all'uso di schede virtuali, come il proxy di livello 7 in cui la crittografia viene applicata su questo livello. Esistono due tipi di proxy:
*   **Semi-trasparente:** basato su SOCKS5 in cui ci sono proxy sock in attesa su una porta in cui viene ricevuto tutto il traffico TCP. L'intera applicazione deve essere progettata tenendo presente l'utilizzo di questo tipo di proxy altrimenti non funzionerà;
*   **Non trasparente:** basato sull'utilizzo del router con reindirizzamento automatico all'interno. Non vengono più utilizzati a causa di alcune vulnerabilità e dell'introduzione del certificato digitale e HTTPS. Bisogna precisare che, quando si utilizzano i proxy, è possibile che si verifichino problemi con la richiesta DNS perché non vengono reindirizzati alla scheda virtuale.

## 3. Modalità di Utilizzo
Una connessione VPN può essere impostata in due modi diversi:
*   **Full tunnel:** ogni pacchetto e traffico prodotto dal client deve essere reindirizzato alla scheda virtuale, quindi al server VPN e successivamente al destinatario reale. La configurazione full tunnel non è sempre la scelta giusta per un'azienda perché in questo modo viene utilizzata l'intera banda dell'azienda sia dalla rete locale che dalle postazioni VPN;
*   **Split tunnel:** quando connetti la tua stazione alla VPN, il gateway viene impostato in base alla stazione che deve raggiungere il pacchetto. Per accedere ad alcune risorse, il gateway predefinito è il server VPN, per altri è il normale gateway predefinito. In questo modo non puoi proteggere completamente la tua rete aziendale ma limiti l'accesso e l'utilizzo della banda.

## 4. Secure VPN
Recentemente, le VPN stanno diventando sempre più utilizzate ed è richiesto un certo grado di [[Sicurezza|sicurezza]]. Di conseguenza, nasce **Secure VPN** e sono caratterizzati da:
*   Meccanismi di autenticazione per gli endpoint del tunnel (durante la configurazione del tunnel);
*   Crittografia del traffico di transito: il traffico è crittografato e questo crea un "tunnel" tra due host;
*   La crittografia deve essere forte e può essere reimpostata nel tempo;
*   Hanno uno o più tunnel e ogni tunnel ha due endpoint.

Si utilizza Secure VPN:
*   Per proteggere il traffico quando Internet è la rete di appoggio;
*   In uno scenario di accesso remoto, in cui un client VPN (utente finale) si connette in modo sicuro alla rete di un ufficio remoto.

I protocolli utilizzati per implementare Secure VPN:
*   **SSH Tunneling:** ha numerosissimi tunneling facilities. Sfruttando TCP, purtroppo, soffre di alcune instabilità;
*   **SSL/TLS VPN:** l’autenticità delle comunicazioni viene preservata grazie all’uso di certificati digitali. Richiede, purtroppo, la necessità di installare particolari software;
*   **PPTP/SSTP (with MPPE):** serve per il point to point tunneling basato su MPPE. La crittografia e l’autenticazione sono molto deboli. Viene usato ancora oggi per compatibilità;
*   **[[IPSec|IPsec]];**
*   **[[IPSec]] + L2TP + IKEv2:** è la tecnologia più all’avanguardia. [[IPSec]] viene usato per la crittografia e la firma digitale. L2TP serve per il tunneling delle informazioni del layer 2, mentre IKEv2 serve per lo scambio delle chiavi e per l’autenticazione.

### Problemi
Purtroppo, le tecnologie VPN non sono compatibili con natting e TCP.
*   Nel primo caso, le VPN crittografano l'intero payload rendendo difficile trovare gli IP di origine e destinazione.
*   Nel secondo caso, TCP ha il proprio stream order. Quindi devi aspettare che arrivi l'intero flusso in modo che possa essere realmente elaborato dal sistema operativo. Ciò dà origine a problemi di **HOL (Head of line blocking)** poiché, per impostazione predefinita, le VPN sono prive di ordini.
