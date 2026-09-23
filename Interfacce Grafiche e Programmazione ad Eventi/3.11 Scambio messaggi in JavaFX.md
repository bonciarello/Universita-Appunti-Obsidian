---
aliases: [Scambio messaggi JavaFX, SMJ]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Nella cartella *src/main/java* andremo ad elencare i *package* di seguito, mentre nel package application.view troveremo le interfacce grafiche in *.fxml*.

## PACKAGE application

Per quanto riguarda il file *Main.java* (per far avviare la parte Client):

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
import javafx.stage.Stage;

public class MainApplication extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		SceneHandler.getInstance().init(primaryStage);
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```

Per quanto riguarda il file *SceneHandler.java*:

```java
package application;

import application.controller.ChatFrameController;
import application.net.client.Client;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Alert.AlertType;
import javafx.stage.Stage;

public class SceneHandler {
    private Scene scene;
    private Stage stage;
    private static SceneHandler instance = null;
    private SceneHandler() {}

    public void init(Stage primaryStage) throws Exception {
    	stage = primaryStage;
    	FXMLLoader loader = new FXMLLoader(getClass().getResource("/application/view/Login.fxml"));
        Parent root = (Parent) loader.load();
    	scene = new Scene(root, 300, 200);
    	stage.setScene(scene);
    	stage.setTitle("Chat");
    	stage.setResizable(false);
    	stage.show();
    }

    public static SceneHandler getInstance() {
    	if(instance == null)
    		instance = new SceneHandler();
    	return instance;
    }

    public void setChatScene() throws Exception {
    	FXMLLoader loader = new FXMLLoader(getClass().getResource("/application/view/ChatFrame.fxml"));
    	Parent root = (Parent) loader.load();
    	ChatFrameController controller = loader.getController();
    	Thread t = new Thread(Client.getInstance());
    	t.setDaemon(true);
    	t.start();
    	controller.start();
    	scene.setRoot(root);
    	stage.hide();
    	stage.setResizable(true);
    	stage.setWidth(800);
    	stage.setHeight(800);
    	stage.show();
    }

    public void showError(String message) {
    	Alert alert = new Alert(AlertType.ERROR);
		alert.setTitle("Error");
		alert.setHeaderText("");
		alert.setContentText(message);
		alert.show();
    }
}
```

## PACKAGE application.controller

Per quanto riguarda il file *ChatFrameController.java*:

```java
package application.controller;

import java.util.ArrayList;
import application.SceneHandler;
import application.net.client.Client;
import application.net.client.Messages;
import javafx.animation.AnimationTimer;
import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.scene.control.Button;
import javafx.scene.control.TextArea;

public class ChatFrameController extends AnimationTimer {
	@FXML
	private TextArea message;

	@FXML
	private TextArea allMessages;

	@FXML
	private Button sendButton;

	private long previousTime = 0;
	private long frequency = 500 * 1000000;

	@FXML
	void sendMessage(ActionEvent event) {
		if(!Client.getInstance().sendMessage(message.getText()))
			SceneHandler.getInstance().showError("Lost connection");;
		message.setText("");
	}

	@Override
	public void handle(long now) {
		if(now - previousTime >= frequency) {
			ArrayList<String> all = Messages.readMessages();
			for(String s : all) {
				allMessages.appendText(s+System.lineSeparator());
			}
			previousTime = now;
		}
	}
}
```

Per quanto riguarda il file *LoginController.java*:

```java
package application.controller;

import application.SceneHandler;
import application.net.client.Client;
import application.net.common.Protocol;
import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.PasswordField;
import javafx.scene.control.TextField;

public class LoginController {
    @FXML
    private Label result;

    @FXML
    private PasswordField password;

    @FXML
    private Button login;

    @FXML
    private TextField username;

    @FXML
    private Button register;

    @FXML
    void registerAction(ActionEvent event) {
        authentication(false);
    }

    @FXML
    void loginAction(ActionEvent event) {
        authentication(true);
    }

    private void authentication(boolean login) {
    	String res = Client.getInstance().authentication(username.getText(), password.getText(), login);
    	if(res.equals(Protocol.OK)) {
    		try {
				SceneHandler.getInstance().setChatScene();
			} catch (Exception e) {
				SceneHandler.getInstance().showError("error while loading the chat");
			}
    	}
    	else {
    		SceneHandler.getInstance().showError(res);
    		Client.getInstance().reset();
    	}
    }
}
```

## PACKAGE application.net.client

Per quanto riguarda il file *Client.java*:

```java
package application.net.client;

import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.Socket;
import application.SceneHandler;
import application.net.common.Protocol;
import application.net.common.User;

public class Client implements Runnable {
	private static Client instance = null;
	private Socket socket;
	private ObjectOutputStream out;
	private ObjectInputStream in;

