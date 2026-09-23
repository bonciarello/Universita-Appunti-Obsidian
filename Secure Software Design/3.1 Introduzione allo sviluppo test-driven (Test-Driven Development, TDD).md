---
aliases: [Introduzione allo sviluppo test-driven, "Test-Driven Development, TDD"]
tags: [secure-software-design]
---
**Test-Driven Development (TDD)** è una metodologia che aiuta a creare codice migliore. L'idea è quella di scrivere prima i test e poi il codice al fine di verificare che tutte le feature siano state implementate e soprattutto che funzionino adeguatamente. 

*Il test è una prova che alcune funzionalità mancano nel sistema.* Il TDD è lento e molto probabilmente faresti più veloce senza test ma ovviamente è utile a cercare il bug. Come lo trovi? Devi scrivere degli esempi.

**Se tutti i test passano, possiamo effettuare il refactoring:** non eseguire il refactoring senza test.

In sintesi, possiamo dividerlo nei seguenti passi:
1. prima scrivi test, poi codifica;
2. aggiungi la quantità minima di codice necessaria per superare i test;
3. non dovresti avere più di un test fallito alla volta;
4. scrivere il codice che supera i test, quindi refactoring;
5. un test dovrebbe fallire la prima volta che lo esegui: in caso contrario, chiediti perché lo stai aggiungendo;
6. mai refactoring senza test.
