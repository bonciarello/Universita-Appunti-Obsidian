---
aliases: [mysql]
tags: [basi-di-dati]
---
## Installare MySQL

Dal link *http://dev.mysql.com/* e fare il download di diverse versioni di MySQL:

- **ESEMPIO:** MySQL 8.0 per diverse piattaforme tra cui Linux e Windows;
- Altri tool e driver utili come MySQL Workbench, Dbeaver Community (*https://dbeaver.io/download/*) e MySQL Connector/J (JDBC).

## Storia

Il codice sorgente di MySQL era inizialmente di proprietà della società MySQL AB, veniva però distribuito con la licenza **GNU GPL** oltre che con una licenza commerciale.

Nel luglio 2007 la società svedese MySQL AB aveva 385 dipendenti in numerosi paesi. I suoi principali introiti provenivano dal supporto agli utilizzatori di MySQL tramite il pacchetto **Enterprise**, dalla vendita delle licenze commerciali e dall'utilizzo da parte di terzi del marchio MySQL.

Il 16 gennaio 2008 **Sun Microsystems** ha acquistato la società per un miliardo di dollari, stimando il mercato del [[Database|database]] in 15 miliardi di dollari.

Il 20 aprile 2009 alla stessa Sun Microsystems è stata proposta l'acquisizione da parte di **Oracle** per 7,4 miliardi di dollari.

Il padre di MySQL, **Michael Widenius**, ha lanciato una petizione online per opporsi alla fusione. Nonostante ciò l'Unione europea ha dato parere favorevole, e l'acquisizione è stata completata il 27 gennaio 2010. Il software **MediaWiki**, che gestisce i siti del progetto **Wikipedia**, è basato su [[Database|database]] MySQL.

Il ramo 5.5 è il primo a includere estensioni non Open Source, disponibili solo nella versione Enterprise (a pagamento).

A partire dal 2012, diverse distribuzioni Linux e alcuni utenti importanti (come Wikipedia) hanno iniziato a sostituire MySQL con il fork **MariaDB**.

## Panoramica degli strumenti

Come molti altri DBMS, MySQL viene eseguito come servizio o, in altre parole, come **daemon**.

Un servizio o demone è un programma in esecuzione continua nel sistema operativo, il cui compito è quello di rimanere in attesa di richieste finalizzate alla fruizione di determinate funzionalità.

Nel caso dei DBMS, tutto lo scambio di dati con il demone avrà come scopo la gestione dei [[Database|database]].

Il demone alla base del DBMS prende il nome di **mysqld**.

## RDBMS

![[RDBMS.PNG]]

## Connessione/disconnessione

Per connettersi al server è necessario fornire login e password.

```sql
shell> mysql -h host -u user -p 
Enter password: ********
```
*host* e *user* rappresentano rispettivamente l'hostname dove risiede MySQL e lo username di un utente che possiede un account sul server.

*-p* specifica al server la richiesta della password all'utente.

## Comandi SQL

- **Estrazione dati:** *SELECT*;
- **Data Manipulation Language (DML):** *INSERT*, *UPDATE*, *DELETE*;
- **Data Definition Language (DDL):** *CREATE*, *ALTER DROP*, *RENAME*, *TRUNCATE*;
- **Controllo transazioni:** *COMMIT*, *ROLLBACK*, *SAVEPOINT*;
- **Data Control Language (DCL):** *GRANT*, *REVOKE*.