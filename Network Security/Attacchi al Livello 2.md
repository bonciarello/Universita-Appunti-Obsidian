---
aliases: [AL, Attacchi Livello 2]
tags: [network-security]
---
La maggior parte degli attacchi **MITM** appartiene ad aggressori che condividono il tuo stesso dominio di collisione, cioè, si trovano nella stessa rete locale. Ovviamente, questo non significa che i MITM non possano appartenere ad un aggressore al di fuori della rete locale. I MITM sono collegati a problemi di [[Sicurezza|sicurezza]] nel livello 2. In particolare, il protocollo **ARP** ha il ruolo principale negli attacchi MITM, perché può consentire attacchi che violano la riservatezza e la disponibilità.

Alcuni dei problemi comuni associati al livello 2:
*   ARP Poisoning;
*   DHCP Spoofing;
*   Reflection Attack;
*   DNS Spoofing;
*   DNS Rebinding.

## ARP Poisoning
L’**ARP Poisoning**, noto anche come ARP Spoofing, è un tipo di attacco utilizzato per manipolare o corrompere l’ARP table all’interno di una rete locale. Questa tecnica viene impiegata per intercettare o manipolare il traffico di rete e/o eseguire attacchi MITM.

Un attaccante invia falsi messaggi ARP all’interno della rete, dichiarando falsamente che il suo indirizzo MAC corrisponde all’indirizzo IP di un altro dispositivo presente nella rete. In questo modo, l’attaccante induce gli host nella rete a inviare il traffico destinato all’indirizzo IP legittimo all’indirizzo MAC controllato dall’attaccante.

A peggiorare la situazione esistono alcuni meccanismi interni all’ARP come quello del **Gratuitous ARP** che permette di inviare sporadicamente delle Broadcast ARP Response senza la necessità che vi siano state delle request. Questo, come si può immaginare, aggiorna automaticamente le tabelle degli host causandone “l’avvelenamento”.

Purtroppo, l’ARP Poisoning può verificarsi anche autonomamente. Nella stessa rete, magari per configurazione statica, due dispositivi possono avere lo stesso indirizzo IP. Pertanto, chi vince la race condition riesce ad aggiornare le ARP table dei vicini e quindi diventa un MITM accidentale (partial MITM).

Le possibili contromisure sono:
*   **ARP Watching:** è un tool che monitora tutta l’attività ARP intercettando ogni pacchetto di broadcast ARP che giunge sull’interfaccia di rete;
*   **Static ARP tables:** l’ARP table è configurata manualmente. Il problema è che si deve fare anche l’update manualmente;
*   **ARP Jamming:** si crea un deamon all’interno della rete attraverso cui si forzano informazioni buone. Inoltre, cerca di creare traffico da IP che dovrebbero essere inattivi al fine di evitare che un MITM possa configurarsi;
*   **SSL/TLS;**
*   **Tecnologia VPN:** si creano dei tunnel sicuri tra due dispositivi;
*   **Metodologia User Isolation:** gli utenti non possono comunicare se non che con il router, quindi qualsiasi comunicazione P2P viene bloccata.

## DHCP Spoofing
Il **DHCP Spoofing** è un tipo di attacco in cui viene manipolato o compromesso il protocollo DHCP (Dynamic Host Configuration Protocol) all’interno di una rete. Questo attacco mira a fornire agli host nella rete configurazioni DHCP false o dannose. L’attaccante invia pacchetti DHCP falsificati all’interno della rete, dichiarando falsamente di essere un server DHCP affidabile e legittimo modificando dati come il server DNS, l’indirizzo IP e il GW di destinazione.

Come per l’ARP Poisoning, anche qui potrebbe accadere involontariamente (**Maisonette Episode**). Basta infatti che un altro dispositivo (ad esempio un router) venga messo all’interno della rete affinché concorra a diventare un server DHCP.

Le possibili contromisure sono suddivise in due rami:

*   **Prevention:**
    *   **Switch ACLs/DHCP Snooping:** si introducono all’interno della rete degli switch configurabili in cui opera una sottospecie di firewall ACLs (Access Control List) che permette di mappare una porta ad una specifica porta TCP/UDP al fine di bloccarne i pacchetti;
    *   **DHCP starvator:** disabilita qualsiasi connessione che non provenga da un indirizzo IP autorizzato.
*   **Active Monitoring:** ci sono deamons che guardano la rete locale e controllano i record non legittimi notificando l’admin. Purtroppo, la notifica potrebbe arrivare troppo tardi. Si possono utilizzare due strategie:
    *   Detection of multiple DHCP replies;
    *   DHCP probe.

## Reflection Attack
Il **Reflection Attack** è un tipo di attacco in cui si sfrutta server o dispositivi di rete per generare un volume elevato di traffico indesiderato verso una vittima specifica. Questo attacco sfrutta una caratteristica dei protocolli di rete che consentono il “riflesso” del traffico verso un destinatario senza autenticazione. L’obiettivo principale è quello di dirottare il traffico destinato a un determinato sito web o servizio verso un sito web contraffatto o malevolo controllato dall’attaccante.

Le possibili contromisure sono:
*   Limitare i protocolli che effettuano chiamate in broadcast, come ICMP;
*   Configurazione dei firewall;
*   **Reverse Path filtering (RPF):** consiste nell’andare a verificare che l’indirizzo IP sorgente che sta per transitare nel router possa, in qualche modo, “tornare indietro” seguendo lo stesso percorso e, quindi, interfaccia. Qualora questo non fosse possibile, il pacchetto viene scartato.

## DNS Spoofing
Il **DNS Spoofing** è un tipo di attacco in cui viene manipolato o corrotto la risoluzione DNS (Domain Name System) per indirizzare in modo fraudolento gli utenti verso indirizzi IP falsi o dannosi. L’attaccante modifica i record DNS per associare un nome di dominio legittimo a un indirizzo IP controllato dall’attaccante.

Le possibili contromisure sono:
*   Si cercano di identificare se ci sono risposte multiple (IDS);
*   Si usa il protocollo **DNSSEC** che garantisce l’affidabilità e l’integrità delle risposte DNS tramite meccanismi di firma digitale.

## DNS Rebinding
Il **DNS Rebinding** è un tipo di attacco che sfrutta le vulnerabilità nelle politiche di [[Sicurezza|sicurezza]] dei browser web per aggirare le restrizioni di [[Sicurezza|sicurezza]] associate al modello Same-Origin Policy (SOP). L’obiettivo principale del DNS Rebinding è quello di accedere a risorse o informazioni all’interno di una rete locale a cui altrimenti non avrebbe accesso. Il funzionamento del DNS Rebinding si basa sull’uso di un dominio che inizialmente punta a un indirizzo IP controllato dall’attaccante, ma successivamente cambia per puntare a un indirizzo IP all’interno della rete locale della vittima. Ciò viene realizzato mediante la manipolazione dei record DNS o l’utilizzo di timeout brevi per le risoluzioni DNS.

Le possibili contromisure sono:
*   Configurazione corretta del firewall e del router;
*   Aggiornamento e patching dei sistemi utilizzati.
