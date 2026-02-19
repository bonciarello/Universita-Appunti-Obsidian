---
aliases: [CS, Chat Swing]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Come realizzare una chat come la seguente?

![](Chat.png)

Per quanto riguarda il file *ChatFrame.java*:
```java
package chat;

import java.awt.BorderLayout;
import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import javax.swing.BoxLayout;
import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JList;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JSplitPane;
import javax.swing.JTextArea;

public class ChatFrame extends JFrame {
	private static final long serialVersionUID = 4213060829881951932L;

	public void createWindow() {
		JPanel left = createLeftPanel(); JPanel right = createRightPanel();
		JScrollPane leftScroll = new JScrollPane(left);
		JSplitPane splitPane = new JSplitPane(JSplitPane.HORIZONTAL_SPLIT, leftScroll, right);
		splitPane.setDividerSize(10); splitPane.setDividerLocation(200);
		this.add(splitPane); this.setTitle("Chat"); this.setSize(800, 500);
		this.setMinimumSize(new Dimension(400, 400));
		this.setVisible(true);
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
	}

	public JPanel createLeftPanel() {
		JPanel left = new JPanel();
		left.setLayout(new BoxLayout(left, BoxLayout.PAGE_AXIS));
		Contact c1 = new Contact(new ImageIcon(getClass().getResource("/resources/img1.png")), "Mario Rossi");
		Contact c2 = new Contact(new ImageIcon(getClass().getResource("/resources/img2.png")), "Maria Bianchi");
		Contact c3 = new Contact(new ImageIcon(getClass().getResource("/resources/img3.png")), "Francesco Verdi");
		Contact c4 = new Contact(new ImageIcon(getClass().getResource("/resources/img4.png")), "Simona Gialli");
		left.add(c1); left.add(c2); left.add(c3); left.add(c4);
		return left;
	}

	private void handleMessage(JTextArea message, JTextArea allMessages) {
		String msg = message.getText();
		allMessages.append(msg + System.lineSeparator());
		message.setText("");
	}

	public JPanel createRightPanel() {
		JPanel right = new JPanel();
		right.setLayout(new BorderLayout());
		JTextArea allMessages = new JTextArea();
		allMessages.setEditable(false);
		JScrollPane scrollAllMessages = new JScrollPane(allMessages);
		right.add(scrollAllMessages, BorderLayout.CENTER);

		JPanel south = new JPanel();
		south.setLayout(new BoxLayout(south, BoxLayout.LINE_AXIS));
		JTextArea message = new JTextArea();
		message.setLineWrap(true);
		JScrollPane scrollMessages = new JScrollPane(message);
		JButton send = new JButton("Send");
		south.setPreferredSize(new Dimension(550, 100));
		south.add(scrollMessages);
		south.add(send);

		send.addActionListener(new ActionListener() {

			@Override
			public void actionPerformed(ActionEvent e) {
				handleMessage(message, allMessages);
			}
		});

		message.addKeyListener(new KeyAdapter() {
			@Override
			public void keyPressed(KeyEvent e) {
				if(e.getKeyCode() == KeyEvent.VK_ENTER) {
					if(!e.isShiftDown()) {
						handleMessage(message, allMessages);
						e.consume();
					}
					else {
						message.append(System.lineSeparator());
					}
				}
			}
		});

		right.add(south, BorderLayout.SOUTH);
		return right;
	}

	public static void main(String[] args) {
		ChatFrame f = new ChatFrame();
		f.createWindow();
	}
}
```

Per quanto riguarda il file *Contact.java*:
```java
package chat;

import java.awt.Image;
import javax.swing.ImageIcon;
import javax.swing.JLabel;

public class Contact extends JLabel {
	private static final long serialVersionUID = 228236008666904772L;

	public Contact(ImageIcon img, String text) {
		Image picture = img.getImage().getScaledInstance(50, 50, Image.SCALE_SMOOTH);
		this.setIcon(new ImageIcon(picture));
		this.setText(text);
	}
}
```
