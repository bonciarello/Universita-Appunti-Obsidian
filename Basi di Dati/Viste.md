Una **vista** è una relazione *derivata* a partire dalle relazioni di *base* o di altre viste per mezzo di interrogazioni. Essa consente di mettere a disposizione degli utenti rappresentazioni diverse degli stessi dati. Una vista è:

- dotata di uno schema
- utilizzabile nelle interrogazioni alla stregua delle tabelle di base
Due tipologie di viste:

- *virtuali:* prive di estensione (tuple);
- *materializzate:* tabelle derivate il cui contenuto (tuple) viene effettivamente memorizzato nella base di dati (problema: mantenimento della allineamento con le tabelle di base).
Lo schema logico di riferimento comprende le seguenti relazioni di base :

- **Imp(<u>matr**, nome, dip*, stipendio, sup*)</u>
- **Dip(<u>cod**, nome, azienda*, direttore)</u>
- **Azienda(<u>cod**, nome, città, regione, fatturato)</u>
A partire dalle suddette relazioni, possiamo definire varie viste, ad esempio, la vista **Info-Dip** che, per ogni dipartimento, riporta codice, nome, stipendio totale, stipendio massimo, numero di impiegati e azienda di appartenenza.

```sql
CREATE VIEW Info-Dip(cod, nome, stip-tot, max-stip, num-imp, azienda) AS
    SELECT cod, Dip.nome, sum(stipendio), max(stipendio), count(*), azienda
    FROM Imp, Dip
    WHERE cod=dip
    GROUP BY cod, Dip.nome, azienda
```

Il suddetto costrutto genera una relazione il cui schema è **Info-Dip(cod, nome, stip-tot, max-stip, num-imp, azienda)** e le cui tuple potrebbe, ad esempio, essere le seguenti:

![[vista1.PNG]]

Lo schema della vista è visibile nello schema logico della BD alla stregua degli schemi delle relazioni di base.
Lo schema logico della BD esteso con la vista *Info-Dip* equivale a:

- **Imp(<u>matr**, nome, dip*, stipendio, sup*)</u>
- **Dip(<u>cod**, nome, azienda*, direttore)</u>
- **Azienda(<u>cod**, nome, città, regione, fatturato)</u>
- **Info-Dip(cod, nome, stip-tot, max-stip, num-imp, azienda)**

Altre viste possono essere definite a partire dalle relazioni (sia di base sia derivate) presenti nel suddetto schema.

Le viste possono essere utilizzate nella formulazione di interrogazioni alla stessa stregua di qualsiasi altra relazione. Di seguito riportiamo alcuni esempi:

```sql
SELECT azienda, stip-tot, num-imp
FROM Info-Dip
WHERE nome = "ricerca"
```

![[vista2.PNG]]

```sql
SELECT azienda, sum(stipendio), count(*)
FROM Imp AS I, Dip AS D
WHERE D.cod = I.dip AND D.nome = "ricerca"
GROUP BY cod, azienda
```

```sql
SELECT nome
FROM Info-Dip
WHERE num-imp >= ALL (SELECT num-imp
                        FROM Info-Dip)
```
```sql
SELECT Dip.nome
FROM Imp, Dip
WHERE Imp.dip = Dip.Cod
GROUP BY Dip.cod, Dip.nome
HAVING count(*) >= ALL (SELECT count(*)
                        FROM Imp, Dip
                        WHERE Imp.dip = Dip.Cod
                        GROUP BY Dip.cod)
```
**POTERE ESPRESSIVO:** ci sono query esprimibili solo grazie alle viste (le sole tabelle di base non sono sufficienti) – quindi le viste incrementano il potere espressivo di SQL. In particolare, si tratta di query in cui vi è una applicazione in cascata di funzioni di aggregazione.

**ESEMPIO 3:** per ogni azienda, numero medio di impiegati nei dipartimenti:

```sql
SELECT azienda, avg(num-imp)
FROM Info-Dip
GROUP BY azienda
```

![[vista3.PNG]]

**ESEMPIO 4: per ogni regione, numero medio di imprese per città:**

```sql
CREATE VIEW Info-Aziende(città, regione, #aziende) AS
    SELECT citta', regione, count(*) 
    FROM Azienda
    GROUP BY citta', regione
```sql
SELECT regione, avg(#aziende)
FROM Info-Aziende
GROUP BY regione
```

## Esecuzione di query

- *virtuale:* trasformazione della query sulla vista in una query equivalente sulle relazioni di base – a tempo di compilazione;
- *materializzata:* quando una query sulla vista viene eseguita per la prima volta, le tuple generate vengono memorizzate come per le relazioni di base. Problema: aggiornamento necessario ogniqualvolta le relazioni di base, in funzione delle quali è definita la vista, vengono aggiornate.

## Vantaggi uso viste

- semplicità interrogazioni;
- maggiore potere espressivo - viste ricorsive;
- protezione dei dati: gruppi di utenti, per motivi di sicurezza/privacy, possono accedere alla BD attraverso opportune viste;
- indipendenze logica: se viene modificato lo schema logico della BD, si può definire un insieme di viste tali da mantenere inalterato lo schema iniziale – per gli utenti non cambia quindi la percezione della BD.