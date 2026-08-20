---
tags: [java, inmutabilidad, list-copyof, colecciones, defensa, encapsulamiento, java-moderno]
---

# 47 - List.copyOf() (inmutabilidad)

`List.copyOf(coleccion)` crea una copia **inmutable** de cualquier coleccion: `ArrayList`, `Set`, `LinkedList`, o cualquier `Collection`. Una vez creada, la lista resultante no puede modificarse: ni `add`, ni `remove`, ni `set`, ni `clear`. Es la herramienta principal de **defensa** en Java moderno para proteger el estado interno de tus objetos.

---

## 1. Por que existe List.copyOf()

Antes de Java 9, proteger una lista requeria multiples pasos y tenia trampas ocultas:

```java
// Antes de Java 9: verboso y propenso a errores
List<String> original = new ArrayList<>();
original.add("a");
original.add("b");

// Opcion 1: Collections.unmodifiableList (envuelve pero no copia)
List<String> vista = Collections.unmodifiableList(original);
// TRAMPA: si modificas "original", "vista" tambien cambia
original.add("c");  // vista ahora tiene 3 elementos

// Opcion 2: copiar manualmente
List<String> copia = new ArrayList<>(original);
copia.add("d");
List<String> inmutable = Collections.unmodifiableList(copia);
// Funciona, pero son 2 lineas
```

Con `List.copyOf()` es una sola linea, sin trampas, y la JVM optimiza internamente:

```java
List<String> inmutable = List.copyOf(original);
// inmutable es una copia REAL: cambiar "original" no afecta a "inmutable"
```

---

## 2. Como funciona internamente

`List.copyOf()` no siempre crea un `ArrayList` nuevo. La JVM elige la implementacion mas eficiente segun el tamano y tipo de la coleccion fuente:

```java
List<String> fuente = new ArrayList<>(List.of("a", "b", "c"));

List<String> copia = List.copyOf(fuente);
// Internamente: copia los elementos a un array optimizado
// La lista resultante NO es un ArrayList, es una implementacion interna de Java
```

Comportamiento clave:

```java
// Si la fuente ya es inmutable, PUEDE devolver la misma referencia
List<String> yaInmutable = List.of("a", "b");
List<String> resultado = List.copyOf(yaInmutable);
// resultado PODRIA ser la misma referencia que yaInmutable
// (optimizacion de memoria: no copiar lo que ya es inmutable)

// Si la fuente es mutable, SIEMPRE crea una copia
List<String> mutable = new ArrayList<>(List.of("a", "b"));
List<String> copia = List.copyOf(mutable);
// copia es una referencia DIFERENTE a mutable
```

---

## 3. Las 4 reglas de List.copyOf()

### Regla 1: Null no esta permitido

```java
List<String> conNull = new ArrayList<>();
conNull.add("a");
conNull.add(null);

List<String> copia = List.copyOf(conNull);
// NullPointerException: "List.copyOf does not permit null elements"
```

Esta restriccion existe porque una lista inmutable con `null` seria peligrosa: no podrias distinguir "elemento ausente" de "elemento es null".

### Regla 2: No modifica la fuente

```java
List<String> original = new ArrayList<>(List.of("x", "y", "z"));
List<String> copia = List.copyOf(original);

original.add("w");
// original: [x, y, z, w]
// copia:    [x, y, z]       <- no cambio
```

### Regla 3: Crea una copia REAL (snapshot)

```java
List<String> mutable = new ArrayList<>(List.of("a", "b"));
List<String> copia = List.copyOf(mutable);

mutable.set(0, "CAMBIO");
// mutable: [CAMBIO, b]
// copia:   [a, b]          <- mantiene el estado del momento de copiar
```

### Regla 4: Los metodos de modificacion lanzan UnsupportedOperationException

```java
List<String> inmutable = List.copyOf(List.of("a", "b"));

inmutable.add("c");       // UnsupportedOperationException
inmutable.remove(0);      // UnsupportedOperationException
inmutable.set(0, "x");    // UnsupportedOperationException
inmutable.clear();        // UnsupportedOperationException
```

---

## 4. Cuando usar List.copyOf(): patron defensiva

