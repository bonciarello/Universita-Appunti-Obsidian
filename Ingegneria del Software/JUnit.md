---
aliases: [junit]
tags: [ingegneria-del-software]
---
Il testing è un metodo empirico per verificare la correttezza di un software ed è un processo automatizzato volto a mostrare il comportamento di un software su un dato input:

Abbiamo due categorie: 

- **Black box:** dato un input, verifica se il software fornisce il risultato atteso, ignorando come funziona realmente il software;
- **White box:** vengono testate singole porzioni del codice sorgente.

**Unit Testing** è una metodologia di test della scatola bianca che verifica l'*unità di un codice sorgente*.

Un'**unità** è la più piccola porzione di codice che può essere testata: nella programmazione procedurale può essere un singolo programma o una funzione. In Java può essere una classe, un'interfaccia o anche un metodo.

Unit Testing è il test di un'unità specifica.

## Perchè Unit Testing?

Un codice sorgente non può essere considerato corretto senza essere verificato. Possiamo verificare in due modi:

- **Approccio *Divide-et-impera*:** suddividi il sistema in unità, ogni unità viene sottoposta a debug separatamente, ridurre la probabilità di presentare bug e gli errori non vengono propagati tra le unità;
- **Test di supporto [[Regressione|regressione]]:** verificare che l'applicazione funzioni come specificato anche dopo che sono state apportate modifiche/aggiunte/modifica. La funzionalità originale continua a funzionare come specificato anche dopo modifiche/aggiunte/modifica all'applicazione software. Le modifiche/integrazioni/modifica all'applicazione software non hanno introdotto alcun nuovo bug.

## JUnit

Il test unitario può essere eseguito da un software. **JUnit** è un framework Java Unit Testing: API per creare facilmente test, strutture di asserzione complete (risultato previsto rispetto a quello effettivo), corridore di prova per i test di corsa e strutture di aggregazione di prova.

I concetti base di JUnit:

- **Test Case:** un metodo che verifica una funzionalità specifica di un'unità
- **Test Suite:** una raccolta di Unit Test

Le convenzioni di JUnit sono:

- il nome di un metodo di test case dovrebbe indicare il comportamento previsto (per esempio, va bene *sqrtWorks*, *minWorks*, ecc. mentre non va bene *test1*, *myTest*, ecc.);
- le classi di test di solito terminano il loro nome con *"Test"* (per esempio, va bene *MathTest*, *PersistenceTest*, ecc. mentre non va bene *MyClass*, *Test1*, ecc.);

JUnit è un **annotation driven** (cioè la chiocciola sopra la funzione) e non è necessario estendere alcuna classe speciale.

I casi di test sono annotati con *@Test*: i metodi di prova sono nulli e non accettano parametri ma possiamo inserire informazioni extra suggeriscono comportamenti specifici come per esempio:

- *@Test(timeout = 10)*, il test ha successo se termina entro 10 secondi;
- *@Test(expected = ...)*, il test ha successo se l'eccezzione non si verifica;
- *@Ignore("reason")*, il test viene "ignorato".

Altre annotazioni sono:

- *@Before*, contrassegnare un metodo per essere invocato prima di ogni test case;
- *@After*, contrassegnare un metodo per essere invocato dopo ogni test case;
- *@BeforeClass*, contrassegnare un metodo per essere invocato all'inizio del test;
- *@AfterClass*, contrassegnare un metodo per essere invocato alla fine del test;
- *@Before* e *@After* hanno lo scopo di preparare/rilasciare il dispositivo di prova per ogni caso di test;
- *@BeforeClass* e *@AfterClass* sono metodi *statici* e devono apparire al massimo una volta in ogni test.

Parametrized Tests ci permette di eseguire un determinato test case con dei parametri predefiniti.

Usando *@RunWith* (*@Parameterized.class*) e un parametro contrassegnato con *@Parameters*, possiamo eseguire un test su più valori del parametro.

```java
@RunWith(value=Parameterized.class)
public class FactorialTest {
    private long expected;
    private int value;
    @Parameters
    public static Collection<Object[]> data() {
        return Arrays.asList(new Object[][]{{1,0},{1,1},{2,2},{120,5}});
    }
    public FactorialTest(long expected, int value) { // constructor
        this.expected = expected;
        this.value = value;
    }
    @Test
    public void factorial() {
        assertEquals(expected, new Calculator().factorial(value));
    }
}
```

Test Suites raggruppano i test in gerarchie:

```java
@RunWith(value=Suite.class)
@SuiteClasses(value={MyProgramTest.class, AnotherTest.class})
public class AllTests{
    ...
}
```

Abbiamo anche degli asserts:

- *assertEquals(expected, actual)*, lavora sia con oggetti, int, long, byte, string, ecc. Vede se i due elementi sono uguali, se no fallisce il test case;
- *assertEquals(expected, actual, epsilon)*, con float e double;
- *assertTrue/assertFalse(bool)*;
- *assertNull/assertNotNull(object)*;
- *assertSame/assertNotSame(object, object)*;
- *assertArrayEquals (object[], object[])*.
