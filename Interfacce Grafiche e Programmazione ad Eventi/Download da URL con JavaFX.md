Nella cartella *src/main/java* andremo ad elencare i *package* di seguito, mentre nella cartella *src/main/resources* si trova il file css *style.css*. Tutto il programma gira sul view *MainWindow.fxml* presente in *src/main/java/application/view*. Il file *style.css* è il seguente:

```css
// questa modifica di stile vale per tutti gli oggetti Button
.button {
	-fx-background-color: red;
}

// questa modifica di stile vale per tutti gli oggetti che richiamano lo stile "mystyle"
.mystyle {
	-fx-background-color: green;
}
```

## PACKAGE application

Per quanto riguarda il file *Main.java*:

```java
package application;

public class Main {
	public static void main(String[] args) {
		MainApplication.main(args);
	}
}
```

Per quanto riguarda il file *MainApplication.java* (una sorta di main):

```java
package application;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Scene;
import javafx.scene.layout.AnchorPane;
import javafx.stage.Stage;

public class MainApplication extends Application {
	@Override
	public void start(Stage primaryStage) throws Exception {
		FXMLLoader loader1 = new FXMLLoader(getClass().getResource("/application/view/MainWindow.fxml"));
		AnchorPane root1 = (AnchorPane) loader1.load();
		Scene scene = new Scene(root1,600,600);
		scene.getStylesheets().add(getClass().
		    getResource("/application/css/style.css").toExternalForm());
		primaryStage.setTitle("Load");
		primaryStage.setScene(scene);
		primaryStage.setMinHeight(600);
		primaryStage.setMaxHeight(600);
		primaryStage.show();
	}

	public static void main(String[] args) { launch(args); }
}
```

Per quanto riguarda il file *DownloadFromUrl.java* (sia download pagina che download immagine):

```java
package application;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;
import javafx.concurrent.Service;
import javafx.concurrent.Task;
import javafx.scene.image.Image;

public class DownloadFromUrl extends Service<Object> {
	private String urlText;
	private boolean downloadImage;

	@Override
	protected Task<Object> createTask() {
		return new Task<Object>() {
			@Override
			protected Object call() throws Exception {
				if (downloadImage) {
					Image image = new Image(urlText, 100, 100, true, true);
					return image;
				} else {
					URL url = new URL(urlText);
					BufferedReader reader = new BufferedReader(new InputStreamReader(url.openStream()));
					StringBuilder builder = new StringBuilder();
					while (reader.ready()) {
						String line = reader.readLine();
						builder.append(line);
					}
					reader.close();
					return builder.toString();
				}
			}
		};
	}

	public void setUrlAndType(String text, boolean downloadImage) {
		urlText = text;
		this.downloadImage = downloadImage;
	}
}
```

## PACKAGE application.controller

Per quanto riguarda il file *MainWindowController.java*:

```java
package application.controller;

import application.DownloadFromUrl;
import javafx.concurrent.WorkerStateEvent;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.fxml.FXML;
import javafx.scene.control.Alert;
import javafx.scene.control.Alert.AlertType;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.TextArea;
import javafx.scene.control.TextField;
import javafx.scene.image.Image;
import javafx.scene.image.ImageView;
import javafx.scene.layout.BorderPane;

public class MainWindowController {
	@FXML
	private BorderPane borderPane;

	@FXML
	private Button loadImageButton;

	@FXML
	private Button loadButton;

	@FXML
	private TextField urlField;

	private DownloadFromUrl downloadFromUrl = new DownloadFromUrl();

	@FXML
	void load(ActionEvent event) {
		String text = urlField.getText();
		downloadFromUrl.setUrlAndType(text, false);
		downloadFromUrl.restart();
	}

	@FXML
	void loadImage(ActionEvent event) {
		String text = urlField.getText();
		downloadFromUrl.setUrlAndType(text, true);
		downloadFromUrl.restart();
	}

	@FXML
	void initialize() {
		loadButton.getStyleClass().add("mystyle");
		downloadFromUrl.setOnSucceeded(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				if (event.getSource().getValue() instanceof String) {
					String result = (String) event.getSource().getValue();
					TextArea t = new TextArea();
					t.setText(result);
					t.setEditable(false);
					borderPane.setCenter(t);
				} else if(event.getSource().getValue() instanceof Image) {
					Image img = (Image) event.getSource().getValue();
					Label l = new Label("", new ImageView(img));
					borderPane.setCenter(l);
				}
			}
		});
		downloadFromUrl.setOnFailed(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				Alert alert = new Alert(AlertType.ERROR);
				alert.setTitle("Connection error");
				alert.setContentText("Cannot load URL: check your connection and the inserted URL");
				alert.showAndWait();
			}
		});
	}
}
```
