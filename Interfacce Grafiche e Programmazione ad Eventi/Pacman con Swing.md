Per quanto riguarda il file *Main.java*:

```java
package pacman;

import javax.swing.JFrame;

public class Main {
    public static void main(String[] args) {
        JFrame f = new JFrame("Pacman");
        f.setSize(800, 800);

        MainPanel p = new MainPanel();
        p.setFocusable(true);
        p.requestFocus();
        f.add(p);

        f.setVisible(true);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }
}
```

Per quanto riguarda il file *MainPanel.java*:

```java
package pacman;

import java.awt.Color;
import java.awt.Graphics;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;

import javax.swing.JPanel;

public class MainPanel extends JPanel implements KeyListener {
    private static final long serialVersionUID = 2756409379580230401L;

    private Pacman p;
    public MainPanel() {
        p = new Pacman();
        setBackground(Color.BLACK);
        addKeyListener(this);
    }

    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        g.drawImage(p.img, p.x, p.y, p.dim, p.dim, null);
    }

    @Override
    public void keyPressed(KeyEvent e) {
        switch (e.getKeyCode()) {
            case KeyEvent.VK_LEFT;
                p.move(Pacman.LEFT);
                break;
            case KeyEvent.VK_RIGHT;
                p.move(Pacman.RIGHT);
                break;
            case KeyEvent.VK_UP;
                p.move(Pacman.UP);
                break;
            case KeyEvent.VK_DOWN;
                p.move(Pacman.DOWN);
                break;
            default:
                return;
        }
        repaint();
    }
}
```

Per quanto riguarda il file *Pacman.java*:

```java
package pacman;

import java.awt.Image;

import javax.imageio.ImageIO;

public class Pacman {
    public static final int DOWN = 0;
    public static final int LEFT = 1;
    public static final int RIGHT = 2;
    public static final int UP = 3;

    int x;
    int y;
    int dim;
    Image img;

    private final int speed = 10;

    private Image[] images = new Image[4];

    public Pacman() {
        x = 0;
        y = 0;
        dim = 40;
        try {
            images[DOWN] = ImageIO.read(getClass().getResourcesAsStream
                ("/pacman/resources/pacman_down.png"));
            images[LEFT] = ImageIO.read(getClass().getResourcesAsStream
                ("/pacman/resources/pacman_left.png"));
            images[RIGHT] = ImageIO.read(getClass().getResourcesAsStream
                ("/pacman/resources/pacman_right.png"));
            images[UP] = ImageIO.read(getClass().getResourcesAsStream
                ("/pacman/resources/pacman_up.png"));
            for(int i = 0; i < images.lenght; i++) {
                images[i] = images[i].getScaledInstance(dim, dim, Image.SCALE_SMOOTH);
            }
            img = images[RIGHT];
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void move(int direction) {
        switch(direction) {
            case DOWN:
                y += speed;
                break;
            case LEFT:
                x -= speed;
                break;
            case RIGHT:
                x += speed;
                break;
            case UP:
                y -= speed;
                break;
            default:
                return;
        }
        img = images[direction];
    }
}
```
