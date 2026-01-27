Abbiamo 11 pattern:

- **Template Method** (TM)
- **Interpreter** (IN)
- **Mediator** (ME, mediatore)
- **Chain of Responsibility** (CoR, Catena di Responsabilità)
- **Observer** (OB)
- **Strategy** (STG)
- **Command** (CMD)
- **State** (ST)
- **Visitor** (VST)
- **Iterator** (ITR)
- **Memento** (MMT, Ricordo)

## Template Method (TM)

L'**intento** è definire lo scheletro di un algoritmo in un'operazione, rinviando alcuni passaggi alle sottoclassi.
TM consente alle sottoclassi di ridefinire alcuni passaggi di un algoritmo senza modificare la struttura dell'algoritmo.

### Motivazione

**Consideriamo un framework applicativo che fornisca le classi *Application* e *Document*:** *Application* è responsabile dell'apertura di documenti esistenti archiviati in un formato esterno. *Document* rappresenta le informazioni in un documento una volta lette dal file.

*Application* e *Document* potrebbero avere sottoclassi per soddisfare esigenze specifiche.

![[TMesempio.png]]

```cpp
void Application::OpenDocument(const char* name) {
    if(!CanOpenDocument(name)) {
        // non puo' gestire questo documento
        return;
    }
    
    Document* doc = DoCreateDocument();
    
    if(doc) {
        _docs->AppDocument(doc);
        AboutToOpenDoucment(doc);
        doc->Open();
        doc->DoRead();
    }
}
```

Chiamiamo OpenDocument un **metodo modello**.
Un metodo modello definisce un algoritmo in termini di operazioni astratte che le sottoclassi eseguono l'override per fornire un comportamento concreto.

### Applicabilità

Usa Template Method:

- per implementare una volta le parti invarianti di un algoritmo e lasciare alle sottoclassi il compito di implementare il comportamento che può variare;
- quando il comportamento comune tra le sottoclassi dovrebbe essere fattorizzato e localizzato in una classe comune per evitare la duplicazione del codice ("refactoring per generalizzare");
- per controllare le estensioni delle sottoclassi: è possibile definire un metodo modello che chiami operazioni di "aggancio" che consentano estensioni solo in quei punti.

### Struttura

![[TMstruttura.png]]

- **AbstractClass (Application):** definisce operazioni primitive astratte che le sottoclassi concrete definiscono per implementare i passaggi di un algoritmo. Implementa un metodo template che definisce lo scheletro di un algoritmo. Il metodo template chiama operazioni primitive così come operazioni definite in *AbstractClass* o quelle di altri oggetti.
- **ConcreteClass (MyApplication):** implementa le operazioni primitive per eseguire passaggi specifici della sottoclasse dell'algoritmo.

### Collaborazione

*ConcreteClass* si basa su *AbstractClass* per implementare i passaggi invarianti dell'algoritmo.

### Conseguenze

Le TM sono fondamentali per il riutilizzo del codice: particolarmente importanti nelle librerie di classi. Scompongono il comportamento comune nelle classi di libreria.

Operazioni chiamate da TM:
- **operazioni concrete:** sia sulla *ConcreteClass* che sulle classi client;
- **operazioni concrete di *AbstractClass*:** operazioni generalmente utili alle sottoclassi;
- **operazioni primitive o operazioni astratte**;
- **factory method (FM)**;
- **operazioni di hook:** forniscono un comportamento predefinito che le sottoclassi possono estendere se necessario; spesso non fa nulla per impostazione predefinita.

Gli scrittori devono capire quali operazioni sono progettate per la sovrascrizione:

- gli hook possono essere sovrascritti;
- le operazioni astratte devono essere sovrascritte.

Può usare una convenzione di denominazione.

### Implementazione

- **Utilizzo del controllo di accesso C++:** le operazioni primitive che un metodo modello chiama possono essere dichiarate protette e devono essere sovrascritte sono dichiarate pure *virtual*.
- **Minimizzare le operazioni primitive:** minimizzare il numero di operazioni primitive da sovrascrivere;
- **Convenzioni di denominazione:** identifica le operazioni che dovrebbero essere sovrascritte anteponendo i nomi dei metodi del modello con "Do-".

### Esempio

```cpp
void View::Display() {
    SetFocus();
    DoDisplay();
    ResetFocus();
}

void View::DoDisplay() { }

void MyView::DoDisplay() {
    // renderizzare il contenuto della vista
}
```

## Interpreter (IN)

### Motivazione

Data una lingua, definire una rappresentazione per la sua grammatica insieme a un interprete che utilizzi la rappresentazione per interpretare le frasi nella lingua.

Il modello Interprete usa una classe per rappresentare ogni regola grammaticale. I simboli sul lato destro della regola sono variabili di istanza di queste classi.

![[INesempio1.png]]

![[INesempio2.png]]

### Applicabilità

Usa Interpreter quando:

- c'è un linguaggio da interpretare e puoi rappresentare le istruzioni nel linguaggio come alberi di sintassi astratti;
- IN funziona meglio quando la grammatica è semplice: per grammatiche complesse, la gerarchia delle classi per la grammatica diventa ampia e ingestibile;
- l'efficienza non è un problema critico.

### Struttura

![[INstruttura.png]]

- **AbstractExpression (RegularExpression):** dichiara un'operazione di interpretazione astratta comune a tutti i nodi nell'albero della sintassi astratta;
- **TerminalExpression (LiteralExpression):** implementa un'operazione di interpretazione associata ai simboli terminali nella grammatica. Un'istanza è richiesta per ogni simbolo terminale in una frase;
- **NonterminalExpression (AlternationExpression, ...):** una di queste classi è richiesta per ogni regola della grammatica. Mantiene le variabili di istanza di tipo *AbstractExpression*. Implementa un'operazione di interpretazione per i simboli non terminali nella grammatica. Interpreter tipicamente si chiama ricorsivamente
- **Context:** contiene informazioni globali per l'interprete.
- **Client:** costruisce (o gli viene dato) un albero di sintassi astratto che rappresenta una particolare frase nella lingua che la grammatica definisce. Richiama l'operazione Interpret.

### Collaborazione

Il client crea la frase come un albero di sintassi astratto di istanze *NonterminalExpression* e *TerminalExpression*. Quindi il client inizializza il contesto e richiama l'operazione *Interpret*.

Ogni nodo *NonterminalExpression* definisce *Interpret* in termini di *Interpret* su ogni sottoespressione.

Le operazioni *Interpret* su ciascun nodo utilizzano il contesto per memorizzare e accedere allo stato dell'interprete.

### Conseguenze

- **È facile cambiare ed estendere la grammatica:** utilizzare le classi per rappresentare la grammatica. Usa l'ereditarietà per modificare o estendere la grammatica. Modifica le espressioni esistenti in modo incrementale. Definire nuove espressioni come variazioni su quelli vecchie;
- **Anche l'implementazione della grammatica è facile:** le classi che definiscono i nodi nell'albero della sintassi astratta hanno implementazioni simili. La generazione di classi che definiscono i nodi spesso può essere automatizzata con un compilatore o un generatore di parser.
- **Le grammatiche complesse sono difficili da mantenere:** Interpreter definisce almeno una classe per ogni regola nella grammatica. Ciò significa che le grammatiche contenenti molte regole possono essere difficili da gestire e mantenere. Se la grammatica è molto complessa, i generatori di parser o compilatore sono più appropriati;
- **Aggiunta di nuovi modi di interpretare le espressioni:** il modello Interpreter semplifica la valutazione di un'espressione in un modo nuovo.

### Implementazione

- **Creazione dell'albero della sintassi astratta:** Interpreter non spiega come creare un albero di sintassi astratto: può essere creato da parser guidato da tabella, da un parser artigianale o direttamente dal cliente. *Interpreter non affronta l'analisi*;
- **Definizione dell'operazione *Interpret*:** non definire necessariamente l'operazione Interpret nelle classi di espressione (il visitatore può aiutare);
- **Condivisione dei simboli del terminale con il modello Flyweight:** le grammatiche le cui frasi contengono molte occorrenze di un simbolo terminale potrebbero trarre vantaggio dalla condivisione di una singola copia di quel simbolo.

### Esempio

Considera un sistema per manipolare e valutare le espressioni booleane implementato in C++: i simboli terminali in questo linguaggio sono variabili booleane (costanti *vero* e *falso*) mentre i simboli non terminali rappresentano espressioni contenenti gli operatori *and*, *or*, e *not*.

