---
aliases: [Multi-threading, Threads]
tags: [interfacce-grafiche-e-programmazione-ad-eventi]
---
Tutti i file elencati si trovano in *src/main/java* nel package *application*.

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
import javafx.concurrent.WorkerStateEvent;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.Label;
import javafx.scene.control.ProgressBar;
import javafx.scene.control.TextField;
import javafx.scene.layout.FlowPane;
import javafx.stage.Stage;

public class MainApplication extends Application {
	public static void main(String[] args) {
		launch(args);
	}

	@Override
	public void start(Stage primaryStage) throws Exception {
		CheckPrime check = new CheckPrime();
		TextField field = new TextField();
		Button b = new Button("Check prime");
		b.setOnAction(new EventHandler<ActionEvent>() {
			@Override
			public void handle(ActionEvent event) {
				String text = field.getText();
				long num = Long.parseLong(text);
				check.setNum(num);
				check.restart();
			}
		});

		Label result = new Label("Res: ");
		ProgressBar bar = new ProgressBar();

		FlowPane pane = new FlowPane();
		pane.getChildren().add(field);
		pane.getChildren().add(b);
		pane.getChildren().add(bar);
		pane.getChildren().add(result);
		bar.progressProperty().bind(check.progressProperty());

		check.setOnSucceeded(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				Boolean res = (Boolean) event.getSource().getValue();
				if(res)
					result.setText("Res: prime");
				else
					result.setText("Res: not prime");
			}
		});

		check.setOnFailed(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				System.out.println("E' avvenuta un'eccezione nel check di primalità");
			}
		});

		check.setOnCancelled(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				System.out.println("Interrupted");
			}
		});

		Scene scene = new Scene(pane, 800, 800);
		primaryStage.setScene(scene);
		primaryStage.show();
		field.setText(""+918278217);
		check.setNum(918278217);
		check.start();
	}
}
```

Per quanto riguarda il file *CheckPrime.java*:

```java
package application;

import javafx.concurrent.Service;
import javafx.concurrent.Task;

public class CheckPrime extends Service<Boolean> {
	private long num;

	public void setNum(long num) { this.num = num; }

	@Override
	protected Task<Boolean> createTask() {
		return new Task<Boolean>() {
			@Override
			protected Boolean call() throws Exception {
				boolean res = true;
				for(long i = 2; i < num; i++) {
					updateProgress(i, num);
					if(num%i==0)
						res = false;
				}
				return res;
			}
		};
	}
}
```
