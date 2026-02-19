---
aliases: [Musica JavaFX, MJ]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Nella cartella *src/main/java* package *application* andremo ad elencare i file di seguito, mentre nella cartella *src/main/resources* package *application* troveremo la musica in formato *.wav*.

Per quanto riguarda il file *Main.java*:

```java
package application;

public class Main {
	public static void main(String[] args) {
		MainApplication.main(args);
	}
}
```

Per quanto riguarda il file *MainApplication.java*:

```java
package application;

import javafx.application.Application;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.layout.VBox;
import javafx.scene.media.Media;
import javafx.scene.media.MediaPlayer;
import javafx.stage.Stage;

public class MainApplication extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		VBox box = new VBox();
		Button play = new Button("play");
		Button pause = new Button("pause");
		Button stop = new Button("stop");
		play.setPrefWidth(70);
		pause.setPrefWidth(70);
		stop.setPrefWidth(70);
		box.getChildren().add(play);
		box.getChildren().add(pause);
		box.getChildren().add(stop);
		Scene scene = new Scene(box);
		primaryStage.setScene(scene);
		primaryStage.setWidth(400);
		primaryStage.setHeight(400);

		Media media = new Media(getClass().getResource("/application/test.wav").toExternalForm());
		MediaPlayer player = new MediaPlayer(media);

		play.setOnAction(new EventHandler<ActionEvent>() {
			@Override
			public void handle(ActionEvent event) {
				player.play();
			}
		});

		pause.setOnAction(new EventHandler<ActionEvent>() {
			@Override
			public void handle(ActionEvent event) {
				player.pause();
			}
		});

		stop.setOnAction(new EventHandler<ActionEvent>() {
			@Override
			public void handle(ActionEvent event) {
				player.stop();
			}
		});
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```
