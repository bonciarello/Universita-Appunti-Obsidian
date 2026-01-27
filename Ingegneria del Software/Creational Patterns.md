Abbiamo 5 pattern:

- **Factory Method** (FM, Metodo Fabbrica)
- **Abstract Factory** (AF, Fabbrica Astratta)
- **Singleton** (SI, Singoletto)
- **Prototype** (PR, Prototipo)
- **Builder** (BU, Costruttore)

## Factory Method (FM)

L'**intento** è definire un'interfaccia per la creazione di un oggetto, ma lasciare che le sottoclassi decidano quale classe istanziare. Factory Method consente a una classe di rinviare l'istanza alle sottoclassi.
È conosciuto anche come *costruttore virtuale*!

### Motivazione

**Considera un framework per applicazioni in grado di presentare più documenti allo utente.**
Per creare un'applicazione di disegno, ad esempio, definiamo le classi *DrawingApplication* e *DrawingDocument*.
La classe *Application* è responsabile della gestione dei documenti.
La classe *Application* non può prevedere la sottoclasse di *Document* da istanziare.
Le sottoclassi dell'applicazione ridefiniscono un'operazione *CreateDocument* astratta su *Application* per restituire la sottoclasse *Document* appropriata.

![[FMcreateDocument.png]]

L'oggetto *Application* è generico e viene detto metodo fabbrica, in questo caso serve per creare il documento. Il metodo fabbrica (messo in corsivo) è virtuale e si aspetta di essere sovrascritto nelle sottoclassi. Abbiamo una situazione in cui una classe non può anticipare la creazione. Questo codice è tutto generico. 

### Applicabilità

Usa un FM quando:

- una classe non può anticipare la classe di oggetti che deve creare;
- una classe vuole che le sue sottoclassi specifichino gli oggetti che crea;
- le classi delegano la responsabilità a una delle diverse sottoclassi di supporto e si desidera localizzare la conoscenza di quale sottoclasse di supporto è il delegato.

### Struttura

![[FMstruttura.png]]

- **Product (Document):** l'interfaccia degli oggetti creati dal metodo factory;
- **ConcreteProduct (MyDocument):** implementa l'interfaccia del *Product*;
- **Creator (Application):** dichiara il metodo di fabbrica (restituisce un *Product*), può definire un'implementazione predefinita dell'FM e può chiamare il metodo di fabbrica per creare un *Product*;
- **ConcreteCreator (MyApplication):** esegue l'override del metodo factory per restituire un'istanza di *ConcreteProduct*.

### Collaborazione

*Creator* si basa sulle sue sottoclassi per definire il metodo factory in modo che restituisca un'istanza del *ConcreteProduct* appropriato.

### Conseguenze

FM elimina la necessità di associare classi specifiche dell'applicazione nel codice: il codice si occupa solo dell'interfaccia del prodotto.

I client potrebbero dover creare una sottoclasse *Creator* solo per creare un particolare oggetto *ConcreteProduct*: va bene quando il client deve comunque sottoclassare la classe *Creator*, altrimenti è uno svantaggio!

FM fornisce hook per le sottoclassi:
- la creazione di oggetti all'interno di una classe con un FM è sempre più flessibile rispetto alla creazione diretta di un oggetto;
- FM fornisce alle sottoclassi un hook per fornire una versione estesa di un oggetto.

FM collega gerarchie di classi parallele: le gerarchie di classi parallele risultano quando una classe delega alcune delle sue responsabilità a una classe separata.

Un altro esempio:

![[FMesempio.png]]

### Implementazione

Abbiamo due possibili implementazioni:
- Creator è una classe astratta e non fornisce un'implementazione per gli FM che fornisce;
- Creator è una classe concreta e fornisce un'implementazione predefinita per gli FM che fornisce.

I metodi di fabbrica parametrizzati consentono al metodo fabbrica di creare più tipi di prodotti. Il metodo factory accetta un parametro che identifica il tipo di oggetto da creare: potrebbe richiedere il downcasting.

È buona norma utilizzare convenzioni di denominazione che chiariscano che stai utilizzando metodi di fabbrica.

```cpp
class Creator {
    public:
        virtual Product* CreateProduct() = 0;
};

template <class TheProduct>
class StandardCreator: public Creator {
    public:
        virtual Product* CreateProduct();
};

template <class TheProduct>
Product* StandardCreator<TheProduct>::CreateProduct() {
    return new TheProduct;
};
```

Utilizzo di modelli per evitare sottoclassi.

