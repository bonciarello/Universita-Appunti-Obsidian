---
aliases: [Paint JavaFX, PJ]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
![](boardJavaFX.png)

Per quanto riguarda il file *Main.java*:

```java
package application;

public class Main {
	public static void main(String[] args) {
		MainApplication.main(args);
	}
}
```

Per quanto riguarda il file *MainApplication.java* (una sorta di main):

```java
package application;

import javafx.application.Application;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class MainApplication extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		Board board = new Board();
		Scene scene = new Scene(board);
		primaryStage.setTitle("Board");
		primaryStage.setScene(scene);
		primaryStage.setWidth(800);
		primaryStage.setHeight(800);
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```

Per quanto riguarda il file *Board.java*:

```java
package application;

import javafx.event.EventHandler;
import javafx.scene.canvas.Canvas;
import javafx.scene.canvas.GraphicsContext;
import javafx.scene.input.KeyCode;
import javafx.scene.input.KeyEvent;
import javafx.scene.input.MouseEvent;
import javafx.scene.layout.StackPane;
import javafx.scene.paint.Color;

public class Board extends StackPane implements EventHandler<MouseEvent> {
	private Canvas canvas;

	public Board() {
		canvas = new Canvas();
		canvas.setOnMousePressed(this); canvas.setOnMouseDragged(this);
		canvas.setOnKeyPressed(new EventHandler<KeyEvent>() {
			@Override
			public void handle(KeyEvent event) {
				if(event.getCode().equals(KeyCode.C)) {
					GraphicsContext graphicsContext = canvas.getGraphicsContext2D();
					graphicsContext.clearRect(0, 0, canvas.getWidth(), canvas.getHeight());
				}
			}
		});
		canvas.setFocusTraversable(true);
		this.getChildren().add(canvas);
		canvas.widthProperty().bind(this.widthProperty());
		canvas.heightProperty().bind(this.heightProperty());
	}

	@Override
	public void handle(MouseEvent event) {
		GraphicsContext graphicsContext = canvas.getGraphicsContext2D();
		graphicsContext.setFill(Color.BLUE);
		graphicsContext.fillOval(event.getX(), event.getY(), 10, 10);
	}
}
```
