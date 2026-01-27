**JavaScript** (JS) è il linguaggio di scripting fondamentale del Web. Nato nel 1996 in Netscape (chiamato inizialmente LiveScript), è oggi standardizzato come **ECMAScript** (ES).
## Introduzione e Utilizzo
JavaScript è un linguaggio interpretato, dinamico e debolmente tipizzato. Sebbene sia nato per i browser (Client-Side), oggi gira ovunque (Server con Node.js, Database come CouchDB, App Desktop con Electron).
### Vantaggi Lato Client
*   **Performance:** Riduce il carico sul server eseguendo calcoli sul dispositivo dell'utente.
*   **Interattività:** Risposta immediata agli eventi (click, input) senza ricaricare la pagina.
### Inserimento nella Pagina
1.  **Esterno (Best Practice):** File separato `.js`.
    ```html
    <script src="script.js"></script>
    ```
2.  **Embedded:** Blocco script nell'HTML.
    ```html
    <script>
      console.log("Ciao!");
    </script>
    ```
3.  **Inline (Sconsigliato):** Negli attributi HTML.
    ```html
    <button onclick="alert('Click!')">Premi</button>
    ```
## Tipi di Dati e Variabili
### Tipi Primitivi
JS ha 6 tipi di dati fondamentali:
1.  **`number`**: Interi e decimali (`42`, `3.14`). Include valori speciali come `NaN` (Not a Number) e `Infinity`.
2.  **`string`**: Testo tra apici singoli o doppi (`"Ciao"`, `'Mondo'`).
3.  **`boolean`**: `true` o `false`.
4.  **`undefined`**: Valore di una variabile dichiarata ma non inizializzata.
5.  **`null`**: Assenza intenzionale di valore (è un oggetto per un bug storico).
6.  **`symbol`** (Introdotto in ES6, per identificatori unici).
### Variabili e Scope
*   **`var`**: Scope di funzione (o globale). Soggetta a **Hoisting** (può essere usata prima della dichiarazione, risultando `undefined`).
*   **`let` / `const`** (ES6+): Scope di blocco (`{}`). `const` non può essere riassegnata.
```javascript
var x = 10;
if (true) {
    var x = 20; // Modifica la x globale!
}
console.log(x); // 20

let y = 10;
if (true) {
    let y = 20; // Nuova variabile locale al blocco
}
console.log(y); // 10
```

> [!WARNING] Hoisting
> Con `var`, le dichiarazioni vengono "spostate" in cima alla funzione.
> ```javascript
> console.log(a); // undefined (non errore!)
> var a = 5;
> ```
## Operatori e Uguaglianza
### Coercizione (Type Coercion)
JS cerca di convertire automaticamente i tipi.
```javascript
console.log(5 == "5");  // true (converte stringa in numero)
console.log(0 == false); // true
console.log("" == 0);    // true
```
### Uguaglianza Stretta (`===`)
Confronta sia il valore che il tipo. **Usare sempre questo.**
```javascript
console.log(5 === "5");  // false
console.log(0 === false); // false
```
### Operatori Logici e Ternario
*   **AND (`&&`)**: Ritorna il primo `falso` o l'ultimo valore.
*   **OR (`||`)**: Ritorna il primo `vero` o l'ultimo valore.
*   **Ternario (`? :`)**: `condizione ? vero : falso`.
## Oggetti
Un oggetto è una collezione di coppie chiave-valore.
```javascript
// Object Literal
var persona = {
    nome: "Mario",
    eta: 30,
    saluta: function() {
        return "Ciao sono " + this.nome;
    }
};

// Accesso proprietà
console.log(persona.nome);    // Dot notation
console.log(persona["eta"]);  // Bracket notation (utile per chiavi dinamiche)

// Modifica dinamica
persona.lavoro = "Developer"; // Aggiunge nuova proprietà
delete persona.eta;           // Rimuove proprietà
```
## Funzioni
Le funzioni sono "cittadini di prima classe" (possono essere passate come variabili).
### Dichiarazione
```javascript
function somma(a, b) {
    return a + b;
}
```
### Funzione Anonima (Function Expression)
```javascript
var quadrato = function(x) {
    return x * x;
};
```
### Scope e Closure
Le funzioni interne "vedono" le variabili della funzione esterna.
```javascript
function esterno(x) {
    return function interno(y) {
        return x + y; // 'x' è ricordata (Closure)
    };
}
var aggiungi5 = esterno(5);
console.log(aggiungi5(10)); // 15
```
## Array
Liste ordinate di valori (anche misti).
```javascript
var lista = ["mela", "pera", 42];

lista.push("banana"); // Aggiunge in fondo
lista.pop();          // Rimuove l'ultimo
lista.length;         // Lunghezza (3)

// Iterazione
for (var i = 0; i < lista.length; i++) {
    console.log(lista[i]);
}
```
## Oggetti Globali Utili
*   **`Math`**: `Math.random()`, `Math.floor()`, `Math.max()`.
*   **`Date`**: Gestione date (`new Date()`).
*   **`JSON`**: Parsing e serializzazione dati.
    ```javascript
    var str = JSON.stringify({a: 1}); // '{"a":1}'
    var obj = JSON.parse(str);        // {a: 1}
    ```
*   **`Window`**: L'oggetto globale del browser (contiene `document`, `alert`, `console`).
