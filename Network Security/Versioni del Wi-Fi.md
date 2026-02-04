Durante gli anni si sono avvicendate diverse metodologie per la gestione del WiFi:
*   Wi-Fi Open;
*   WLAN WEP;
*   WPA Personal/Enterprise;
*   WPA2 Personal/Enterprise;
*   WPA3 Personal/Enterprise.

## 1. Wi-Fi Open
Il primo tipo di rete Wi-Fi è stato il Wi-Fi Open che trasmette le conversazioni in chiaro. Nel Wi-Fi Open non c'è alcun tipo di controllo sull'integrità o sul livello di riservatezza. Per limitare il numero di persone che si connettono alla rete, per ragioni economiche e di "sicurezza", venivano solitamente utilizzati i **Captive Portal**.

I Captive Portal sono stati creati per inserire uno strato di riservatezza nel Wi-Fi Open: per ottenere questo scopo viene utilizzata una forma primitiva di autenticazione. Dopo aver inviato i primi pacchetti della comunicazione (Beacon e Association Packet), la stazione è connessa alla rete ma a livello 3 e 4 c'è un reindirizzamento forzato ad una pagina web del Captive Portal in cui effettuare il login. Questo tipo di reindirizzamento rompe molti controlli sul livello TLS e sul livello del certificato: se l'utente tenta di accedere alla pagina Facebook, quando viene reindirizzato alla pagina di login sta ancora aspettando il certificato della pagina Facebook che non riesce ad arrivare.

In che modo un Captive Portal può verificare se l'utente è autenticato o meno?
*   Controllare l’indirizzo MAC dell’utente, cosa che può essere facilmente bypassata tramite un attacco MAC Spoofing;
*   Utilizzo di cookie di sessione che costringono però a mantenere la pagina web aperta: questo permette di pingarsi a vicenda in modo tale da verificare se l’utente è ancora connesso o meno;
*   Controllando una tripla costituita dall’indirizzo IP, cookies e indirizzo MAC.

### Rogue ESSID
Uno dei principali problemi ancora disponibili nel Wi-Fi Open è il **Rogue ESSID**. Questo è un attacco MITM in cui l'attaccante cerca di impersonare l'ESSID fingendo di essere un vero BSSID. Coinvolge la creazione di una rete wireless fasulla che utilizza lo stesso nome (ESSID) di una rete legittima nella zona circostante. Ad esempio, se esiste una rete Wi-Fi legittima chiamata “MyWiFi”, un attaccante può creare un Rogue ESSID chiamato anche “MyWiFi”. Questo mira a ingannare gli utenti che cercano di connettersi alla rete legittima, spingendoli a connettersi invece alla rete malevola.

> **Commentato [CP8]:** SSID è il nome della rete, BSSID è l’indirizzo MAC di un singolo AP, e ESSID è l’SSID di una rete con più AP coordinati.

## 2. WLAN WEP
Il Wi-Fi Open è stato superato dalla **WLAN WEP (Wired Equivalent Privacy)**: l'idea era di introdurre alcuni controlli di integrità per evitare che gli aggressori potessero iniettare frame senza utilizzare un certificato. In WLAN WEP, una chiave comune a 48 o 64 bit chiamata **WEP Key** viene utilizzata da tutti gli utenti per accedere alla rete a cui vengono aggiunti 24bit per il **vettore di inizializzazione (IV)**. Questo tipo di chiave è facile da rompere perché molto piccola.

La WEP Key viene utilizzata per:
*   Garantire l’autenticazione: questa può essere ottenuta tramite l’approccio Open System Authentication o Shared Key Authentication. Nel primo caso l’utente non fornisce le proprie credenziali nella fase di autenticazione, ma in un secondo momento. Nel secondo caso invece la WEP-Key viene utilizzata per l’autenticazione in un handshake a quattro vie basato su challange e response;
*   Garantire la confidenzialità: all’interno di WEP è possibile configurare fino a quattro chiavi crittografiche di cui solo una poi viene effettivamente utilizzata per la cifratura dei messaggi. La key da scegliere viene determinata dal campo KeyID (2bit).

