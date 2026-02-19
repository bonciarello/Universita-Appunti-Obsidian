---
aliases: [DP]
tags: [secure-software-design]
---
**È un value-object del dominio, in cui tutti i vincoli vengono verificati durante la creazione.** Il significato di un *domain primitive* è strettamente legato al contesto in cui si utilizza. Talvolta ha senso ridefinire un concetto, anche preesistente, per adattarlo meglio ai nostri obiettivi (per esempio, l'email vengono definite dagli RFC ma magari il nostro concetto di email ha ulteriori vincoli di cui tener conto).

La **best practice** sarebbe l'ideale iniziare a sviluppare sin da subito primitive di dominio: questo permette di avere codice più sicuro.

Possiamo immaginare le primitive di dominio come i mattoncini del nostro sistema: le entità costituiscono gli oggetti principali, le funzionalità manipolano questi oggetti e ne cambiano il loro stato interno, affinché tutto sia valido si devono effettuare dei controlli. Qualora questi venissero fatti all'interno dell'entità, il codice delle stesse crescerebbe in maniera esponenziale causando scarsa manutenibilità. Per questo motivo ci vengono in aiuto le primitive di dominio. Come detto in precedenza si effettuano controlli relativi a:
- **origine;**
- **grandezza;**
- **contenuto;**
- **sintassi;**
- **semantica:** questo controllo deve essere riposto all'interno dell'entità poiché dipende dal contesto.
