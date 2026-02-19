---
aliases: [javafx]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
JavaFx permette di realizzare delle applicazioni per pc, cellulari, web, TV e altri tipi di piattaforme.

Una delle innovazioni alla base di JavaFX include la semplicità di realizzare interfacce grafiche. Infatti, JavaFX permette di definire l'aspetto e lo stile della applicazioni usando i fogli di stile (Cascading Style Sheets, CSS). L'interfaccia grafica può essere sviluppata attraverso un linguaggio chiamato **FXML**.

## FXML

FXML è un linguaggio di markup basato su XML. Le interfacce possono essere definite usando questo linguaggio oppure è possibile scrivere e definire interfacce grafiche **senza scrivere codice**, utilizzando uno strumento chiamato **Scene Builder**.

SceneBuilder permette di separare nettamente la grafica dalla logica applicativa:

- attraverso SceneBuilder si *disegna* l'interfaccia grafica;
- SceneBuilder crea in automatico i file FXML;
- attraverso il codice si realizza la logica.

## [[Architettura]]

In generale, un'applicazione JavaFX è composta da tre componenti principali:

- Stage
- Scene
- Scene Graph (insieme di Nodi)

### Stage

Uno stage è una finestra e contiene tutti gli elementi dell'applicazione JavaFX.

> **ATTENZIONE:** dovete chiamare il metodo show() per mostrare il contenuto di uno stage.

```java
primaryStage.setMinWidth(600);
primaryStage.setMinHeight(600);
...
primaryStage.setTitle("Applicazione");
primaryStage.show()
```

### Scene

Un oggetto Scene rappresenta il contenuto delle applicazioni JavaFX. Contiene uno Scene Graph ed è contenuto in un solo Stage.

### Scene Graph

In JavaFX, le applicazioni grafiche sono scritte usando uno **Scene Graph**. Uno Scene Graph può essere visto come il punto di partenza per la costruzione di applicazioni grafiche. Infatti, contiene dei gli oggetti grafici (chiamati **nodi**) che possono essere di tipi diversi:

- Containers (ad esempio BorderPane, FlowPane, ecc.)
- Controlli (ad esempio Button, TextArea, ecc.)
- Oggetti geometrici in 2D e 3D
- Elementi audio/video e immagini

Tipicamente, i nodi sono distribuiti in un qualche ordine gerarchico, in una struttura ad albero (c'è un nodo radice, poi dei nodi figli, ecc.).

> **ATTENZIONE:** non sono gli stessi container, controlli, ecc. delle Swing. Hanno funzionalità simili ma sono classi diverse!
