Come realizzare una chat come la seguente?

![](paint.PNG)

Per quanto riguarda il file *TopPanel.java*:
```java
package board;

import java.awt.Color;
import java.awt.Dimension;
import java.awt.GridLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.image.BufferedImage;
import java.io.File; import java.io.IOException;
import javax.imageio.ImageIO; import javax.swing.JButton;
import javax.swing.JColorChooser; import javax.swing.JFileChooser;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.filechooser.FileFilter;
import javax.swing.filechooser.FileNameExtensionFilter;

public class TopPanel extends JPanel {

	private static final long serialVersionUID = 8039202002735442212L;

	private JButton backgroundButton = new JButton("Change background");
	private JButton colorButton = new JButton("Change color");
	private JButton cleanButton = new JButton("Clean");

	public TopPanel(Board board) {
		setPreferredSize(new Dimension(600, 40));
		this.setLayout(new GridLayout(1, 3, 1, 1));
		this.add(backgroundButton); this.add(colorButton);
		this.add(cleanButton); addListeners(board);
	}

	private void addListeners(Board board) {
		backgroundButton.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				FileFilter imageFilter = new FileNameExtensionFilter("Image files", ImageIO.getReaderFileSuffixes());
				JFileChooser f = new JFileChooser();
				f.setFileFilter(imageFilter);
				int res = f.showOpenDialog(null);
				if(res == JFileChooser.APPROVE_OPTION) {
					File file = f.getSelectedFile();
					try {
						BufferedImage img = ImageIO.read(file);
						if(img == null)
							throw new Exception("Error: cannot open the image");
						board.changeBackground(img);
					} catch (IOException e1) {
						JOptionPane.showMessageDialog(null, "Error: cannot open the image");
					} catch (Exception e1) {
						JOptionPane.showMessageDialog(null, e1.getMessage());
					}
				}
			}
		});

		colorButton.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				Color color = JColorChooser.showDialog(null, "Choose a new color", board.getCurrentColor());
				if(color != null) {
					board.setCurrentColor(color);
				}
			}
		});

		cleanButton.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				board.clean();
			}
		});
	}
}
```

Per quanto riguarda il file *Board.java*:
```java
package board;

import java.awt.Color;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;
import java.awt.event.MouseMotionListener;
import java.util.ArrayList;
import java.util.List;

import javax.swing.JPanel;

public class Board extends JPanel implements MouseListener, MouseMotionListener {

	private static final long serialVersionUID = 5379358483855227909L;
	private List<Point> points = new ArrayList<Point>();
	private Color currentColor = Color.BLACK;
	private Image background = null;

	public Board() {
		addMouseListener(this);
		addMouseMotionListener(this);
		setBackground(Color.WHITE);
	}

	public void changeBackground(Image img) {
		if(img != null) {
			background = img;
			background = background.getScaledInstance(getSize().width, getSize().height, Image.SCALE_SMOOTH);
			repaint();
		}
	}

	public void componentResized() {
		changeBackground(background);
	}

	//NO paintComponent***S***
	@Override
	protected void paintComponent(Graphics g) {
		super.paintComponent(g);
		if(background != null)
			g.drawImage(background, 0, 0, null);
		else
			setBackground(Color.WHITE);
		for(Point p : points) {
			g.setColor(p.color);
			g.fillOval(p.x, p.y, 10, 10);
		}
	}

	private void handleMouseEvent(MouseEvent e) {
		Point p = new Point(e.getX(), e.getY(), currentColor);
		points.add(p);
		repaint();
	}

	public Color getCurrentColor() {
		return currentColor;
	}

	public void setCurrentColor(Color currentColor) {
		this.currentColor = currentColor;
	}

	public void clean() {
		points.clear();
		background = null;
		repaint();
	}

	@Override
	public void mousePressed(MouseEvent e) {
		handleMouseEvent(e);
	}

	@Override
	public void mouseDragged(MouseEvent e) {
		handleMouseEvent(e);
	}

	public void mouseReleased(MouseEvent e) {}
	public void mouseEntered(MouseEvent e) {}
	public void mouseExited(MouseEvent e) {}
	public void mouseClicked(MouseEvent e) {}
	public void mouseMoved(MouseEvent e) {}
}
```

Per quanto riguarda il file *Point.java*:
```java
package board;

import java.awt.Color;

public class Point {
	int x;
	int y;
	Color color;

	public Point(int x, int y, Color color) {
		super();
		this.x = x;
		this.y = y;
		this.color = color;
	}
}
```

Per quanto riguarda il file *Main.java*:
```java
package board;

import java.awt.BorderLayout;
import java.awt.Dimension;
import java.awt.event.ComponentAdapter;
import java.awt.event.ComponentEvent;

import javax.swing.JFrame;

public class Main {

	public static void main(String[] args) {
		JFrame f = new JFrame("Board");
		f.setSize(600, 600);
		Board board = new Board();
		TopPanel top = new TopPanel(board);
		f.add(top, BorderLayout.NORTH);
		f.add(board, BorderLayout.CENTER);

		f.addComponentListener(new ComponentAdapter() {

			@Override
			public void componentResized(ComponentEvent e) {
				board.componentResized();
			}
		});

		f.setMinimumSize(new Dimension(600, 600));
		f.setVisible(true);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
}
```