Purtroppo, la sicurezza non viene tutelata in alcun modo, infatti:
*   Essendo la chiave comune a tutti gli utenti, il traffico può essere facilmente decifrato;
*   Il vettore di inizializzazione è semplice da rompere;
*   Si possono collezionare numerosi messaggi crittografati per effettuare replay attack.

## 3. WPA Personal/Enterprise
L'ultima versione delle reti Wi-Fi è la rete **WPA (Wi-Fi Protected Access)**. Le reti WPA possono essere suddivise in due tipi di rete:
*   **WPA Personal:** in cui è sufficiente la chiave per accedere alla rete e utilizzarla;
*   **WPA Enterprise:** in cui è necessario un account individuale per entrare nella rete.

Le reti WPA sono molto simili alle reti WEP, infatti si usa ancora il vettore di inizializzazione (anche se adesso è estesa a 8 byte), si usa ancora l'algoritmo RC4 e si usa ancora la chiave condivisa ma, adesso, si usa in un modo diverso. Possiamo anche introdurre un nuovo campo, il **MIC (Message Integrity Check)**, che rende difficile applicare la crittoanalisi al testo cifrato.

> **Commentato [CP10]:** Il MIC (Message Integrity Check) è un valore usato per verificare l’integrità e l’autenticità di un messaggio. È una sorta di “firma” generata a partire dal contenuto del messaggio e una chiave segreta, permettendo al destinatario di rilevare eventuali modifiche non autorizzate al messaggio durante la trasmissione. Il MIC garantisce che il messaggio non sia stato alterato e che provenga da una fonte legittima.

L'uso e la generazione di una chiave segreta sono molto complessi nelle reti WPA. Esistono due tipi di chiavi:
*   **Pairwise Key:** utilizzate per la comunicazione tra un STA e un AP;
*   **Group Key:** utilizzate per la comunicazione multicast.

### Pairwise Key
Le Pairwise Key vengono utilizzate per la comunicazione tra una coppia di dispositivi, in genere tra una STA e un AP. Queste chiavi formano una gerarchia che inizia con una chiave master da cui altre chiavi vengono derivate dinamicamente e utilizzate per un periodo di tempo limitato. Al livello più alto della gerarchia ci sono due possibilità:
*   Una **Pre-Shared Key (PSK)**, una chiave segreta condivisa dall'AP e da una STA;
*   Una **Master Session Key (MSK)**, che viene generata durante la fase di autenticazione.

In entrambi i casi (PSK o MSK), esiste una chiave univoca condivisa dall'AP con ogni STA con cui comunica. La **Pairwise Master Key (PMK)** è derivata dalla chiave master. Se viene utilizzato un PSK, il PSK viene utilizzato come PMK; se viene utilizzato un MSK, il PMK viene derivato dal MSK mediante troncamento (se necessario).

La PMK viene utilizzato per generare la **Pair Transient Key (PTK)**, che di fatto è composta da tre chiavi da utilizzare per la comunicazione tra STA e AP:
*   **KCK:** garantisce l’integrità e l’autenticità durante le procedure di associazione;
*   **KEK:** protegge la confidenzialità delle chiavi durante le procedure di associazione;
*   **TK:** garantisce la confidenzialità del traffico.

### Group Key
Le Group Key vengono utilizzate per la comunicazione multicast in cui una STA invia MPDU a più STA. Al livello superiore della gerarchia delle Group Key, si trova la **Group Master Key (GMK)**. La GMK è una chiave di generazione di chiavi utilizzata con altri input per derivare la **Group Temporal Key (GTK)**. A differenza del PTK, che viene generato utilizzando materiale sia da AP che da STA, il GTK viene generato dall'AP e trasmesso agli STA associati.

La GTK viene distribuita in modo sicuro utilizzando le Pairwise Key già stabilite. La GTK viene cambiata ogni volta che un dispositivo entra in rete: non vogliamo che possano accedere ai vecchi frame broadcast. Il GTK viene utilizzato per crittografare e decrittografare i frame broadcast.

Vengono create due chiavi aggiuntive rispetto alle tre precedentemente citate:
*   Chiave temporale AP Tx MIC: utilizzato per firmare i frames;
*   Chiave temporale AP Rx MI: utilizzata per decifrare i frame in entrata.

