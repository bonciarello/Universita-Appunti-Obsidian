L'**SQL injection (SQLi)** è una vulnerabilità della sicurezza web che _consente a un aggressore di interferire con le query che un'applicazione effettua sul suo database_. Ciò può consentire a un aggressore di visualizzare dati che normalmente non è in grado di recuperare. Ciò potrebbe includere dati che appartengono ad altri utenti o qualsiasi altro dato a cui l'applicazione può accedere. In molti casi, un aggressore può modificare o eliminare questi dati, causando modifiche persistenti al contenuto o al comportamento dell'applicazione.

In alcune situazioni, un aggressore può intensificare un attacco di SQLi per compromettere il server sottostante o altre infrastrutture back-end. Può anche consentire loro di eseguire attacchi denial-of-service.

**SQLi è in declino:** basti guardare la OWASP Top Ten per affermare che la priorità di questa tipologia di attacco è diminuita dal 2017 al 2021.

In qualsiasi caso, la maggior parte dei framework web ha meccanismi di protezione integrati _ancora comuni e solitamente critici_.

Possiamo classificare SQLi come di seguito:
- **classic SQLi:** un attaccante manipola direttamente una query SQL inserendo codice SQL malevolo in un modulo web vulnerabile o in un parametro URL. L’obiettivo è ingannare l’applicazione affinché esegua query SQL non desiderate, permettendo l’accesso non autorizzato ai dati, la modifica o persino la cancellazione delle informazioni;
- **blind SQLi:** l’attaccante non riceve feedback diretto dal database riguardo al successo o al fallimento della query iniettata. Invece, l’attaccante formula domande al database a cui si può rispondere con vero o falso e osserva come l’applicazione risponde (ad esempio, il comportamento della pagina o i tempi di risposta) per dedurre informazioni in modo indiretto. È più difficile da sfruttare, ma molto pericoloso;
- **first-order SQLi:** avviene quando l’input malevolo viene eseguito immediatamente dall’applicazione in un singolo ciclo di request-response. Il payload dell’attaccante viene utilizzato direttamente nella costruzione della query SQL, consentendogli di manipolare immediatamente il database;
- **second-order SQLi:** l’input malevolo viene memorizzato dall’applicazione (ad esempio, in un database) e viene eseguito successivamente in un contesto diverso. L’attacco non avviene immediatamente, ma viene attivato quando i dati memorizzati vengono recuperati e utilizzati in una futura query SQL;
- **in-band:** si verifica quando l’attaccante inietta il payload malevolo e riceve i risultati della query attraverso lo stesso canale di comunicazione (ad esempio, la pagina web). È il tipo di SQLi più comune e facile da sfruttare, poiché l’attaccante può vedere direttamente i risultati;
- **out-of-band:** l’attaccante inietta codice SQL ma riceve i risultati attraverso un canale diverso (come una richiesta HTTP separata o una risposta via email). Questo tipo di attacco è meno comune e viene utilizzato quando i metodi “in-band” non sono praticabili o il database non risponde direttamente.
### Qual è l'impatto di un attacco SQLi riuscito?
Un attacco SQLi riuscito può causare l'accesso non autorizzato a dati sensibili, come:
- password;
- dettagli della carta di credito;
- informazioni personali dell'utente.
Gli attacchi di SQLi sono stati utilizzati in molte violazioni di dati di alto profilo nel corso degli anni. Questi hanno causato danni alla reputazione e sanzioni normative. In alcuni casi, un aggressore può ottenere una backdoor persistente nei sistemi di un'organizzazione, portando a una compromissione a lungo termine che può passare inosservata per un periodo prolungato.
### Come rilevare le vulnerabilità di SQLi?
È possibile rilevare manualmente l'SQLi utilizzando un set sistematico di test su ogni punto di ingresso nell'applicazione. Per fare ciò, in genere si invia:
- il carattere di virgoletta singola `'` e si cercano errori o altre anomalie;
- una sintassi specifica di SQL che valuta il valore di base (originale) del punto di ingresso e un valore diverso e si cercano differenze sistematiche nelle risposte dell'applicazione;
- condizioni booleane come `OR 1=1` e `OR 1=2` e si cercano differenze nelle risposte dell'applicazione;
- payload progettati per innescare ritardi temporali quando eseguiti all'interno di una query SQL e si cercano differenze nel tempo impiegato per rispondere;
- payload OAST progettati per innescare un'interazione di rete fuori banda quando eseguiti all'interno di una query SQL e si monitorano le interazioni risultanti.
La maggior parte delle vulnerabilità delle SQLi si verifica all'interno della clausola `WHERE` di una query `SELECT`. La maggior parte dei tester esperti ha familiarità con questo tipo di SQLi.
Tuttavia, le vulnerabilità delle SQLi possono verificarsi in qualsiasi posizione all'interno della query e all'interno di diversi tipi di query. Alcune altre posizioni comuni in cui si verifica la SQLi sono:
- nelle istruzioni `UPDATE`, all'interno dei valori aggiornati o della clausola `WHERE`;
- nelle istruzioni `INSERT`, all'interno dei valori inseriti;
- nelle istruzioni `SELECT`, all'interno del nome della tabella o della colonna;
- nelle istruzioni `SELECT`, all'interno della clausola `ORDER BY`.

