---
aliases: [triggers]
tags: [basi-di-dati]
---
Un trigger è un oggetto di [[Database|database]] archiviato che viene eseguito automaticamente in risposta a un evento di modifica dei dati su una tabella.

Gli [[Eventi|eventi]] di modifica dei dati includono inserimento, aggiornamento ed eliminazione causati dalle istruzioni di inserimento, aggiornamento ed eliminazione.

MariaDB ha archiviato i trigger come oggetti denominati nel dizionario dei dati. Un trigger è sempre associato a una tabella.

## Tipi di trigger MariaDB

MariaDB supporta i seguenti tipi di trigger:

- il trigger *insert* viene eseguito automaticamente quando un'istruzione *insert* aggiunge una nuova riga a una tabella;
- il trigger *update* viene attivato automaticamente quando un'istruzione di aggiornamento modifica i dati su una tabella;
- il trigger *delete* viene richiamato automaticamente quando un'istruzione delete rimuove una o più righe da una tabella.
MariaDB classifica ulteriormente ogni tipo di trigger in base al tempo di trigger, inclusi *before* e *after*:

- un trigger *before* viene attivato subito prima che si verifichi un evento. Ad esempio, prima che si verifichi l'evento di inserimento, è possibile convalidare i valori che vengono inseriti. In genere, si utilizza un trigger prima per la pulizia e la modifica dei dati.
- un trigger *after* viene richiamato dopo che si verifica un evento. In genere, si utilizza un trigger after per mantenere gli audit trail.
Quando combini l'ora di attivazione e l'evento, puoi avere fino a sei attivazioni per tabella:

- *before insert*
- *after	insert*
- *before update*
- *after	update*
- *before delete*
- *after	delete*
A partire da MariaDB 10.2.3, è possibile definire più trigger per lo stesso tempo di trigger (*before* e *after*) ed [[Eventi|eventi]] di trigger (*insert*, *update*, *delete*).

![[trigger1.PNG]]

MariaDB esegue un trigger per ogni riga interessata dall'evento di *insert*, *update* e *delete*. Questo trigger è chiamato trigger a livello di riga.

Se un trigger viene attivato esclusivamente in base all'evento trigger, indipendentemente dal numero di righe interessate, il trigger viene chiamato trigger a livello di istruzione. Attualmente, MariaDB non supporta i trigger a livello di istruzione.

## Triggers vs. stored procedures

Analogamente alle stored procedure, i trigger contengono codice procedurale.
La tabella seguente mostra le principali differenze tra trigger e stored procedure:

![[trigger2.PNG]]

## Gestire i trigger in MariaDB

- **Crea un trigger:** crea un nuovo trigger utilizzando l'istruzione *create trigger*.
- **Elimina trigger:** rimuove un trigger dal [[Database|database]] utilizzando l'istruzione *drop trigger*.

### CREATE TRIGGER statement

L'istruzione create trigger consente di creare un nuovo trigger in un [[Database|database]]. Quanto segue illustra la sintassi di base dell'istruzione create trigger:

```sql
create trigger trigger_name
{before | after} {insert | update |	delete }
on table_name for each row
trigger_body;
```
In questa sintassi:

- In primo luogo, specificare il nome del trigger che si desidera creare dopo le parole chiave *create trigger*. Il nome del trigger deve essere distinto all'interno di un [[Database|database]];
- In secondo luogo, specificare l'ora dell'azione in cui viene richiamato il trigger. Il tempo di azione può essere *before* o *after* la modifica di una riga;
- Terzo, specificare un evento che attiva il trigger. MariaDB supporta *insert*, *update* e *delete* degli [[Eventi|eventi]];
- Quarto, indicare il nome della tabella a cui appartiene il trigger dopo la parola chiave *on*;
- Infine, specificare una o più istruzioni da eseguire quando viene richiamato il trigger.

Se vuoi eseguire più istruzioni, inseriscile all'interno dell'istruzione composta *BEGIN END*.

All'interno del corpo del trigger, è possibile accedere ai valori delle colonne interessate da un'istruzione di *insert*, *update* o *delete*. I modificatori *old* e *new* consentono di accedere ai valori delle colonne prima e dopo l'evento di attivazione.

Ad esempio, se aggiorni la colonna *gdp*, all'interno del corpo del trigger di aggiornamento, puoi accedere al valore del vecchio e del nuovo valore della colonna *gdp* utilizzando queste sintassi: *old.gdp* e *new.gdp*.

Questa tabella mostra la disponibilità dei modificatori *old* e *new* in ciascun trigger:

![[trigger3.PNG]]

### DROP TRIGGER statement

L'istruzione *drop trigger* elimina un trigger da un [[Database|database]]. Quanto segue mostra la sintassi dell'istruzione *drop trigger*:

```sql
drop trigger [if exists] trigger_name;
```
In questa sintassi:

- Innanzitutto, specifica il nome del trigger che desideri eliminare dopo le parole chiave *drop trigger*;
- In secondo luogo, utilizzare la clausola facoltativa *if exists* per eliminare condizionalmente un trigger solo se esiste.
Se rilasci un trigger inesistente senza la clausola *if exist*, MariaDB emetterà un errore. Tuttavia, se si utilizza la clausola *if exist*, MariaDB emetterà una *NOTE*.

Per eseguire correttamente l'istruzione *drop trigger*, è necessario disporre del privilegio di trigger per la tabella associata al trigger.

Quando rimuovi una tabella, MariaDB rimuove automaticamente tutti i trigger associati alla tabella eliminata.

**Facciamo un esempio:** innanzitutto, crea un trigger associato alla tabella *country\_stats*:

```sql
delimiter	$$

create trigger before_stat_update
	before update
	on country_stats for each row
begin
	if new.population >	old.population * 2 then
		signal sqlstate '45000'
			set message_text='Error! the new population	is too	high.';
	end if;
end$$

delimiter	
```
In secondo luogo, mostra i trigger:

```sql
show triggers;
```

![[trigger4.PNG]]

Terzo, rilascia il trigger *before\_stat\_update*:

```sql
drop trigger if exists before_stat_update;
```
Infine, verifica l'eliminazione mostrando nuovamente i trigger:

```sql
show triggers;
```

![[trigger5.PNG]]

Come puoi vedere dall'output, il trigger *before\_stat\_update* è stato eliminato.