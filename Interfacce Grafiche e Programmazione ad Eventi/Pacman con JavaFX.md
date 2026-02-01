Il percorso dei file è elencato di seguito:

![](percorsoFilePacmanFX.PNG)

Nella cartella *src/main/java* andremo ad elencare i *package* di seguito, mentre nella cartella *src/main/resources/images* si trovano le immagini di pacman in tutte le direzioni

## PACKAGE: application

Per quanto riguarda il file *Main.java*:

```java
package application;

public class Main {
	public static void main(String[] args) {
		PacmanMain.main(args);
	}
}
```

Per quanto riguarda il file *PacmanMain.java*:

```java
package application;

import application.controller.MovementController;
import application.model.Game;
import application.view.PacmanGraphics;
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.stage.Stage;
import javafx.stage.StageStyle;

public class PacmanMain extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		Game game = new Game();
		PacmanGraphics pacmanGraphics = new PacmanGraphics();
		MovementController controller = new MovementController(game, pacmanGraphics);
		pacmanGraphics.setController(controller);
		Scene scene = new Scene(pacmanGraphics);
		primaryStage.setScene(scene);
		primaryStage.setResizable(false);
		primaryStage.setWidth(Settings.WINDOW_SIZE_IN_PIXEL);
		primaryStage.setHeight(Settings.WINDOW_SIZE_IN_PIXEL);
		primaryStage.initStyle(StageStyle.UNDECORATED);
		primaryStage.show();
		pacmanGraphics.draw();
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```

Per quanto riguarda il file *Settings.java*:

```java
package application;

public class Settings {
	public final static int WINDOW_SIZE_IN_PIXEL = 800;
	public final static int BLOCK_SIZE_IN_PIXEL = 40;
	public final static int CELL_SIZE = WINDOW_SIZE_IN_PIXEL/BLOCK_SIZE_IN_PIXEL;

	public final static int MOVE_RIGHT = 0;
	public final static int MOVE_LEFT = 1;
	public final static int MOVE_UP = 2;
	public final static int MOVE_DOWN = 3;
}
```

## PACKAGE: application.controller

Per quanto riguarda il file *MovimentController.java*:

```java
package application.controller;

import application.Settings;
import application.model.Game;
import application.view.PacmanGraphics;
import javafx.event.EventHandler;
import javafx.scene.input.KeyEvent;
import javafx.stage.Stage;

public class MovementController implements EventHandler<KeyEvent> {
	private Game game;
	private PacmanGraphics pacmanGraphics;

	public MovementController(Game game, PacmanGraphics pacmanGraphics) {
		this.game = game;
		this.pacmanGraphics = pacmanGraphics;
	}

	@Override
	public void handle(KeyEvent event) {
		int direction;
		switch (event.getCode()) {
		case LEFT:
			direction = Settings.MOVE_LEFT;
			break;
		case RIGHT:
			direction = Settings.MOVE_RIGHT;
			break;
		case DOWN:
			direction = Settings.MOVE_DOWN;
			break;
		case UP:
			direction = Settings.MOVE_UP;
			break;
		case ESCAPE:
			if(pacmanGraphics.getScene().getWindow() instanceof Stage) {
				Stage stage = (Stage) pacmanGraphics.getScene().getWindow();
				stage.close();
			}
		default:
			return;
		}
		game.move(direction);
		pacmanGraphics.setDirection(direction);
		pacmanGraphics.draw();
	}

	public Game getGame() {
		return game;
	}
}
```

## PACKAGE: application.model

Per quanto riguarda il file *Block.java*:

```java
package application.model;

public class Block {
	public static final int EMPTY = 0;
	public static final int PACMAN = 1;
	public static final int POINT = 2;
	public static final int WALL = 3;

	private int type;

	public Block(int type) {
	    this.type = type;
	}

	public void setType(int type) {
	    this.type = type;
	}

	public int getType() {
	    return type;
	}
}
```

Per quanto riguarda il file *Game.java*:

