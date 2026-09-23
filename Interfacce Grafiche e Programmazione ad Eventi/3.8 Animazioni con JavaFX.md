---
aliases: [AJ, Animazioni JavaFX]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Nella cartella *src/main/java* andremo ad elencare i *package* di seguito, mentre nella cartella *src/main/resources/images* si trovano le immagini dell'omino che si muove.

## PACKAGE: application

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

import application.controller.MovementController;
import application.view.GraphicPanel;
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class MainApplication extends Application {
	public static void main(String[] args) {
		launch(args);
	}

	@Override
	public void start(Stage primaryStage) throws Exception {
		GraphicPanel gp = new GraphicPanel();
		MovementController controller = new MovementController(gp);
		gp.addController(controller);
		Scene scene = new Scene(gp);
		primaryStage.setScene(scene);
		primaryStage.setWidth(800);
		primaryStage.setHeight(800);
		primaryStage.setTitle("Java FX animation");
		primaryStage.show();
		GameLoop gameLoop = new GameLoop(controller);
		gameLoop.start();
	}
}
```

Per quanto riguarda il file *GameLoop.java*:

```java
package application;

import application.controller.MovementController;
import javafx.animation.AnimationTimer;

public class GameLoop extends AnimationTimer {
	private MovementController controller;
	private long previousTime = 0;
	private long frequency = 60 * 1000000;

	public GameLoop(MovementController controller) {
		this.controller = controller;
	}

	@Override
	public void handle(long now) {
		if(now-previousTime >= frequency) {
			controller.update();
			previousTime = now;
		}
	}
}
```

## PACKAGE: application.controller

Per quanto riguarda il file *MovementController.java*:

```java
package application.controller;


import application.model.Game;
import application.view.GraphicPanel;
import javafx.event.EventHandler;
import javafx.scene.input.KeyCode;
import javafx.scene.input.KeyEvent;

public class MovementController implements EventHandler<KeyEvent> {
	private GraphicPanel gp;

	public MovementController(GraphicPanel gp) {
		this.gp = gp;
	}

	public void update() {
		gp.update();
	}

	@Override
	public void handle(KeyEvent event) {
		if(event.getCode() != KeyCode.RIGHT)
			return;
		if(event.getEventType().equals(KeyEvent.KEY_PRESSED)) {
		 	Game.getInstance().move();
			gp.move();
		}
		else if(event.getEventType().equals(KeyEvent.KEY_RELEASED)) {
			gp.stop();
		}
	}
}
```

## PACKAGE: application.model

Per quanto riguarda il file *Game.java*:

```java
package application.model;

public class Game {
	private MyCharacter character;

	private static Game game = null;

	private Game() {
		character = new MyCharacter();
		character.x = 0;
		character.y = 0;
		character.speed = 10;
	}

	public static Game getInstance() {
		if(game == null)
			game = new Game();
		return game;
	}

	public MyCharacter getCharacter() { return character; }

	public void move() { character.x += character.speed; }
}
```

Per quanto riguarda il file *MyCharacter.java*:

```java
package application.model;

public class MyCharacter {
	int x; int y;
	int speed;

	public int getX() { return x; }

	public int getY() { return y; }
}
```

## PACKAGE: application.view

Per quanto riguarda il file *CharacterView.java*:

```java
package application.view;

import java.util.ArrayList;
import javafx.scene.image.Image;

public class CharacterView {
	private ArrayList<Image> images;
	Image currentImage;
	int dim;
	private int index;
	private boolean move;

	CharacterView() {
		images = new ArrayList<Image>();
		dim = 147;
		move = false;
		index = 0;
		for(int i = 0; i < 10; i++) {
			Image img = new Image(getClass().getResourceAsStream
			    ("/application/resources/"+i+".png"));
			images.add(img);
		}
		currentImage = images.get(0);
	}

	public void move() { move = true; }

	public void stop() { move = false; }

	public void update() {
		if(move) {
			index++;
			if(index == images.size()) index = 0;
		}
		else { index = 0; }
		currentImage = images.get(index);
	}
}
```

Per quanto riguarda il file *GraphicPanel.java*:

```java
package application.view;

import application.controller.MovementController;
import application.model.Game;
import javafx.scene.canvas.Canvas;
import javafx.scene.layout.StackPane;

public class GraphicPanel extends StackPane {
	private CharacterView cv = new CharacterView();
	private Canvas canvas;

	public GraphicPanel() {
		canvas = new Canvas();
		canvas.setFocusTraversable(true);
		getChildren().add(canvas);
		canvas.widthProperty().bind(this.widthProperty());
		canvas.heightProperty().bind(this.heightProperty());
	}

	public void addController(MovementController controller) {
		canvas.setOnKeyPressed(controller);
		canvas.setOnKeyReleased(controller);
	}

	private void draw() {
		canvas.getGraphicsContext2D().clearRect(0, 0, canvas.getWidth(), canvas.getHeight());
		int x = Game.getInstance().getCharacter().getX();
		int y = Game.getInstance().getCharacter().getY();
		canvas.getGraphicsContext2D().drawImage(cv.currentImage, x, y, cv.dim, cv.dim);
	}

	public void move() { cv.move(); }

	public void stop() { cv.stop(); }

	public void update() {
		cv.update();
		draw();
	}
}
```
