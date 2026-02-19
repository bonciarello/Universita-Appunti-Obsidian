---
aliases: [BGJMP, Breve guida JavaFX - Maven Project]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Come avviare un progetto [[Maven]] che serve per far funzionare [[JavaFX]]:

1. Avvia *Eclipse*;
2. Clicca con il tasto destro nella parte sinistra dove sono i vostri progetti;
3. Seleziona *New → New project* (non java project):
![](1.png)
4. Scegli *[[Maven]] → [[Maven]] project* e clicca su *Next*:
![](2.png)
5. Seleziona esattamente gli stessi elementi dell'immagine di seguito e clicca su: *Next*
![](3.png)
6. Inserite gli altri valori per come riportato qui in basso:
   - **GROUP ID:** l'identificativo univoco dell'organizzazione o del gruppo che ha creato il progetto. In genere è il nome di dominio dell'organizzazione, ad esempio it.unical.mat o it.unical.demacs.informatica
   - **ARTIFACT ID:** Indica il nome che vuoi dare al programma che verrà generato (in genere è il nome principale dell'applicazione).
   - **VERSION:** è la versione del progetto, al momento si può lasciare quella che trovi.
   - **NAME:** Indica il nome mostrato per il progetto, è spesso usato per la documentazione.
![](4.png)
7. Il progetto è stato creato e dovresti vedere una schermata come quella mostrata di seguito:
![](5.png)
8. Fai doppio click sul file pom.xml e inserisci prima di </project> questi elementi:
```xml
<properties>
    <maven.compiler.source>15</maven.compiler.source>
    <maven.compiler.target>15</maven.compiler.target>
</properties>

<dependencies>
    <!-- https://mvnrepository.com/artifact/org.openjfx/javafx-base -->
    <dependency>
    	<groupId>org.openjfx</groupId>
    	<artifactId>javafx-base</artifactId>
    	<version>15.0.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.openjfx/javafx-controls -->
    <dependency>
    	<groupId>org.openjfx</groupId>
    	<artifactId>javafx-controls</artifactId>
    	<version>15.0.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.openjfx/javafx-graphics -->
    <dependency>
    	<groupId>org.openjfx</groupId>
    	<artifactId>javafx-graphics</artifactId>
    	<version>15.0.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.openjfx/javafx-fxml -->
    <dependency>
    	<groupId>org.openjfx</groupId>
    	<artifactId>javafx-fxml</artifactId>
    	<version>15.0.1</version>
    </dependency>

	<!-- https://mvnrepository.com/artifact/org.openjfx/javafx-web -->
    <dependency>
    	<groupId>org.openjfx</groupId>
    	<artifactId>javafx-web</artifactId>
    	<version>15.0.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.openjfx/javafx-media -->
    <dependency>
    	<groupId>org.openjfx</groupId>
    	<artifactId>javafx-media</artifactId>
    	<version>15.0.1</version>
    </dependency>
</dependencies>
```
