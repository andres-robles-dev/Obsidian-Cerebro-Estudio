# 29 - Clase Generica

```java
public class NombreClase<TipoGenerico> {

    private final TipoGenerico nombreAtributo;

    public NombreClase(TipoGenerico nombreAtributo) {
        this.nombreAtributo = nombreAtributo;
    }

    public TipoGenerico getNombreAtributo() {
        return nombreAtributo;
    }

    public boolean tieneValor() {
        return nombreAtributo != null;
    }
}

// Version con tipo acotado (solo Number o subclases):
public class NombreClase<TipoGenerico extends Number> {

    private final TipoGenerico nombreAtributo;

    public NombreClase(TipoGenerico nombreAtributo) {
        this.nombreAtributo = nombreAtributo;
    }

    public double duplicar() {
        return nombreAtributo.doubleValue() * 2;
    }
}

// Metodo generico (tipo solo de un metodo):
public static <TipoGenerico> List<TipoGenerico> nombreMetodo(List<TipoGenerico> coleccion) {
    return coleccion;
}
```

**Cuando se usa:** cuando una clase propia debe funcionar con VARIOS tipos sin cambiar la logica (cajas, resultados, repositorios, pares clave-valor).

**Reglas:**
- El `<TipoGenerico>` se declara en la clase y viaja por campos, constructor, parametros y retornos
- Solo acepta tipos REFERENCIA (nunca primitivos: usa `Integer`, `Double`...)
- Al usarla: `NombreClase<String> variable = new NombreClase<>("valor");` (diamond <>)
- Metodo generico: `<TipoGenerico>` va ANTES del tipo de retorno
- `T extends Number` restringe el tipo a numeros (o cualquier superclase/interfaz)

---

## Ejemplo de uso

```java
public class Caja<T> {
    private final T contenido;

    public Caja(T contenido) {
        this.contenido = contenido;
    }

    public T getContenido() {
        return contenido;
    }

    public boolean contieneAlgo() {
        return contenido != null;
    }
}

// Uso:
Caja<String> cajaTexto   = new Caja<>("Hola");
Caja<Integer> cajaNumero = new Caja<>(42);

String texto = cajaTexto.getContenido();      // sin cast: T ya es String
Integer numero = cajaNumero.getContenido();   // sin cast: T ya es Integer

// El generico mas usado del JDK es Optional<T> (tema 41):
Optional<String> nombre = Optional.of("Ana");
String valor = nombre.orElse("Sin nombre");
```

**Ver temas:** [[45 - Genericos en Profundidad]] - [[41 - Optional en Profundidad]] - [[15 - Listas y ArrayList]]