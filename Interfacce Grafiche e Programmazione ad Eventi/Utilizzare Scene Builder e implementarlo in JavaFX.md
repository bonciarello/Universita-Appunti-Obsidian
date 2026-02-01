Per quanto riguarda la parte grafica, si sviluppa tutto su Scene Builder, in maniera *drag and drop*, tutti i vari controlli grafici sono gestiti come i livelli su Photoshop. Dopo aver sviluppato l'interfaccia grafica, si esporta l'interfaccia grafica come file *.fxml* e, per utilizzarlo su Eclipse, si inserisce nel package *application* di *src/main/resources*. Adesso si passa alla parte logica, si sviluppa tutto in *src/main/java*, package *application*.

Per quanto riguarda il file *Main.java*:

```java
package application;

public class Main {
	public static void main(String[] args) {
		ChatFrame.main(args);
	}
}
```

Per quanto riguarda il file *ChatFrame.java* (una sorta di main):

```java
package application;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.layout.AnchorPane;
import javafx.stage.Stage;

public class ChatFrame extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		FXMLLoader loader = new FXMLLoader(getClass().getResource("/application/ChatFrame.fxml"));
		AnchorPane root = (AnchorPane) loader.load();
		Scene scene = new Scene(root, 800, 800);
		primaryStage.setScene(scene);
		primaryStage.setTitle("ChatFrame");
		primaryStage.setMinHeight(400);
		primaryStage.setMinWidth(400);
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```

Per eseguire il programma, bisogna eseguire il *Main.java*: se eseguiamo *ChatFrame.java* non parte!

Per quanto riguarda il file *ChatFrameController.java*, si genere su JavaFX Scene Builder tramite *View → Show Sample Controller Skeleton*. Otterremo un file del genere:

```java
package application;

import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TextArea;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.VBox;

public class ChatFrameController {
    @FXML
    private VBox contatti;

    @FXML
    private TextArea message;

    @FXML
    private TextArea allMessages;

    @FXML
    private Button sendButton;

    @FXML
    void sendMessage(ActionEvent event) {
		allMessages.appendText(message.getText()+System.lineSeparator());
		message.setText("");
    }

    @FXML
    void initialize() {
    	for(int i = 1; i <= 4; i++) {
    	    // primo BOOL mantiene il rapporto, secondo BOOL scala immagine
	    	Image img = new Image(getClass().getResourceAsStream("/application/images/img"+i+".png"), 50, 50, true, true);
	    	Label l = new Label("Mario Rossi", new ImageView(img));
	    	l.setMinSize(200, 50);
	    	contatti.getChildren().add(l);
    	}
    }
}
```
