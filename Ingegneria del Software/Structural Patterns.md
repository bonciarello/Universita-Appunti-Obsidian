Abbiamo 7 pattern:

- **Adapter** (AD, Adattatore)
- **Bridge** (BR, Ponte)
- **Composite** (CO)
- **Decorator** (DE)
- **Facade** (FA, Facciata)
- **Proxy** (PR)
- **Flyweight** (FL, Peso mosca)

## Adapter (AD)

L'**intento** è convertire l'interfaccia di una classe in un'altra interfaccia che i client si aspettano.
L'*Adapter* consente alle classi di lavorare insieme che non potrebbero altrimenti a causa di interfacce incompatibili. È conosciuto anche come *wrapper*!

### Motivazione

Una classe progettata per il riutilizzo non può essere riutilizzata perché la sua interfaccia non corrisponde all'interfaccia specifica del dominio richiesta.

**Consideriamo un editor di disegno che consenta agli utenti di disegnare e disporre linee, poligoni, testo, ecc.**
L'editor implementa un'interfaccia astratta *Shape* che ha una forma modificabile e può disegnare se stessa. Sottoclassando Shape implementiamo:

- una classe *LineShape* per le linee;
- una classe *PolygonShape* per poligoni, ecc.

Ma i *TextShape* sono molto complessi rispetto alle *Lines*!
Supponiamo che esista un sofisticato toolkit pronto all'uso per la visualizzazione e la modifica del testo basato su una classe *TextView* con un'interfaccia diversa da *Shape* e quindi non possiamo usare gli oggetti *TextView* e *Shape* in modo intercambiabile.

Potremmo definire *TextShape* in modo che adatti l'interfaccia di *TextView* a quella di *Shape*: questo può essere fatto usando l'ereditarietà o la **composizione di oggetti**.

![[ADesempio.png]]

### Applicabilità

Usa AD quando:
- vuoi utilizzare una classe esistente e la sua interfaccia non corrisponde a quella di cui hai bisogno;
- vuoi creare una classe riutilizzabile che cooperi classi che non hanno necessariamente interfacce compatibili;
- (solo oggetto *Adapter*) è necessario utilizzare diverse sottoclassi esistenti, ma non è pratico adattare la loro interfaccia sottoclasse ciascuna.

### Struttura

Abbiamo due tipi di strutture: **adattatori a classi** (struttura di sopra) e **adattatori ad oggetti** (struttura di sotto).

![[ADstruttura.png]]

- **Target (Shape):** definisce l'interfaccia specifica del dominio utilizzata dal *Client*;
- **Client (DrawingEditor)**: collabora con oggetti conformi all'interfaccia *Target*;
- **Adaptee (TextView):** definisce un'interfaccia esistente che necessita di adattamento;
- **Adapter (TextShape):** adatta l'interfaccia di *Adaptee* all'interfaccia *Target*.

### Collaborazione

I *Client* chiamano le operazioni su un'istanza dell'*Adapter*. A sua volta, l'*Adapter* chiama le operazioni di *Adaptee* che eseguono la richiesta.

### Conseguenze

Informazioni sugli adattatori a classi:

- adatta *Adaptee* a *Target* impegnandosi in una classe *Adapter* concreta;
- un adattatore di classe non funzionerà quando vogliamo adattare una classe e tutte le sue sottoclassi;
- consente ad *Adapter* di sovrascrivere parte del comportamento di *Adaptee*, poiché *Adapter* è una sottoclasse di *Adaptee*;
- introduce un solo oggetto e non è necessaria alcuna ulteriore indiretto del puntatore per arrivare all'adattato.

Informazioni sugli adattatori ad oggetti:

- consente a un singolo *Adapter* di funzionare con molti *Adaptee*, ovvero lo stesso *Adaptee* e tutte le sue sottoclassi (se presenti);
- l'adattatore può anche aggiungere funzionalità a tutti gli *Adaptee* contemporaneamente;
- rende più difficile ignorare il comportamento di *Adaptee* (richiederà la sottoclasse *Adaptee* e fare in modo che *Adapter* faccia riferimento alla sottoclasse piuttosto che all'*Adaptee* stesso).

**Quanto adattamento fa Adapter?** Dalla ridenominazione all'aggiunta di funzionalità e dipende da quanto l'interfaccia di *Target* è simile a quella di *Adaptee*.

Con gli **adattatori collegabili** abbiamo che una classe è più riutilizzabile quando si minimizzano i presupposti che altre classi devono fare per usarla.
Costruendo l'adattamento dell'interfaccia in una classe, si elimina il presupposto che altre classi vedano la stessa interfaccia. Fondamentalmente serve per descrivere classi con adattamento dell'interfaccia integrato.

Con l'**utilizzo di adattatori bidirezionali** forniamo trasparenza: gli adattatori non sono trasparenti per tutti i client e l'oggetto adattato non è più conforme all'interfaccia *Adaptee*.
**Gli adattatori a due vie possono fornire tale trasparenza.**

![[ADconseguenza.png]]

### Implementazione

In un'implementazione C++ di un adattatore di classe, *Adapter* erediterà pubblicamente da *Target* e privatamente da *Adaptee*.
Pertanto *Adapter* sarebbe un sottotipo di *Target* ma non di *Adaptee*.

Per gli **adattori collegabili** utilizziamo operazioni astratte e oggetti delegati oppure adattori parametrizzati (*Smalltalk*).

### Esempio dell'editor del disegno

```cpp
class Shape {
    public:
        virtual void BoundingBox (Point& bottomLeft, Point& topRight) const;
        virtual Manipulator* CreateManipulator() const;
};

class TextView {
    public:
        TextView();
        void GetOrigin(Coord& x, Coord& y) const;
        void GetExtent(Coord& width, Coord& height) const;
        virtual bool IsEmpty() const;
};

class TextShape: public Shape, private TextView {
    public:
        TextShape();
        
        virtual void BoundingBox(Point& bottomLeft, Point& topRight) const;
        virtual bool IsEmpty() const;
        virtual Manipulator* CreateManipular() const;
};

void TextShape::BoundingBox(Point& bottomLeft, Point& topRight) const {
    Coord bottom, left, width, height;
    
    GetOrigin(bottom, left);
    GetExtend(width, height);
    
    bottomLeft = Point(bottom, left);
    topRight = Point(bottom+height, left+width);
}

bool TextShape::IsEmpty() const {
    return TextView::IsEmpty();
}

Manipulator* TextShape::CreateManipulator() const {
    return new TextManipulator(this);
}

class TextShape: public Shape {
    public:
        TextShape(TextView*);
        
        virtual void BoundingBox(Point& bottomLeft, Point& topRight) const;
        virtual bool IsEmpty() const;
        virtual Manipulator* CreateManipulator() const;
    private:
        TextView* _text;
};

Manipulator* TextShape::CreateManipulator() const {
    return new TextManipulator(this);
}

TextShape::TextShape(TextView* t) {
    _text = t;
}

void TextShape::BoundingBox(Point& bottomLeft, Point& topRight) const {
    Coord bottom, left, width, height;
    
    _text->GetOrigin(bottom, left);
    _text->GetExtent(width, height);
    
    bottomLeft = Point(bottom, left);
    topRight = Point(bottom+height, left+width);
}

bool TextShape::IsEmpty() const {
    return _text->IsEmpty();
}
```

## Facade (FA)

L'**intento** è fornire un'interfaccia unificata a un insieme di interfacce in un sottosistema. *Facade* definisce un'interfaccia di livello superiore che semplifica l'utilizzo del sottosistema.

### Motivazione

**La strutturazione di un sistema in sottosistemi aiuta a ridurre la complessità.**
Un obiettivo di progettazione comune è ridurre al minimo la comunicazione e le dipendenze tra i sottosistemi e introdurre un oggetto di facciata che fornisca un'unica interfaccia semplificata a un sottosistema.

![[FAmotivazione.png]]

Considera uno strumento di sviluppo in cui le applicazioni possono accedere al sottosistema del compilatore: *Scanner*, *Parser*, *ProgramNode*, *BytecodeStream* e *ProgramNodeBuilder*.

Alla maggior parte dei clienti di un compilatore generalmente non interessano i dettagli: vogliono semplicemente compilare del codice e non hanno bisogno delle interfacce potenti ma di basso livello.

La soluzione è fornire un'interfaccia unificata di livello superiore in grado di proteggere i client da queste classi: una classe *Compiler*. La classe *Compiler* funge da *facciata*!

![[FAesempio.png]]

### Applicabilità

Usa lo schema Facade quando:
- si desidera fornire un'interfaccia semplice a un sottosistema complesso. Questo rende il sottosistema più riutilizzabile e più facile da personalizzare (l'FA nasconde i refactoring) ma diventa anche più difficile da usare per i client che non hanno bisogno di personalizzare;
- si desidera fornire una semplice visualizzazione predefinita del sottosistema che sia sufficientemente buona per la maggior parte dei client. Solo i clienti che necessitano di maggiore personalizzazione dovranno guardare oltre la facciata;
- ci sono molte dipendenze tra i client e le classi di implementazione. FA disaccoppia il sottosistema dai client e da altri sottosistemi;
- vuoi sovrapporre i tuoi sottosistemi.

