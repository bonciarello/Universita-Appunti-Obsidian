## 1. Introduzione
Le reti Wi-Fi sono regolate dallo standard **802.11** che definisce principalmente le tecnologie Wi-Fi. In ogni rete Wi-Fi ci sono alcune stazioni, abbreviate con **STA**, ognuna di queste ha un indirizzo MAC e una **NIC (Network Interface Controller)**. Ogni STA può anche sintonizzarsi su un canale o saltare da un canale all'altro; in quest'ultimo caso, quando la NIC "salta" da un canale all'altro, possiamo parlare di **channel hopping**.

Le reti wireless funzionano utilizzando la tecnologia **Radio Frequency (RF)**, una frequenza associata alla propagazione delle onde radio all'interno dello spettro elettromagnetico. Un campo elettromagnetico viene generato quando una corrente RF viene fornita a un'antenna che può quindi diffondersi nello spazio.

Un sistema riconosciuto come **access point (AP)** è il cuore di una rete wireless. Il ruolo principale di un AP è trasmettere un segnale wireless rilevato e sintonizzato dai computer. Anche la RF è governata dallo standard 802.11 che utilizza lo spettro 2.4-2.5 GHz. Ogni spettro è suddiviso in canali con frequenza centrale e larghezza di banda.

## 2. Bande e Canali
La banda a 2,4 GHz è suddivisa in 14 canali distanziati di 5 MHz, a partire dal canale 1, che è centrato su 2,412 GHz. Non tutti i canali sono liberamente utilizzabili, ma ci sono limitazioni: alcuni di essi sono liberamente utilizzabili, ad esempio quelli su cui è collegato il Bluetooth. Un esempio di canali non utilizzabili sono quelli utilizzati dall'aeroporto per il radar aereo.

La banda utilizzata consente di definire due limiti:
*   La velocità di trasferimento;
*   La compatibilità con altri dispositivi vicini.

La trasmissione avviene in un certo range di frequenza, ma ha anche un range superiore e inferiore in cui trasmette il segnale, anche se in una potenza decisamente inferiore, ma che può ridurre l'efficacia degli altri ripetitori. I canali della banda 2.4 GHz sono parzialmente sovrapposti tra loro in frequenza; quindi, c'è una forte interferenza tra due canali consecutivi. Quando una particolare STA è sintonizzato su un determinato canale, la STA non riceve i pacchetti dell'altro canale e non sa cosa sta succedendo al di fuori del suo canale, questo perché ogni canale è come un mezzo trasmissivo separato e diverso e le conversazioni sugli altri canali vengono interpretate come rumori poiché i diversi canali si sovrappongono. Per comunicare, i dispositivi devono utilizzare una versione Wi-Fi comune: cronologicamente avremo g, n, ac e ax.

## 3. Modalità della NIC
Ogni NIC in una STA può essere impostata in due diverse modalità per ricevere diversi tipi di pacchetti:

*   **Modalità monitor:** nessun filtro sul BSSID. Tutti i telegrammi ricevuti vengono bufferizzati.
*   **Modalità managed:** configurazione standard, vengono bufferizzati solo i frame il cui BSSID corrisponde a quello a cui è associato lo STA. Essenzialmente i frame che non superano il cosiddetto BSSID Filter vengono scartati.

## 4. Discovery e Fingerprinting
La lista delle reti WiFi che il nostro computer riesce a identificare viene stilata in diversi modi:
*   **Beacon Frames:** l’access point notifica in broadcast la sua presenza;
*   **Probe Frames:** sono delle richieste esplicite che il client fa nei confronti dell’access point. Tramite esse è possibile contattare access point nascosti;

La nostra NIC, inoltre, mantiene in memoria una cronologia di tutti gli access point a cui ci siamo collegati e, ogni volta che viene accesa, manda Probe Frame a raffica. L’insieme delle stazioni da noi conosciute può costituire una sottospecie di firma dell’utente (**fingerprint**) che viene utilizzata addirittura per identificare se un determinato dispositivo è stato in una specifica zona.

## 5. WDS (Wireless Distribution System)
Un **Wireless Distribution System (WDS detto anche Back Haul)** è un sistema che consente l'interconnessione wireless di access point in una rete IEEE 802.11. Consente di espandere una rete wireless utilizzando più access point senza il tradizionale requisito di una rete cablata per collegarli. Il notevole vantaggio di WDS rispetto ad altre soluzioni è che conserva gli indirizzi MAC dei frame client attraverso i collegamenti tra gli access point.

Un access point può essere una stazione:
*   **Base principale:** generalmente collegata all'Ethernet (cablata);
*   **Relay:** ritrasmette i dati tra stazioni base remote, client wireless o altre stazioni relay;
*   **Remota:** accetta le connessioni dai client wireless e le passa alle stazioni relay o alle stazioni base principali.

Le connessioni tra "client" vengono effettuate utilizzando gli indirizzi MAC. Tutte le stazioni base in un sistema di distribuzione wireless devono essere configurate per utilizzare lo stesso canale radio, metodo di crittografia (nessuno, WEP, WPA o WPA2) e le stesse chiavi di crittografia.