El uso mas comun es **proteger el estado interno** de un objeto. Si tu clase recibe una lista y la almacena, DEBES copiarla para que quien la llamo no pueda modificarla desde fuera.

### En constructores

```java
public class Equipo {
    private final List<String> miembros;

    public Equipo(List<String> miembros) {
        // Sin copyOf: peligro
        // this.miembros = miembros;  // el creador puede hacer miembros.clear()

        // Con copyOf: seguro
        this.miembros = List.copyOf(miembros);
    }
}

// Demostracion del peligro sin copyOf:
List<String> nombres = new ArrayList<>(List.of("Ana", "Luis"));
Equipo equipo = new Equipo(nombres);
nombres.clear();  // Sin copyOf, equipo.miembros quedaria vacio
// Con copyOf, equipo.miembros sigue siendo [Ana, Luis]
```

### En getters

```java
public class Equipo {
    private final List<String> miembros;

    public Equipo(List<String> miembros) {
        this.miembros = List.copyOf(miembros);
    }

    // MAL: devuelve la referencia interna
    public List<String> getMiembrosInseguro() {
        return miembros;
    }

    // BIEN: devuelve copia inmutable
    public List<String> getMiembros() {
        return miembros;  // ya es inmutable, no necesita otro copyOf
    }

    // Alternativa: si quieres que puedan modificar sin afectar al interno
    public List<String> getMiembrosCopia() {
        return new ArrayList<>(miembros);  // copia mutable para el llamador
    }
}
```

### En metodos que construyen colecciones

```java
public class Filtrador {
    public List<String> filtrarPalabrasLargas(List<String> palabras, int minLongitud) {
        List<String> resultado = palabras.stream()
            .filter(p -> p.length() >= minLongitud)
            .toList();  // toList() ya devuelve inmutable (Java 16+)

        return List.copyOf(resultado);  // doble seguridad
    }
}
```

---

## 5. List.copyOf() vs otras formas de inmutabilidad

| Metodo | Copia la fuente? | Acepta null? | Ya-inmutable retorna misma referencia? | Moderno? |
|--------|-----------------|-------------|--------------------------------------|----------|
| `List.copyOf(c)` | Si | No | Si (optimizacion) | Java 9+ |
| `List.of(e...)` | N/A (crea nueva) | No | N/A | Java 9+ |
| `Collections.unmodifiableList(c)` | No (envuelve) | Si | N/A | Java 2+ |
| `List.copyOf(List.copyOf(c))` | Si (doble) | No | Puede (doble optimizacion) | Java 9+ |
| `c.stream().toList()` | Si (cada elemento) | No | No | Java 16+ |

### Por que NO usar Collections.unmodifiableList

```java
// Collections.unmodifiableList NO crea una copia
List<String> original = new ArrayList<>(List.of("a", "b"));
List<String> envuelta = Collections.unmodifiableList(original);

original.add("c");
// envuelta ahora tiene [a, b, c]  <- SEGURO es una trampa

// List.copyOf SI crea una copia
List<String> copia = List.copyOf(original);
original.add("d");
// copia sigue siendo [a, b, c]  <- SEGURO
```

`Collections.unmodifiableList` es un wrapper que solo bloquea metodos de modificacion sobre la misma lista. No protege contra cambios en la fuente original. Solo es util si controlas ambas referencias y garantizas que la fuente no cambia.

---

## 6. Set.copyOf() y Map.copyOf()

El patron es identico para Set y Map:

```java
// Set.copyOf
Set<String> original = new HashSet<>(Set.of("a", "b", "c"));
Set<String> copiaSet = Set.copyOf(original);
// copiaSet es inmutable

// Map.copyOf
Map<String, Integer> originalMap = new HashMap<>(Map.of("x", 1, "y", 2));
Map<String, Integer> copiaMap = Map.copyOf(originalMap);
// copiaMap es inmutable
```

Las mismas reglas aplican: sin null, copia real, UnsupportedOperationException en modificacion.

---

## 7. List.copyOf() con objetos mutables

La lista es inmutable, pero los objetos que contiene pueden no serlo. Esto es lo que se llama **inmutabilidad superficial**:

