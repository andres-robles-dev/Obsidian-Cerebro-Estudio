# 03 - Constructor

```java
public class Producto {
    private final String codigo;
    private double precio;

    // Constructor principal
    public Producto(String codigo) {
        this.codigo = codigo;
    }

    // Sobrecarga: delega con this(...) y agrega logica
    public Producto(String codigo, double precio) {
        this(codigo);              // this() SIEMPRE primero
        this.precio = precio;
    }
}
```

**Cuando se usa:** para inicializar un objeto en el momento de crearlo con `new`, garantizando que el objeto nace en un estado valido.

**Reglas:**
- Mismo nombre que la clase, sin tipo de retorno (ni siquiera `void`)
- `this.campo = campo;` para desambiguar parametro de campo
- `this(...)` delega a otro constructor de la misma clase y va SIEMPRE de primera linea
- Puedes tener varios constructores (sobrecarga) con distintos parametros
- Si no defines ninguno, Java crea el constructor por defecto (sin parametros)

**Ver tema:** [[07 - Constructores y this]]