```java
package application.model;

import java.util.Random;

import application.Settings;

public class Game {
	private Block[][] blocks = new Block[Settings.CELL_SIZE][Settings.CELL_SIZE];
	private int x;
	private int y;

	private int coins;

	public Game() {
		x = 0;
		y = 0;
		coins = 0;
		for (int i = 0; i < blocks.length; i++) {
			for (int j = 0; j < blocks[i].length; j++) {
				blocks[i][j] = new Block(Block.EMPTY);
			}
		}
		blocks[x][y].setType(Block.PACMAN);
		Random r = new Random();
		int min = 1;
		int max = 100;
		for (int i = 0; i < blocks.length; i++) {
			for (int j = 0; j < blocks[i].length; j++) {
				int result = r.nextInt(max - min) + min;
				if (i != 0 && j != 0) {
					if (result >= 1 && result <= 10)
						blocks[i][j].setType(Block.WALL);
					else if (result >= 11 && result <= 50)
						blocks[i][j].setType(Block.POINT);
					else
						blocks[i][j].setType(Block.EMPTY);
				}
			}
		}
	}

	public void move(int direction) {
		if (collision(newX(direction), newY(direction)))
			return;
		blocks[x][y].setType(Block.EMPTY);
		x = newX(direction);
		y = newY(direction);
		if (blocks[x][y].getType() == Block.POINT)
			coins++;
		blocks[x][y].setType(Block.PACMAN);
	}

	private int newX(int direction) {
		if (direction == Settings.MOVE_RIGHT) {
			return (x + 1) % Settings.CELL_SIZE;
		} else if (direction == Settings.MOVE_LEFT) {
			if (x == 0)
				return Settings.CELL_SIZE - 1;
			return x - 1;
		}
		return x;
	}

	private int newY(int direction) {
		if (direction == Settings.MOVE_UP) {
			if (y == 0)
				return Settings.CELL_SIZE - 1;
			return y - 1;
		} else if (direction == Settings.MOVE_DOWN) {
			return (y + 1) % Settings.CELL_SIZE;
		}
		return y;
	}

	private boolean collision(int x, int y) {
		return blocks[x][y].getType() == Block.WALL;
	}

	public Block[][] getBlocks() {
		return blocks;
	}
}
```

## PACKAGE: application.view

Per quanto riguarda il file *PacmanGraphics.java*:

```java
package application.view;

import application.Settings;
import application.controller.MovementController;
import application.model.Block;
import application.model.Game;
import javafx.scene.canvas.Canvas;
import javafx.scene.image.Image;
import javafx.scene.layout.Background;
import javafx.scene.layout.BackgroundFill;
import javafx.scene.layout.StackPane;
import javafx.scene.paint.Color;

public class PacmanGraphics extends StackPane {
	private Canvas canvas;
	private Image[] images;
	private int direction;
	private MovementController controller;

	private void initImages() {
		images[Settings.MOVE_RIGHT] = new Image(getClass().getResourceAsStream("/images/pacman_right.png"));
		images[Settings.MOVE_LEFT] = new Image(getClass().getResourceAsStream("/images/pacman_left.png"));
		images[Settings.MOVE_UP] = new Image(getClass().getResourceAsStream("/images/pacman_up.png"));
		images[Settings.MOVE_DOWN] = new Image(getClass().getResourceAsStream("/images/pacman_down.png"));
	}

	public PacmanGraphics() {
		canvas = new Canvas();
		images = new Image[4];
		getChildren().add(canvas);
		canvas.widthProperty().bind(this.widthProperty());
		canvas.heightProperty().bind(this.heightProperty());
		initImages();
		direction = Settings.MOVE_RIGHT;
		this.setBackground(new Background(new BackgroundFill(Color.BLACK, null, null)));
	}

	public void setController(MovementController controller) {
		this.controller = controller;
		canvas.setFocusTraversable(true);
		canvas.setOnKeyPressed(this.controller);
	}

	public void setDirection(int direction) {
	    this.direction = direction;
	}

	public void draw() {
		canvas.getGraphicsContext2D().clearRect(0, 0, canvas.getWidth(), canvas.getHeight());
		Game game = controller.getGame();
		for(int i = 0; i < game.getBlocks().length; i++) {
			int x = Settings.BLOCK_SIZE_IN_PIXEL * i;
			for(int j = 0; j < game.getBlocks()[i].length; j++) {
				int y = Settings.BLOCK_SIZE_IN_PIXEL * j;
				switch(game.getBlocks()[i][j].getType()) {
				case Block.PACMAN:
					canvas.getGraphicsContext2D().drawImage(images[direction], x, y, Settings.BLOCK_SIZE_IN_PIXEL, Settings.BLOCK_SIZE_IN_PIXEL);
					break;
				case Block.POINT:
					canvas.getGraphicsContext2D().setFill(Color.YELLOW);
					canvas.getGraphicsContext2D().fillOval(x+Settings.BLOCK_SIZE_IN_PIXEL/4, y+Settings.BLOCK_SIZE_IN_PIXEL/4, Settings.BLOCK_SIZE_IN_PIXEL/2, Settings.BLOCK_SIZE_IN_PIXEL/2);
					break;
				case Block.WALL:
					canvas.getGraphicsContext2D().setFill(Color.BLUE);
					canvas.getGraphicsContext2D().fillRect(x+Settings.BLOCK_SIZE_IN_PIXEL/4, y, Settings.BLOCK_SIZE_IN_PIXEL*0.5, Settings.BLOCK_SIZE_IN_PIXEL);
					break;
				default:
					break;
				}
			}
		}
	}
}
```
