---
aliases: [PT]
tags: [cyber-offence-and-defence]
---
Il **Path Traversal** (o directory traversal) consente a un aggressore di leggere file arbitrari sul server che esegue un'applicazione.
Ciò potrebbe includere:
*   codice applicativo e dati;
*   credenziali per i sistemi back-end;
*   file sensibili del sistema operativo.

In alcuni casi, un aggressore potrebbe riuscire a scrivere su file arbitrari sul server, modificando così i dati o il comportamento dell'applicazione e assumendo il controllo completo del server.

## Lettura di file arbitrari tramite Path Traversal

Immagina un'applicazione di shopping che carica immagini tramite [[HTML]]:
`<img src="/loadImage?filename=218.png">`

L'URL `loadImage` accetta un parametro `filename` e legge da `/var/www/images/`.
Se l'applicazione non implementa difese, un aggressore può richiedere:
`https://insecure-website.com/loadImage?filename=../../../etc/passwd`

Ciò fa sì che l'applicazione legga:
`/var/www/images/../../../etc/passwd`

La sequenza `../` (dot-dot-slash) permette di salire di livello nella directory, arrivando alla radice e leggendo `/etc/passwd`.

*   **Unix:** `/etc/passwd` è un file standard con i dettagli degli utenti.
*   **Windows:** Sia `../` che `..\` sono validi. Un attacco equivalente sarebbe:
    `https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini`

## Ostacoli comuni allo sfruttamento

Molte applicazioni implementano difese che possono spesso essere aggirate.

1.  **Percorsi assoluti:** Se l'applicazione rimuove le sequenze `../`, potresti riuscire a usare direttamente un percorso assoluto (es. `filename=/etc/passwd`).
2.  **Sequenze nidificate:** Se l'app rimuove `../` una sola volta in modo non ricorsivo, puoi usare `....//` o `....\/`. Quando la parte interna viene rimossa, rimane `../`.
3.  **URL Encoding:** I server web potrebbero filtrare le sequenze prima di passarle all'app. Puoi aggirare questo usando la codifica URL (`%2e%2e%2f`) o la doppia codifica URL (`%252e%252e%252f`).
4.  **Codifiche non standard:** Come `..%c0%af` o `..%ef%bc%8f`.
5.  **Validazione dell'inizio del percorso:** Se l'app richiede che il file inizi con `/var/www/images`, puoi includerlo seguito dai traversals:
    `filename=/var/www/images/../../../etc/passwd`
6.  **Validazione dell'estensione (Null Byte):** Se l'app richiede l'estensione `.png`, nelle versioni vecchie di alcuni linguaggi (es. PHP < 5.3.4) era possibile usare un byte nullo (`%00`) per troncare il percorso:
    `filename=../../../etc/passwd%00.png`

## Come prevenire un attacco di Path Traversal

Il modo più efficace è evitare del tutto di passare l'input fornito dall'utente alle API del [[File System|file system]].

Se non puoi evitarlo, usa due livelli di difesa:
1.  **Convalida l'input:** Confrontalo con una whitelist di valori consentiti (es. solo alfanumerici).
2.  **Canonizzazione:** Usa l'API del [[File System|file system]] per canonizzare il percorso (risolvere i `../`) e verifica che inizi con la directory base prevista.

Esempio Java:
```java
File file = new File(BASE_DIRECTORY, userInput);
if (file.getCanonicalPath().startsWith(BASE_DIRECTORY)) {
    // process file
}
```