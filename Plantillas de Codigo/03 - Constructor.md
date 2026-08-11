# 03 - Constructor

```java
public class NombreClase {
    private TipoDato nombreAtributo;
    private TipoDato otroAtributo;

    public NombreClase(TipoDato nombreAtributo) {
        this.nombreAtributo = nombreAtributo;
    }

    public NombreClase(TipoDato nombreAtributo, TipoDato otroAtributo) {
        this(nombreAtributo);
        this.otroAtributo = otroAtributo;
    }
}
```

**Cuando se usa:** para inicializar un objeto en el momento de crearlo con new, garantizando que el objeto nace en un estado valido.

**Reglas:**
- Mismo nombre que la clase, sin tipo de retorno (ni siquiera void)
- this.nombreAtributo = nombreAtributo desambigua parametro de campo
- this(...) delega a otro constructor y va SIEMPRE en la primera linea
- Puedes tener varios constructores (sobrecarga) con distintos parametros
- Si no defines ninguno, Java crea el constructor por defecto (sin parametros)

---

## Ejemplo de uso

```java
public class Producto {
    private final String codigo;
    private double precio;

    public Producto(String codigo) {
        this.codigo = codigo;
    }

    public Producto(String codigo, double precio) {
        this(codigo);
        this.precio = precio;
    }
}
```

**Ver tema:** [[07 - Constructores y this]]