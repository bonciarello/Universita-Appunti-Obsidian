Nella cartella *src* si trovano tutti i *package* di seguito, anche le immagini dell'omino che si muove.

## PACKAGE: application

Per quanto riguarda il file *Main.java*:

```java
package application;

import javax.swing.JFrame;

import application.controller.MovementController;
import application.view.GraphicPanel;

public class Main {
	public static void main(String[] args) {
		JFrame f = new JFrame();
		f.setSize(800, 800);
		GraphicPanel gp = new GraphicPanel();
		MovementController controller = new MovementController(gp);
		gp.addKeyListener(controller);
		gp.setFocusable(true);
		f.add(gp);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		f.setVisible(true);
		GameLoop gameLoop = new GameLoop(controller);
		Thread t = new Thread(gameLoop);
		t.start();
	}
}
```

Per quanto riguarda il file *GameLoop.java*:

```java
package application;

import application.controller.MovementController;

public class GameLoop implements Runnable {
	private MovementController controller;
	private int frequency = 60;

	public GameLoop(MovementController controller) {
		this.controller = controller;
	}

	@Override
	public void run() {
		while(true) {
			controller.update();
			try {
				Thread.sleep(frequency);
			} catch (InterruptedException e) {
				return;
			}
		}
	}
}
```

## PACKAGE: application.controller

Per quanto riguarda il file *MovementController.java*:

```java
package application.controller;

import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;

import application.model.Game;
import application.view.GraphicPanel;

public class MovementController implements KeyListener {
	private GraphicPanel gp;

	public MovementController(GraphicPanel gp) { this.gp = gp; }

	public void update() { gp.update(); }

	@Override
	public void keyPressed(KeyEvent e) {
		if(e.getKeyCode() == KeyEvent.VK_RIGHT) {
			Game game = Game.getInstance();
			game.move();
			gp.move();
		}
	}

	@Override
	public void keyReleased(KeyEvent e) {
		if(e.getKeyCode() == KeyEvent.VK_RIGHT) {
			gp.stop();
		}
	}

	@Override
	public void keyTyped(KeyEvent e) {}
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
	int x;
	int y;
	int speed;

	public int getX() { return x; }

	public int getY() { return y; }
}
```

## PACKAGE: application.view

Per quanto riguarda il file *CharacterView.java*:

```java
package application.view;

import java.awt.Image;
import java.io.IOException;
import java.util.ArrayList;
import javax.imageio.ImageIO;

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
		try {
			for(int i = 0; i < 10; i++) {
				Image img = ImageIO.read(getClass().getResourceAsStream
				    ("/application/resources/"+i+".png"));
			    images.add(img);
			}
			currentImage = images.get(0);
		} catch (IOException e) {
			e.printStackTrace();
		}
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

import java.awt.Graphics;
import javax.swing.JPanel;
import application.model.Game;

public class GraphicPanel extends JPanel {
	private static final long serialVersionUID = 2420221890130352491L;

	private CharacterView cv = new CharacterView();

	@Override
	protected void paintComponent(Graphics g) {
		super.paintComponent(g);
		int x = Game.getInstance().getCharacter().getX();
		int y = Game.getInstance().getCharacter().getY();
		g.drawImage(cv.currentImage, x, y, cv.dim, cv.dim, null);
	}

	public void move() { cv.move(); }

	public void stop() { cv.stop(); }

	public void update() {
		cv.update();
		repaint();
	}
}
```
