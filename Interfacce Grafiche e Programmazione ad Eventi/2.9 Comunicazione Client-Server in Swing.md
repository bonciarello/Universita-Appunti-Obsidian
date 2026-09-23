---
aliases: [CS, Comunicazione Client-Server Swing]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
## PARTE Server

Per quanto riguarda il file *Main.java*:

```java
import java.awt.GridLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;

public class Main {
	public static void main(String[] args) {
		JFrame f = new JFrame("Server");
		f.setSize(400,400);
		JPanel p = new JPanel();
		p.setLayout(new GridLayout(2, 1));

		JButton startServer = new JButton("Start server");
		JTextArea area = new JTextArea();

		startServer.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				Server server = new Server(area);
				server.startServer();
				startServer.setEnabled(false);
			}
		});

		p.add(startServer);
		p.add(new JScrollPane(area));
		f.add(p);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		f.setVisible(true);
	}
}
```

Per quanto riguarda il file *MessagesHandler.java*:

```java
import java.io.BufferedOutputStream;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import javax.swing.JTextArea;

public class MessagesHandler implements Runnable {
	private Socket socket;
	private JTextArea area;

	public MessagesHandler(Socket socket, JTextArea area) {
		this.socket = socket;
		this.area = area;
	}

	@Override
	public void run() {
		try {
			BufferedOutputStream bOut = new BufferedOutputStream(socket.getOutputStream());
			PrintWriter out = new PrintWriter(bOut, true);
			out.println("Welcome!");

			BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
			while(!Thread.currentThread().isInterrupted()) {
				if(in.ready()) {
					String line = in.readLine();
					area.append("Client wrote: " + line + System.lineSeparator());
					if(line.equals("ciao"))
						out.println("ciao a te!");
					else if(line.equals("come stai?"))
						out.println("bene, tu?");
				}
			}
		} catch (IOException e) {
			area.append("Connection error: " + e.getMessage() + System.lineSeparator());
		}
	}
}
```

Per quanto riguarda il file *Server.java*:

```java
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import javax.swing.JTextArea;

public class Server implements Runnable {
	private JTextArea area;
	private ServerSocket server;
	private ExecutorService executor;

	public Server(JTextArea area) {
		this.area = area;
		executor = Executors.newCachedThreadPool();
	}

	public void startServer() {
		try {
			server = new ServerSocket(8000);
			Thread t = new Thread(this);
			t.start();
		} catch (IOException e) {
			area.append("Error while starting the server: " + e.getMessage() + System.lineSeparator());
		}
	}

	@Override
	public void run() {
		while (!Thread.currentThread().isInterrupted()) {
			try {
				area.append("Waiting for new connections..." + System.lineSeparator());
				Socket socket = server.accept();
				area.append("Client connected." + System.lineSeparator());
				MessagesHandler handler = new MessagesHandler(socket, area);
				executor.submit(handler);
			} catch (IOException e) {
				area.append("Connection error: " + e.getMessage() + System.lineSeparator());
				return;
			}
		}
	}
}
```

## PARTE Client

Per quanto riguarda il file *Main.java*:

```java
import java.awt.GridLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;

public class Main {
	public static void main(String[] args) {
		JFrame f = new JFrame("Client");
		f.setSize(400,400);
		JPanel p = new JPanel();
		p.setLayout(new GridLayout(3, 1));

		JButton startServer = new JButton("Send message");
		JTextArea area = new JTextArea();
		area.setEditable(false);
		JTextArea myMessages = new JTextArea();
		Client c = new Client(area);
		c.startClient();
		startServer.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				c.sendMessage(myMessages.getText());
				myMessages.setText("");
			}
		});

		p.add(startServer);
		p.add(new JScrollPane(area));
		p.add(new JScrollPane(myMessages));
		f.add(p);
		f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		f.setVisible(true);
	}
}
```

Per quanto riguarda il file *Client.java*:

```java
import java.io.BufferedOutputStream;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;
import javax.swing.JTextArea;

public class Client implements Runnable {
	private JTextArea area;
	private Socket socket;

	public Client(JTextArea area) { this.area = area; }

	public void startClient() {
		try {
			socket = new Socket("localhost", 8000);
			Thread t = new Thread(this);
			t.start();
		} catch (IOException e) {
			area.append("Connection error: " + e.getMessage() + System.lineSeparator());
		}
	}

	public void sendMessage(String text) {
		if(socket == null)
			return;
		try {
			BufferedOutputStream bOut = new BufferedOutputStream(socket.getOutputStream());
			PrintWriter out = new PrintWriter(bOut, true);
			out.println(text);
		} catch (IOException e) {
			area.append("Connection error: " + e.getMessage() + System.lineSeparator());
		}
	}

	@Override
	public void run() {
		if(socket == null)
			return;
		try {
			BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
			while(!Thread.currentThread().isInterrupted()) {
				if(in.ready()) {
					area.append("Server wrote: " + in.readLine() + System.lineSeparator());
				}
			}
		} catch (IOException e) {
			area.append("Connection error: " + e.getMessage() + System.lineSeparator());
		}
	}
}
```