```cpp
class MyProduct: public Product {
    public:
        MyProduct();
        // ...
};

StandardCreator<MyProduct> myCreator;
```

### Esempio del labirinto

Abbiamo il gioco del labirinto, immaginiamo di dover costruire degli oggetti che rappresentano stanze, muri e porte e immaginiamo di muovere un avatar per trovare l'uscita. Dobbiamo creare una mappa e la rappresentazione è quella di seguito:

![[FMmaze.png]]

Utilizziamo il metodo fabbrica perchè se è vero che ci sono degli oggetti generici, successivamente vorremo poter aggiungere dei labirinti con delle caratteristiche speciali.

Ecco la classe che fa da creatore:

```cpp
class MazeGame {
    public:
        Maze* CreateMaze();
        
        // metodi fabbrica
        virtual Maze* MakeMaze() const { return new Maze; }
        virtual Room* MakeRoom(int n) const { return new Room(n); }
        virtual Wall* MaleWall() const { return new Wall; }
        virtual Door* MakeDoor(Room* r1, Room* r2) const { return new Door(r1, r2); }
};
```

Ecco il metodo che crea il labirinto:

```cpp
Maze* MazeGame::CreateMaze() {
    Maze* aMaze = MakeMaze();
    
    Room* r1 = MakeRoom(1);
    Room* r2 = MakeRoom(2);
    Door* theDoor = MakeDoor(r1, r2);
    
    aMaze->AddRoom(r1);
    aMaze->AddRoom(r2);
    
    r1->SetSide(North, MakeWall());
    r1->SetSide(East, theDoor);
    r1->SetSide(South, MakeWall());
    r1->SetSide(West, MakeWall());
    
    r2->SetSide(North, MakeWall());
    r2->SetSide(East, MakeWall());
    r2->SetSide(South, MakeWall());
    r2->SetSide(West, theDoor);
    
    return aMaze;
}
```

Ecco i metodi che servono per creare il labirinto con funzioni speciali:

```cpp
class BombedMazeGame: public MazeGame {
    public:
        BombedMazeGame();
        
        virtual Wall* MakeWall() const { return new BombedWall; }
        virtual Room* MakeRoom(int n) const { return new RoomWithABomb(n); }
};

class EnchantedMazeGame: public MazeGame {
    public:
        EnchantedMazeGame();
        
        virtual Room* MakeRoom(int n) const { return new EnchantedRoom(n, CastSpell()); }
        virtual Door* MakeDoor(Room* r1, Room* r2) const { return new DoorNeedingSpell(r1, r2); }
    protected:
        Spell* CastSpell() const;
};
```

## Abstract Factory (AF)

L'**intento** è fornire un'interfaccia per creare famiglie di oggetti correlati o dipendenti senza specificare le classi concrete. È conosciuto anche come *kit*.

### Motivazione

**Considera un toolkit dell'interfaccia utente che supporti più standard di look-and-feel, come Motif e Presentation Manager.**
Aspetto e sensazioni diversi definiscono aspetti e comportamenti diversi per i "widget" dell'interfaccia utente come barre di scorrimento, finestre e pulsanti.
Per essere portabile attraverso gli standard di aspetto, un'applicazione non dovrebbe codificare i suoi widget per un aspetto particolare.

![[AFesempio.png]]

### Applicabilità

Usa una AF quando:
- un sistema dovrebbe essere indipendente da come i suoi prodotti sono creati, composti e rappresentati.
- un sistema dovrebbe essere configurato con una delle molteplici famiglie di prodotti.
- una famiglia di oggetti prodotto correlati è progettata per essere utilizzata insieme ed è necessario applicare questo vincolo.
- vuoi fornire una libreria di classi di prodotti e vuoi rivelare solo le loro interfacce, non le loro implementazioni.

### Struttura

![[AFstruttura.png]]

- **AbstractFactory (WidgetFactory):** dichiara un'interfaccia per la creazione di prodotti astratti;
- **ConcreteFactory (MotifWidgetFactory, ...):** implementa le operazioni creando prodotti concreti;
- **AbstractProduct (Window, ScrollBar):** dichiara un'interfaccia per un tipo di oggetto prodotto;
- **ConcreteProduct (MotifWindow, ...):** definisce un oggetto prodotto che deve essere creato dall'AF e implementa l'interfaccia *AbstractProduct*;
- **Client:** utilizza solo le interfacce dichiarate da *AbstractFactory* e *AbstractProduct*;