### Struttura

![[FAstruttura.png]]

- **Facade (Compiler):** sa quali classi del sottosistema sono responsabili di una richiesta e delega le richieste del client agli oggetti del sottosistema appropriati;
- **Subsystem classes (Scanner, Parser, ProgramNode, etc.):** implementare la funzionalità del sottosistema, gestire il lavoro assegnato dall'oggetto Facade e non conoscere la facciata.

### Collaborazione

I client comunicano con il sottosistema inviando richieste a *Facade*, che le inoltra agli oggetti del sottosistema appropriati come gli oggetti del sottosistema eseguono il lavoro effettivo oppure la facciata traduce la sua interfaccia in interfacce di sottosistema.

I client che utilizzano la facciata non devono accedere direttamente ai suoi oggetti del sottosistema.

### Conseguenze

- **Protegge i client dai componenti del sottosistema**;
- **Riduce il numero di oggetti di cui si occupano i client**, rendendo il sottosistema più facile da usare;
- **Promuove un accoppiamento debole tra il sottosistema e i suoi clienti:** l'accoppiamento debole consente di variare i componenti del sottosistema senza influire sui suoi client;
- **Aiuta a stratificare un sistema e le dipendenze tra gli oggetti**;
- **Può eliminare dipendenze complesse o circolari**;
- **Riduce le dipendenze di compilazione:** ricompilazione ridotta al minimo in caso di modifica del sottosistema e semplificazione dei sistemi di porting su altre piattaforme;
- **Non impedisce alle applicazioni di utilizzare le classi del sottosistema se necessario:** puoi scegliere tra facilità d'uso e generalità.

### Implementazione

Bisogna ridurre l'accoppiamento client-sottosistema rendendo *Facade* una classe astratta:

