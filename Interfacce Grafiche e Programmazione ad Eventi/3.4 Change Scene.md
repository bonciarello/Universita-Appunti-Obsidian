---
aliases: [CS]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
![](changeScene.png)

Per quanto riguarda il file *Main.java*:

```java
package application;

public class Main {
	public static void main(String[] args) {
		ChangeWindowApplication.main(args);
	}
}
```

Per quanto riguarda il file *Window1Controller.java*:

```java
package application;

import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.scene.control.Button;

public class Window1Controller {
    @FXML
    private Button button;

    @FXML
    void change(ActionEvent event) {
    	SceneHandler.setCurrent("window2");
    }
}
```

Per quanto riguarda il file *Window2Controller.java*:

```java
package application;

import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.scene.control.Button;

public class Window2Controller {
    @FXML
    private Button button;

    @FXML
    void change(ActionEvent event) {
    	SceneHandler.setCurrent("window1");
    }
}
```

Per quanto riguarda il file *SceneHandler.java*:

```java
package application;

import java.util.HashMap;

import javafx.scene.Scene;
import javafx.scene.layout.Pane;

public class SceneHandler {
    private static HashMap<String, Pane> allWindows = new HashMap<String,Pane>();
    private static Scene main;

    public static void init(Scene m) {
        main = m;
    }

    public static void add(String name, Pane pane){
         allWindows.put(name, pane);
    }

    public static void setCurrent(String name){
    	main.setRoot(allWindows.get(name));
    }
}
```

Per quanto riguarda il file *ChangeWindowApplication.java* (una sorta di main):

```java
package application;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.layout.AnchorPane;
import javafx.stage.Stage;

public class ChangeWindowApplication extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		FXMLLoader loader1 = new FXMLLoader(getClass().getResource("/application/Window1.fxml"));
		FXMLLoader loader2 = new FXMLLoader(getClass().getResource("/application/Window2.fxml"));
		AnchorPane root1 = (AnchorPane) loader1.load();
		AnchorPane root2 = (AnchorPane) loader2.load();
		Scene scene = new Scene(root1,400,400);
		SceneHandler.init(scene);
		SceneHandler.add("window1", root1);
		SceneHandler.add("window2", root2);
		primaryStage.setScene(scene);
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```