	private Client() {
		try {
			socket = new Socket("localhost", 8000);
			out = new ObjectOutputStream(socket.getOutputStream());
		} catch (IOException e) {
			out = null;
			SceneHandler.getInstance().showError("Cannot connect to the server");
		}
	}

	public static Client getInstance() {
		if(instance == null)
			instance = new Client();
		return instance;
	}

	@Override
	public void run() {
		while(out != null && in != null) {
			try {
				String mess = (String) in.readObject();
				Messages.addMessage(mess);
			} catch (Exception e) {
				out = null;
				SceneHandler.getInstance().showError("Lost connection");
			}
		}
	}

	public void reset() {
		instance = null;
		out = null;
		in = null;
		socket = null;
	}

	public String authentication(String username, String password, boolean login) {
		if(login)
			sendMessagePrivate(Protocol.LOGIN);
		else {
			//Check if password has at least 6 characters
			sendMessagePrivate(Protocol.REGISTRATION);
		}
		sendMessagePrivate(new User(username, password));
		try {
			in = new ObjectInputStream(socket.getInputStream());
			String res = (String) in.readObject();
			return res;
		} catch (Exception e) {
			out = null;
			return Protocol.ERROR;
		}
	}

	private boolean sendMessagePrivate(Object message) {
		if(out == null)
			return false;
		try {
			out.writeObject(message);
			out.flush();
		} catch (IOException e) {
			out = null;
			return false;
		}
		return true;
	}

	public boolean sendMessage(String message) {
		return sendMessagePrivate(message);
	}
}
```

Per quanto riguarda il file *Messages.java*:

```java
package application.net.client;

import java.util.ArrayList;

public class Messages {
	private static ArrayList<String> allMessages = new ArrayList<String>();

	public synchronized static void addMessage(String message) {
		allMessages.add(message);
	}

	public synchronized static ArrayList<String> readMessages() {
		ArrayList<String> tmp = new ArrayList<String>();
		for(String mess : allMessages)
			tmp.add(mess);
		allMessages.clear();
		return tmp;
	}
}
```

## PACKAGE application.net.common

Per quanto riguarda il file *Protocol.java*:

```java
package application.net.common;

public class Protocol {
	public final static String LOGIN = "login";
	public final static String REGISTRATION = "registration";

	public final static String OK = "ok";
	public final static String ERROR = "error during connection";
	public final static String AUTHENTICATION_ERROR = "invalid username/password";
	public final static String USER_LOGGED_ERROR = "user already logged in";
	public final static String USER_EXISTS_ERROR = "user exists";
}
```

Per quanto riguarda il file *User.java*:

```java
package application.net.common;

import java.io.Serializable;

public class User implements Serializable {
	private static final long serialVersionUID = -7962764615375068572L;

	private String username;
	private String password;

	public User(String username, String password) {
		this.username = username;
		this.password = password;
	}

	public String getUsername() {
		return username;
	}

	public String getPassword() {
		return password;
	}
}
```

## PACKAGE application.net.server

Per quanto riguarda il file *DatabaseHandler.java*:

```java
package application.net.server;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import org.springframework.security.crypto.bcrypt.BCrypt;
import application.net.common.User;

public class DatabaseHandler {
	private static DatabaseHandler instance = null;
	private Connection con = null;

