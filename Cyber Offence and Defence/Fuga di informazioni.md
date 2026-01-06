La fuga di informazioni (o information disclosure) si verifica quando un sito web rivela involontariamente informazioni sensibili ai propri utenti.

## Tipi di informazioni trapelate
*   **Dati sugli utenti:** Nomi utente, indirizzi email, informazioni finanziarie.
*   **Dati commerciali:** Segreti industriali o informazioni aziendali sensibili.
*   **Dettagli tecnici:** Versioni dei software, nomi di database, percorsi di file, configurazioni di rete.

## Cause comuni
1.  **Impossibilità di rimuovere contenuti interni:** Commenti degli sviluppatori lasciati nel codice HTML o file di backup accessibili.
2.  **Configurazione non sicura:** Funzionalità di debug attive in produzione o messaggi di errore troppo dettagliati.
3.  **Design difettoso:** Risposte diverse che permettono l'enumerazione di dati.

## Fonti comuni di fuga di informazioni

### Messaggi di errore dettagliati
Possono rivelare:
*   Tecnologie utilizzate (motori di template, tipi di database).
*   Versioni del software (permette di cercare exploit noti).
*   Parametri sfruttabili per attacchi come SQLi.

### Dati di debug
Molti siti generano registri o pagine di debug (es. `phpinfo()`) che contengono:
*   Variabili di sessione.
*   Credenziali per componenti back-end.
*   Percorsi di file e directory sul server.

### Commenti degli sviluppatori
Commenti HTML che potrebbero contenere directory nascoste o indizi sulla logica applicativa.

### Elenchi di directory
Server configurati male che mostrano il contenuto delle cartelle se manca una pagina indice, esponendo file temporanei o sensibili.

### File per web crawler
File come `robots.txt` o `sitemap.xml` che, pur servendo ai motori di ricerca, indicano esplicitamente agli aggressori quali directory sono considerate sensibili o nascoste.

### File di backup e codice sorgente
*   **Backup temporanei:** Editor di testo che creano file come `index.php~`. Richiederli può permettere di leggere il codice sorgente invece di eseguirlo.
*   **Git:** Esposizione della directory `.git/` che permette di ricostruire la cronologia del codice e trovare segreti o vulnerabilità vecchie.

## Come prevenire la fuga di informazioni
*   **Consapevolezza:** Istruire il team su cosa sia considerato sensibile.
*   **Automazione:** Usare processi di build per rimuovere automaticamente i commenti e i file di debug.
*   **Errori generici:** Restituire messaggi di errore uniformi e poco descrittivi all'utente finale, registrando i dettagli solo internamente.
*   **Disabilitare il debug:** Assicurarsi che ogni funzione di diagnostica sia spenta in ambiente di produzione.
*   **Controllo configurazioni:** Verificare le impostazioni di sicurezza di tutte le tecnologie di terze parti integrate.