Maven è uno strumento di gestione del progetto per Java.
Basato sul concetto di Project Object Model (POM).
Gestisce il processo di costruzione di un progetto (dalla sorgente al programma).
Supporta in modo nativo il riutilizzo del codice e l'integrazione tra progetti.

Maven è uno strumento di costruzione. Maven adotta un ciclo di vita dell'edificio che suddivide il processo di costruzione in più fasi, le più comuni sono:

- **validate:** convalida che il progetto sia corretto e che tutte le informazioni siano disponibili;
- **compile:** compila i sorgenti del progetto (in Bytecode per i sorgenti Java);
- **test:** verifica i sorgenti compilati utilizzando un framework di unit test (ad es. con JUnit);
- **package:** impacchetta il codice sorgente compilato in un pacchetto distribuibile (ad es. come file JAR);
- **install:** installa il pacchetto nel repository locale in modo da poter essere utilizzato in altri progetti locali;
- **deploy:** copia il pacchetto in un repository remoto permettendo di condividere il progetto con altri sviluppatori e altri progetti esterni

Alcune altre fasi importanti sono:

- **site:** per generare la documentazione;
- **clean:** per pulire le uscite.

Il ciclo di vita della build è una catena di fasi.
Quando viene eseguita una fase verranno eseguite tutte le fasi che la precedono nel ciclo di vita: ad esempio, la fase del pacchetto implica l'esecuzione di convalida, compilazione e test.

![[POM.png]]

## POM

Tutte le informazioni relative a un progetto Maven sono centralizzate nel file *pom.xml*.

Un POM è diviso in più parti.

![[POMparts.png]]

## Maven Coordinates

Un progetto Maven è identificato da una tripla di valori:

```xml
<groupId, artifactId, version>
```

Per esempio:

```xml
<groupId>com.mycompany.app</groupId>
<artifactId>my.app</artifactId>
<version>1.0</version>
```

## Dipendenze

Maven consente di dichiarare le dipendenze del progetto in modo dichiarativo nel file pom.xml.
Non è necessario scaricare manualmente i file JAR e includerli nel progetto.
Maven adotta un sistema di repository.
Un repository può essere locale (cartella .m2), remoto o centrale.

```xml
<dependencies>
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
	    <version>3.8.1</version>
	</dependency>
</dependencies>
```

## Archetipi

Gli archetipi sono modelli di progetto da cui un programmatore può partire quando crea un nuovo progetto Maven. Il comando:

```bash
mvn archetype:generate
```

crea una struttura di cartelle e un file POM in base all'archetipo scelto.

## Convention Over Configuration

Maven adotta il cosiddetto principio "Convention Over Configuration".

Una convenzione è un insieme di comportamenti predefiniti. L'uso delle convenzioni consente di scrivere un progetto standard con una configurazione minima: (ad es. viene utilizzata la struttura di cartelle predefinita, si assumono convenzioni di denominazione e così via).

Tuttavia, è ancora possibile modificare la configurazione quando necessario.

## Obiettivi e plugin

Gli obiettivi sono azioni eseguibili in Maven.
Le fasi di compilazione di Maven sono obiettivi: pacchetto, test, installazione ecc.
Gli obiettivi sono forniti da artefatti Maven chiamati Plugin.
Maven viene fornito con alcuni plugin predefiniti che sono sempre inclusi e forniscono (tra le altre cose) gli obiettivi del ciclo di vita della build.
Altri plugin possono essere aggiunti per eseguire obiettivi specifici.

Esistono due tipi di plugin:

- **plugin di compilazione:** utilizzati durante le fasi di costruzione del progetto (compilazione, confezionamento, ecc.)
- **plugin di reporting:** utilizzati per la reportistica (es. durante la fase di generazione della documentazione)