	private DatabaseHandler() {
		try {
			con = DriverManager.getConnection("jdbc:sqlite:Database.db");
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static DatabaseHandler getInstance() {
		if(instance == null)
			instance = new DatabaseHandler();
		return instance;
	}

	public synchronized boolean insertUser(User user) throws SQLException {
		if(con == null || con.isClosed() || user == null)
			return false;

		if(existsUser(user))
			return false;

		PreparedStatement p = con.prepareStatement("INSERT INTO users VALUES(?, ?);");
		p.setString(1, user.getUsername());
		p.setString(2, BCrypt.hashpw(user.getPassword(), BCrypt.gensalt(12)));
		p.executeUpdate();
		p.close();
		return true;
	}

	public synchronized boolean existsUser(User user) throws SQLException {
		if(con == null || con.isClosed() || user == null)
			return false;

		String query = "SELECT * FROM users WHERE username=?;";
		PreparedStatement p = con.prepareStatement(query);
		p.setString(1, user.getUsername());
		ResultSet rs = p.executeQuery();
		boolean result = rs.next();
		p.close();
		return result;
	}

	public synchronized boolean checkUser(User user) throws SQLException {
		if(con == null || con.isClosed() || user == null)
			return false;

		String query = "SELECT * FROM users WHERE username=?;";
		PreparedStatement p = con.prepareStatement(query);
		p.setString(1, user.getUsername());
		ResultSet rs = p.executeQuery();
		boolean result = false;
		if(rs.next()) {
			String password = rs.getString("password");
			result = BCrypt.checkpw(user.getPassword(), password);
		}
		p.close();
		return result;
	}
}
```

Per quanto riguarda il file *MainServer.java* (per far avviare la parte Server):

```java
package application.net.server;

public class MainServer {
	public static void main(String[] args) {
		Server s = new Server();
		s.startServer();
	}
}
```

Per quanto riguarda il file *MessagesHandler.java*:

```java
package application.net.server;

import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.Socket;
import application.net.common.Protocol;
import application.net.common.User;

public class MessagesHandler implements Runnable {
	private Socket socket;
	private ObjectInputStream in;
	private ObjectOutputStream out;
	private String username = "";

	public MessagesHandler(Socket socket) throws IOException {
		this.socket = socket;
		this.out = new ObjectOutputStream(socket.getOutputStream());
	}

	private void closeStreams() throws IOException {
		if (out != null)
			out.close();
		out = null;
		if (in != null)
			in.close();
		in = null;
		if (socket != null)
			socket.close();
		socket = null;
	}

	@Override
	public void run() {
		try {
			this.in = new ObjectInputStream(socket.getInputStream());
			String input = (String) in.readObject();
			if (input.equals(Protocol.LOGIN)) {
				User user = (User) in.readObject();
				if (!DatabaseHandler.getInstance().checkUser(user)) {
					sendMessage(Protocol.AUTHENTICATION_ERROR);
					closeStreams();
					return;
				}
				username = user.getUsername();
			} else if (input.equals(Protocol.REGISTRATION)) {
				User user = (User) in.readObject();
				if (DatabaseHandler.getInstance().existsUser(user)) {
					sendMessage(Protocol.USER_EXISTS_ERROR);
					closeStreams();
					return;
				} else {
					if (!DatabaseHandler.getInstance().insertUser(user)) {
						sendMessage(Protocol.ERROR);
						closeStreams();
						return;
					}
				}
				username = user.getUsername();
			} else {
				sendMessage(Protocol.ERROR);
				closeStreams();
				return;
			}

			if(!UsersHandler.insertUser(username, this)) {
				sendMessage(Protocol.USER_LOGGED_ERROR);
				closeStreams();
				return;
			}
			sendMessage(Protocol.OK);
			UsersHandler.sendMessage("[SERVER] " + username + " join the chat.");
			sendMessage("[SERVER] Online users: " + UsersHandler.allUsers());
			while(true) {
				String mess = (String) in.readObject();
				UsersHandler.sendMessage("["+username+"] wrote: " + mess);
			}

		} catch (Exception e) {
			if(!username.equals("")) {
				UsersHandler.removeUser(username);
				UsersHandler.sendMessage("[SERVER] " + username + " left the chat.");
			}
			else {
				sendMessage(Protocol.ERROR);
			}
			out = null;
			return;
		}

	}

	public void sendMessage(String message) {
		if (out == null)
			return;
		try {
			out.writeObject(message);
			out.flush();
		} catch (IOException e) {
			if(!username.equals("")) {
				UsersHandler.removeUser(username);
				UsersHandler.sendMessage("[SERVER] " + username + " left the chat.");
			}
		}
	}
}
```

Per quanto riguarda il file *Server.java*:

```java
package application.net.server;

import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Server implements Runnable {
	private ServerSocket server;
	private ExecutorService executor;

	public void startServer() {
		try {
			server = new ServerSocket(8000);
			executor = Executors.newCachedThreadPool();
			Thread t = new Thread(this);
			t.start();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	@Override
	public void run() {
		while(true) {
			try {
				System.out.println("[SERVER] Waiting for connections...");
				Socket socket = server.accept();
				System.out.println("[SERVER] Client connected...");
				MessagesHandler m = new MessagesHandler(socket);
				executor.submit(m);
			} catch (IOException e) {
				return;
			}
		}
	}
}
```

Per quanto riguarda il file *UserHandler.java*:

```java
package application.net.server;

import java.util.ArrayList;
import java.util.HashMap;

public class UsersHandler {
	private static HashMap<String,MessagesHandler> users = new HashMap<String, MessagesHandler>();

	public synchronized static boolean insertUser(String username, MessagesHandler handler) {
		if(users.containsKey(username))
			return false;
		users.put(username, handler);
		return true;
	}

	public synchronized static void removeUser(String username) {
		users.remove(username);
	}

	public synchronized static String allUsers() {
		ArrayList<String> onlineUsers = new ArrayList<String>();
		for(String s : users.keySet())
			onlineUsers.add(s);
		return onlineUsers.toString();
	}

	public synchronized static void sendMessage(String message) {
		for(String s : users.keySet()) {
			MessagesHandler m = users.get(s);
			m.sendMessage(message);
		}
	}
}
```
