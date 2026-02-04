Nella **Network Security** l'attaccante opera a livello di rete: può intercettare, modificare, iniettare pacchetti e controllare le proprie macchine. Molti problemi derivano da errori di progettazione nei protocolli originali (pensati in un'epoca in cui Internet era un ambiente "amichevole").

## 1. Attacchi ai protocolli (TCP/IP)

### Sequence Prediction Attacks
TCP usa numeri di sequenza a 32 bit per ordinare i pacchetti.
1.  **Handshake:** Il client invia $C$, il server risponde con $S$ e conferma $C$.
2.  **L'attacco:** Se l'attaccante può prevedere il numero di sequenza del server ($S$), può creare una **Spoofed Connection**. Invia pacchetti al server fingendosi il client.
3.  **Reset Attack (DoS):** Una volta indovinato il numero di sequenza, l'attaccante può inviare pacchetti `RST` per abbattere connessioni attive o forzare il ricalcolo delle tabelle di routing nei router.

### SYN Flooding
Sfrutta la gestione delle connessioni "semi-aperte". L'attaccante invia migliaia di pacchetti `SYN` da indirizzi IP falsi. Il server alloca risorse per ogni richiesta in attesa dell'ultimo `ACK`, esaurendo la memoria.
*   **Difesa:** **SYN Cookies** (rendere il server stateless durante l'handshake).

### Bandwidth Amplification
Attacchi DoS che usano un "riflettore" per inondare la vittima.
*   **Ping Flood:** Inviare `ICMP echo request` a indirizzi broadcast falsificando l'IP sorgente (quello della vittima).
*   **DNS Amplification:** Sfrutta il fatto che una piccola query DNS può generare una risposta molto grande (specialmente con DNSSEC).

## 2. Attacchi al DNS e al Livello 2

### DNS Spoofing
Il DNS usa UDP (senza numeri di sequenza). L'attaccante invia una risposta falsa prima del server legittimo, indovinando la porta.
*   **Difesa:** **DNSSEC** (firme digitali per i record).

### ARP Poisoning (ARP Spoofing)
Si basa sulla mancanza di autenticazione nel protocollo ARP.
1.  L'attaccante associa il proprio indirizzo MAC all'IP del gateway (o di un altro nodo).
2.  Il traffico viene deviato verso l'attaccante (**Man-in-the-Middle**).
3.  Permette spionaggio, modifica dati o DoS.

## 3. Strumenti di Difesa

### Firewall
Filtrano il traffico in base a regole (IP, porte).
*   **Stati:** Scartare tutto eccetto il consentito (*Whitelist*) o Inoltrare tutto eccetto il vietato (*Blacklist*).
*   **Stateful Inspection:** Traccia lo stato delle connessioni TCP.
*   **Application Gateway:** Proxy che analizza il traffico a livello applicativo (più sicuro ma lento).
*   **DMZ (Zona Demilitarizzata):** Area tra due firewall per ospitare server pubblici.

### IPS e IDS
*   **IPS (Intrusion Prevention System):** Versione evoluta del firewall che blocca attacchi attivamente.
*   **IDS (Intrusion Detection System):** Rileva intrusioni analizzando sensori e dati.
    *   **Host-based (HIPS/HIDS):** Monitora il singolo computer (chiamate di sistema, file critici).
    *   **Network-based (NIPS/NIDS):** Monitora il traffico dell'intera rete.
    *   **Signature-based:** Cerca pattern noti (es. tentativi di login "root").
    *   **Anomaly-based:** Rileva deviazioni dal comportamento normale (es. picchi di traffico).

### Honeypots
Macchine "esca" con vulnerabilità simulate per attirare attaccanti, studiarne le tecniche e proteggere i sistemi reali.
*   **Low interaction:** Emula solo alcuni servizi.
*   **High interaction:** Sistemi reali completi (molto rischiosi ma ricchi di informazioni).

## 4. VPN (Virtual Private Network)
Utilizzano cifratura (es. **IPsec**) per creare connessioni sicure su reti pubbliche. IPsec può operare in:
*   **Transport Mode:** Cifra solo il payload.
*   **Tunnel Mode:** Cifra l'intero pacchetto IP (ideale per gateway-to-gateway).