- avere sottoclassi concrete per diverse implementazioni di un sottosistema;
- i client interagiscono con il sottosistema tramite la classe *Facade* astratta (i client non conoscono l'implementazione del sottosistema).

Bisogna ridurre l'accoppiamento client-sottosistema configurando un oggetto FA con diversi oggetti del sottosistema: si personalizza semplicemente sostituendo uno o più dei suoi oggetti del sottosistema.

Dettagli su classi di sottosistemi pubblici e privati:

- sia i sottosistemi che le classi hanno interfacce e incapsulano qualcosa;
- l'interfaccia pubblica (classi a cui tutti i clienti possono accedere) e privata (classi per estensori di sottosistemi) di un sottosistema;
- la FA fa parte dell'interfaccia pubblica, ovviamente, ma non è l'unica parte;
- ci sono lingue che non ti permettono di nascondere classi di sottosistemi private

### Esempio del compilatore

```cpp
class Scanner{
    public:
        Scanner(istream&);
        virtual ~Scanner();
        
        virtual Token& Scan();
    private:
        istream& _inputStream;
}

class Parser {
    public:
        Parser();
        virtual ~Parser();
        
        virtual void Parse(Scanner&, ProgramNodeBuilder&);
}

class CodeGenerator {
    public:
        virtual void Visit(StatementNode*);
        virtual void Visit(ExpressionNode*);
        //...
    protected:
        CodeGenerator(BytecodeStream&);
        BytecodeStream& _output;
}

// ...and more

class Compiler {
    public:
        Compiler();
        
        virtual void Compile(istream&, BytecodeStream&);
};

void Compiler::Compile (istream& input, BytecodeStream& output) {
    Scanner scanner(input);
    ProgramNodeBuilder builder;
    Parser parser;
    
    parser.Parse(scanner, builder);
    
    RISCCodeGenerator generator(output);
    ProgramNode* parseTree = builder.GetRootNode();
    parseTree->Traverse(generator);
}
```

## Bridge (BR)

L'**intento** è disaccoppiare un'astrazione dalla sua implementazione in modo che i due possano variare indipendentemente. È conosciuto anche come *handle/body*!

**Non stiamo parlando di classe astratte con astrazione.** Una cosa è l'astrazione (che sono la modellazione astratta di un oggetto, parliamo di classe concreta) e un'altra le classi astratte. Però, una persona puà modellare in astratto senza avere delle classi astratte.

### Motivazione

Quando un'astrazione può avere una delle diverse implementazioni possibili, il modo normale per adattarle è utilizzare l'ereditarietà: una classe astratta definisce l'interfaccia per l'astrazione, le sottoclassi concrete lo implementano in modi diversi e non è abbastanza flessibile.

**Consideriamo l'implementazione di un'astrazione Window portatile in un toolkit dell'interfaccia utente:** richiediamo che possa supportare sia il sistema *X Window* che il *Presentation Manager (PM)* di IBM. Questo può essere risolto usando l'**ereditarietà** e la **composizione dell'oggetto**.

Se usiamo l'**ereditarietà** definiamo una classe astratta *Window* e le sottoclassi *XWindow* e *PMWindow*.

Ma questo approccio ha due inconvenienti:

- è scomodo estendere l'astrazione di *Window* per coprire diversi tipi di finestre o nuove piattaforme;
- rende il codice client dipendente dalla piattaforma: ogni volta che un client crea una finestra, crea un'istanza di una classe concreta che ha un'implementazione specifica.

Quella di seguito è una struttura di esempio blanda:

![[BResempioFacile.png]]

Quello di seguito, invece, è la struttura precedente ma ben sviluppata:

![[BResempio.png]]

### Applicabilità

Usa lo schema Bridge quando:

- vuoi evitare un legame permanente tra un'astrazione e la sua implementazione;
- l'implementazione deve essere selezionata o cambiata in fase di esecuzione;
- sia le astrazioni che le loro implementazioni dovrebbero essere estendibili mediante sottoclassi;
- le modifiche nell'implementazione non dovrebbero richiedere la ricompilazione dell'astrazione;
- *(in C++)* si vuole nascondere completamente ai client l'implementazione di un'astrazione;
- hai una proliferazione di classi come mostrato in precedenza nel primo diagramma della motivazione;
- vuoi condividere un'implementazione tra più oggetti e questo fatto dovrebbe essere nascosto al client.

### Struttura

![[BRstruttura.png]]

- **Abstraction (Window):** definisce l'interfaccia dell'astrazione e mantiene un riferimento a un *Implementor*;
- **RefinedAbstraction (IconWindow):** estende l'interfaccia definita da *Abstraction*;
- **Implementor (WindowImp):** definisce l'interfaccia per le classi di implementazione: *Implementor* e *Abstraction* possono avere un'interfaccia diversa (per il primo *primitivo*, per il secondo *livello superiore*);
- **ConcreteImplementor (XWindowImp, ...):** implementa l'interfaccia *Implementor* e ne definisce l'implementazione concreta.

### Collaborazione

L'astrazione inoltra le richieste del client al relativo oggetto *Implementor*.

### Conseguenze

Nell'**interfaccia e implementazione del disaccoppiamento**, un'implementazione non è legata in modo permanente a un'interfaccia. L'implementazione di un'astrazione può essere configurata/modificata in fase di esecuzione ed elimina le dipendenze in fase di compilazione dall'implementazione: essenziale quando è necessario garantire la compatibilità binaria tra diverse versioni di una libreria di classi. Infine, incoraggia la stratificazione che può portare a un sistema meglio strutturato.

Abbiamo un'**estensibilità migliorata**: infatti, è possibile estendere le gerarchie di *Abstraction* e *Implementor* in modo indipendente.

**Nasconde i dettagli di implementazione dai client:** infatti, è possibile proteggere i client dai dettagli di implementazione, come la condivisione degli oggetti dell'implementatore e il relativo meccanismo di conteggio dei riferimenti (se presente).

### Implementazione

**Utilizziamo solo un *Implementor*** cioè in un caso degenerato.
In situazioni in cui c'è una sola implementazione, non è necessaria la creazione di una classe *Implementor* astratta.

Procediamo con la **creazione dell'oggetto *Implementor* corretto**:

- istanziare uno nel suo costruttore;
- scegliere inizialmente un'implementazione predefinita e modificarla in seguito in base all'utilizzo;
- delegare la decisione ad un altro oggetto del tutto.

Utilizziamo l'**ereditarietà multipla** in C++ per combinare un'interfaccia con la sua implementazione (eredita pubblicamente da *Abstraction* e privatamente da *ConcreteImplementor*).

### Esempio della finestra per più sistemi

```cpp
class Window {
    public:
        Window(View* contents);
        
        // richieste gestite da window
        virtual void DrawContents();
        
        virtual void Open();
        virtual void Close();
        virtual void Iconify();
        virtual void Deiconify();
        
        // richieste inoltrate all'implementazione
        virtual void SetOrigin(const Point& at);
        virtual void SetExtent(const Point& extent);
        virtual void Raise();
        virtual void Lower();
        
        virtual void DrawLine(const Point&, const Point&);
        virtual void DrawRect(const Point& const Point&);
        virtual void DrawPolygon(const Point[], int n);
        virtual void DrawText(const char*, const Point&);
    protected:
        WindowImp* GetWindowImp();
        View* GetView();
    private:
        WindowsImp* _imp;
        View* _contents; // il contenuto della finestra
};

class WindowImp {
    public:
        virtual void ImpTop() = 0;
        virtual void ImpBottom() = 0;
        virtual void ImpSetExtend(const Point&) = 0;
        virtual void ImpSetOrigin(const Point&) = 0;
        
        virtual void DeviceRect(Coord, Coord, Coord, Coord) = 0;
        virtual void DeviceText(const char*, Coord, Coord) = 0;
        virtual void DeviceBitmap(const char*, Coord, Coord) = 0;
        // molte piu' funzioni per disegnare sulla finestra...
    protected:
        WindowsImp();
};

class ApplicationWindow: public Window {
    public:
        //...
        virtual void DrawContents();
};


void ApplicationWindow::DrawContents() {
    GetView()->DrawOn(this);
}

class IconWindow: public Window {
    public:
        //...
        virtual void DrawContents();
    private:
        const char* _bitmapName;
};

void IconWindow::DrawContents() {
    WindowImp* imp = GetWindowImp();
    if(imp != 0)
        imp->DeviceBitmap(_bitmapName, 0.0, 0.0);
}

void Window::DrawRect(const Point& p1, const Point& p2) {
    WindowImp* imp = GetWindowImp();
    imp->DeviceRect(p1.X(), p1.Y(), p2.X(), p2.Y());
}

class XWindowImp: public WindowImp {
    public:
        XWindowImp();
        
        virtual void DeviceRect(Coord, Coord, Coord, Coord);
        // resto dell'interfaccia pubblica...
    private:
        // molti stati specifici del sistema X Window, tra cui:
        Display* _dpy;
        Drawable _winid; // window id
        GC _gc; // window contenuti grafici
};

class PMWindowImp: public WindowImp {
    public:
        PMWindowImp();
        virtual void DeviceRect(Coord, Coord, Coord, Coord);
        
        // resto dell'interfaccia pubblica...
    private:
        // molti stati specifici del sistema PM Window, tra cui:
        HPS _hps;
};

void XWindowImp::DeviceRect(Coord x0, Coord y0, Coord x1, Coord y1) {
    int x = round(min(x0, x1));
    int y = round(min(y0, y1));
    int w = round(abs(x0 - x1));
    int h = round(abs(y0 - y1));
    XDrawRectangle(_dpy, _winid, _gc, x, y, w, h);
}

void PMWindowImp::DeviceRect(Coord x0, Coord y0, Coord x1, Coord y1) {
    Coord left = min(x0, x1);
    Coord right = max(x0, x1);
    Coord bottom = min(y0, y1);
    Coord top = max(y0, y1);
    
    PPOINTL point[4];
    
    point[0].x = left; point[0].y = top; 
    point[1].x = right; point[1].y = top; 
    point[2].x = right; point[2].y = bottom; 
    point[3].x = left; point[3].y = bottom; 
    
    if(
        (GpiBeginPath(_hps, 1L) == false) ||
        (GpiSetCurrentPosition(_hps, &point[3]) == false) ||
        (GpiPolyLine(_hps, 4L, point) == GPI_ERROR) ||
        (GpiEndPath(_hps) == false)
    ) {
        // segnala errore
    } else {
        GpiStrokePath(_hps, 1L, 0L);
    }
}

WindowImp* Window::GetWindowImp() {
    if(_imp == 0)
        _imp = WindowSystemFactory::Instance()->MakeWindowImp();
    return _imp;
}
```

## Composite (CO)

L'**intento** è comporre oggetti in strutture ad albero per rappresentare gerarchie parzialmente intere.
Composite consente ai clienti di trattare i singoli oggetti e le composizioni di oggetti in modo uniforme.

### Motivazione

**Implementare un sistema di editor di disegni che consenta agli utenti di creare diagrammi complessi con componenti semplici:** i componenti possono essere raggruppati in modo ricorsivo, il codice che usa queste classi deve trattare oggetti primitivi e contenitori in modo diverso e il dover distinguere questi oggetti rende l'applicazione più complessa.

![[COmotivazione.png]]

Il pattern Composite è una classe astratta che rappresenta sia le primitive che i loro contenitori.
Dichiara inoltre le operazioni che tutti gli oggetti compositi condividono, come le operazioni per l'accesso e la gestione dei suoi figli.

![[COmotivazioneSchema.png]]

### Applicabilità

Usa il pattern Composite quando:

- si desidera rappresentare gerarchie di oggetti parzialmente interi;
- vuoi che i clienti siano in grado di ignorare la differenza tra composizioni di oggetti e singoli oggetti;
- i client tratteranno tutti gli oggetti nella struttura composita in modo uniforme.

### Struttura

![[COstruttura.png]]

- **Component (Graphic):** dichiara l'interfaccia per gli oggetti nella composizione. Implementa il comportamento predefinito per l'interfaccia comune a tutte le classi, a seconda dei casi. Dichiara un'interfaccia per l'accesso e la gestione dei suoi componenti figlio. Opzionalmente, definisce un'interfaccia per accedere al genitore di un componente nella struttura ricorsiva e la implementa se appropriato;
- **Leaf (Rectangle, Line, Text, ecc.):** rappresenta gli oggetti foglia nella composizione. *Una foglia non ha figli.* Definisce il comportamento per gli oggetti primitivi;
- **Composite (Picture):** definisce il comportamento per i componenti che hanno figli. Memorizza i componenti figlio. Implementa le operazioni relative ai figli nell'interfaccia del componente;
- **Client:** manipola gli oggetti nella composizione tramite l'interfaccia del componente.

![[COstrutturaSchema.png]]

### Collaborazione

I client utilizzano l'interfaccia della classe *Component* per interagire con gli oggetti nella struttura composita: se il destinatario è una *Leaf*, la richiesta viene gestita direttamente; se il destinatario è un *Composite*, di solito inoltra le richieste ai suoi componenti figlio, eventualmente eseguendo operazioni aggiuntive prima e/o dopo l'inoltro.

### Conseguenze

- **Definisce gerarchie di classi costituite da oggetti primitivi e oggetti compositi:** gli oggetti primitivi possono essere composti in oggetti più complessi e così via in modo ricorsivo. Laddove il codice client si aspetta un oggetto primitivo, può anche accettare un oggetto composito;
- **Rende semplice il client:** i client possono trattare strutture composite e singoli oggetti in modo uniforme. Questo semplifica il codice client, perché evita di dover scrivere funzioni in stile tag e case-statement;
- **Semplifica l'aggiunta di nuovi tipi di componenti:** le sottoclassi *Composite* o *Leaf* appena definite funzionano automaticamente con le strutture esistenti. I client non devono essere modificati per le nuove classi di componenti;
- **MA può rendere il tuo design eccessivamente generale:** rende più difficile restringere i componenti di un composito. Non puoi fare affidamento sul sistema dei tipi per far rispettare quei vincoli per te.

### Implementazione
Abbiamo vari tipi di implementazione:
1. **Riferimenti genitori espliciti:** 
    - *può semplificare l'attraversamento e la gestione di una struttura composita:* semplifica lo spostamento della struttura e l'eliminazione di un componente;
    - *definire il riferimento padre nel componente:* le classi *Leaf* e *Composite* possono ereditare il riferimento e le operazioni;
    - *è essenziale mantenere coerente la struttura:* cambiare il genitore di un componente solo quando viene aggiunto o rimosso da un composto.
2. **Condivisione di componenti:** 
    - *per ridurre i requisiti di archiviazione*;
    - ma quando un componente non può avere più di un genitore, la condivisione dei componenti diventa difficile (i figli potrebbero immagazzinare più genitori ma ciò può portare ad ambiguità).
3. **Massimizzare l'interfaccia del componente:** 
    - *per rendere i clienti ignari delle specifiche classi *Leaf* o *Composite* che stanno utilizzando:* la classe *Component* dovrebbe definire quante più operazioni comuni possibili. La classe *Component* di solito fornisce implementazioni predefinite per queste operazioni e le sottoclassi *Leaf* e *Composite* le sovrascriveranno;
    - *tuttavia, questo obiettivo a volte entrerà in conflitto con il principio della progettazione della gerarchia di classi che dice che una classe dovrebbe definire solo operazioni significative per le sue sottoclassi*.
4. **Dichiarazione delle operazioni di gestione del figlio:** la classe *Composite* (la radice della gerarchia) implementa le operazioni Aggiungi e Rimuovi ma è corretto? Sicurezza o trasparenza?
5. **Ordinamento dei figli:** molti disegni specificano un ordinamento sui figli di *Composite* (ad es. analizzare alberi). Il pattern **Iterator** può guidarti in questo;
6. **Memorizzazione nella cache per migliorare le prestazioni:** *se hai bisogno di attraversare o cercare frequentemente le composizioni, la classe *Composite* può memorizzare nella cache l'attraversamento o cercare informazioni sui suoi figli.* Il *Composite* può memorizzare nella cache i risultati effettivi o solo le informazioni che gli consentono di cortocircuitare l'attraversamento o la ricerca;

**Chi dovrebbe eliminare i componenti?** Nelle lingue senza garbage collection, di solito è meglio rendere un *Composite* responsabile dell'eliminazione dei suoi figli quando viene distrutto.
Un'eccezione a questa regola è quando gli oggetti *Leaf* sono immutabili e quindi possono essere condivisi.

**Qual è la migliore struttura dati per l'archiviazione dei componenti?** Si può utilizzare una varietà di strutture di dati (ad esempio elenchi collegati, alberi, array e tabelle hash). La scelta della struttura dei dati dipende (come sempre) dall'efficienza.

### Esempio del set-up del computer

Apparecchiature come computer e componenti stereo sono spesso organizzati in gerarchie parziali o di contenimento.

```cpp
class Equipment {
    public:
        virtual ~Eqipment();
        
        const char* Name() { return _name; }
        
        virtual Watt Power();
        virtual Currency NetPrice();
        virtual Currency DiscountPrice();
        
        virtual void Add(Equipment*);
        virtual void Remove(Equipment*);
        virtual Iterator* CreateIterator();
    protected:
        Equipment(const char*);
    private:
        const char* _name;
};

claa FloppyDisk: public Equipment {
    public:
        FloppyDisk(const char*);
        virtual ~FloppyDisk(); 
        
        virtual Watt Power();
        virtual Currency NetPrice();
        virtual Currency DiscountPrice();
};

class CompositeEquipment: public Equipment {
    public:
        virtual ~CompositeEqupment();
        
        virtual Watt Power();
        virtual Currency NetPrice();
        virtual Currency DiscountPrice();
        
        virtual void Add(Equipment*);
        virtual void Remove(Equipment*);
        virtual Iterator* CreateIterator();
    protected:
        CompositeEquipment(const char*);
    private:
        List _equipment;
};

Currency CompositeEquipment::NetPrice() {
    Iterator* i = CreateIterator();
    Currency total = 0;
    
    for(i->First(); !i->IsDone(); i->Next()) {
        total += i->CurrentItem()->NetPrice();
    }
    delete i;
    return total;
}

class Chassis: public CompositeEquipment {
    public:
        Chassis(const char*);
        virtual ~Chassis();
        
        virtual Watt Power();
        virtual Currency NetPrice();
        virtual Currency DiscountPrice();
};

//...

Cabinet* cabinet = new Cabinet("PC Cabinet");
Chassis* chassis = new Chassis("PC Chassis");

cabinet->Add(chassis);

Bus* bus = new Bus("MCA Bus");
bus->Add(new Card("16Mbs Token Ring"));

chassis->Add(bus);
chassis->Add(new FloppyDisk("3.5in Floppy"));

cout<<"The net price is "<<chassis->NetPrice()<<endl;
```

## Decorator (DE)

L'**intento** è associare responsabilità aggiuntive a un oggetto in modo dinamico e Decorator fornisce un'alternativa flessibile alla sottoclasse per estendere le funzionalità.

### Motivazione

**A volte vogliamo aggiungere responsabilità a singoli oggetti, non a un'intera classe:** un toolkit dell'interfaccia utente grafica dovrebbe consentire di aggiungere proprietà come bordi o comportamenti come lo scorrimento a qualsiasi componente dell'interfaccia utente.

**Un modo per aggiungere responsabilità è con l'ereditarietà:** ereditare un bordo da un'altra classe mette un bordo attorno a ogni istanza di sottoclasse.
Questo è *inflessibile*: la scelta del bordo è statica e un cliente non può controllare come e quando decorare il componente con un bordo.

**Un approccio più flessibile consiste nel racchiudere il componente in un altro oggetto che aggiunge il bordo:** l'oggetto che lo racchiude è chiamato *decoratore*.

**Il decoratore si conforma all'interfaccia del componente che decora:** è trasparente ai client del componente.

Il decoratore inoltra le richieste al componente e può eseguire azioni aggiuntive (come disegnare un bordo) prima o dopo l'inoltro.

La trasparenza consente di annidare i decoratori in modo ricorsivo: consentendo un numero illimitato di responsabilità aggiuntive.

![[DEmotivazione.png]]

DE consente ai decoratori di apparire ovunque un VisualComponent possa. I client generalmente non riescono a distinguere tra un componente decorato e uno non decorato, e quindi **i client non dipendono affatto dalla decorazione**.

![[DEesempio.png]]

### Applicabilità

Usiamo Decorator:

- per aggiungere responsabilità ai singoli oggetti in modo dinamico e trasparente, ovvero senza influenzare altri oggetti;
- per le responsabilità che possono essere revocate;
- quando l'estensione per sottoclasse non è pratica: a volte è possibile un gran numero di estensioni indipendenti e produrrebbe un'esplosione di sottoclassi per supportare ogni combinazione;
- oppure una definizione di classe potrebbe essere nascosta o altrimenti non disponibile per la sottoclasse.

### Struttura

![[DEstruttura.png]]

- **Component (VisualComponent):** definisce l'interfaccia per gli oggetti a cui possono essere aggiunte responsabilità in modo dinamico;
- **ConcreteComponent (TextView):** definisce un oggetto al quale possono essere attribuite ulteriori responsabilità;
- **Decorator:** mantiene un riferimento a un oggetto *Component* e definisce un'interfaccia conforme all'interfaccia di *Component*;
- **ConcreteDecorator (BorderDecorator, ...):** aggiunge responsabilità al componente.

### Collaborazione

*Decorator* inoltra le richieste al suo oggetto *Component* e (facoltativamente) esegue operazioni aggiuntive prima e dopo l'inoltro della richiesta.

### Conseguenze

I benefici sono:

- **Maggiore flessibilità rispetto all'ereditarietà statica:** le responsabilità possono essere aggiunte e rimosse in fase di esecuzione, l'ereditarietà richiede la creazione di una nuova classe per ogni responsabilità aggiuntiva e DE semplifica anche l'aggiunta di una proprietà due volte (ad esempio un doppio bordo);
- **Evita le classi ricche di funzionalità in alto nella gerarchia:** offre un approccio pay-as-you-go per aggiungere responsabilità e le classi semplici sono nidificate in modo incrementale rispetto a classi personalizzabili complesse.

I punti negativi sono:

- **Il decoratore e il suo componente non sono identici:** dal punto di vista dell'identità dell'oggetto, un componente decorato *non è identico* al componente stesso. Quindi non dovresti fare affidamento sull'identità dell'oggetto quando usi i decoratori.
- **Tanti piccoli oggetti:** il design si traduce spesso in sistemi composti da tanti piccoli oggetti che si somigliano tutti. Gli oggetti differiscono solo nel modo in cui sono interconnessi, non nella classe o nel valore delle variabili. Quindi, *è facile da personalizzare ma difficile da imparare ed eseguire il debug*.

### Implementazione

- **Conformità dell'interfaccia:** l'interfaccia di un oggetto decoratore deve essere conforme all'interfaccia del componente che decora. Le classi *ConcreteDecorator* devono quindi ereditare da una classe comune (almeno in C++);
- **Omettendo la classe *Decorator* astratto:** quando hai solo bisogno di aggiungere una responsabilità o quando hai a che fare con una gerarchia di classi esistente, unire la responsabilità di *Decorator* per l'inoltro delle richieste al componente in *ConcreteDecorator*;
- **Mantenere le classi *Component* leggere:** componenti e decoratori devono discendere da una classe *Component* comune per garantire un'interfaccia conforme.

### Esempio dei componenti grafici di una finestra

```cpp
class VisualComponent {
    public:
        VisualComponent();
        
        virtual void Draw();
        virtual void Resize();
        //...
};
//...
class Decorator: public VisualComponent {
    public:
        Decorator(VisualComponent*);
        
        virtual void Draw();
        virtual void Resize();
        //...
    private:
        VisualComponent* _component;
};

void Decorator::Draw() {
    _component->Draw();
}

void Decorator::Resize() {
    _component->Resize();
}
//...
class BorderDecorator: public Decorator {
    public:
        BorderDecorator(VisualComponent*, int borderWidth;
        
        virtual void Draw();
    protected:
        void DrawBorder(int);
    private:
        int _width;
};

void BorderDecorator::Draw() {
    Decorator::Draw();
    DrawBorder(_width);
}
//...
window->SetContents(new BorderDecorator(new ScrollDecorator(textView), 1));
```

## Flyweight (FL)

L'**intento** è usare la condivisione per supportare in modo efficiente un gran numero di oggetti a grana fine.

### Motivazione

Strutture di oggetti complesse possono semplificare la progettazione di un'applicazione, ma la implementazione ingenua può essere proibitiva.
Ad esempio, **consideriamo un editor di documenti che incorpora elementi come tabelle e figure:** la struttura dell'oggetto dell'applicazione potrebbe imitare la struttura fisica del documento.

![[FLmotivazione.png]]

Quello rappresentato sopra è un bel design, ma: potrebbe facilmente richiedere centinaia di migliaia di oggetti *char*, che consumeranno molta memoria e potrebbero comportare un sovraccarico di runtime inaccettabile!

Un **flyweight** è un oggetto condiviso che può essere utilizzato in più contesti contemporaneamente in cui un oggetto è *indipendente* in ogni contesto ed FL non può fare supposizioni sul contesto.

Possiamo avere uno stato **intrinseco** ed **estrinseco**:

- **Stato intrinseco:** memorizzato nel flyweight e informazioni indipendenti dal contesto del flyweight;
- **Stato estrinseco:** dipende e varia con il contesto del flyweight, non può essere condiviso e il cliente passa lo stato estrinseco al flyweight quando ne ha bisogno.

Facciamo un esempio, se facciamo come abbiamo rappresentato precedentemente:

![[FLesempio1.png]]

Utilizzando un FL per rappresentare i caratteri diventa:

![[FLesempio2.png]]

![[FLesempio.png]]

Un FL che rappresenta la lettera "a" memorizza solo il codice del carattere corrispondente; non ha bisogno di memorizzare la sua posizione o il carattere. Oggetti carattere meno diversi significa il numero totale di oggetti è sostanzialmente inferiore

### Applicabilità

Devono sussistere tutte le seguenti condizioni:

- un'applicazione utilizza un numero elevato di oggetti;
- i costi di stoccaggio sono elevati a causa dell'enorme quantità di oggetti;
- la maggior parte degli stati degli oggetti può essere resa estrinseca;
- molti gruppi di oggetti possono essere sostituiti da relativamente pochi oggetti condivisi una volta rimosso lo stato estrinseco;
- l'applicazione non dipende dall'identità dell'oggetto: gli oggetti FL possono essere condivisi e, quindi, i test di identità restituiranno *true* per oggetti concettualmente distinti.

### Struttura

![[FLstruttura.png]]

- **Flyweight:** dichiara un'interfaccia attraverso la quale i pesi mosca possono ricevere e agire sullo stato estrinseco;
- **ConcreteFlyweight (Character):** implementa l'interfaccia Flyweight e aggiunge spazio di archiviazione per lo stato intrinseco, se presente e deve essere condivisibile e indipendente dal contesto;
- **UnsharedConcreteFlyweight (Row, Column):** non tutte le sottoclassi Flyweight devono essere condivise: *FL abilita la condivisione, non la impone*;
- **FlyweightFactory:** crea e gestisce oggetti flyweight e garantisce che i flyweight siano condivisi correttamente: fornisce un'istanza esistente o ne crea una, se non esiste;
- **Client:** mantiene un riferimento ai pesi mosca e calcola o memorizza lo stato estrinseco dei pesi mosca.

![[FLstrutturaSpc.png]]

### Collaborazione

Un FL deve essere caratterizzato o da uno stato *intrinseco* o *estrinseco*:

- lo stato intrinseco è archiviato nell'oggetto *ConcreteFlyweight*;
- lo stato estrinseco è memorizzato o calcolato dagli oggetti *Client*;
- i *Client* passano questo stato al flyweight quando invocano le sue operazioni.

I clienti non devono creare un'istanza diretta di *ConcreteFlyweights*.
I clienti devono ottenere oggetti *ConcreteFlyweight* esclusivamente dalla *FlyweightFactory*.

### Conseguenze

- **FL può introdurre costi di runtime:** *(trasferimento, ricerca e/o calcolo dello stato estrinseco)* specialmente per ciò che era precedentemente memorizzato come intrinseco, tali costi sono compensati da risparmi di spazio che aumentano man mano che vengono condivisi più flyweight;
- **Fattori di risparmio di stoccaggio:** la riduzione del numero totale di istanze che deriva dalla condivisione, la quantità di stato intrinseco per oggetto e se lo stato estrinseco viene calcolato o archiviato;
- **Più flyweight vengono condivisi, maggiore è il risparmio di spazio di archiviazione**;
- **Il risparmio aumenta con la quantità di stato condiviso:** risparmi sullo spazio di archiviazione in due modi: la condivisione riduce il costo dello stato intrinseco e scambi lo stato estrinseco per il tempo di calcolo;
- **Il pattern FL è spesso combinato con il pattern *Composite* (ad es. per rappresentare strutture ad albero):** i nodi foglia flyweight non possono memorizzare un puntatore al loro genitore, il puntatore genitore viene passato al flyweight come parte del suo stato estrinseco e questo ha un impatto importante sul modo in cui gli oggetti nella gerarchia comunicano tra loro.

### Implementazione

- **Rimozione dello stato estrinseco:** l'applicabilità di FL è determinata dalla facilità con cui è possibile identificare lo stato estrinseco e rimuoverlo dagli oggetti condivisi. La rimozione dello stato estrinseco non aiuta se ci sono tanti tipi diversi di stato estrinseco quanti sono gli oggetti prima della condivisione;
- **Gestione degli oggetti condivisi:** gli oggetti sono condivisi significa che i client non dovrebbero istanziarli direttamente. *FlyweightFactory* consente ai clienti di individuare un particolare flyweight. La condivisione potrebbe implicare una qualche forma di conteggio dei riferimenti o garbage collection.

### Esempio della formattazione del documento

```cpp
class Glyph {
    public:
        virtual ~Glyph();
        
        virtual void Draw(Window*, GlyphContext&);
        
        virtual void SetFont(Font*, GlyphContext&);
        virtual Font* GetFont(GlyphContext&);
        
        virtual void First(GlyphContext&);
        virtual void Next(GlyphContext&);
        virtual bool IsDone(GlyphContext&);
        virtual Glyph* Currento(GlyphContext&);
        
        virtual void Insert(Glyph*, GlyphContext&);
        virtual void Remove(GlyphContext&);
    protected:
        Glyph();
};
//...
class Character: public Ghyph {
    public:
        Character(chat);
        
        virtual void Draw(Window*, GlyphContext&);
    private:
        char _charcode;
};

class GlyphContext {
    public:
        GlyphContext();
        virtual ~GLyphContext();
        
        virtual void Next(int step=1);
        virtual void Insert(int quality=1);
        
        virtual Font* GetFont();
        virtual void SetFont(Font*, int span=1);
    private:
        int _index;
        BTree* _fonts;
};
//...
const int NCHARCODES = 128;

class GlyphFactory {
    public:
        GlyphFactory();
        virtual ~GlyphFactory();
        
        virtual Character* CreateCharacter(char);
        virtual Row* CreateRow();
        virtual Column* CreateColumn();
    private:
        Character* _character[NCHARCODES];
};

GlyphFactory::GlyphFactory() {
    for(int i = 0; i < NCHARCODES; ++i)
        _character[i] = 0;
}
//...
Character* GlyphFactory::CreateCharacter(char c) {
    if(!_character[c]) 
        _character[c] = new Character(c);
    return _character[c];
}

Row* GlyphFactory::CreateRow() {
    return new Row;
}

Column* GlyphFactory::CreateColumn() {
    return new Column;
}
```

![[FLimpl.png]]

![[FLimplGrafo.png]]

## Proxy (PR)

L'**intento** è fornire un surrogato o un segnaposto per un altro oggetto per controllarne l'accesso. È conosciuto anche come *surrogate*!

### Motivazione

Uno dei motivi per controllare l'accesso a un oggetto è *posticipare l'intero costo della sua creazione* e inizializzazione fino a quando non sarà effettivamente necessario utilizzarlo.

**Considera un editor di documenti in grado di incorporare oggetti grafici in un documento:** alcuni oggetti grafici (ad es. immagini raster di grandi dimensioni) possono essere costosi da creare ma l'apertura di un documento dovrebbe essere veloce quindi dovremmo evitare di creare tutti gli oggetti costosi contemporaneamente all'apertura del documento (non tutti questi oggetti saranno visibili nel documento contemporaneamente).

*Dovremmo creare ogni oggetto costoso su richiesta o ogni volta che un'immagine diventa visibile? Ma cosa mettiamo nel documento al posto dell'immagine? E come nascondere questo fatto? (l'immagine è creata su richiesta)*

![[PRmotivazione1.png]]

Il proxy dell'immagine crea l'immagine reale solo quando l'editor del documento gli chiede di visualizzarsi richiamando la sua operazione di disegno.

![[PRmotivazione2.png]]

Il proxy memorizza anche la sua estensione, ovvero larghezza e altezza. L'estensione consente al proxy di rispondere alle richieste per la sua dimensione dal formattatore senza effettivamente istanziare l'immagine.

### Applicabilità

Il proxy è applicabile ogni volta che è necessario un riferimento più versatile o sofisticato a un oggetto rispetto a un semplice puntatore.

Situazioni di applicabilità comuni sono:

- Un **proxy remoto** fornisce un rappresentante locale per un oggetto in uno spazio di indirizzi diverso;
- Un **proxy virtuale** crea oggetti costosi su richiesta (es. ImageProxy);
- Un **proxy di protezione** controlla l'accesso all'oggetto originale (per oggetti che dovrebbero avere diritti di accesso diversi);
- Un **riferimento intelligente** è un sostituto di un semplice puntatore che esegue azioni aggiuntive quando si accede a un oggetto come per esempio: 
    - contare il numero di riferimenti all'oggetto reale in modo che possa essere liberato automaticamente quando non ci sono più riferimenti;
    - per caricare un oggetto persistente in memoria quando viene fatto riferimento per la prima volta;
    - per verificare che l'oggetto reale sia bloccato prima di accedervi per garantire che nessun altro oggetto possa modificarlo.

### Struttura

![[PRstruttura.png]]

- **Proxy (ImageProxy):** mantiene un riferimento che consente al proxy di accedere al soggetto reale. Il proxy può fare riferimento a un soggetto se le interfacce RealSubject e Subject sono le stesse. Fornisce un'interfaccia identica a quella del Soggetto in modo che un proxy possa sostituire il soggetto reale. Controlla l'accesso al soggetto reale e può essere responsabile della sua creazione e cancellazione;
- **Subject (Graphic):** definisce l'interfaccia comune per RealSubject e Proxy in modo che un Proxy possa essere utilizzato ovunque sia previsto un RealSubject;
- **RealSubject (Image):** definisce l'oggetto reale rappresentato dal proxy.

### Collaborazione

Il proxy inoltra le richieste a RealSubject quando appropriato, a seconda del tipo di proxy.

### Conseguenze

Il modello Proxy introduce un livello di indiretto quando si accede a un oggetto. Per esempio:

- un proxy remoto può nascondere il fatto che un oggetto risiede in uno spazio di indirizzi diverso;
- un proxy virtuale può eseguire ottimizzazioni come la creazione di un oggetto su richiesta;
- sia i proxy di protezione che i riferimenti intelligenti consentono ulteriori attività di manutenzione quando si accede a un oggetto.

Copy-on-write (creazione su richiesta):

- copiare un oggetto grande e complicato può essere un'operazione costosa;
- utilizzando un proxy per posticipare il processo di copiatura;
- richiede il conteggio dei riferimenti.

### Implementazione

**Overload dell'operatore di accesso ai membri in C++:** il C++ supporta l'overloading *operator$->$*, l'operatore di accesso ai membri che ti consente di eseguire ulteriori operazioni ogni volta che un oggetto viene dereferenziato. Questo può essere utile per implementare alcuni tipi di proxy.

**L'overload di "$->$" non è una buona soluzione per ogni tipo di proxy**, per esempio, se un proxy ha bisogno di sapere con precisione quale operazione viene chiamata. Considera l'esempio di immagine, l'immagine dovrebbe essere caricata quando viene chiamata l'operazione Draw e non ogni volta che viene fatto riferimento all'immagine.

**Il proxy non deve sempre conoscere il tipo di soggetto reale:** PR si occupa del suo argomento attraverso un'interfaccia astratta.

**Identificatori di oggetti indipendenti dallo spazio degli indirizzi:** il modo in cui un PR si riferisce al soggetto prima che venga istanziato.

### Esempio di editor di documenti con implementazione grafici

```cpp
class Graphic {
    public:
        virtual ~Graphic();
        
        virtual void Draw(const Point& at) = 0;
        virtual void HandleMouse(Event& event) = 0;
        
        virtual const Point& GetExtent() = 0;
        
        virtual void Load(istream& from) = 0;
        virtual void Save(ostream& to) = 0;
    protected:
        Graphic();
};
//...
class Image: public Graphic {
    public:
        Image(const char* file); // carica immagini da un fili
        virtual ~Image();
        virtual void Draw(const Point& at);
        virtual void HandleMouse(Event& event);
        
        virtual const Point& GetExtent();
        
        virtual void Load(istream& from);
        virtual void Save(ostream& to);
    private:
        //...
};
//...
class ImageProxy: public Graphic {
    public:
        ImageProxy(const char* imageFile);
        virtual ~ImageProxy();
        
        virtual void Draw(const Point& at);
        virtual void HandleMouse(Event& event);
        
        virtual const Point& GetExtent();
        
        virtual void Load(istream& from);
        virtual void Save(ostream& to);
    protected:
        Image* GetImage();
    private:
        Image* _image;
        Point _extent;
        char* _fileName;
};
//...
ImageProxy::ImageProxy(const char* fileName) {
    _fileName = strdup(fileName);
    _extent = Point::Zero;
    _image = 0;
}

Image* ImageProxy::GetImage() {
    if(_image == 0) 
        _image = new Image(_fileName);
    return _image;
}
//...
const Point& ImageProxy::GetExtent() {
    if(_extent == Point::Zero)
        _extent = GetImage()->GetExtent();
    return _extent;
}

void ImageProxy::Draw(const Point& at) {
    GetImage()->Draw(at);
}

void ImageProxy::HandleMouse(Event& event) {
    GetImage()->HandleMouse(event);
}
//...
void ImageProxy::Save(ostream& to) {
    to<<_extent<<_fileName;
}

void ImageProxy::Load(istream& from) {
    from>>_extent>>_fileName;
}
//...
class TextDocument {
    public:
        TextDocument();
        
        void Insert(Graphic*);
        //...
};

TextDocument* text = new TextDocument;
//...
text->Insert(new ImageProxy("anImageFileName"));
```

## Conclusioni

Ci sono molte somiglianze tra i modelli strutturali probabilmente perché i modelli strutturali si basano sullo stesso piccolo insieme di meccanismi del linguaggio per strutturare codice e oggetti.

Confrontiamo e contrapponiamo gruppi di modelli strutturali:

- **Adapter VS Bridge:** la differenza fondamentale è nei loro intenti. *Adapter* si concentra sulla risoluzione delle incompatibilità tra due interfacce esistenti. *Bridge* collega un'astrazione e le sue (potenzialmente numerose) implementazioni. Con *Adapter* è necessario quando due classi incompatibili devono lavorare insieme, per evitare la replica del codice. Con *Bridge* è necessario quando un'astrazione deve avere diverse implementazioni ed entrambe possono evolversi indipendentemente. *Adapter* fa funzionare le cose dopo che sono state progettate. *Bridge* li fa funzionare prima che lo siano.
- **Adapter VS Facade:** potresti pensare a un *Facade* come un annuncio a un insieme di altri oggetti ma *Facade* definisce una nuova interfaccia, mentre un *Adapter* riutilizza una vecchia interfaccia. Ricorda che un *Adapter* fa lavorare insieme due interfacce esistenti invece di definirne una completamente nuova.
- **Composite VS Decorator VS Proxy:** *Composite* e *Decorator* sono simili. Entrambi si basano sulla composizione ricorsiva. *Decorator* è progettato per consentire di aggiungere responsabilità agli oggetti senza sottoclassi. *Composite* si concentra sulla strutturazione delle classi in modo che molti oggetti correlati possano essere trattati in modo uniforme e più oggetti possano essere trattati come uno solo. Questi intenti sono distinti ma complementari. *Composite* e *Decorator* sono spesso usati insiemi. *Decorator* e *Proxy* sono molto simili: entrambi descrivono come fornire un livello di indiretto a un oggetto e le implementazioni mantengono un riferimento a un altro oggetto a cui inoltrano le richieste. Tuttavia, ancora una volta, sono destinati a scopi diversi. Come *Decorator*, *Proxy* compone un oggetto e fornisce un'interfaccia identica ai client. A differenza di *Decorator*, *Proxy* non riguarda l'attaccamento o il distacco di proprietà in modo dinamico e non è progettato per la composizione ricorsiva. L'intento di *Proxy* è quello di fornire un sostituto per un soggetto quando è scomodo o indesiderabile accedere direttamente al soggetto. In *Decorator*, il componente fornisce solo una parte della funzionalità. Nella composizione ricorsiva *Decorator* una parte essenziale mentre *Proxy* si concentra su una relazione statica (proxy-soggetto).
