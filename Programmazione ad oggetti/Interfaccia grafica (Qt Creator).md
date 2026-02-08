Breve guida agli elementi fondamentali per creare GUI con Qt.
- **Struttura File:** Headers, Sources e Forms (`.ui`) .
- **Segnali e Slot:** Meccanismo per gestire gli eventi (es. click su un bottone -> `Go to slot`).
- **Widget Principali:**
    - `QLineEdit / QTextEdit`: Per input di testo. Metodi `toPlainText()` (lettura) e `setPlainText()` (scrittura) .
    - `QRadioButton`: Scelta singola.
    - `QComboBox`: Menu a tendina (`addItem`, `currentIndex`) .
    - `QListWidget`: Lista di elementi.
    - `QStackedWidget`: Per gestire più "pagine" nella stessa finestra.

Nota sull'uso delle stringhe in Qt: Spesso è necessario convertire tra `std::string` e `QString` (`QString::fromStdString` o `QString::number`) .