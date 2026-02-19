---
aliases: [Introduzione Sicurezza Informatica, ISI]
tags: [metodi-e-strumenti-per-la-sicurezza]
---
La **[[Sicurezza|sicurezza]]** è definita come la capacità di imporre una politica prestabilita in presenza di un avversario. Per valutarla, occorre considerare tre aspetti fondamentali:
1.  **Modello della minaccia:** chi sono i potenziali avversari e quali poteri hanno.
2.  **Policy:** l'obiettivo di [[Sicurezza|sicurezza]] da raggiungere.
3.  **Meccanismo:** le capacità e gli strumenti per implementare la policy.

> **Nota:** Una minaccia può essere rappresentata anche da un utente benevolo inesperto. Ad esempio, imporre il cambio frequente della password è oggi considerata una pratica deprecata poiché induce gli utenti a scegliere password deboli o prevedibili.

## 1. Complessità e Attori
I sistemi informatici sono intrinsecamente complessi (migliaia di [[Reti|reti]], stratificazioni software, protocolli). La [[Sicurezza|sicurezza]] è difficile perché l'obiettivo è "negativo": bisogna garantire che certe azioni *non* avvengano, indipendentemente dall'attaccante.

Si distinguono tre tipologie di attori nel mondo della [[Sicurezza|sicurezza]]:
*   **Black Hats (Red Team):** sfruttano le vulnerabilità per scopi malevoli e cercano di coprire le proprie tracce.
*   **White Hats (Blue Team):** operano eticamente (spesso su delega) per prevenire problemi. Nessun sistema è sicuro al 100%.
*   **Gray Hats (Purple Team):** hanno scopi etici ma possono compiere azioni tecnicamente illegali.

### Curiosità e Statistiche
*   I sistemi di [[Sicurezza|sicurezza]] degli aerei sono isolati per evitare compromissioni integrate.
*   L'82% della ricerca di vulnerabilità è legato al cyber crime.
*   **Rubber Hose Cryptanalysis:** un metodo "pratico" che consiste nel farsi svelare la password tramite coercizione fisica.

## 2. Difetti, Debolezze e Vulnerabilità
Esistono due tipi di difetti principali:
*   **Flaw:** un difetto di progettazione, intrinseco a tutte le implementazioni del sistema.
*   **Bug:** un difetto di implementazione specifico.

## 3. Politiche di [[Sicurezza]] (Security Policies)
Le politiche definiscono gli obiettivi: proteggere l'informazione (memorizzata e trasmessa) e le risorse (HW, SW, firmware). L'utente inesperto rimane l'anello più debole.

### La Triade CIA e oltre
1.  **Confidenzialità (Confidentiality):** accesso solo a identità autorizzate (es. saldo del conto noto solo al titolare). È una proprietà "in lettura".
2.  **Integrità (Integrity):** modifica solo da parte di identità autorizzate. È una proprietà "in scrittura".
3.  **Disponibilità (Availability):** i sistemi devono rispondere tempestivamente alle richieste autorizzate.
4.  **Autenticità:** identificazione certa di un'entità (login, PIN).
5.  **Responsabilità (Accountability):** capacità di abbinare un evento a un responsabile (log, firma digitale).
6.  **Resilienza:** capacità di continuare a operare sotto attacco e riprendersi rapidamente.

## 4. Modello della Minaccia (Threat Model)
È un insieme di assunzioni precise sull'avversario. Se il modello non corrisponde alla realtà, il progetto di [[Sicurezza|sicurezza]] fallisce.
*   **Caratteristiche base:** Di norma si assume che l'avversario non conosca le password e non abbia accesso fisico iniziale ai dispositivi.
*   **Utente di rete:** Può intercettare, modificare o iniettare pacchetti se il protocollo lo permette.

## 5. Autenticazione, Autorizzazione e Audit
*   **Autenticazione:** Lega un'identità a un'entità (es. tramite autenticazione multi-fattore).
*   **Autorizzazione:** Definisce quando un'entità può compiere un'azione (es. Access Control Policies).
*   **Audit:** Meccanismo per risalire alle cause di una violazione tramite **log** protetti.

## 6. Tipologie di Attacco (Modello DAD)
L'opposto della triade CIA è il modello **DAD**:
*   Confidenzialità $\rightarrow$ **Disclosure** (Divulgazione/Eavesdropping/Stealing).
*   Integrità $\rightarrow$ **Alteration** (Alterazione/Corruption).
*   Disponibilità $\rightarrow$ **Destruction** (Distruzione/DoS).

### Esempi di violazioni delle Policy
*   **Domande di [[Sicurezza|sicurezza]] deboli:** Permettere l'accesso se si conosce la password *O* la risposta a domande banali (es. caso Sarah Palin, le cui risposte erano su Wikipedia).
*   **Interazione tra sistemi:** Attacchi a catena sfruttando debolezze incrociate (es. violazione account Gmail tramite Apple e Amazon sfruttando procedure di ripristino collegate).

## 7. Problematiche del Modello della Minaccia
*   **Competenza dell'utente:** Vulnerabilità a phishing e siti non autenticati.
*   **Tempo:** Algoritmi sicuri in passato (es. DES 56 bit) sono oggi banali da violare con hardware economico.
*   **Buffer Overflow:** Un attacco classico dove si sfrutta una vulnerabilità per sovrascrivere la memoria (Stack Frame Pointer e Return Address) per eseguire codice arbitrario. Può avvenire sia in scrittura che in lettura.

## 8. Regole per una [[Sicurezza]] Pratica
Raramente serve una [[Sicurezza|sicurezza]] perfetta. Le regole d'oro sono:
1.  Rendere il **costo dell'attacco superiore al valore** delle informazioni.
2.  Rendere il sistema **meno attraente** di altri (utile contro lo spam).
3.  Utilizzare tecniche che eliminino **intere classi di attacchi**.
4.  Aumentare il **valore per il difensore** (es. VPN che offrono flessibilità lavorativa).