```cpp
BooleanExp ::= VariableExp | Constant | OrExp | AndExp | NotExp | '(' BooleanExp ')'
AndExp ::= BooleanExp 'and' BooleanExp
OrExp ::= BooleanExp 'or' BooleanExp
AndExp ::= 'not' BooleanExp
Constant ::= 'true' | 'false'
Variable ::= 'A' | 'B' | ... | 'X' | 'Y' | 'Z'

class BooleanExp {
    public:
        BooleanExp();
        virtual ~BooleanExp();
        virtual bool Evaluate(Context&) = 0;
        virtual BooleanExp* Replace(const char*, BooleanExp&) = 0;
        virtual BooleanExp* Copy() const = 0;
};

class Context {
    public:
        bool Lookup(const char*) const;
        void Assign(VariableExp*, bool);
};
//...
class VariableExp: public BooleanExp {
    public:
        VariableExp(const char*);
        virtual ~VariableExp();
        
        virtual bool Evaluate(Context&);
        virtual BooleanExp* Replace(const char*, BooleanExp&);
        virtual BooleanExp* Copy() const;
    private:
        char* _name;
};

bool VariableExp::Evaluate(Context& aContext) {
    return aContext.Lookup(_name);
}
//...
class AndExp: public BooleanExp {
    public:
        AndExp(BooleanExp*, BooleanExp*);
        virtual ~AndExp();
        
        virtual bool Evaluate(Context&);
        virtual BooleanExp* Replace(const char*, BooleanExp&);
        virtual BooleanExp* Copy() const;
    private:
        BooleanExp* _operand1;
        BooleanExp* _operand2;
};

AndExp::AndExp(BooleanExp* op1, BooleanExp* op2) {
    _operand1 = op1;
    _operand2 = op2;
}

bool AndExp::Evaluate(Context& aContext) {
    return _operand1->Evaluate(aContext) && _operand2->Evaluate(aContext);
}
//...
BooleanExp* expression;
Context context;
VariableExp* x = new VariableExp("X");
VariableExp* y = new VariableExp("Y");

expression = new OrExp(new AndExp(new Constant(true), x), new AndExp(y, new NotExp(x)));

context.Assign(x, false);
context.Assign(y, true);

bool result = expression->Evaluate(context);

VariableExp* z = new VariableExp("Z");
NotExp not_z(z);

BooleanExp* replacement = expression->Replace("Y", not_z);

context.Assign(z, true);

result = replacement->Evaluate(context);
```

## Mediator (ME)

L'**intento** è definire un oggetto che incapsula il modo in cui interagiscono un insieme di oggetti. Mediator promuove l'accoppiamento lasco impedendo agli oggetti di fare riferimento l'uno all'altro in modo esplicito e consente di variare la loro interazione in modo indipendente.

### Motivazione

