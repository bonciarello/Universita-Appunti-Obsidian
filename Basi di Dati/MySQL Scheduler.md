## Scheduling

È possibile definire due tipologie di eventi:

- ad una sola esecuzione;
- ricorrente.

Nel primo caso si specifica il *timestamp* di esecuzione, mentre nel secondo caso viene specificato l’intervallo di ripetizione, sotto forma di un numero di minuti, ore, giorni, fino ad anni.

Procediamo con un semplice esempio di *schedulazione*, atto ad illustrate la sintassi da utilizzare.

Si ha una tabella che servirà da contenitore:

```sql
CREATE TABLE 'regeventi' (
    'ID_RIGA' int(11) NOT NULL AUTO_INCREMENT,
    'data' datetime DEFAULT NULL,
    'conteggio' int(11) DEFAULT NULL,
    PRIMARY KEY ('ID_RIGA')
    ) ENGINE=InnoDB DEFAULT CHARSET=latin1;
```

La tabella oltre alla chiave primaria ha un campo data che memorizzerà il momento in cui scatta l’evento, e un campo numerico che fungerà da contatore delle sue repliche.

La seguente procedura verrà richiamata periodicamente dall’evento:

```sql
delimiter |
CREATE PROCEDURE 'memScatto'() NOT DETERMINISTIC
SQL SECURITY DEFINER COMMENT ''
BEGIN
    DECLARE iConteggio INT;
    SET iConteggio = 0;
    
    SELECT IFNULL(max(conteggio)+1, 1) INTO iConteggio from regeventi;
    
    INSERT INTO 'regeventi' ('data', 'conteggio') VALUES (now(), iConteggio);
END |
delimiter;
```

Si creerà un evento di tipologia ricorrente, con intervallo di un minuto, che si occuperà di richiamare la procedura *memScatto*:

```sql
CREATE EVENT contaMinuti ON SCHEDULE EVERY 1 MINUTE DO call memScatto;
```

Con questi comandi DDL è stato preparato il sistema, ma occorre attivare il processo di *scheduling*.

Per far si che un evento venga eseguito dal DBMS, è necessario che venga registrato nell’event scheduler di MySQL. Sarà questo infatti ad occuparsi di eseguire periodicamente (nel tempo definito dall’amministratore) i vari eventi. Prima di creare un evento, bisogna che l’event scheduler venga attivato all’interno del nostro database, perché di default questo è disabilitato.

Questo si ottiene tramite una variabile globale *event\_scheduler*. Si possono controllare i valori di tutte le variabili settate utilizzando il comando:

```sql
SHOW GLOBAL VARIABLES [LIKE 'pattern'];
```
Nel nostro caso:

```sql
Show global varables like '%event%';
```

Se *event\_scheduler* è a OFF, la schedulazione degli eventi è disattivata e si può utilizzare il seguente comando per l’attivazione:

```sql
SET GLOBAL event_scheduler = ON;
```

Una volta in esecuzione il processo che sovrintende agli eventi, ogni minuto scatterà *contaMinuti*, che richiamerà la procedura *memScatto* e questa inserirà un record nella tabella *regeventi*. La query *SELECT * FROM 'regeventi';*  mostrerà i record inseriti con data, che differisce esattamente di un minuto uno dall’altro.

Con il comando:

```sql
SHOW PROCESSLIST;
```
controlliamo tutti i processi attivi in MySQL.

## Creazione di un evento

Lo scheletro SQL per la creazione di un evento è il seguente:

```sql
CREATE EVENT [IF NOT EXIST] event_name 
ON SCHEDULE periodo_di_esecuzione 
DO corpo_dell_evento
```

Nella prima parte del codice SQL presentato, vediamo che un evento deve possedere un nome univoco prima di poter essere creato. La clausola *ON SCHEDULE*, invece, definisce il periodo in cui l’evento deve essere eseguito.

Abbiamo due possibili configurazioni temporali per un evento:

- ON SCHEDULE AT [...]
- ON SCHEDULE EVERY number-of-time-units time-unit [STARTS timestamp] [ENDS timestamp]

La sintassi *AT* permette l’esecuzione dell’evento una singola volta, mentre *EVERY* permette di definire sia il numero di volte che l’evento deve essere eseguito, sia il suo intervallo temporale di esecuzione.

## CREATE EVENT Syntax

```sql
CREATE [DEFINER = { user | CURRENT_USER }] 
	EVENT [IF NOT EXISTS] event_name 
	ON SCHEDULE schedule 
	[ON COMPLETION [NOT] PRESERVE] 
	[ENABLE | DISABLE | DISABLE ON SLAVE] 
	[COMMENT 'comment'] 
	DO event_body; 

	schedule: AT timestamp [+ INTERVAL interval] ... | EVERY interval [STARTS timestamp [+ INTERVAL interval] ...] 
	[ENDS timestamp [+ INTERVAL interval] ...] 

	interval: quantity {YEAR | QUARTER | MONTH | DAY | HOUR | MINUTE | WEEK | SECOND | YEAR_MONTH | DAY_HOUR | DAY_MINUTE | DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND}
```

### Esempio

```sql
CREATE EVENT myevent 
    ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR 
    DO 
    	UPDATE myschema.mytable SET mycol = mycol + 1;
```

Viene creato un evento di nome *myevent* che esegue, una volta dopo un’ora dalla sua creazione, l’SQL specificato dopo il DO.

## Cancellare un evento

```sql
SHOW CREATE EVENT <event_name>\G

DROP EVENT [IF EXISTS] event_name
```

## Event\_scheduler

Per far sì che un evento venga eseguito dal DBMS, è necessario che venga registrato nell’event scheduler di MySQL. Sarà questo infatti ad occuparsi di eseguire periodicamente (nel tempo definito dall’amministratore) i vari eventi. Prima di creare un evento, bisogna che l’event scheduler venga attivato all’interno del nostro database, perché di default questo è disabilitato. Per far ciò bisogna lanciare il seguente comando SQL nella console di MySQL:

```sql
SET GLOBAL event_scheduler = ON;
```
dopo aver abilitato lo scheduler possiamo verificare quali eventi sono registrati al suo interno. Eseguiamo il comando:

```sql
SHOW PROCESSLIST;
```
Per disabilitare l’event scheduler:

```sql
SET GLOBAL event_scheduler = OFF;
```