```java
public class Usuario {
    private String nombre;
    private int edad;

    public Usuario(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public String getNombre() { return nombre; }
    public int getEdad() { return edad; }
    public void setEdad(int edad) { this.edad = edad; }  // mutable
}

// La lista es inmutable, pero el objeto Usuario no
List<Usuario> usuarios = List.copyOf(List.of(new Usuario("Ana", 25)));
// usuarios.add(...);  ERROR: lista inmutable

// PERO el objeto dentro si puede cambiar
usuarios.get(0).setEdad(26);  // OK: el objeto Usuario es mutable
// El estado interno del objeto cambio aunque la referencia de la lista no
```

Para inmutabilidad total, los objetos dentro tambien deben ser inmutables (records, clases con final fields):

```java
// Inmutabilidad TOTAL: lista inmutable + objetos inmutables
public record UsuarioInmutable(String nombre, int edad) {}

List<UsuarioInmutable> usuarios = List.copyOf(
    List.of(new UsuarioInmutable("Ana", 25))
);
// Nadie puede cambiar nada: ni la lista ni los objetos dentro
```

---

## 8. Copia defensiva en builders y patrones comunes

### Builder que acumula y luego congela

```java
public class PedidoBuilder {
    private List<String> productos = new ArrayList<>();

    public PedidoBuilder agregarProducto(String producto) {
        productos.add(producto);
        return this;
    }

    public Pedido construir() {
        // Congela: copia mutable -> inmutable
        return new Pedido(List.copyOf(productos));
    }
}

public class Pedido {
    private final List<String> productos;

    public Pedido(List<String> productos) {
        this.productos = List.copyOf(productos);  // defensa doble
    }

    public List<String> productos() {
        return productos;  // ya inmutable
    }
}
```

### Metodo que retorna colecciones

```java
public class Catalogo {
    private final List<String> productos;

    public Catalogo(List<String> productos) {
        this.productos = List.copyOf(productos);
    }

    // retorna inmutable: el llamador no puede modificar el catalogo
    public List<String> getProductos() {
        return productos;
    }

    // retorna copia mutable: el llamador puede modificar su copia
    public List<String> getProductosModificables() {
        return new ArrayList<>(productos);
    }
}
```

---

## 9. Rendimiento y buenas practicas

- `List.copyOf()` en una lista ya inmutable puede devolver la misma referencia (costo ~0)
- En una lista mutable, copia los elementos (costo O(n))
- La implementacion interna usa arrays compactos, no `ArrayList`
- No es thread-safe para escritura (porque no hay escritura), pero la iteracion es segura

**Regla de oro**: siempre usa `List.copyOf()` al:
1. Recibir una lista en un constructor y almacenarla
2. Devolver una lista desde un getter que no debe ser modificada
3. Congelar una coleccion que ya no cambiara
4. Pasar una lista a otro metodo que podria modificarla

---

## Errores Comunes

> **Olvidar copyOf en el constructor**. Si接收 una lista y la guardas directamente, quien la creo puede modificarla despues. Siempre `this.lista = List.copyOf(lista);`

> **Usar Collections.unmodifiableList pensando que protege**. No crea copia. La fuente original sigue conectada. Usa `List.copyOf()` en su lugar.

> **No manejar la NullPointerException**. `List.copyOf()` lanza NPE si hay null. Si la fuente puede tener null, filtra antes: `lista.stream().filter(Objects::nonNull).toList()`

> **Copiar dos veces sin necesidad**. `List.copyOf(List.copyOf(lista))` es desperdicio. Una sola llamada es suficiente.

> **No entender que la lista es inmutable pero los objetos dentro no**. Si contiene `Usuario mutable`, alguien puede hacer `lista.get(0).setNombre("x")`. Para inmutabilidad total, usa records.

---

## Conexiones

- [[15 - Listas y ArrayList]] - ArrayList como fuente para copyOf
- [[21 - Getters y Setters]] - Copia defensiva en getters
- [[24 - ArrayList - Metodos Avanzados]] - toList() como alternativa
- [[26 - Private en Profundidad]] - Encapsulamiento de estado
- [[37 - Record (y Value Objects)]], - Inmutabilidad total con records
- [[38 - List.of y Colecciones Inmutables]] - Familia completa: of, copyOf, Set, Map

---

## Tags
`#java #inmutabilidad #list-copyof #colecciones #defensa #encapsulamiento #java-moderno`