*Il design orientato agli oggetti incoraggia la distribuzione del comportamento tra gli oggetti:* la distribuzione può comportare molte connessioni tra gli oggetti (fino a quando ogni oggetto conosce l'altro) e può essere difficile modificare il comportamento del sistema in modo significativo.

**Considerare l'implementazione di finestre di dialogo in un'interfaccia utente grafica:** ad es. una finestra di dialogo che presenta una raccolta di widget come pulsanti, menu e campi di immissione. Spesso ci sono dipendenze tra i widget nella finestra di dialogo. Diverse finestre di dialogo avranno diverse dipendenze tra i widget.

*Evita questi problemi incapsulando il comportamento collettivo in un oggetto mediatore separato* responsabile del controllo e del coordinamento delle interazioni di un gruppo di oggetti. Gli oggetti conoscono solo il mediatore, riducendo così il numero di interconnessioni.

![[MEmotivazione1.png]]

![[MEmotivazione2.png]]

Ecco alcuni *passaggi* in breve:
1. la casella di riepilogo dice al suo direttore che è cambiato;
2. il regista ottiene la selezione dalla casella di riepilogo;
3. il regista passa la selezione al campo di immissione;
4. ora che il campo di immissione contiene del testo, il regista abilita i pulsanti per avviare un'azione.

![[MEmotivazione3.png]]

- *DialogDirector* definisce il comportamento generale di una finestra di dialogo;
- I client chiamano l'operazione *ShowDialog* per visualizzare la finestra di dialogo;
- *CreateWidgets* è un'operazione astratta per creare i widget;
- *WidgetChanged* è un'operazione astratta per informare il loro direttore che sono cambiati;
- *CreateWidgets* viene sovrascritto per creare i widget appropriati e sovrascrive *WidgetChanged* per gestire le modifiche.

### Applicabilità

Usa Mediator quando:

- un insieme di oggetti comunicano in modi ben definiti ma complessi. Le interdipendenze risultanti sono non strutturate e difficili da comprendere;
- riutilizzare un oggetto è difficile perché fa riferimento e comunica con molti altri oggetti;
- un comportamento distribuito tra diverse classi dovrebbe essere personalizzabile senza molte sottoclassi.

### Struttura

![[MEstruttura1.png]]

- **Mediator (DialogDirector):** definisce un'interfaccia per comunicare con il *Colleague*;
- **ConcreteMediator (FontDialogDirector):** implementa il comportamento cooperativo coordinando gli oggetti *Colleague* e conosce e mantiene i suoi colleghi.
- **Colleague classes (ListBox, EntryField):** ogni classe *Colleague* conosce il proprio oggetto *Mediatore* e ogni *Colleague* comunica tramite il proprio mediatore.

### Collaborazione

I client accedono a un'istanza Singleton esclusivamente tramite l'operazione *Istance* di Singleton.

![[MEstruttura2.png]]

### Conseguenze

- **Limita le sottoclassi:** comportamento è localizzato, la modifica richiede la sottoclasse solo *Mediator* e le classi *Colleague* possono essere riutilizzate così come sono;
- **Disaccoppia i colleghi:** promuove l'accoppiamento libero tra colleghi ed è possibile variare e riutilizzare le classi *Colleague* e *Mediator* in modo indipendente;
- **Semplifica i protocolli oggetto:** sostituisce le interazioni molti-a-molti con interazioni uno-a-molti (ME e colleghi) anche perchè uno-a-molti è più facile da capire, mantenere ed estendere;
- **Astrae il modo in cui gli oggetti cooperano:** ti consente di concentrarti su come gli oggetti interagiscono indipendentemente dal loro comportamento individuale e può aiutare a chiarire come interagiscono gli oggetti in un sistema;
- **Centralizza il controllo:** la ME scambia la complessità dell'interazione con la complessità nel mediatore e il mediatore può diventare un monolite difficile da mantenere.

### Implementazione

- **Omissione della classe Mediator astratta:** non è necessario definire una classe *Mediator* astratta quando i colleghi lavorano con un solo mediatore;
- **Comunicazione collega-mediatore:** i colleghi devono comunicare con il proprio mediatore quando si verifica un evento di interesse. Implementare il *Mediator* come osservatore e definisce un'interfaccia di notifica specializzata in *Mediator* che consente ai colleghi di essere più diretti nella loro comunicazione.

### Esempio di finestra di dialogo

```cpp
class DialogDirector {
    public:
        virtual ~DialogDirector();
        
        virtual void ShowDialog();
        virtual void WidgetChanged(Widget*) = 0;
    protected:
        DialogDirector();
        virtual void CreateWidgets() = 0;
};
//...
class Widget {
    public:
        Widget(DialogDirector*);
        virtual void Changed();
        
        virtual void Handlemouse(MouseEvent& event);
        //...
    private:
        DialogDirector* _director;
};

void Widget::Changed() {
    _director->WidgetChanged(this);
}
//...
class ListBox: public Widget {
    public:
        ListBox(DialogDirector*);
        
        virtual const char* GetSelection();
        virtual void SetList(List<char*>* listItems);
        virtual void HandleMouse(mouseEvent& event);
        //...
};

class EntryField: public Widget {
    public:
        virtual void SetText(const char* text);
        virtual const char* GetText();
        virtual void HandleMouse(MouseEvent& event);
        //...
};
//...
class Button: public Widget {
    public:
        Button(DialogDirector*);
        
        virtual void SetText(const char* text);
        virtual void HandleMouse(MouseEvent& event);
        //...
};

void Button::HandleMouse(MouseEvent& event) {
    //...
    Changed();
}
//...
class FontDialogDirector: public DialogDirector {
    public:
        FontDialogDirector();
        virtual ~FontDialogDirector();
        virtual void WidgetChanged(Widget*);
    protected:
        virtual void CreateWidgets();
    private:
        Button* _ok;
        Button* _cancel;
        ListBox* _fontList;
        EntryField* _fontName;
};
//...
void FontDialogDirector::CreateWidgets() {
    _ok = new Button(this);
    _cancel = new Button(this);
    _fontList = new ListBox(this);
    _fontName = new EntryField(this);
    
    // riempi la listBox con i nomi dei font disponibili
    // assemblare i widget nella finestra di dialogo
}
//...
void FontDialogDirector::WidgetChanged(Widget* theChangeWidget) {
    if(theChangedWidget == _fontList) 
        _fontName->SetText(_fontList->GetSelection());
    else if(theChangedWidget == _ok)
        // applica la modifica del carattere e chiudi la finestra di dialogo
    else if(theChangedWidget == _cancel)
        // ignora la finestra di dialogo
}
```

## Chain of Responsibility (CoR)

L'**intento** è evitare di associare il mittente di una richiesta al suo destinatario dando a più di un oggetto la possibilità di gestire la richiesta e concatenare gli oggetti riceventi e passare la richiesta lungo la catena finché un oggetto non la gestisce.

### Motivazione

**Consideriamo una funzione di aiuto sensibile al contesto per una GUI:** ottieni informazioni di aiuto su qualsiasi parte dell'interfaccia semplicemente facendo clic su di essa. L'aiuto dipende dalla parte selezionata dell'interfaccia. Se non esistono informazioni di aiuto specifiche, viene visualizzato un messaggio di aiuto più generale.

Il problema qui è che l'oggetto che alla fine *fornisce* l'aiuto non è noto esplicitamente all'oggetto (ad esempio il pulsante) che *avvia* la richiesta di aiuto.

L'idea del CoR è quella di disaccoppiare mittenti e destinatari dando a più oggetti la possibilità di gestire una richiesta: la richiesta viene passata lungo una catena di oggetti finché uno di loro non la gestisce.

### Applicabilità

Usa CoR quando:

- più di un oggetto può gestire una richiesta e il gestore non è noto a priori: il gestore dovrebbe essere accertato automaticamente;
- si desidera inviare una richiesta a uno dei diversi oggetti senza specificare esplicitamente il destinatario;
- l'insieme di oggetti in grado di gestire una richiesta dovrebbe essere specificato dinamicamente.

![[CoResempio.png]]

### Struttura

![[CoRstruttura.png]]

- **Handler (HelpHandler):** definisce un'interfaccia per la gestione delle richieste e (opzionale) implementa il successore;
- **ConcreteHandler (PrintButton, PrintDialog):** gestisce le richieste di cui è responsabile, può accedere al suo successore; 
- **Client:** avvia la richiesta a un *ConcreteHandler*.

### Collaborazione

Quando un client invia una richiesta, la richiesta si propaga lungo la catena finché un *ConcreteHandler* non si assume la responsabilità di gestirla.

### Conseguenze

- **Accoppiamento ridotto:** CoR libera un oggetto dal sapere quale altro oggetto gestisce una richiesta e può semplificare le interconnessioni di oggetti;
- **Maggiore flessibilità nell'assegnazione delle responsabilità agli oggetti:** è possibile aggiungere o modificare le responsabilità per la gestione di una richiesta aggiungendo o modificando in altro modo la catena in fase di esecuzione. Puoi combinare questo con la sottoclasse per specializzare i gestori in modo statico;
- **Ricezione non garantita:** poiché una richiesta non ha un destinatario esplicito, non c'è alcuna garanzia che venga gestita. Una richiesta può anche non essere gestita quando la catena non è configurata correttamente.

### Implementazione

- **Implementazione della catena dei successori:** definire nuovi collegamenti (di solito nell'*Handler*, ma *ConcreteHandlers* potrebbe invece definirli), usa i link esistenti e, se la struttura non riflette la catena di responsabilità richiesta, dovrai definire collegamenti ridondanti.
- **Collegamento successori:** se non ci sono riferimenti preesistenti per definire una catena, dovrai presentarli tu stesso;
- **Rappresentare le richieste:** la richiesta è un'invocazione di un'operazione hardcoded e bisogna utilizzare una singola funzione del gestore che accetta un codice di richiesta come parametro.

Un esempio d'implementazione:

```cpp
void Handler::HandleRequest(Request* theRequest) {
    switch(theRequest->GetKind()) {
        case Help:
            // lanciare l'argomento al tipo appropriato
            HandleHelp((HelpRequest*) theRequest);
            break;
        case Print:
            HandlePrint((PrintRequest*) theRequest);
            //...
            break;
        default:
            //...
            break;
    }
}
```

### Esempio

```cpp
typedef int Topic;
consty Topic NO_HELP_TOPIC = -1;

class HelpHandler {
    public:
        HelpHandler(HelpHandler* = 0, Topic = NO_HELP_TOPIC);
        virtual bool HasHelp();
        virtual void SetHandler(HelpHandler*, Topic);
        virtual void HandleHelp();
    private:
        HelpHandler* _successor,;
        Topic _topic;
};
//...
HelpHandler::HelpHandler(HelpHandler* h, Topic t): _successor(h), _topic(t) { }

bool HelpHandler::HasHelp() { return _topic != NO_HELP_TOPIC; }

void HelpHandler::HandleHelp() {
    if(_successor != 0)
        _successor->HandleHelp();
}

class Widget: public HelpHandler {
    protected:
        Widget(Widget* parent, Topic t = NO_HELP_TOPIC);
    private:
        Widget* _parent;
};

Widget::Widget(Widget* w, Topic t): HelpHandler(w, t) {
    _parent = w;
}
//...
class Button: public Widget {
    public:
        Button(Widget* d, Topic t = NO_HELP_TOPIC);
        virtual void HandleHelp();
        // operazioni del widget che il pulsante sovrascrive
};

Button::Button(Widget* h, Topic t): Widget(h, t) { }

void Button::HandlerHelp() {
    if(HasHelp())
        // offrire aiuto sul pulsante
    else 
        HelpHandler::HandleHelp();
}
//...
class Dialog: public Widget {
    public:
        Dialog(Widget* d, Topic t = NO_HELP_TOPIC);
        virtual void HandleHelp();
        // operazioni del widget che il dialog sovrascrive
};

Dialog::Dialog(Widget* h, Topic t): Widget(0) {
    SetHandler(h, t);
}

void Dialog::HandlerHelp() {
    if(HasHelp())
        // offrire aiuto sul dalog
    else 
        HelpHandler::HandleHelp();
}
//...
class Application: public Widget {
    public:
        Application(Topic t): HelpHandler(0, t) { }
        virtual void HandleHelp();
        // operazioni specifiche per l'applicazione...
};

void Application::HandlerHelp() {
    // mostra un elenco di argomenti della guida
}
//...
const Topic PRINT_TOPIC = 1;
const Topic PAPER_ORIENTATION_TOPIC = 2;
const Topic APPLICATION_TOPIC = 3;

Application* application = new Application(APPLICATION_TOPIC);
Dialog* dialog = new Dialog(application, PRINT_TOPIC);
Button* button = new Button(dialog, PAPER_ORIENTATION_TOPIC);

button->HandleHelp();
```

## Observer (OB)

L'**intento** è definire una dipendenza uno-a-molti tra gli oggetti in modo che quando un oggetto cambia stato, tutti i suoi dipendenti vengano notificati e aggiornati automaticamente. È conosciuto anche come *dipendenti* oppure come pubblica-sottoscrivi.

### Motivazione

Dato un insieme di classi cooperanti c'è la necessità di mantenere la coerenza: non vuoi rendere le classi strettamente accoppiate poichè riduce la loro riutilizzabilità.

**Consideriamo toolkit dell'interfaccia utente grafica in cui:**

- separare gli aspetti di presentazione dell'interfaccia utente dai dati dell'applicazione sottostante;
- le classi che definiscono i dati e le presentazioni dell'applicazione possono essere riutilizzate indipendentemente.

![[OBesempio.png]]

L'oggetto dati è rappresentato da:

- un foglio di calcolo e/o un grafico a barre;
- nessun limite al numero di oggetti dipendenti a due: potrebbe esserci un numero qualsiasi di interfacce utente diverse per gli stessi dati.

Gli oggetti chiave sono **soggetto** e **osservatore**: un soggetto può avere un numero qualsiasi di osservatori dipendenti mentre tutti gli osservatori sono avvisati ogni volta che il soggetto subisce un cambiamento di stato.

### Applicabilità

Usa OB quando:

- un'astrazione ha due aspetti, uno dipendente dall'altro: l'incapsulamento di questi aspetti in oggetti separati consente di variarli e riutilizzarli in modo indipendente;
- una modifica a un oggetto richiede la modifica di altri e non sai quanti oggetti ci sono;
- un oggetto notifica altri oggetti senza fare supposizioni su chi siano questi oggetti: in altre parole, non vuoi che questi oggetti siano strettamente accoppiati.

### Struttura

![[OBstruttura.png]]

- **Subject:** conosce i suoi osservatori. Qualsiasi numero di oggetti *Observer* può osservare un soggetto. Fornisce un'interfaccia per attaccare e staccare oggetti *Observer*;
- **Observer:** definisce un'interfaccia di aggiornamento per gli oggetti che dovrebbero essere notificati dei cambiamenti in un soggetto;
- **ConcreteSubject:** memorizza lo stato di interesse per gli oggetti *ConcreteObserver*. Invia una notifica ai suoi osservatori quando il suo stato cambia;
- **ConcreteObserver:** mantiene un riferimento a un oggetto *ConcreteSubject*. I negozi affermano che dovrebbero rimanere coerenti con quelli del soggetto. Implementa l'interfaccia di aggiornamento di *Observer* per mantenere il suo stato coerente con quello del soggetto.

### Collaborazione

*ConcreteSubject* avvisa i suoi osservatori ogni volta che si verifica un cambiamento che potrebbe rendere lo stato dei suoi osservatori incoerente con il proprio.

Dopo essere stato informato di un cambiamento nel soggetto concreto, un oggetto *ConcreteObserver* può interrogare il soggetto per informazioni. *ConcreteObserver* utilizza queste informazioni per conciliare il proprio stato con quello del soggetto.

![[OBcollab.png]]

### Conseguenze

OB ti consente di variare soggetti e osservatori in modo indipendente. Puoi riutilizzare i soggetti senza riutilizzare i loro osservatori e viceversa. OB consente di aggiungere osservatori senza modificare il soggetto o altri osservatori.

Abbiamo un accoppiamento astratto tra *Subject* e *Observer*: un soggetto conosce la sua lista di osservatori, ciascuno conforme all'osservatore astratto, e non conosce la classe concreta di nessun osservatore (l'accoppiamento è astratto e minimale).

*Observer* e *Subject* possono appartenere a diversi livelli di astrazione in un sistema: un soggetto di livello inferiore può comunicare e informare un osservatore di livello superiore.

**Abbiamo un supporto per la comunicazione broadcast**: la notifica che un soggetto invia non deve specificare il suo destinatario. La notifica viene trasmessa automaticamente a tutti gli oggetti interessati che si sono iscritti. Questo ti dà la libertà di aggiungere e rimuovere osservatori in qualsiasi momento.

**Potremmo avere degli aggiornamenti inaspettati:** gli osservatori non conoscono la presenza l'uno dell'altro. Un'operazione innocua sull'argomento può provocare una cascata di aggiornamenti. I criteri di dipendenza possono portare ad aggiornamenti spuri che possono essere difficili da rintracciare.

### Implementazione

- *Mappare i soggetti ai loro osservatori:* memorizzare i riferimenti agli osservatori e utilizzare una ricerca associativa (ad esempio una tabella hash) per mantenere la mappatura soggetto-osservatore;
- *Osservando più di un soggetto:* se un osservatore dipende da più soggetti, bisogna estendere l'interfaccia di aggiornamento in questi casi per far sapere all'osservatore quale soggetto sta inviando la notifica;
- *Assicurarsi che lo stato del soggetto sia coerente prima della notifica:* lo stato del soggetto deve essere coerente prima di chiamare *Notify* e bisogna utilizzare un template method prima di chiamare *Notify*;
- *Evitare protocolli di aggiornamento specifici dell'osservatore:* abbiamo due modelli di aggiornamento: 
    - **modello push:** il soggetto invia agli osservatori informazioni dettagliate sul cambiamento, che lo vogliano o meno. Presuppone che i soggetti sappiano qualcosa sui loro osservatori. Potrebbe rendere gli osservatori meno riutilizzabili;
    - **modello pull:** il soggetto non invia altro che la minima notifica, e gli osservatori chiedono esplicitamente i dettagli in seguito. Sottolinea l'ignoranza del soggetto nei confronti dei suoi osservatori, mentre il modello push. Può essere inefficiente, perché le classi *Observer* devono accertare cosa è cambiato senza l'aiuto del *Subject*.
- *Specificare in modo esplicito le modifiche di interesse:* migliorare l'efficienza degli aggiornamenti registrando gli osservatori solo per specifici eventi di interesse (il soggetto informa solo gli osservatori che abbiano manifestato interesse a tale evento.). 
```cpp
void Subject::Attach(Observer*, Aspect& interest);
void Observer::Update(Subject*, Aspect& interest);
```
- *Combinare le classi Subject e Observer:* se la lingua non ha ereditarietà multipla (come Smalltalk) non definire classi separate Subject e Observer ma combinare le loro interfacce in una classe.

**Chi fa scattare l'aggiornamento (chiamato *Notify*)?**

- *operazioni di impostazione dello stato su *Subject* chiamata *Notify*:* i client non devono ricordarsi di chiamare *Notify* sull'argomento e diverse operazioni consecutive causeranno diversi aggiornamenti consecutivi, che potrebbero essere inefficienti;
- *rendi i clienti responsabili di chiamare Notify:* l'aggiornamento può essere attivato dopo che è stata apportata una serie di modifiche allo stato e i client hanno la responsabilità aggiuntiva di attivare l'aggiornamento.

Avviene l'incapsulamento di semantiche di aggiornamento complesse quando la dipendenza tra soggetti e osservatori è complessa e, quindi, è possibile utilizzare un *ChangeManager*.

Il *ChangeManager* mappa un soggetto ai suoi osservatori, definisce una particolare strategia di aggiornamento e aggiorna tutti gli osservatori dipendenti su richiesta di un soggetto.

![[OBimplementazione.png]]

### Esempio

```cpp
class Observer {
    public:
        virtual void Update(Subject* theChangeSubject) = 0;
    protected:
        Observer();
};
//...
class Subject {
    public:
        virtual ~Subject();
        
        virtual void Attach(Observer*);
        virtual void Detach(Observer*);
        virtual void Notify();
    protected:
        Subject();
    private:
        List<Observer*> *_observers;
};

void Subject::Attach(Observer* o) {
    _observers->Append(o);
}

void Subject::Detach(Observer* o) {
    _observers->Remove(o);
}

void Subject::Notify() {
    ListIterator<Observer*> i(_observers);
    
    for(i.First(); !i.IsDone(); i.Next()) {
        i.CurrentItem()->Update(this);
    }
}
//...
class ClockTimer: public Subject {
    public:
        virtual int GetHour();
        virtual int GetMinute();
        virtual int GetSecond();
        
        void Tick();
};

void ClockTimer::Tick() {
    // aggiornare lo stato del cronometraggio interno...
    Notify();
}
//...
class DigitalClock: public Widget, public Observer {
    public:
        DigitalClock(ClockTimer*);
        virtual ~DigitalClock();
        
        // annulla l'operazione dell'osservatore
        virtual void Update(Subject*);
        
        // sovrascrive il funzionamento del Widget e definisce come disegnare l'orologio digitale
        virtual void Draw();
    private:
        ClockTimer* _subject;
};

DigitalClock::DigitalClock(ClockTimer* s) {
    _subject = s;
    _subject->Attach(this);
}

DigitalClock::~DigitalClock() {
    _subject->Detach(this);
}
//...
void DigitalClock::Update(Subject* theChangedSubject) {
    if(theChangedSubject == _subject)
        Draw();
}

void DigitalClock::Draw() {
    // ottenere i nuovi valori dal soggetto
    
    int hour = _subject->GetHour();
    int minute = _subject->GetMinute();
    // ecc.
    
    // disegna l'orologio digitale
}
//...
class AnalogClock: public Widget, public Observer {
    public:
        AnalogClock(ClockTimer*);
        virtual void Update(Subject*);
        virtual void Draw();
        // ...
};
//...
ClockTimer* timer = new ClockTimer;
AnalogClock* analogClock = new AnalogClock(timer);
DigitalClock* digitalClock = new DigitalClock(timer);
```

## Iterator (ITR)

L'**intento** è fornire un modo per accedere sequenzialmente agli elementi di un oggetto aggregato senza esporre la rappresentazione sottostante. È conosciuto anche come *cursor*.

### Motivazione

Un **oggetto aggregato come una lista** dovrebbe dare un modo per accedere agli elementi senza esporre i suoi interni, potrebbe essere attraversato in modi diversi, a seconda di alcune attività, potrebbe anche essere necessario avere più di un attraversamento in sospeso ma non vuoi gonfiare l'interfaccia di *List* con le operazioni.

L'idea chiave è **assumersi la responsabilità dell'accesso** e dell'attraversamento dell'oggetto elenco e inserirlo in un oggetto iteratore.

![[ITRmotivazione1.png]]

Separare l'attraversamento da List: definire iteratori per diverse politiche di attraversamento senza toccare l'elenco. L'iteratore e l'elenco sono accoppiati: il client si impegna a una particolare struttura aggregata e, quindi, cambia la classe aggregata senza modificare il codice client tramite iterazione polimorfica.

![[ITRmotivazione2.png]]

### Applicabilità

Usa il pattern Iteratore:

- per accedere ai contenuti di un oggetto aggregato senza esporre la sua rappresentazione interna;
- per supportare più attraversamenti di oggetti aggregati;
- per fornire un'interfaccia uniforme per l'attraversamento di diverse strutture aggregate (cioè per supportare l'iterazione polimorfica).

### Struttura

![[ITRstruttura.png]]

- **Iterator:** definisce un'interfaccia per l'accesso e l'attraversamento degli elementi;
- **ConcreteIterator:** implementa l'interfaccia *Iterator* tiene traccia della posizione corrente nell'attraversamento dell'aggregato;
- **Aggregate:** definisce un'interfaccia per creare un oggetto *Iterator*;
- **ConcreteAggregate:** implementa l'interfaccia di creazione di *Iterator* per restituire un'istanza del *ConcreteIterator* corretto.

### Collaborazione

Un *ConcreteIterator* tiene traccia dell'oggetto corrente nell'aggregato e può calcolare l'oggetto successivo nell'attraversamento.

### Conseguenze

- **Supporta le variazioni nell'attraversamento di un aggregato:** aggregati complessi possono essere attraversati in molti modi;
- **Semplificare la modifica dell'algoritmo di attraversamento:** è sufficiente sostituire l'istanza dell'iteratore con un'altra;
- **Semplificare l'interfaccia di *Aggregate*:** l'interfaccia di attraversamento di *Iterator* elimina la necessità di un'interfaccia simile in *Aggregate*;
- **Più di un attraversamento può essere in sospeso su un aggregato:** *Iterator* tiene traccia del proprio stato di attraversamento.

### Implementazione

**Chi controlla l'iterazione?** L'*iteratore interno* ha un'operazione da eseguire e l'iteratore applica tale operazione a ogni elemento dell'aggregato mentre gli iteratori esterni (deboli in un linguaggio come C++) sono più flessibili.

**Chi definisce l'algoritmo di attraversamento?** L'*aggregato* usa l'iteratore come cursore per memorizzare solo lo stato dell'iterazione. Un client invocherà l'operazione *Next* sull'aggregato con il cursore come argomento e l'operazione *Next* cambierà lo stato del cursore. Con l'*iteratore*, l'algoritmo di attraversamento potrebbe aver bisogno di accedere alle variabili private dell'aggregato: viola l'incapsulamento dell'aggregato. È facile usare algoritmi di iterazione diversi sullo stesso aggregato.

**Quanto è robusto l'iteratore?** L'iteratore robusto assicura che gli inserimenti e le rimozioni non interferiscano con l'attraversamento.

Usare **iteratori polimorfici in C++ hanno il loro costo** e dovrebbero essere usati solo quando ce n'è bisogno. Un altro inconveniente: il cliente è responsabile della loro cancellazione: il pattern *Proxy* fornisce un rimedio.

**Gli iteratori possono avere accesso privilegiato.** L'iteratore e l'aggregato sono strettamente accoppiati: usa *friend* in C++ (aggiungi un *friend* per ogni iteratore) e includi operazioni protette per l'accesso ai membri dell'aggregato.

Gli **iteratori per i compositi** sono iteratori esterni difficili da implementare rispetto al tipo *Composite*.

Gli **iteratori nulli** sono un iteratore degenerato utile per gestire le condizioni al contorno.
Un *NullIterator* viene sempre eseguito con l'attraversamento: *IsDone* restituisce *true*.

### Esempio

```cpp
template <class Item>
class List {
    public:
        List(long size = DEFAULT_LIST_CAPACITY);
        
        long Count() const;
        Item& Get(long index) const;
        // ...
};

template <class Item>
class Iterator {
    public:
        virtual void First() = 0;
        virtual void Next() = 0;
        virtual bool IsDone() const = 0;
        virtual Item CurrentItem() const = 0;
    protected:
        Iterator();
};
//...
template <class Item>
class ListIterator: public Iterator<Item> {
    public:
        ListIterator(const List<Item>* aList);
        virtual void First();
        virtual void Next();
        virtual bool IsDone() const;
        virtual Item CurrentItem() const;
    private:
        const List<Item>* _list;
        long _current;
};

template <class Item>
ListIterator<Item>::ListIterator(const List<Item>* aList): _list(aList), _current(0) { }

template <class Item>
void ListIterator<Item>::First() {
    _current = 0;
}

template <class Item>
Item ListIterator<Item>::CurrentItem() const {
    if(IsDone())
        throw IteratorOutOfBounds;
    return _list->Get(_current);
}
//...
void PrintEmployees(Iterator<Employee*>& i) {
    for(i.First(); !i.IsDone(); i.Next())
        i.CurrentItem()->Print();
}

List<Employee*>* employees;
    // ...
ListIterator<Employee*> forward(employees);
ReverseListIterator<Employee*> backward(employees);

PrintEmployess(forward);
PrintEmployess(backward);
//...

// Implementazione di iteratori plimorfici
template <class Item>
class AbstractList {
    public:
        virtual Iterator<Item>* CreateIterator() const = 0;
        // ...
};

template <class Item>
Iterator<Item>* List<Item>::CreateIterator() const {
    return new ListIterator<Item>(this);
}
```

## Memento (MMR)

L'**intento** è che, senza violare l'incapsulamento, cattura ed esternalizza lo stato interno di un oggetto in modo che l'oggetto possa essere ripristinato in questo stato in un secondo momento. È conosciuto anche come *token*.

### Motivazione

A volte *è necessario registrare lo stato interno di un oggetto* per implementare i checkpoint o annullare i meccanismi. È necessario salvare le informazioni sullo stato in modo da poter ripristinare lo stato degli oggetti. *Lo stato è solitamente inaccessibile ad altri oggetti*: esporre questo stato violerebbe l'incapsulamento.

**Consideriamo ad esempio un editor grafico che supporti la connettività tra oggetti:** un utente può connettere due rettangoli con una linea e i rettangoli rimangono connessi quando l'utente ne sposta uno.

![[MMTmotivazione.png]]

Una classe **ConstraintSolver** registra le connessioni man mano che vengono eseguite e genera equazioni matematiche che le descrivono.
Il meccanismo di annullamento deve funzionare con *ConstraintSolver*.

Per risolvere il problema utilizziamo un oggetto memento.
Un **memento** è un oggetto che memorizza un'istantanea dello stato interno di un altro oggetto: l'**originator** del memento. Il meccanismo di annullamento richiederà un memento all'originator. Il memento è "opaco" per altri oggetti.

### Applicabilità

Usa il pattern Memento quando:

- un'istantanea di (una parte dello) stato di un oggetto deve essere salvata in modo che possa essere ripristinato in quello stato in seguito;
- un'interfaccia diretta per ottenere lo stato esporrebbe i dettagli di implementazione e interromperebbe l'incapsulamento dell'oggetto.

### Struttura

![[MMTstruttura.png]]

- **Memento (SolverState):** memorizza lo stato interno dell'oggetto *Originator* e protegge dall'accesso da parte di oggetti diversi dall'originator;
- **Originator (ConstraintSolver):** crea un memento contenente un'istantanea del suo stato interno attuale e utilizza il memento per ripristinare il suo stato interno;
- **Caretaker (undo mechanism):** è responsabile della custodia del memento e non opera né esamina il contenuto di un memento.

### Collaborazione

Un custode richiede un memento da un originator, lo trattiene per un po' di tempo e lo restituisce all'originator.

A volte il custode non restituirà il memento all'originator, perché il mittente potrebbe non aver mai bisogno di tornare a uno stato precedente.

I memento sono passivi. Solo l'originator che ha creato un memento assegnerà o recupererà il suo stato.

![[MMTcollaborazione.png]]

### Conseguenze

**Preserva i confini di incapsulamento:** MMT protegge altri oggetti da componenti interni di *Originator* potenzialmente complessi, preservando così i confini di incapsulamento.

**MMT semplifica *Originator*:** il fatto che i clienti gestiscano lo stato che chiedono semplifica l'*Originator* e impedisce ai clienti di informare gli originator quando hanno finito.

**L'uso dei memento potrebbe essere costoso:** i memento potrebbero comportare un sovraccarico considerevole se l'*Originator* deve copiare grandi quantità di informazioni e i clienti creano e restituiscono memento al creatore abbastanza spesso. A meno che l'incapsulamento e il ripristino dello stato di origine non siano economici, il modello potrebbe non essere appropriato.

**Definizione di interfacce strette e larghe:** può essere difficile in alcune lingue garantire che solo l'originator possa accedere allo stato del memento.

**Costi nascosti nella cura dei memento:** un custode è responsabile dell'eliminazione degli MMT di cui si occupa.

### Implementazione

I memento hanno due interfacce: una larga per i creatori e una stretta per altri oggetti. C++ ti consente di farlo rendendo l'*Originator* amico di Memento e rendendo privata l'ampia interfaccia di *Memento*: solo l'interfaccia stretta dovrebbe essere dichiarata pubblica.

**Memorizzazione di modifiche incrementali:** se MMT viene creato in una sequenza prevedibile, *Memento* può salvare solo le modifiche incrementali come, per esempio, comandi annullabili in un elenco di cronologia.

### Esempio

```cpp
class Graphic;
// classe base per oggetti grafici nell'editor grafico

class MoveCommand {
    public:
        MoveCommand(Graphic* target, const Point& delta);
        void Execute();
        void Unexecute();
    private:
        ConstraintSolverMemento* _state;
        Point _delta;
        Graphic* _target;
};
//...
class ConstraintSolver {
    public:
        static ConstraintSolver* Instance();
        
        void Solve();
        void AddConstraint(Graphic* startConnection, Graphic* endConnection);
        void removeConstraint(Graphic* startConnection, Graphic* endConnection);
        
        ConstraintSolverMemento* CreateMemento();
        void SetMemento(ConstraintSolverMemento*);
    private:
        // stato non banale e operazioni per imporre la semantica della connettività
};

class ConstraintSolverMemento {
    public:
        virtual ~ConstraintSolverMemento();
    private:
        friend class ConstraintSolver;
        ConstraintSolverMemento();
        
        // stato del risolutore di vincoli privati
};
//...
void MoveCommand::Execute() {
    ConstraintSolver* solver = ConstraintSolver::Instance();
    _state = solver->CreateMemento(); // creazione di memento
    _target->Move(_delta);
    solver->Solve();
}

void MoveCommand::Unexecute() {
    ConstraintSolver* solver = ConstraintSolver::Instance();
    _target->Move(-_delta); 
    solver->SetMemento(_state); // ripristinare lo stato del risolutore
    solver->Solve();
}
```

## State (ST)

L'**intento** è consentire a un oggetto di modificare il suo comportamento quando cambia il suo stato interno. L'oggetto sembrerà cambiare la sua classe. È conosciuto anche come *oggetti per gli Stati* ed è simile a *Flyweight* e *Singleton*.

### Motivazione

**Considera una classe TCPConnection che rappresenta una connessione di rete:** un oggetto *TCPConnection* può trovarsi in uno dei diversi stati: *Established*, *Listening*, *Closed*.

Quando un oggetto *TCPConnection* riceve richieste da altri oggetti, risponde in modo diverso a seconda del suo stato corrente.

![[STmotivazione.png]]

La classe *TCPState* dichiara un'interfaccia comune a tutte le classi che rappresentano diversi stati operativi.
Le sottoclassi di *TCPState* implementano un comportamento specifico dello stato.

### Applicabilità

Utilizzare ST in uno dei seguenti casi:

- il comportamento di un oggetto dipende dal suo stato e deve cambiare il suo comportamento in fase di esecuzione a seconda di quello stato;
- le operazioni hanno istruzioni condizionali di grandi dimensioni e multiparte che dipendono dallo stato dell'oggetto.

### Struttura

![[STstruttura.png]]

- **Context (TCPConnection):** definisce l'interfaccia di interesse per i client e mantiene un'istanza di una sottoclasse *ConcreteState* che definisce lo stato corrente;
- **State (TCPState):** definisce un'interfaccia per incapsulare il comportamento associato a un particolare stato del *Context*;
- **ConcreteState subclasses (TCPEstablished, TCPListen, TCPClosed):** ogni sottoclasse implementa un comportamento associato ad uno stato del *Context*.

### Collaborazione

Il contesto delega le richieste specifiche dello stato all'oggetto *ConcreteState* corrente.

Un contesto può passare se stesso come argomento all'oggetto *State* che gestisce la richiesta: ciò consente all'oggetto *State* di accedere al contesto se necessario.

Context è l'interfaccia principale per i clienti: i client possono configurare un contesto con oggetti di stato e, una volta configurato un contesto, i suoi client non devono occuparsi direttamente degli oggetti di stato.

Sia *Context* che le sottoclassi *ConcreteState* possono decidere quale stato succede a un altro e in quali circostanze.

### Conseguenze

Localizza il comportamento specifico dello stato e il comportamento delle partizioni per stati diversi.

ST mette tutti i comportamenti associati a un particolare stato in un oggetto: nuovi stati e transizioni possono essere aggiunti facilmente definendo nuove sottoclassi.

Dichiarazioni condizionali di grandi dimensioni sono indesiderabili: rendono il codice meno esplicito e sono difficili da modificare ed estendere.

La ST impone una struttura al codice e ne rende più chiaro l'intento.

Rende esplicite le transizioni di stato: se lo stato è definito in termini di valori di dati interni, le transizioni di stato non hanno una rappresentazione esplicita. con oggetti separati per stati diversi rende le transizioni più esplicite.

Gli oggetti di stato possono essere condivisi: se gli oggetti *State* non hanno variabili di istanza, i contesti possono condividere un oggetto *State* (*Flyweight*).

### Implementazione

Chi definisce le transizioni di stato? ST non specifica quale partecipante definisce i criteri per le transizioni di stato e possono essere implementati interamente nel *Context*:

- le stesse sottoclassi di *State* specificano il loro stato successore e quando effettuare la transizione;
- ciò richiede l'aggiunta di un'interfaccia al contesto che consente agli oggetti di *State* di impostare lo stato corrente del *Context* in modo esplicito;
- uno svantaggio è che una sottoclasse di *State* avrà conoscenza di almeno un'altra, il che introduce dipendenze di implementazione tra le sottoclassi.

In C++ puoi imporre la struttura al codice guidato dallo stato:

- utilizzare le tabelle per mappare gli input alle transizioni di stato;
- per ogni stato la tabella associa ogni possibile input a uno stato successivo;
- converte il codice condizionale (e le funzioni virtuali, nel caso del modello State) in una ricerca in una tabella.

Il vantaggio principale è la regolarità: modificare i dati invece di cambiare il codice del programma. Ci sono alcuni svantaggi:

- una ricerca in una tabella è spesso meno efficiente di una chiamata di funzione (virtuale);
- criteri di transizione meno espliciti e più difficili da comprendere;
- è difficile aggiungere azioni per accompagnare le transizioni di stato;
- il modello ST modella il comportamento specifico dello stato, mentre l'approccio guidato dalla tabella definisce le transizioni di stato.

Creazione e distruzione di oggetti di *State* solo quando sono necessari oppure per crearli in anticipo e non distruggerli mai.

Utilizzo dell'ereditarietà dinamica: la modifica del comportamento può essere ottenuta modificando la classe dell'oggetto in fase di esecuzione e non è possibile nella maggior parte dei linguaggi orientati agli oggetti.

### Esempio

Diamo ora il codice C++ per l'esempio di connessione TCP descritto nella motivazione.

```cpp
class TCPOctetStream;
class TCPState;

class TCPConnection {
    public:
        TCPConnection();
        
        void ActiveOpen();
        void PassiveOpen();
        void Close();
        
        void Send();
        void Acknowledge();
        void Synchronize();
        
        void ProcessOctet(TCPOctetStream*);
    protected:
        friend class TCPState;
        void ChangeState(TCPState*);
    private:
        TCPState* _state;
};
//...
class TCPState {
    public:
        virtual void Transmit(TCPConnection*, TCPOctetStream*);
        virtual void ActiveOpen(TCPConnection*);
        virtual void PassiveOpen(TCPConnection*);
        virtual void Close(TCPConnection*);
        virtual void Acknowledge(TCPConnection*);
        virtual void Synchronize(TCPConnection*);
        virtual void Send(TCPConnection*);
    protected:
        void ChangeState(TCPConnection*, TCPState*);
};
//...
void TCPConnection::PassiveOpen() {
    _state->PassiveOpen(this);
}

void TCPConnection::Close() {
    _state->Close(this);
}

void TCPConnection::Acknowledge() {
    _state->Acknowledge(this);
}

void TCPConnection::Synchronize() {
    _state->Synchronize(this);
}

TCPConnection::TCPConnection() {
    _state = TCPClosed::Instance();
}

void TCPConnection::ChangeState(TCPState* s) {
    _state = s;
}
//...
class TCPEstablished: public TCPState {
    static TCPState* Instance();
    
    virtual void Trasmit(TCPConnection*, TCPOctetStream*);
    virtual void Close(TCPConnection*);
};

class TCPListen: public TCPState {
    static TCPState* Instance();
    
    virtual void Send(TCPConnection*);
    //...
};

class TCPlosed: public TCPState {
    static TCPState* Instance();
    
    virtual void ActiveOpen(TCPConnection*);
    virtual void PassiveOpen(TCPConnection*);
    //...
};
//...
void TCPClosed::ActiveOpen(TCPConnection* t) {
    ChangeState(t, TCPEstablished::Instance());
}

void TCPClosed::PassiveOpen(TCPConnection* t) {
    ChangeState(t, TCPListen::Instance());
}

void TCPEstablished::Close(TCPConnection* t) {
    ChangeState(t, TCPEstablished::Instance());
}

void TCPEstablished::Transmit(TCPConnection* t, TCPOctetStream* o) {
    t->ProcessOctet(o);
}

void TCPListen::Send(TCPConnection* t) {
    ChangeState(t, TCPEstablished::Instance());
}
```

## Strategy (STG)

L'**intento** è definire una famiglia di algoritmi, incapsulali ciascuno e rendili intercambiabili. La strategia consente all'algoritmo di variare indipendentemente dai client che lo utilizzano. È conosciuto anche come *policy* ed è molto simile al *flyweight*.

### Motivazione

![[STGmotivazione.png]]

Esistono molti algoritmi per suddividere un flusso di testo in righe:
- *Cablarli tutti non è desiderabile:* ciò rende i client più grandi e più difficili da mantenere, soprattutto se supportano più algoritmi di interruzione di riga. Algoritmi diversi saranno appropriati in momenti diversi. È difficile aggiungere nuovi algoritmi e variare quelli esistenti quando l'interruzione di riga è parte integrante di un client;
- *Evita questi problemi definendo classi che incapsulano diversi algoritmi di interruzione di riga:* tale algoritmo è chiamato strategia.

### Applicabilità

Usa STG quando:

- molte classi correlate differiscono solo nel loro comportamento. Le strategie forniscono un modo per configurare una classe con uno dei tanti comportamenti;
- hai bisogno di diverse varianti di un algoritmo;
- un algoritmo utilizza dati di cui i clienti non dovrebbero essere a conoscenza;
- una classe definisce molti comportamenti come più istruzioni condizionali nelle sue operazioni.

### Struttura

![[STGstruttura.png]]

- **Strategy (Compositor):** dichiara un'interfaccia comune a tutti gli algoritmi supportati. *Context* utilizza questa interfaccia per chiamare l'algoritmo definito da una *ConcreteStrategy*;
- **ConcreteStrategy (SimpleCompositor, ...):** implementa l'algoritmo utilizzando la interfaccia STG;
- **Context (Composition):** è configurato con un oggetto *ConcreteStrategy*, mantiene un riferimento a un oggetto *Strategy* e può definire un'interfaccia che consente a *Strategy* di accedere ai suoi dati.

### Collaborazione

- **Strategy* e *Context* interagiscono per implementare l'algoritmo scelto:** un contesto può passare tutti i dati richiesti dall'algoritmo alla strategia quando l'algoritmo viene chiamato oppure, il contesto può passare se stesso come argomento alle operazioni di *Strategy*. Ciò consente alla strategia di richiamare il contesto come richiesto;
- **Un contesto inoltra le richieste dei suoi clienti alla sua strategia. I client di solito creano e passano un oggetto *ConcreteStrategy* al contesto:** i client interagiscono esclusivamente con il contesto;
- **C'è spesso una famiglia di classi *ConcreteStrategy* tra cui un cliente può scegliere.**

### Conseguenze

- **Famiglie di algoritmi correlati:** STG definisce una famiglia di algoritmi per contesti da riutilizzare; l'ereditarietà può aiutare a scomporre le funzionalità comuni degli algoritmi;
- **Un'alternativa alla sottoclasse:** sottoclassando una classe *Context*, il comportamento viene cablato in *Context*;
- **Le *Strategy* eliminano le istruzioni condizionali:** STG elimina le istruzioni condizionali (con molte istruzioni condizionali, applica STG);
- **Una scelta di implementazioni:** scegliere diverse implementazioni dello stesso comportamento (con diversi compromessi tempo/spazio);
- **I clienti devono essere consapevoli delle diverse *Strategy*:** utilizzare il modello della *Strategy* solo quando la variazione del comportamento è rilevante per i clienti;
- **Overhead di comunicazione tra *Strategy* e *Context*:** è probabile che alcune *ConcreteStrategies* non utilizzino tutte le informazioni che passano da loro. In tal caso è necessario un accoppiamento più stretto tra *Strategy* e *Context*;
- **Aumento del numero di oggetti:** le *Strategy* aumentano il numero di oggetti.

### Implementazione

1. **Definizione delle interfacce *Strategy* e *Context*:** una *ConcreteStrategy* ha bisogno di un accesso efficiente a tutti i dati di cui ha bisogno da un contesto e viceversa. Due approcci: 
    - *Context* passa i dati nei parametri alle operazioni di *Strategy*;
    - *Context* si passa come argomento;
2. **Strategy* come parametri del modello:** la *Strategy* è selezionata in fase di compilazione e non deve essere modificata in fase di esecuzione: 
```cpp
template <class AStrategy>
class Context {
        void Operation() {
            theStrategy.DoAlgorithm();
        }
        // ...
    private:
        AStrategy theStrategy:
};

class MyStrategy {
    public:
        void DoAlgorithm();
};

Context<MyStrategy> aContext;
```
    Non c'è bisogno di definire una *Strategy* astratta e il legame statico aumenta l'efficienza;
3. **Rendere opzionali gli oggetti *Strategy*:** *Context* verifica se ha un oggetto *Strategy* prima di accedervi: se ce n'è uno, lo usa normalmente, se non ce n'è uno, *Context* esegue il comportamento predefinito. I client non devono assolutamente occuparsi degli oggetti *Strategy*, a meno che non amino il comportamento predefinito.

### Esempio

Vediamo il codice di alto livello per l'esempio della sezione della *Motivazione*. La classe *Composition* mantiene una raccolta di istanze di *Component*, che rappresentano testo ed elementi grafici in un documento.
Una composizione dispone gli oggetti componenti in linee utilizzando un'istanza di una sottoclasse *Compositor*, che incapsula una strategia di interruzione di riga.

```cpp
class Composition {
    public:
        Composition(Compositor*);
        void Repair();
    private:
        Compositor* _compositor;
        Component* _componente; // l'elenco dei componenti
        int _componentCount; // il numero di componenti
        int _lineWidth; // la larghezza della linea della Composizione
        int* _lineBreaks; // la posizione delle interruzioni di riga nei componenti
        int _lineCount; // il numero delle righe
};

class Compositor {
    public:
        virtual int Compose(Coord natural[], Coord stretch[], Coord shrink[], int componentCount, int lineWidth, int breaks[]) = 0;
    protected:
        Compositor();
};
//...
class SimpleCompositor: public Compositor {
    public:
        SimpleCompositor();
        
        virtual int Compose(Coord natural[], Coord stretch[], Coord shrink[], int componentCount, int lineWidth, int breaks[]);
        //...
}
//...
void Composition::Repair() {
    Coord* natural;
    Coord* stretchability;
    Coord* shrinkability;
    
    // preparare gli array con le dimensioni dei componenti desiderate
    // ...
}

// determinare dove sono le interruzioni:
class TeXCompositor: public Compositor {
    public:
        TeXCompositor();
        
        virtual int Compose(Coord natural[], Coord stretch[], Coord shrink[], int componentCount, int lineWidth, int breaks[]);
        // ...
};
//...
class ArrayCompositor: public Compositor {
    public:
        TeXCompositor();
        
        virtual int Compose(Coord natural[], Coord stretch[], Coord shrink[], int componentCount, int lineWidth, int breaks[]);
        // ...
};

Composition* quick = new Composition(new SimpleCompositor);
Composition* slick = new Composition(new TeXCompositor);
Composition* iconic = new Composition(new ArrayCompositor(100));
```

## Command (CMD)

L'**intento** è incapsulare una richiesta come oggetto, consentendo così di parametrizzare i client con richieste diverse, accodare o registrare le richieste e supportare operazioni annullabili. È conosciuto come *action* e *transaction*.

### Motivazione

**Considera un'interfaccia utente, i toolkit includono oggetti come pulsanti e menu che eseguono una richiesta in risposta all'input dell'utente.** Il pattern *Command* consente agli oggetti del toolkit di effettuare richieste di oggetti dell'applicazione non specificati trasformando la richiesta stessa in un oggetto. Questo oggetto può essere archiviato e passato in giro come altri oggetti.

![[CMDmotivazione1.png]]

L'applicazione configura ogni *MenuItem* con un'istanza di una sottoclasse *Command* concreta.
L'utente seleziona un *MenuItem* che chiama *Execute* al suo comando ed *Execute* esegue l'operazione.

![[CMDmotivazione2.png]]

### Applicabilità

Usa il modello Command quando vuoi:

- parametrizzare gli oggetti in base a un'azione da eseguire;
- specifica, accoda ed esegui le richieste in momenti diversi;
- supportare *annulla* cioè abbiamo uno stato di memorizzazione per invertire i suoi effetti nel *Command* stesso;
- le modifiche alla registrazione del supporto possono essere riapplicate in caso di arresto anomalo del sistema;
- strutturare un sistema attorno a operazioni di alto livello costruite su operazioni primitive.

### Struttura

![[CMDstruttura.png]]

- **Command:** dichiara un'interfaccia per l'esecuzione di un'operazione;
- **ConcreteCommand (PasteCommand, OpenCom...):** definisce un'associazione tra un oggetto *Receiver* e un'azione e implementa *Execute* invocando l'operazione o le operazioni corrispondenti sul *Receiver*;
- **Client (Application):** crea un oggetto *ConcreteCommand* e imposta il suo ricevitore;
- **Invoker (MenuItem):** chiede al comando di eseguire la richiesta;
- **Receiver (Document, Application):** sa come eseguire le operazioni associate richieste.

### Collaborazione

1. Il client crea un oggetto *ConcreteCommand* e specifica il suo destinatario;
2. Un oggetto *Invoker* memorizza l'oggetto *ConcreteCommand*;
3. L'*Invoker* invia una richiesta chiamando *Execute* sul comando: quando i comandi sono annullabili, *ConcreteCommand* memorizza lo stato per l'annullamento del comando prima di richiamare *Execute*;
4. L'oggetto *ConcreteCommand* invoca operazioni sul suo destinatario per eseguire la richiesta.

![[CMDcollaborazione.png]]

### Conseguenze

Disaccoppia l'oggetto che richiama l'operazione da quello che sa come eseguirla.
I comandi sono oggetti di prima classe: possono essere manipolati ed estesi come qualsiasi altro oggetto.
Puoi assemblare i comandi in un comando composito: i comandi Composite sono un'istanza di Composite.
È facile aggiungere nuovi *Command*: non è necessario modificare le classi esistenti.

### Implementazione

Quanto dovrebbe essere intelligente un comando? Dalla semplice definizione di un legame tra un destinatario e le azioni della richiesta, all'implementazione di tutto da solo, tra CMD che hanno una conoscenza sufficiente per trovare il loro destinatario in modo dinamico.

Supportare l'annullamento e il ripristino fornendo un modo per invertire l'esecuzione; per supportare un livello di annullamento, un'applicazione deve memorizzare solo l'ultimo comando eseguito. Per annullare e ripetere a più livelli, l'applicazione necessita di un elenco cronologico di comandi.

Evitare l'accumulo di errori nel processo di annullamento: l'isteresi può essere un problema nell'assicurare un meccanismo di annullamento/ripristino affidabile e che conservi la semantica. Per evitare accumuli di errori CMD può essere un MMT.

Utilizzo di modelli C++ per evitare la sottoclassificazione: se i CMD non sono annullabili e non richiedono argomenti.

### Esempio

```cpp
class Command {
    public:
        virtual ~Command();
        virtual void Execute() = 0;
    protected:
        Command();
};
//...
class OpenCommand: public Command {
    public:
        OpenCommand(Application*);
        virtual void Execute();
    protected:
        virtual const char* AskUser();
    private:
        Application* _application;
        char* _response;
};

OpenCommand::OpenCommand(Application* a) {
    _application = a;
}

void OpenCommand::Execute() {
    const char* name = AskUser();
    if(name != 0) {
        Document* document = new Document(name);
        _application->Add(document);
        document->Open();
    }
}
//...
template<class Receiver>
class SimpleCommand: public Command {
    public:
        typedef void(Receiver::* Action)();
        SimpleCommand(Receiver* r, Action a): _receiver(r), _action(a) { }
        virtual void Execute();
    private:
        Action _action;
        Receiver* _receiver;
};

template <class Receiver>
void SimpleCommand<Receiver>..Execute() {
    (receiver->*_action)();
}
//...
class MacroCommand: public Command {
    public:
        MacroCommand();
        virtual ~MarcoCommand();
        virtual void Add(Command*);
        virtual void Remove(Command*);
        virtual void Execute();
    private:
        List<Command*>* _cmds;
};

void MacroCommand::Execute() {
    ListIterator<Command*> i(_cmds);
    for(i.First(); !i.IsDone(); i.Next()) {
        Command* c = i.CurrentItem();
        c->Execute();
    }
}

void MacroCommand::Add(Command* c) {
    _cmds->Append(c);
}

void MacroCommand::Remove(Command* c) {
    _cmds->Remove(c);
}
```

### Modelli correlati

- Un Composite può essere usato per implementare MacroCommands.
- Un Memento può mantenere lo stato richiesto dal comando per annullare il suo effetto.
- Un comando che deve essere copiato prima di essere inserito nell'elenco della cronologia funge da Prototipo.

## Visitor (VST)

L'**intento** è rappresentare un'operazione da eseguire sugli elementi di una struttura a oggetti e Visitor consente di definire una nuova operazione senza modificare le classi degli elementi su cui opera.

### Motivazione

**Considera un compilatore che rappresenta i programmi come alberi di sintassi astratti:** dovrà eseguire operazioni o alberi di sintassi astratti e le operazioni dovranno trattare i nodi che rappresentano istruzioni di assegnazione in modo diverso dai nodi che rappresentano variabili o espressioni aritmetiche.

![[VSTmotivazione1.png]]

Tutte queste operazioni sull'albero della sintassi astratta sono distribuite e porta a un sistema difficile da comprendere, mantenere e modificare.

Il Visitor raggruppa le operazioni correlate da ciascuna classe in un oggetto separato. Ogni nuova operazione potrebbe essere aggiunta separatamente. Le classi dei nodi sono indipendenti dalle operazioni ad esse applicabili. Viene passato ad elementi della sintassi astratta; un elemento "accetta" il visitatore, il VST eseguirà quindi l'operazione (prima era nella classe dell'elemento) per quell'elemento.

![[VSTmotivazione2.png]]

### Applicabilità

Usa VST quando:

- una struttura di oggetti contiene molte classi di oggetti con interfacce diverse e si desidera eseguire operazioni su questi oggetti che dipendono dalle loro classi concrete;
- molte operazioni distinte e non correlate devono essere eseguite su oggetti in una struttura di oggetti;
- se le classi che definiscono la struttura dell'oggetto cambiano raramente e spesso si desidera definire nuove operazioni sulla struttura.

### Struttura

![[VSTstruttura.png]]

- **Element (Node):** definisce il metodo *accept()* che prende un *Visitor* come argomento;
- **ConcreteElement (AssignmentNode,VariableRefNode):** implementa un oggetto *Element* che prende un *Visitor* come argomento;
- **ObjectStructure (Program):** contiene una collezione di *Element* che può essere visitata dagli oggetti *Visitor*;
- **Visitor (NodeVisitor):** dichiara un metodo *visit()* per ogni *Element*; il nome del metodo ed il parametro identificano la classe *Element* che ha effettuato l’invocazione;
- **ConcreteVisitor (TypeCheckingVisitor):** implementa il metodo *visit()* e definisce l’algoritmo da applicare per l’*Element* passato come parametro.

### Collaborazione

Un cliente crea un oggetto *ConcreteVisitor* e attraversa la struttura dell'oggetto, visitando ogni elemento.

Quando un elemento viene visitato, chiama l'operazione *visit()* che corrisponde alla sua classe.
L'elemento si autofornisce per consentire al visitatore di accedere al suo stato, se necessario.

![[VSTcollaborazione.png]]

### Conseguenze

Tale pattern presenta i seguenti vantaggi/svantaggi:

- **Facilità nell’aggiungere nuovi *Visitor*:** definendo un nuovo *Visitor* sarà possibile aggiungere una nuova operazione ad un *Element*;
- **Difficoltà nell’aggiungere nuovi *Element*:** definire un nuovo *Element* comporterà la modifica dell’interfaccia *Visitor* e di tutte le implementazioni;
- **Separazione tra stato ed algoritmi:** gli algoritmi di elaborazioni sono nascosti nelle classi *Visitor* e non vengono esposti nelle classi *Element*;
- **Iterazione su struttura eterogenea:** la classe *Visitor* è in grado di accedere a tipi diversi, senza la necessità che tra di essi ci sia un vincolo di parentela. In poche parole, il metodo *visit()* può definire come parametro un tipo X oppure un tipo Y senza che tra di essi ci sia alcuna relazione di parentela, diretta o indiretta;
- **Accumulazione dello stato:** un *Visitor* può accumulare delle informazioni di stato a seguito dell’attraversamento degli *Element*;
- **Violazione dell’incapsulamento:** i *Visitor* devono poter accedere allo stato degli *Element* e questo può comportare la violazione dell’incapsulamento.

### Implementazione

Il pattern Visitor suscita sensazioni di odio-amore e ciò è molto comprensibile a causa della sua complessità e dei suoi limiti. Inoltre non è semplice identificare la casistica in cui utilizzarlo anche a fronte del fatto che, se le specifiche cambiano, questo pattern resta molto faraginoso da mantenere.

Nella sua forma più semplice questo pattern esprime semplicemente il Double Dispatch che ne costituisce la sua natura che occorrerà rivedere.

Inoltre occorre osservare che questo pattern è stato oggetto di proposte di rivisitazione.

### Esempio

```cpp
class EquimentVisitor {
    public:
        virtual ~EquipmentVisitor();
        
        virtual void VisitFloppyDisk(FloppyDisk*);
        virtual void VisitCard(Card*);
        virtual void VisitChassis(Chassis*);
        virtual void VisitBus(Bus*);
        
        // e così via per altre sottoclassi concrete di Equipment
    protected:
        EquipmentVisitor();
};
//...
void FloppyDisk::Accept(EquipmentVisitor& visitor) {
    visitor.VisitFloppyDisk(this);
}

void Chassis::Accept(EquipmentVisitor& visitor) {
    for(ListIterator i(_parts); !i.IsDone(); i.Next())
        i.CurrentItem()->Accept(visitor);
    visitor.VisitChassis(this);
}
//...
class PricingVisitor: public EquipmentVisitor {
    public:
        PricingVisitor();
        
        Currency& GetTotalPrice();
        
        virtual void VisitFloppyDisk(FloppyDisk*);
        virtual void VisitCard(Card*);
        virtual void VisitChassis(Chassis*);
        virtual void VisitBus(Bus*);
        //...
    private:
        Currency _total;
};

void PricingVisitor::VisitFloppyDisk(FloppyDisk* e) {
    _total += e->NetPrice();
}

void PricingVisitor::VisitChassis(Chassis* e) {
    _total += e->DiscountPrice();
}
```

### Modelli correlati

- **Composite:** i *Visitors* possono essere utilizzati per applicare un'operazione su una struttura di oggetti definita da *Composite*;
- **Interpreter:** il *Visitors* può essere applicato per fare l'interpretazione.

## Discussione

- **Disaccoppiamento di mittenti e destinatari** con: 
    - *CMD:* un oggetto separato consente al mittente di lavorare con diversi ricevitori;
    - *OB:* soggetti disaccoppiati dagli osservatori da un'interfaccia per la segnalazione di cambiamenti nei soggetti e un legame mittente-ricevitore più flessibile rispetto a CMD, con più ricevitori che variano durante il tempo di esecuzione;
    - *ME:* gli oggetti si riferiscono l'un l'altro indirettamente tramite un ME e meno sottoclassi tramite comunicazione centralizzata, sicurezza di tipo più debole;
    - *CoR:* richiesta di disaccoppiamento facendola passare lungo una catena di potenziali destinatari ed è buono se mittente e destinatario fanno già parte della struttura del sistema.
- **Combinazioni di modelli comuni:** 
    - il CdR utilizza la TM;
    - il CdR utilizza CMD per rappresentare le richieste;
    - IN usa ST per analizzare i contesti;
    - VST per implementare valutazioni IN;
    - VST funziona bene con Composite;
    - il CdR si integra con Composite.