### Collaborazione

Una singola istanza di una classe *ConcreteFactory* viene creata in fase di esecuzione:
- questa *ConcreteFactory* crea oggetti prodotto con una particolare implementazione;
- per creare oggetti di prodotto diversi, i clienti dovrebbero utilizzare una *ConcreteFactory* diversa.

*AbstractFactory* rinvia la creazione di oggetti prodotto alla sua sottoclasse *ConcreteFactory*.

### Conseguenze

**Isola le classi concrete:** il pattern Abstract Factory ti aiuta a controllare le classi di oggetti, i client manipolano le istanze attraverso le loro interfacce astratte e i nomi delle classi di prodotto non compaiono nel codice cliente.

**Semplifica lo scambio di famiglie di prodotti:** la classe di una concrete factory appare solo una volta in un'applicazione:
- ciò semplifica la modifica della fabbrica di calcestruzzo utilizzata da un'applicazione;
- può utilizzare diverse configurazioni di prodotto semplicemente cambiando la concrete factory.

**Promuove la coerenza tra i prodotti:** un'applicazione utilizza oggetti di una sola famiglia alla volta.

**È difficile supportare nuovi tipi di prodotti:** estendere fabbriche astratte per produrre nuovi tipi di Prodotti non è facile. Il supporto di nuovi tipi di prodotti richiede l'estensione dell'interfaccia di fabbrica:
- comporta la modifica della classe AF e di tutte le sue sottoclassi;
- questo può essere (parzialmente) risolto.

### Implementazione

Factory come singleton: un'applicazione in genere richiede solo un'istanza di *ConcreteFactory* (Singleton).

Passiamo alla creazione dei prodotti:
- AF dichiara solo un'interfaccia per la creazione di prodotti;
- sta alle sottoclassi *ConcreteFactory* crearle effettivamente;
- implementa AF utilizzando il metodo della fabbrica;
- implementa AF utilizzando Prototype.

Passiamo alla definizione di fabbriche estensibili:

- AF solitamente definisce un'operazione diversa per ogni tipo di prodotto;
- un design più flessibile consiste nell'aggiungere un parametro alle operazioni che creano oggetti.
- più facile da usare in un linguaggio tipizzato dinamicamente come Smalltalk che in C++.

### Esempio del labirinto

Ecco la classe che fa da creatore:

```cpp
class MazeFactory {
    public:
        MazeFactory();
        
        virtual Maze* MakeMaze() const { return new Maze; }
        virtual Wall* MakeWall() const { return new Wall; }
        virtual Room* MakeRoom(int n) const { return new Room(n); }
        virtual Door* MakeDoor(Room* r1, Room* r2) const { return new Door(r1, r2); }
}
```

Ecco il metodo che crea il labirinto:

```cpp
Maze* MazeGame::CreateMaze(MazeFactory& factory) {
    Maze* aMaze = factory.MakeMaze();
    Room* r1 = factory.MakeRoom(1);
    Room* r2 = factory.MakeRoom(2);
    Door* aDoor = factory.MakeDoor(r1, r2);
    
    aMaze->AddRoom(r1);
    aMaze->AddRoom(r2);
    
    r1->SetSide(North, factory.MakeWall());
    r1->SetSide(East, aDoor);
    r1->SetSide(South, factory.MakeWall());
    r1->SetSide(West, factory.MakeWall());
    
    r2->SetSide(North, factory.MakeWall());
    r2->SetSide(East, factory.MakeWall());
    r2->SetSide(South, factory.MakeWall());
    r2->SetSide(West, aDoor);
}
```

Ecco i metodi che servono per creare il labirinto con funzioni speciali:

```cpp
class EnchantedMazeFactory: public MazeFactory {
    public:
        EnchantedMazeFactory();
        
        virtual Room* MakeRoom(int n) const { return new EnchantedRoom(n, CastSpell()); }
        virtual Door* MakeDoor(Room* r1, Room* r2) const { return new DoorNeedingSpell(r1, r2); }
    protected:
        Spell* CastSpell() const;
};

Wall* BombedMazeFactory::MakeWall() const {
    return new BombedWall;
}

Room* BombedMazeFactory::MakeRoom(int n) const {
    return new RoomWithABomb(n);
}
```

Quando andremo ad inserire tutto all'interno del *main*, il risultato sarà il seguente:

```cpp
MazeGame game;
BombedMazFactory factory;

game.CreateMaze(factory);
```

## Singleton (SI)

