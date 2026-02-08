### Composizione ("Ha un...")
Quando una classe contiene istanze di altre classi come attributi. Ad esempio, un `Supermercato` _ha una_ lista di prodotti .
### Ereditarietà ("È un...")
Permette di creare nuove classi basate su classi esistenti, ereditandone attributi e metodi.
- **Superclasse (Base):** `Persona`.
- **Sottoclasse (Derivata):** `Studente` (che _è una_ `Persona`).

```c++
class Studente : public Persona {
    // Studente eredita tutto ciò che è public/protected in Persona
};
```