### Handshake
Alcune annotazioni importanti:
*   Se uno degli interlocutori non conosce la PMK giusta, l’handshake non funziona;
*   Essendo terminato il calcolo del PTK, i frame devono essere firmati ed è possibile solo possedere il giusto MIC, generato con il KCK usato per confermare la transazione EAPOL; se il MIC è sbagliato, l'handshake non funziona.
*   Se l'handshake continua, l'AP calcola lo stesso PTK dello STA e invia un messaggio firmato allo STA, includendo il GTK; lo STA controlla il MIC del frame e, se il MIC è errato, blocca il login nell'AP;
*   Dal quarto e ultimo messaggio (usato solo come riconoscimento), la STA può inviare messaggi ed emergere; l'AP aggiorna la sua tabella interna con l'indirizzo MAC della STA identificando la STA come un nuovo utente che può utilizzare la rete.

> **Commentato [CP9]:** Ecco una spiegazione semplice del funzionamento dell’handshake a quattro vie:
> 1. AP invia nonce: L’AP invia un numero casuale (nonce) al client.
> 2. Client genera chiave: Il client utilizza il nonce ricevuto dall’AP e un proprio nonce per generare una chiave di sessione.
> 3. Client invia nonce e chiave hashata: Il client invia il proprio nonce e una chiave hashata all’AP.
> 4. AP conferma: L’AP verifica la chiave hashata e, se corretta, invia una conferma al client.
> Questo scambio assicura che entrambe le parti abbiano una chiave condivisa senza trasmettere la chiave vera e propria attraverso la rete.

Ci sono alcuni problemi:
*   Se l'aggressore si trova nella stessa rete dell'utente, può registrare l'handshake e calcolare la chiave per impersonare l'utente;
*   Se l'utente è già connesso, può de-autenticarlo in modo che debba rifare la parte dell'handshake;
*   Utilizzando una Rainbow Table, l'attaccante può facilmente calcolare la chiave: alcuni aggressori forniscono una Rainbow Table di una coppia di SSID e una passphrase come Rainbow Table di un possibile SSID; il problema è che a volte l'AP sceglie passphrase semplici e predefinite che possono essere violate.

## 4. WPA Enterprise
WPA Personal ed Enterprise sono molto diverse e simili allo stesso tempo. Anche in quello Enterprise c'è un handshake ma non c'è una passphrase utilizzata per connettere la stazione alla rete. L'autenticazione viene effettuata da un server di autenticazione che utilizza il protocollo **RADIUS** per comunicare con l'AP. Ci sono tre diversi personaggi in questo scenario: l'AP, chiamato autenticatore, la stazione richiedente, quella che chiede di entrare nella stazione, e il server. Il protocollo utilizzato per le reti aziendali WPA è l'802.1X.

La stazione richiedente e l'AP avviano una conversazione utilizzando il protocollo **EAP**, che consente diversi tipi di autenticazione. Durante questa fase, l'AP inoltra i pacchetti di autenticazione al server RADIUS, bloccando ogni diversa conversazione con il supplicant se non quella utilizzata. Quando la fase di autenticazione è andata a buon fine, inizia la derivazione della Master Key: è una chiave usata una sola volta che è un Nonce casuale ed è decisa da entrambe le parti dell'autenticazione. I dati condivisi nella prima parte della conversazione possono essere testo in chiaro o testo cifrato utilizzando una cifratura scelta in precedenza. In alcune reti, come edoroam, viene utilizzato il protocollo **PEAP MSCHAPv2**: è una versione protetta del protocollo EAP. In questo caso, sia il client che il server vengono autenticati. In particolare, il richiedente deve autenticarsi mediante challenge. Il vantaggio di questo tipo di protocollo è che avere un server autenticato evita la presenza di infrastrutture Rouge.

> **Commentato [CP11]:** La transazione EAPOL (Extensible Authentication Protocol over LAN) è un protocollo utilizzato nelle reti Ethernet e Wi-Fi per gestire l’autenticazione tra un dispositivo client e un AP o server.

