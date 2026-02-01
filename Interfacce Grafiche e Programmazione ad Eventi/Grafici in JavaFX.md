Nella cartella *src/main/java* andremo ad elencare i file di seguito, mentre nella cartella *src/main/resources* package *application* troveremo l'interfaccia grafica.

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

Per quanto riguarda il file *MainApplication.java*:

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
		FXMLLoader loader = new FXMLLoader(getClass().getResource("/application/Main.fxml"));
		AnchorPane root = (AnchorPane) loader.load();
		Scene scene = new Scene(root, 600, 600);
		primaryStage.setScene(scene);
		primaryStage.setTitle("Report vaccinazioni");
		primaryStage.show();
	}

	public static void main(String[] args) {
		launch(args);
	}
}
```

Per quanto riguarda il file *DatiVaccinazioni.java*:

```java
package application;

public class DatiVaccinazioni implements Comparable<DatiVaccinazioni>{
	private String data;
	private String regione;
	private int primaDose;
	private int secondaDose;

	public DatiVaccinazioni(String data, String regione, int primaDose, int secondaDose) {
		super();
		this.data = data;
		this.regione = regione;
		this.primaDose = primaDose;
		this.secondaDose = secondaDose;
	}

	public String getData() {
		return data;
	}

	public String getRegione() {
		return regione;
	}

	public int getPrimaDose() {
		return primaDose;
	}

	public int getSecondaDose() {
		return secondaDose;
	}

	@Override
	public int compareTo(DatiVaccinazioni o) {
		return data.compareTo(o.data);
	}
}
```

Per quanto riguarda il file *DataService.java*:

```java
package application;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.ArrayList;
import java.util.List;
import javafx.concurrent.Service;
import javafx.concurrent.Task;

public class DataService extends Service<List<DatiVaccinazioni>> {
	private final String dati = "https://raw.githubusercontent.com/italia/covid19-opendata-vaccini/
	    master/dati/somministrazioni-vaccini-summary-latest.csv";

	@Override
	protected Task<List<DatiVaccinazioni>> createTask() {
		return new Task<List<DatiVaccinazioni>>() {

			@Override
			protected List<DatiVaccinazioni> call() throws Exception {
				URL csvDati = new URL(dati);
				BufferedReader reader = new BufferedReader(new InputStreamReader(csvDati.openStream()));
				String line = reader.readLine();
				line = reader.readLine();
				ArrayList<DatiVaccinazioni> l = new ArrayList<DatiVaccinazioni>();
				while(line != null) {
					String[] res = line.split(",");
					l.add(new DatiVaccinazioni(res[0], res[10], Integer.parseInt(res[5]), Integer.parseInt(res[6])));
					line = reader.readLine();
				}
				reader.close();
				return l;
			}
		};
	}
}
```

## PACKAGE application.controller

Per quanto riguarda il file *MainController.java*:

```java
package application.controller;

import java.util.Collections;
import java.util.List;
import application.DataService;
import application.DatiVaccinazioni;
import javafx.concurrent.WorkerStateEvent;
import javafx.event.ActionEvent;
import javafx.event.EventHandler;
import javafx.fxml.FXML;
import javafx.scene.chart.BarChart;
import javafx.scene.chart.CategoryAxis;
import javafx.scene.chart.NumberAxis;
import javafx.scene.chart.XYChart;
import javafx.scene.control.Button;
import javafx.scene.control.DatePicker;
import javafx.scene.control.TextField;
import javafx.scene.layout.BorderPane;

public class MainController {
	@FXML
	private BorderPane borderPane;

	@FXML
	private Button processButton;

	@FXML
	private DatePicker data;

	@FXML
	private TextField regione;

	private DataService service = new DataService();

	@FXML
	void process(ActionEvent event) {
		service.restart();
	}

	@FXML
	void initialize() {
		service.setOnSucceeded(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				if(data.getValue() == null)
					return;
				int year = data.getValue().getYear();
				int month = data.getValue().getMonthValue();
				String m = "" + month;
				if(month < 10)
					m = "0" + month;
				int day = data.getValue().getDayOfMonth();
				String dd = "" + day;
				if(day < 10)
					dd = "0" + day;
				String date = year + "-" + m + "-" + dd;

				CategoryAxis xAxis = new CategoryAxis();
				NumberAxis yAxis = new NumberAxis();
				BarChart<String, Number> chart = new BarChart<String, Number>(xAxis, yAxis);
				chart.setTitle("Somministrazioni vaccini - Regione: " + regione.getText());
				xAxis.setLabel("Data");
				yAxis.setLabel("Dosi");

				@SuppressWarnings("unchecked")
				List<DatiVaccinazioni> result = (List<DatiVaccinazioni>) event.getSource().getValue();
				XYChart.Series<String, Number> primaDose = new XYChart.Series<String, Number>();
				primaDose.setName("Prima dose");
				XYChart.Series<String, Number> secondaDose = new XYChart.Series<String, Number>();
				secondaDose.setName("Seconda dose");

				Collections.sort(result);
				for(DatiVaccinazioni d : result) {
					if(d.getRegione().equalsIgnoreCase(regione.getText()) && d.getData().compareTo(date) >= 0) {
						primaDose.getData().add(new XYChart.Data<String, Number>(d.getData(), d.getPrimaDose()));
						secondaDose.getData().add(new XYChart.Data<String, Number>(d.getData(), d.getSecondaDose()));
					}
				}
				chart.getData().add(primaDose);
				chart.getData().add(secondaDose);
				borderPane.setCenter(chart);
			}
		});

		service.setOnFailed(new EventHandler<WorkerStateEvent>() {
			@Override
			public void handle(WorkerStateEvent event) {
				System.err.println(event.getSource().getException().getMessage());
			}

		});
	}
}
```
