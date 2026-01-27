Le asserzioni sono costrutti SQL attraverso i quali è possibile (dichiarativamente) specificare *vincoli di integrità* su una base di dati. Un vincolo è una condizione logica che i dati devono soddisfare affinchè la BD si trovi in uno stato consistente. Le asserzioni fanno parte dello schema della BD.

La struttura di una *assertion* è la seguente:

```sql
CREATE ASSERTION nome
CHECK (condizione)
```

Se la *condizione* (booleana) è verificata, il vincolo espresso dalla ASSERTION è soddisfatto.
Altrimenti, viene annullato l’effetto dell’aggiornamento che ha portato alla violazione del vincolo.

La verifica dei vincoli può essere:

- *immediata:* la loro violazione annulla l'ultima modifica;
- *differita:* la loro violazione annulla l'intera transazione.
Si consideri la seguente BD:

- **Imp(matr, nome, dip*, stip, superiore*, livello)**
- **Dip(cod, nome, dir*, azienda)**
- **Azienda(cod, nome, città)**

dove *superiore* e *direttore* sono chiavi secondarie i cui valori sono le matricole degli impiegati (quindi, sia i superiori che i direttori sono impiegati), mentre *dip* è una chiave secondaria i cui valori sono i codici dei dipartimenti di afferenza degli impiegati.

**ESEMPIO 1:** il direttore di un dipartimento deve essere un impiegato che afferisce allo stesso dipartimento:

```sql
CREATE ASSERTION Vincolo- Direttore
CHECK (NOT EXISTS (SELECT *
                     FROM Dip AS D, Imp AS I
                     WHERE D.dir = I.matr AND cod != dip))
```

![[v1.PNG]]

La suddetta asserzione genera l’insieme di tutte le tuple di Dip il cui codice è diverso dal codice del dipartimento di afferenza del proprio direttore (tupla in rosso nell’esempio sopra). Se l’insieme è vuoto, la condizione NOT EXISTS è soddisfatta, quindi, la clausola CHECK è soddisfatta, quindi, il vincolo è soddisfatto. Altrimenti, viene annullata l’operazione di aggiornamento che ha causato la violazione del vincolo (ad es. l’inserimento della tupla dell’impiegato i2).

```sql
CREATE ASSERTION Vincolo-Stipendi
CHECK (NOT EXISTS (SELECT *
                    FROM Imp AS I
                    WHERE stip > (SELECT stip
                                    FROM Imp AS S
                                    WHERE I.superiore = S.matr)))
```

*NOTA:* col NOT EXISTS la tecnica di base è quella di scrivere una query che seleziona tuple che violano il vincolo.

```sql
CREATE ASSERTION Vincolo-Esistenza
CHECK (NOT EXISTS (SELECT *
                     FROM Dip
                     WHERE cod NOT IN (SELECT dip
                                         FROM Imp))
```
*NOTA:* la sotto-query restituisce l’insieme dei codici dei dipartimenti ai quali afferisce almeno un impiegato.

```sql
CREATE ASSERTION Vincolo-Esistenza
CHECK (NOT EXISTS (SELECT *
                     FROM Dip AS D
                     WHERE 0 = (SELECT COUNT(*)
                                 FROM Imp
                                 WHERE Imp.dip = D.cod )))
```

La sotto-query restituisce, per ogni dipartimento D, il relativo numero di impiegati. La condizione F = 0 = (SELECT COUNT(*) ... ) è verificata, per un dipartimento D, se il rispettivo numero di impiegati è zero. La clausola CHECK è verificata se non esiste un dipartimento D che soddisfi la condizione F.

Le condizioni utilizzabili nella CHECK sono le stesse che possono apparire come argomento della clausola ***where*** di un’interrogazione SQL.

```sql
CREATE ASSERTION Vincolo-numMax-Imp
CHECK (10 >= ALL (SELECT COUNT(*) 
                    FROM Imp
                    GROUP BY dip))
```

La sotto-query genera l’insieme in cui ogni elemento è il numero di impiegati di un dipartimento (che ha almeno 1 impiegato). La condizione 10 $>$= ALL verifica che ognuno di tali elementi sia minore o uguale a 10.

```sql
CREATE ASSERTION Vincolo-numMax-Imp
CHECK (NOT EXISTS (SELECT Dip 
                    FROM Imp
                    GROUPBY Dip
                    HAVING COUNT(*) > 10))
```

```sql
CREATE ASSERTION Vincolo-Esistenza
CHECK (EXISTS (SELECT *
                 FROM Imp))
```sql
CREATE ASSERTION Vincolo-Esistenza
CHECK (0 < (SELECT COUNT(*)
             FROM Imp))
```

Le asserzioni vengono eseguite ogniqualvolta viene effettuato un aggiornamento sulle relazioni di base. Se il vincolo non è soddisfatto nel nuovo stato della base di dati, l’aggiornamento viene disfatto. Aggiornamento IMMEDIATO o DIFFERITO (nel caso di transazioni). Nel primo caso si ha un rollback parziale viene effettuato dopo l’aggiornamento, nel secondo si ha un rollback di tutta la transazione.