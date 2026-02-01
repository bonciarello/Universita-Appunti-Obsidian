Come realizzare una calcolatrice come la seguente?

![](Calcolatrice.png)

Per quanto riguarda il file *Main.java*:
```java
package calcolatrice;

import java.awt.BorderLayout;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.GridLayout;
import javax.swing.BorderFactory;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JTextField;

import calcolatrice.buttons.GenericButton;
import calcolatrice.buttons.NumberButton;
import calcolatrice.buttons.OperationButton;

public class Main {
	public static void main(String[] args) {
		JFrame f = new JFrame("Calc");
		JTextField numberArea = new JTextField();
		numberArea.setPreferredSize(new Dimension(300, 50));
		numberArea.setFont(new Font("arial",  Font.PLAIN, 48));
		numberArea.setHorizontalAlignment(JTextField.RIGHT);
		numberArea.setBorder(BorderFactory.createEmptyBorder());
		JPanel p = new JPanel();
		p.setLayout(new GridLayout(5, 4, 1, 1));
		JButton[] buttons = {
			new GenericButton("AC"),
			new GenericButton("+/-"),
			new GenericButton("%"),
			new OperationButton("/"),
			new NumberButton("7"),
			new NumberButton("8"),
			new NumberButton("9"),
			new OperationButton("X"),
			new NumberButton("4"),
			new NumberButton("5"),
			new NumberButton("6"),
			new OperationButton("-"),
			new NumberButton("1"),
			new NumberButton("2"),
			new NumberButton("3"),
			new OperationButton("+"),
			new NumberButton("0"),
			new GenericButton(","),
			new GenericButton("mod"),
			new OperationButton("=")
		};
		for(JButton b : buttons)
			p.add(b);
		f.add(numberArea, BorderLayout.NORTH);
		f.add(p, BorderLayout.CENTER); f.setSize(300, 450);
		f.setMinimumSize(new Dimension(300, 450)); f.setVisible(true);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}
}
```

Per quanto riguarda il file *OperationButton.java*:
```java
package calcolatrice.buttons;

import java.awt.Color;

public class OperationButton extends GenericButton {
	public OperationButton(String text) {
		super(text);
		setBackground(new Color(255,100,0));
		setForeground(Color.WHITE);
	}
}
```

Per quanto riguarda il file *NumberButton.java*:
```java
package calcolatrice.buttons;

import java.awt.Color;

public class NumberButton extends GenericButton {
	public NumberButton(String text) {
		super(text);
		setBackground(new Color(200,200,200));
	}
}
```

Per quanto riguarda il file *GenericButton.java*:
```java
package calcolatrice.buttons;

import javax.swing.JButton;

public class GenericButton extends JButton {
	public GenericButton(String text) {
		super(text);
		setOpaque(true);
		setBorderPainted(false);
	}
}
```