> **Commentato [CP12]:** PEAP (Protected Extensible Authentication Protocol) MSCHAPv2 è un metodo di autenticazione usato nelle reti Wi-Fi per proteggere le credenziali degli utenti. PEAP crea un tunnel sicuro utilizzando TLS (Transport Layer Security) per proteggere le comunicazioni, e all’interno di questo tunnel sicuro, utilizza MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol version 2) per autenticare l’utente. Questo metodo migliora la sicurezza impedendo l’intercettazione delle credenziali durante la trasmissione.

Il server RADIUS memorizza un database di utenti registrati. Offre servizi di triple AAA:
*   **Authentication:** dietro le quinte esiste un database che permette di registrare gli utenti;
*   **Authorization:** ci sono un insieme di regole di firewall che vengono applicate a gruppi di utenti;
*   **Accounting:** importante quando si vuole garantire le responsabilità delle proprie azioni.

I server RADIUS possono essere configurati gerarchicamente in modo tale da passare le richieste di autenticazione ai relativi server (meccanismo simil server DNS). Succede che il server RADIUS locale della zona, non riuscendo a identificare l'utente perché il dominio non è quello previsto, inoltra la risposta ad un server gerarchicamente superiore che cerca di identificare il dominio di provenienza e lo inoltra al server competente. Sfortunatamente, questo porta a problemi di privacy poiché un utente potrebbe essere identificato e localizzato geograficamente in base all'origine della richiesta di autenticazione. Il vantaggio è che, se una stazione è compromessa, le altre non ne risentono. Lo svantaggio è che i certificati RADIUS sono ancora mal gestiti sul lato client. Solitamente il client non riesce a capire se l'errore causato dal certificato esiste perché la certificazione del server è scaduta oppure perché c'è una CA falsa e, quindi, il certificato è falso.

## 5. Attacchi e Vulnerabilità
Alcune cose da sapere:
*   Sono state riscontrate alcune vulnerabilità nella WPA Personal causate dalla debole generazione della passphrase. Di solito, la passphrase predefinita nell'AP non viene modificata dall'utente comune; alcuni algoritmi che utilizzano l'indirizzo MAC dell'AP possono ottenere la passphrase predefinita;
*   WPA ha una nuova versione **WPA3**: lo standard di questa nuova versione utilizza un handshake basato sullo scambio Diffie-Hellman e supporta il Forward Secrecy;
*   WPA è ancora vulnerabile agli attacchi **IP Spoofing**: immaginiamo uno scenario in cui vi siano due diversi supplicant e un AP: il supplicant utilizza la propria chiave privata per cifrare i dati e solitamente tale chiave non è nota alle altre postazioni. Il PTK utilizzato si basa sull'indirizzo MAC utilizzato dalla stazione quindi un problema per l'attaccante potrebbe essere trovare la firma correlata al MIC. Il problema è che anche senza cambiare indirizzo, la stazione potrebbe comunque impersonare l'altra stazione. L'IP è solo una parte del payload e non viene verificato da nessuno. Quindi, cambiando l'IP ma mantenendo l’indirizzo MAC corretto, l'aggressore può firmare i propri pacchetti con il proprio MIC ma può arrivare ad avvelenare la tabella ARP dell'AP creando un'associazione errata tra l'IP della vittima e il proprio indirizzo MAC. Quindi, in questo modo, ogni volta che l'AP deve inviare un pacchetto, invierà il pacchetto all'aggressore e l'attaccante reindirizzerà il pacchetto alla vittima utilizzando l'indirizzo MAC della vittima come indirizzo di destinazione. Il pacchetto inviato dall'aggressore alla vittima raggiungerà, ovviamente, l'AP prima e solo dopo la vittima ma questo va bene perché:
    *   Prima il pacchetto viene crittografato e decrittografato utilizzando il PTK dell'attaccante;
    *   Dopo viene crittografato e decrittografato utilizzando il PTK della vittima.

> **Commentato [CP13]:** WPA (Wi-Fi Protected Access) è vulnerabile agli attacchi di IP spoofing perché, anche se protegge le trasmissioni dati tramite crittografia, non verifica l’integrità dell’indirizzo IP di origine. Un attaccante può falsificare l’indirizzo IP di un dispositivo legittimo per inviare dati malintenzionati, poiché WPA non autentica i pacchetti IP stessi. Questo consente di aggirare le misure di sicurezza basate solo sull’indirizzo IP.