L'**intento** è assicurarsi che una classe abbia solo un'istanza e fornire un punto di accesso globale ad essa.

### Motivazione

**Si usa se è necessario avere esattamente un'istanza di una classe.** 
Una variabile globale rende accessibile un oggetto, ma non ti impedisce di istanziare più oggetti.

Rendi la classe stessa responsabile della sua unica istanza: la classe può garantire che nessun'altra istanza possa essere creata intercettando le richieste di creazione di nuovi oggetti, e può fornire un modo per accedere all'istanza.

### Applicabilità

Usa il modello Singleton quando:

- ci deve essere esattamente un'istanza di una classe e deve essere accessibile ai client da un punto di accesso noto;
- quando la sola istanza dovrebbe essere estensibile mediante sottoclassi e i client dovrebbero essere in grado di utilizzare un'istanza estesa senza modificare il codice.

### Struttura

![[SIstruttura.png]]

- **Singleton:** definisce un'operazione di istanza che consente ai client di accedere alla sua istanza univoca e l'istanza è un'operazione di classe (ad es. membro statico). *Può essere responsabile della creazione della propria istanza univoca.*

### Collaborazione

Il cliente accede al singleton solamente attraverso il metodo definito dalla classe stessa.

### Conseguenze

- **Accesso controllato alla sola istanza:** controllo rigoroso su come e quando il client accede;
- **Spazio dei nomi ridotto:** nessun inquinamento dello spazio dei nomi da parte di variabili globali;
- **Consente il perfezionamento delle operazioni e della rappresentazione:** la classe Singleton può essere sottoclasse e bisogna utilizzare l'istanza della classe necessaria in fase di esecuzione;
- **Consente un numero variabile di istanze**;
- **Più flessibile delle operazioni di classe:** le funzioni membro statiche in C++ non sono mai virtuali.

### Implementazione

**Garantire un'istanza univoca** cioè bisogna nascondere l'operazione che crea l'istanza dietro un'operazione di classe (costruttore privato + membro statico).

**Sottoclasse della classe singleton:** la variabile che fa riferimento all'istanza singleton deve essere inizializzata con un'istanza della sottoclasse. Può essere flessibile utilizzare un registro di singleton.

```cpp
class Singleton {
    public:
        static Singleton* Instance();
    protected:
        Singleton();
    private:
        static Singleton* _instance;
};

Singleton* Singleton::_instance = 0;

Singleton* Singleton::Instance() {
    if(_instance == 0)
        _instance = new Singleton;
    return _instance;
}
```

Un esempio di implementazione di Singleton:

```cpp
class Singleton {
    public:
        static void Register(const char* name, Singleton*);
        static Singleton* Instance();
    protected:
        static Singleton* Lookup(const char* name);
    private:
        static Singleton* _instance;
        static List<NameSingletonPair>* _registry;
};

Singleton* Singleton::Instance() {
    if(_instance == 0) {
        const char* singletonName = getenv("SINGLETON");
        // l'utente o l'ambiente lo fornisce all'avvio
        
        _instance = Lookup(singletonName);
        // la ricerca restituisce 0 se non esiste tale singleton
    }
    return _instance;
}

MySingleton::MySingleton() {
    //...
    Singleton::Register("MySingleton", this);
}

static MySingleton theSingleton;
```

### Esempio del labirinto

```cpp
class MazeFactory {
    public:
        static MazeFactory* Instance();
    protected:
        MazeFactory();
    private:
        static MazeFactory* _instance;
};

MazeFactory* MazeFactory::_instance = 0;

MazeFactory* MazeFactory::Instance() {
    if(_instance == 0)
        _instance = new MazeFactory;
    return _instance;
}
```

## Prototype (PR)

L'**intento** è specificare i tipi di oggetti da creare utilizzando un'istanza prototipo e creare nuovi oggetti copiando (clonando) questo prototipo.

### Motivazione

**Considera di creare un editor per gli spartiti musicali personalizzando un framework generale.** Le partiture vengono create aggiungendo nuovi oggetti che rappresentano note, pause e righi da una tavolozza. La creazione di sottoclassi da una classe Graphic astratta produce molte sottoclassi che differiscono solo per il tipo di oggetto musicale che istanziano.

![[PResempio.png]]

Fai in modo che GraphicTool crei un nuovo Graphic clonando un'istanza di una sottoclasse Graphic (il prototipo!).

### Applicabilità

Usa il PR quando:

