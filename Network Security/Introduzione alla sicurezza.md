---
aliases: [Introduzione sicurezza, IS]
tags: [network-security]
---
## 1. Il Triangolo della CIA
Nella [[Sicurezza|sicurezza]], è estremamente importante il **triangolo della CIA**:

*   **Confidentiality (Riservatezza):** i dati devono essere riservati;
*   **Integrity (Integrità):** i dati non devono essere manomessi;
*   **Availability (Disponibilità):** servizio pubblico non disattivabile (denial service).

Questo triangolo può essere trasformato in un pentagono, la **CIAAA**, aggiungendo i termini di:

*   **Authenticity (Autenticità):** verificare che il pacchetto sia realmente originato dal mittente e non sia stato modificato;
*   **Accountability (Responsabilità):** la possibilità di attribuire a qualcuno quello che ha fatto, ogni azione è legata a qualcuno. Il non ripudio è legato alla responsabilità.

> **Il non ripudio** è la proprietà che non consente alle persone di rifiutare di aver fatto qualcosa. Ogni persona è responsabile di ciò che ha firmato (certificazione digitale).

## 2. Tipi di Attacco
Esistono diversi tipi di attacco:

*   **Attacco all'integrità:** furto di identità, contraffazione di dati;
*   **Attacco alla riservatezza:** furto di informazioni debole e forte;
*   **Attacco alla qualità del servizio:** denial of service;
*   **Effetto dominio (attacco passo dopo passo):** un utente malintenzionato può essere interessato a compromettere la tua rete in diversi modi, non solo uno.

## 3. Superficie di Attacco
La [[Sicurezza|sicurezza]] non riguarda solo la rete ma anche la parte fisica della rete stessa, come USB, plug e qualcosa del genere.

*   Ad esempio, considerando un **server Web**, la superficie di attacco è la porta TCP e SSH aperta a Internet.
*   Un altro esempio, se la macchina è **virtuale**, c'è una console remota per accedere alla macchina; la console è una superficie di attacco.

## 4. Definizioni Importanti

*   **Weakness:** una condotta generale che può portare alla generazione di minacce. Un [[Database|database]] contiene tutte le diverse weakness esistenti e si chiama **CWE** (Common Weakness Enumeration);
*   **Vulnerability:** un potenziale specifico di violazione della [[Sicurezza|sicurezza]], possiamo dire che una vulnerabilità è un caso particolare di weakness. Un [[Database|database]] contiene tutte le possibili vulnerabilità esistenti e si chiama **CVE** (Common Vulnerability Enumeration);
*   **Attacco:** un attacco alla [[Sicurezza|sicurezza]] del sistema, un tentativo deliberato di eludere il servizio di [[Sicurezza|sicurezza]].
