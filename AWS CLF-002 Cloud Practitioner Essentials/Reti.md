## Connettività ad AWS

Un **Amazon VPC (Virtual Private Cloud)** è una rete privata virtuale che puoi creare e gestire all’interno di AWS. Ti consente di definire un intervallo di indirizzi IP privati e ospitare al suo interno risorse come istanze EC2 o load balancer. In pratica, è come avere la tua rete dedicata nel cloud, con controllo completo su configurazioni e regole di accesso.

Le **sottoreti** suddividono la VPC in segmenti più piccoli, permettendo di separare le risorse pubbliche da quelle private. In base alla sottorete in cui collochi una risorsa, puoi decidere se renderla accessibile da Internet (ad esempio un sito web) oppure solo da una rete privata interna (come un database aziendale).

Un **gateway internet** è una connessione tra un VPC e internet. È come una porta d’ingresso pubblica: senza di esso, nessuna risorsa della VPC può comunicare con l’esterno. Serve quindi quando vuoi rendere accessibili risorse pubbliche, ad esempio un sito web ospitato su EC2.

Un **Virtual Private Gateway** permette di collegare la tua VPC a una rete privata esterna (ad esempio il tuo data center aziendale) tramite una connessione VPN. È come avere una navetta privata e sicura che collega l’azienda alle risorse AWS, accessibile solo da utenti autenticati. La connessione è cifrata, ma utilizza comunque Internet, quindi può risentire di limiti di banda e latenza.

**AWS Direct Connect** è un servizio che consente di stabilire una connessione privata dedicata tra il data center e un VPC: questa connessione garantisce maggiore velocità, minore latenza e più sicurezza. È la soluzione ideale per aziende con forti esigenze di affidabilità o requisiti normativi, perché offre un collegamento diretto e dedicato al cloud AWS. All’interno della stessa VPC puoi configurare più tipi di gateway, in sottoreti diverse, a seconda delle esigenze: ad esempio un gateway internet per i servizi pubblici, un Virtual Private Gateway per la connessione VPN privata e un Direct Connect per un collegamento dedicato. Questo consente grande flessibilità nella progettazione della rete.

*   **AWS PrivateLink** collega il tuo VPC privatamente a servizi e risorse come se fossero nel tuo VPC.
*   **VPN sito-sito AWS** crea una connessione sicura tra il data center o le filiali e le risorse del cloud AWS.
*   **VPN Client di AWS** è un servizio di rete che puoi utilizzare per connettere il personale remoto e le reti on-premises al cloud. Fornisce l'autenticazione avanzata e un accesso elastico e remoto in un servizio completamente gestito.

## Sottoreti e liste di controllo accessi di rete

Una **Network ACL (Access Control List)** è un firewall virtuale che controlla il traffico in entrata e in uscita a livello di sottorete. È stateless, cioè non mantiene memoria delle connessioni: ogni pacchetto, in entrata o in uscita, viene valutato singolarmente. Puoi immaginarla come il controllo passaporti di un aeroporto: ogni viaggiatore (pacchetto) deve essere autorizzato sia quando entra che quando esce.

Un **Security Group** è un firewall virtuale che agisce a livello di istanza (ad esempio EC2). È stateful, quindi ricorda le decisioni prese: se una richiesta è stata autorizzata, la risposta corrispondente sarà automaticamente consentita. A differenza delle ACL, che operano sulle sottoreti e verificano ogni pacchetto, i Security Group filtrano solo il traffico verso le risorse a cui sono associati, come un portiere che controlla chi può entrare in un condominio.

Un pacchetto viaggia da Internet verso una risorsa in una VPC tramite i seguenti passi:

1.  Il pacchetto entra nella VPC attraverso un Internet Gateway.
2.  Viene valutato da una Network ACL, che decide se può entrare nella sottorete.
3.  Se il pacchetto è destinato a un’istanza EC2, le regole del Security Group associato verificano le autorizzazioni.

In questo modo, sia le ACL che i Security Group lavorano insieme per garantire sicurezza e controllo del traffico.

## Reti globali

Il **DNS (Domain Name System)** è un servizio che agisce come un traduttore: invece di tradurre lingue umane, traduce i nomi di dominio (es. www.miosito.it) in indirizzi IP (es. 192.1.1.1) che i computer comprendono. Quando un utente inserisce un indirizzo nel browser, il DNS trova l’IP corrispondente e reindirizza la richiesta al server corretto.

**Amazon Route 53** è il servizio DNS (Domain Name System) di AWS. Traduce i nomi dei siti web in indirizzi IP comprensibili dai computer, permettendo agli utenti di accedere alle applicazioni ospitate in AWS semplicemente digitando un dominio. È altamente disponibile e scalabile, può gestire enormi volumi di richieste ed è in grado di indirizzare il traffico in base a policy avanzate.

Amazon Route 53 supporta diverse modalità di instradamento del traffico:

*   **Routing basato sulla latenza** per ridurre i tempi di risposta.
*   **Geolocalizzazione DNS** per indirizzare gli utenti in base alla loro posizione geografica.
*   **Geoproximity** per avvicinare gli utenti a determinate regioni AWS.
*   **Weighted round robin** per distribuire il traffico in percentuali configurabili.

Oltre a risolvere i nomi di dominio, Amazon Route 53 consente anche di registrare domini. Questo significa che puoi acquistare, configurare e gestire i tuoi domini direttamente da AWS, senza dover usare provider esterni.