- un sistema dovrebbe essere indipendente da come i suoi prodotti sono creati, composti e rappresentati;
- le classi da istanziare sono specificate in fase di esecuzione;
- le istanze hanno una delle poche combinazioni di stato diverse.

### Struttura

![[PRstruttura.png]]

- **Prototype (Graphic):** dichiara un'interfaccia per la clonazione stessa;
- **ConcretePrototype (Staff, WholeNote, HalfNote):** implementa un'operazione di clonazione stessa;
- **Client (GraphicTool):** crea un nuovo oggetto chiedendo a un prototipo di clonarsi.

### Collaborazione

Un client chiede al prototipo di clonarsi. 

### Conseguenze

- **PR simile ad AF e BU** infatti nasconde le classi del prodotto concreto al cliente e consente al cliente di lavorare con classi specifiche dell'applicazione senza modifiche;
- **Permette di aggiungere e rimuovere prodotti a tempo di esecuzione:** un po' più flessibile rispetto ad altri modelli di creazione, perché un cliente può installare e rimuovere prototipi in fase di esecuzione;
- **Definisce nuovi oggetti variando dei valori:** PR consente agli utenti di definire nuove "classi" senza programmazione;
- **Riduce la generazione di sottoclassi:** il pattern FM produce spesso delle gerarchie parallele tra *Creator* e *Product*;
- **Configurazione dinamica di un'applicazione con classi:** il pattern *Prototype* è la chiave per sfruttare funzionalità come la riflessione Java in un linguaggio come il C++.

### Implementazione

**Particolarmente utile con i linguaggi statici (C++)** in cui le classi non sono oggetti e in fase di esecuzione sono disponibili poche o nessuna informazione sul tipo.

Abbiamo vari problemi relativi inerenti alla implementazione:

- **utilizzo di un gestore di prototipi:** quando il numero di prototipi in un sistema non è fisso, tenere un registro dei prototipi disponibili;
- **implementare l'operazione *clone*:** è particolarmente complicato quando le strutture degli oggetti contengono riferimenti circolari;
- Inizializzare i diversi cloni: si dovrebbe creare un metodo init. 

### Esempio del labirinto

```cpp
class MazePrototypeFactory: public MazeFactory {
    public:
        MazePrototypeFactory(Maze*, Wall*, Room*, Door*);
        
        virtual Maze* MakeMaze() const;
        virtual Room* MakeRoom(int) const;
        virtual Wall* MakeWall() const;
        virtual Door* MakeDoor(Room*, Room*) const;
    private:
        Maze* _prototypeMaze;
        Room* _prototypeRoom;
        Wall* _prototypeWall;
        Door* _prototypeDoor;
};

MazePrototypeFactory::MazePrototypeFactory (Maze* m, Wall* w, Room* r, Door* d) {
    _prototypeMaze = m;
    _prototypeRoom = r;
    _prototypeWall = w;
    _prototypeDoor = d;
}

Wall* MazePrototypeFactory::MakeWall() const {
    return _prototypeWall->Clone();
}

Door* MazePrototypeFactory::MakeDoor(Room* r1, Room* r2) const {
    Door* door = _prototypeDoor->Clone();
    door->Initialize(r1, r2);
    return door;
}

class BombedWall: public Wall {
    public:
        BombedWall();
        BombedWall(const BombedWall&);
        
        virtual Wall* Clone() const;
        bool HasBomb();
    private:
        bool _bomb;
};

BombedWall::BombedWall(const BombedWall& other): Wall(other) {
    _bomb = other._bomb;
}

Wall* BombedWall::Clone() const {
    return new BombedWall(*this);
}

MazeGame game;
MazePrototypeFactory simpleMazeFactory(new Maze, new Wall, new Room, new Door);

Maze* maze = game.CreateMaze(simpleMazeFactory);

MazePrototypeFactory bombedMazeFactory (new Maze, new BombedWall, new RoomWithABomb, new Door);
```

## Builder (BU)

L'**intento** è separare la costruzione di un oggetto complesso dalla sua rappresentazione in modo che lo stesso processo di costruzione possa creare rappresentazioni diverse.

### Motivazione

**Considera un lettore per il formato RTF (Rich Text Format):** dovrebbe essere in grado di convertire RTF in molti formati di testo in testo ASCII semplice o in un widget di testo che può essere modificato in modo interattivo.
Dovrebbe essere facile aggiungere una nuova conversione senza modificare il lettore.

![[BUesempio.png]]

### Applicabilità

Usa il modello Builder quando:

