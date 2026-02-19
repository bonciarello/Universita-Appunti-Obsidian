---
aliases: [CCS]
tags: [secure-software-design]
---
Come sviluppatori, ci vengono costantemente ricordate priorità e scadenze. Gli angoli taglienti e i trucchetti di quattro soldi a volte fanno parte della realtà che dobbiamo accettare, o lo sono? *La verità è che, alla fine, decidi tu quale sintassi usare, quali algoritmi applicare e come guidare il flusso di esecuzione.* Se capisci veramente perché alcuni costrutti di codice sono migliori di altri, usarli non richiede più tempo perso nella scrittura di codice errato. Lo stesso vale per la [[Sicurezza|sicurezza]]. Gli hacker non si preoccupano delle scadenze o delle priorità: **un sistema debole è sfruttabile, indipendentemente dal motivo o dalle circostanze in cui è stato costruito.**

*Condividiamo tutti la responsabilità della progettazione di software sicuro.* Esistono un insieme di strategie che permettono di risolvere problemi relativi alla [[Sicurezza|sicurezza]]:
- **immutabilità:** risolve i problemi legati all'integrità e alla disponibilità. In un contesto multi-thread previene la generazione di inconsistenze. Oggetti mutevoli hanno un impatto maggiore a livello di costi e di pericolosità;
- **fail fast:** gli errori devono essere gestiti prima che questi possano creare degli oggetti invalidi. L'idea è che ogni classe definisce un *"contratto"* che, se devi fallire, fallo sin da subito in maniera tale da testare al meglio input/output indesiderati;
- **validation:** assicura che l'input sia conforme a qualche specifica. Le verifiche che possono essere fatte ai dati possono essere "leggere" (Come un check sulla lunghezza) o "pesanti" (Check su un [[Database|database]]). Ci sono diversi tipi di verifica:
    - *origine:* verifica che i dati provengano dal giusto sender. Generalmente previene attacchi *DoS* e *DDoS* (**IP check**, **access key**, ecc.);
    - *dimensione:* verifica che la dimensione sia corretta;
    - *contenuto:* si verifica che il contenuto lessicale dei dati sia corretto;
    - *sintassi:* si verifica se il formato è corretto. Per esempio se i caratteri sono posizionati correttamente;
    - *semantica:* si verifica se i dati hanno un "senso" rispetto il contesto/stato del sistema.

## Esempio di immutabilità con uno shop online
*Immagina un normale negozio online in cui i clienti accedono e aggiungono articoli a un carrello.* Ogni cliente ha un punteggio di credito associato basato sulla cronologia degli acquisti e sui punti di iscrizione. Un punteggio di credito elevato consente di pagare tramite fattura o carta di credito, mentre un punteggio di credito basso consente solo pagamenti con carta di credito. Il calcolo del punteggio di credito è piuttosto costoso e viene eseguito continuamente per uniformare il carico complessivo del sistema.

Tutto sommato, il sistema ha funzionato bene, fino ad adesso. Quando è stata eseguita l'ultima campagna di marketing, si è presentato molto traffico nel negozio online. Quindi il sistema non ha gestito bene il carico e i clienti si sono lamentati del timeout degli ordini, delle lunghe attese e delle alternative di pagamento incoerenti. Quest'ultimo sembrava un problema minore, ma quando il dipartimento delle finanze ha riferito che molti clienti con punteggi di credito bassi avevano fatture in sospeso, è iniziata un'indagine di [[Sicurezza|sicurezza]] in piena regola: il sistema deve essere stato compromesso! Ovviamente, il calcolo del punteggio di credito era il principale sospetto, ma con sorpresa di tutti, la causa principale si è rivelata un problema molto più grande: il design dell'oggetto *Customer*.

L'oggetto *Customer* mostra due dettagli interessanti. Innanzitutto, tutti i campi vengono inizializzati tramite metodi *setter*, il che implica che lo stato interno può cambiare dopo la creazione dell'oggetto. Questo è problematico perché non si sa mai quando l'oggetto è inizializzato correttamente. L'altra osservazione è che ogni metodo è contrassegnato come sincronizzato per impedire la modifica simultanea del campo, che a sua volta può portare a conflitti di thread (quando i thread sono costretti ad attendere che un altro thread rilasci uno o più blocchi prima dell'esecuzione).

```java
public class Customer {
    private static final int MIN_INVOICE_SCORE = 500;
    private Id id;
    private Name name;
    private Order order;
    private CreditScore creditScore;
    
    public synchronized Id getId() { return id; }
    public synchronized void setId(final Id id) { this.id = id; }
    public synchronized Name getName() { return name; }
    public synchronized void setName(Name name) { this.name = name; }
    public synchronized Order getOrder() {
        this.order = OrderService.fetchLatestOrder(id);
        return order;
    }
    public synchronized void setOrder(Order order) { this.order = order; }
    public synchronized CreditScore getCreditScore() { return creditScore; }
    public synchronized void setCreditScore(CreditScore creditScore) {
        this.creditScore = creditScore;
    }
    public synchronized boolean isAcceptedForInvoicePayment() {
        return creditScore.compute() > MIN_INVOICE_SCORE;
    }
    ...
}
```

Il modo in cui queste scelte progettuali si riferiscono alla [[Sicurezza|sicurezza]] non è ovvio, ma quando si classificano i problemi del negozio online come problemi di integrità dei dati o di disponibilità dei dati, la correlazione diventa chiara.

L'integrità dei dati implica la coerenza dei dati durante l'intero ciclo di vita; la disponibilità dei dati garantisce che i dati siano ottenibili al livello di prestazioni previsto in un sistema. Entrambi i concetti sono essenziali per comprendere la causa principale dei problemi del negozio online. Ad esempio, il mancato recupero dei dati è un problema di disponibilità che spesso si riduce al codice che impedisce l'accesso parallelo o simultaneo. Allo stesso modo, l'identificazione del codice che consente la modifica è il punto di partenza quando si analizzano i problemi di integrità. I seguenti punti mostrano i problemi del negozio online classificati come problemi di disponibilità e integrità:
- **attese lunghe e scarse prestazioni (*disponibilità*):** il sistema non riesce ad accedere ai dati dei clienti in modo affidabile e si verifica un timeout;
- **ordini scaduti al momento del pagamento (*disponibilità*):** il sistema non riesce a recuperare i dati necessari per elaborare l'ordine in modo tempestivo;
- **alternative di pagamento incoerenti (*integrità*):** il punteggio di credito viene modificato in modo illegale.

Queste categorie ti danno un'idea di cosa cercare nella classe *Customer*.