- l'algoritmo per la creazione di un oggetto complesso dovrebbe essere indipendente dalle parti che compongono l'oggetto e da come sono assemblate;
- il processo di costruzione deve consentire rappresentazioni diverse per l'oggetto che viene costruito.

### Struttura

![[BUstruttura.png]]

- **Builder (TextConverter):** interfaccia astratta per la creazione di parti di un prodotto;
- **ConcreteBuilder (ASCIIConverter, ...):** costruisce e assembla parti del prodotto implementando l'interfaccia Builder e fornisce un'interfaccia per il recupero del prodotto (ad esempio, GetASCIIText, GetTextWidget);
- **Director (RTFReader):** costruisce un oggetto utilizzando l'interfaccia Builder;
- **Product (ASCIIText, TeXText, TextWidget):** rappresenta l'oggetto complesso in costruzione e comprende le classi che ne definiscono le parti costituenti.

### Collaborazione

- Il client crea l'oggetto *Director* e lo configura con l'oggetto *Builder* desiderato.
- Il *Director* informa il costruttore ogni volta che una parte del prodotto deve essere costruita.
- Il *Builder* gestisce le richieste del regista e aggiunge parti al prodotto;
- Il client recupera il prodotto dal *Builder*.

![[BUcollaborazione.png]]

### Conseguenze

**Ti consente di variare la rappresentazione interna di un prodotto:** l'oggetto Builder fornisce al regista un'interfaccia astratta per la costruzione del prodotto.
L'interfaccia consente al costruttore di nascondere la rappresentazione e la struttura interna del prodotto. Nasconde anche come viene assemblato il prodotto.

**Isola il codice per la costruzione e la rappresentazione:** BU migliora la modularità incapsulando il modo in cui un oggetto complesso è costruito e rappresentato ed i clienti non devono sapere nulla della struttura interna del prodotto.

**Ti dà un controllo più preciso sul processo di costruzione:** il prodotto viene costruito passo dopo passo sotto il controllo del regista e solo quando il prodotto è finito il regista lo recupera dal costruttore.

### Implementazione

Una classe *Builder* astratta che definisce un'operazione per ogni componente che un regista può chiedergli di creare.

Le operazioni non fanno nulla per impostazione predefinita.
Una classe *ConcreteBuilder* sovrascrive le operazioni per i componenti che è interessata a creare.

**Realizzare un interfaccia di montaggio e costruzione:** i costruttori costruiscono i loro prodotti passo dopo passo e l'interfaccia BU consente la costruzione di prodotti per tutti i tipi di concrete builders.

**Perché nessuna classe astratta per i prodotti?** I prodotti differiscono così notevolmente nella loro rappresentazione (ad es. *ASCIIText* e *TextWidget* differiscono) e ciò è un problema.

Altro problema sono i **metodi vuoti come predefiniti in Builder**: le funzioni membro virtuali hanno metodi vuoti che consentono ai client di sovrascrivere solo le operazioni a cui sono interessati.

### Esempio del labirinto

```cpp
class MazeBuilder {
    public:
        virtual void BuildMaze() { }
        virtual void BuildRoom(int room) { }
        virtual void BuildDoor(int roomFrom, int roomTo) { }
        
        virtual Maze* GetMaze() { return 0; }
    protected:
        MazeBuilder();
};

Maze* MazeGame::CreateMaze(MazeBuilder& builder) {
    builder.BuildMaze();
    
    builder.BuildRoom(1);
    builder.BuildRoom(2);
    builder.BuildRoom(1, 2);
    
    return builder.GetMaze();
}
```

## Conclusioni

Due modi comuni per parametrizzare un sistema in base alle classi di oggetti che crea:

- per sottoclassare la classe che crea gli oggetti (Factory Method);
- parametrizzare un sistema basandosi sulla composizione di oggetti (Abstract Factory, Builder, Prototype).

Lo svantaggio principale di FM è che può richiedere la creazione di una nuova sottoclasse solo per cambiare la classe del prodotto: tali modifiche possono verificarsi a cascata.

Il modello "composition-based" prevede la creazione di un nuovo "factory object" la cui responsabilità è quella di creare oggetti di prodotto:

- **Abstract Factory** ha il factory object che produce oggetti di diverse classi;
- **Builder** ha il **factory object** che costruisce un prodotto complesso in modo incrementale utilizzando un protocollo complesso;
- **Prototype** ha il factory object (il Prototype stesso) che costruisce un prodotto copiando un oggetto prototipo.
