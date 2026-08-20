---
tags: [java, fundamentos, colecciones, list, set, map, inmutable, java9]
---

# 38 - List.of y Colecciones Inmutables en Profundidad

`List.of()` es el metodo de fabrica que crea listas inmutables en una linea. Junto con `Set.of()`, `Map.of()` y `copyOf()`, forma la familia de colecciones inmutables de Java (JEP 269, Java 9). Este tema es la continuacion en profundidad de [[15 - Listas y ArrayList]].

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es List.of?

Es un metodo estatico de la interfaz `List` que crea una lista **inmutable** con los elementos que le pasas:

```java
List<String> colores = List.of("Rojo", "Verde", "Azul");
```

### Por que es inmutable?

No puedes añadir, quitar ni modificar elementos:

```java
List<String> colores = List.of("Rojo", "Verde", "Azul");

colores.add("Amarillo");       // ERROR: UnsupportedOperationException
colores.remove("Rojo");        // ERROR: UnsupportedOperationException
colores.set(0, "Negro");       // ERROR: UnsupportedOperationException
```

### Como se crea una lista normal para comparar

```java
// Lista mutable (puedes cambiarla):
List<String> mutable = new ArrayList<>();
mutable.add("Rojo");

// Lista inmutable (no puedes cambiarla):
List<String> inmutable = List.of("Rojo", "Verde");
```

### Para que sirve

- Listas de valores fijos (constantes, opciones, configuracion)
- Pasar datos a un metodo sin que los modifique
- Iterar sin miedo a cambios

```java
public class Config {
    // Constantes que nunca cambian:
    public static final List<String> PERMISOS_ADMIN = List.of("leer", "escribir", "borrar");
    public static final List<String> DIAS_LABORABLES = List.of("LUN", "MAR", "MIE", "JUE", "VIE");
}
```

---

## NIVEL 2 - Mid

### List.of vs new ArrayList vs Arrays.asList

| Metodo | Mutable | Null | Duplicados | Tamano |
|--------|---------|------|-----------|--------|
| `List.of(...)` | No | Prohibido | Permitidos | Fijo |
| `new ArrayList<>(...)` | Si | Permitido | Permitidos | Variable |
| `Arrays.asList(...)` | Si (set) | Permitido | Permitidos | Fijo |

```java
// List.of: totalmente inmutable
List<String> a = List.of("x", "y");

// Arrays.asList: mutable con set, pero tamano fijo
List<String> b = Arrays.asList("x", "y");
b.set(0, "z");              // OK
// b.add("w");              // ERROR: no puedes añadir

// ArrayList: completamente mutable
List<String> c = new ArrayList<>(List.of("x", "y"));
c.add("w");                 // OK
```

### Las reglas de List.of

```java
// 1. NO acepta null:
List.of("a", null);   // NullPointerException

// 2. SI acepta duplicados:
List.of("a", "a");    // OK -> [a, a]

// 3. El orden se mantiene:
List.of("a", "b", "c").get(1);  // "b"

// 4. Hasta 10 argumentos directos:
List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);  // OK
List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11);  // ERROR de compilacion
// Para mas de 10, usa varargs:
List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, ...);  // con array: List.of(numeros)
```

### Set.of y Map.of

```java
// Set inmutable: no duplicados, sin orden garantizado
Set<String> roles = Set.of("ADMIN", "USER");
// Set.of("A", "A");  // IllegalArgumentException: duplicado

// Map inmutable: pares clave-valor
Map<String, Integer> edades = Map.of("Ana", 30, "Luis", 25);
// Map.of maximo 10 pares; Map.ofEntries para mas:

Map<String, Integer> muchos = Map.ofEntries(
    Map.entry("a", 1), Map.entry("b", 2), Map.entry("c", 3)
    // ... cuantos quieras
);
```

### List.copyOf / Set.copyOf / Map.copyOf

Crea una copia inmutable de cualquier coleccion:

```java
List<String> mutable = new ArrayList<>();
mutable.add("a");
mutable.add("b");

List<String> inmutable = List.copyOf(mutable);
// inmutable.add("c");  // ERROR

// El original puede seguir cambiando, la copia no:
mutable.add("c");  // inmutable sigue siendo [a, b]
```

### Como "modificar" una lista inmutable

Crea una nueva a partir de ella:

```java
List<String> original = List.of("a", "b", "c");

// Para añadir:
List<String> nueva = new ArrayList<>(original);
nueva.add("d");                  // [a, b, c, d]

// O de una forma mas funcional:
List<String> ampliada = Stream.concat(original.stream(), Stream.of("d")).toList();
```

---

## NIVEL 3 - Senior

### Inmutabilidad de referencia vs de contenido

```java
// La lista es inmutable, pero los OBJETOS que contiene no lo son:
record Punto(int x, int y) { }

Punto p = new Punto(1, 2);
List<Punto> puntos = List.of(p);   // la LISTA no puede cambiar
// puntos.add(new Punto(3,4));     // ERROR

// PERO el objeto Punto es inmutable tambien (record).
// Si fueran objetos mutables, podrias modificar su contenido:
class Contador { int valor; }
Contador c = new Contador();
List<Contador> contadores = List.of(c);
c.valor = 99;                      // El contenido cambio aunque la lista sea inmutable
contadores.get(0).valor = 50;      // Tambien desde la lista
```

### Implementacion interna (rendimiento y memoria)

```java
// List.of NO es un ArrayList disfrazado. La JVM elige la implementacion optima:

// - List.of() vacia -> ImmutableCollections.ListN (usa un array)
// - List.of(1 elemento) -> List12
// - List.of(1, 2) -> List12 (formato compacto para 1-2 elementos)
// - List.of(muchos) -> ListN (array estandar)

// Ventajas:
// - Sin codigo de modificacion (menos superficie)
// - Memoria: no necesita "capacity" ni campos extra
// - Iteracion mas rapida (arrays puros)
// - Thread-safe por diseno (nada puede cambiar)
```

### Diferencias sutiles con Arrays.asList

```java
// Arrays.asList devuelve una vista del array ORIGINAL:
String[] array = {"a", "b"};
List<String> vista = Arrays.asList(array);
array[0] = "X";           // El cambio se refleja en la lista
vista.set(1, "Y");        // Y viceversa

// List.of crea una copia desconectada:
String[] array2 = {"a", "b"};
List<String> copia = List.of(array2);
array2[0] = "X";          // NO afecta a la lista
```

### Inmutabilidad para seguridad y programacion defensiva

```java
// Proteger la configuracion interna:
public class Servicio {
    private final List<String> reglas;

    public Servicio(List<String> reglas) {
        // Copia defensiva: si el que llama modifica su lista, no nos afecta
        this.reglas = List.copyOf(reglas);
    }

    public List<String> reglas() {
        return reglas;  // Ya es inmutable, seguro devolverla
    }
}

// Uso seguro:
List<String> reglasExternas = new ArrayList<>();
Servicio s = new Servicio(reglasExternas);
reglasExternas.add("HACK");  // No afecta al servicio
```

### Combinacion con Streams y toList

```java
// Java 16+: stream.toList() tambien devuelve una lista inmutable
List<Integer> pares = IntStream.range(1, 100)
    .filter(n -> n % 2 == 0)
    .boxed()
    .toList();  // inmutable

// Diferencia con collect(Collectors.toList()):
// - toList(): inmutable (Java 16+)
// - collect(Collectors.toList()): mutable (compatible con versiones viejas)

// Para controlar el tipo mutable:
List<Integer> mutable = IntStream.range(1, 100)
    .filter(n -> n % 2 == 0)
    .boxed()
    .collect(Collectors.toCollection(ArrayList::new));
```

---

# METODO PROFUNDO (Curso completo de List.of y colecciones inmutables)

## 1. Origen: JEP 269 (Java 9)

Antes de Java 9, crear una lista inmutable era verboso y con trampas:

```java
// Como se hacia antes (con Arrays.asList):
List<String> inmutable = Collections.unmodifiableList(
    Arrays.asList("a", "b", "c")
);
// - Verboso
// - Arrays.asList: vista del array, tamano fijo
// - unmodifiableList: solo envuelve, sigue consumiendo memoria de modificacion
// - Muchos errores: olvidar el wrapper, o el wrapper solo protege "superficialmente"

// Con List.of:
List<String> inmutable = List.of("a", "b", "c");  // 1 linea, sin trampas
```

### Los 3 metodos de fabrica de la familia

| Metodo | Crea | Rechaza |
|--------|------|---------|
| `List.of(e...)` | Lista inmutable ordenada | null, duplicados? (List SI permite duplicados) |
| `Set.of(e...)` | Set inmutable | null, duplicados |
| `Map.of(k, v, k, v...)` | Map inmutable | null (en clave y valor), claves duplicadas |
| `List.copyOf(c)` | Copia inmutable de la coleccion | null, coleccion con null |

---

## 2. Contratos detallados de List.of

### Reglas oficiales (JavaDoc)

1. **Inmutable**: no soporta `add`, `remove`, `set`, ni ningun metodo de modificacion
2. **Sin null**: `NullPointerException` si cualquier elemento es null
3. **Orden**: los elementos se mantienen en el orden de insercion
4. **Duplicados**: PERMITIDOS en List.of (a diferencia de Set.of)
5. **Serializable**: implementa Serializable
6. **Iteracion segura**: no lanza ConcurrentModificationException (no puede cambiar)

```java
// Demostracion de cada regla:
List<String> lista = List.of("a", "b", "b");  // duplicados OK: [a, b, b]

try {
    List.of("a", null);
} catch (NullPointerException e) {
    System.out.println("null no permitido");
}

try {
    lista.add("c");
} catch (UnsupportedOperationException e) {
    System.out.println("no se puede añadir");
}
```

### Por que null esta prohibido?

```java
// Diseño deliberado de Java 9:
// - null en colecciones causa NullPointerExceptions impredecibles
// - Las colecciones inmutables "eliminan" el problema del null
// - Forza a escribir codigo mas limpio (Optional si necesitas ausencia)

// Antes: codigo defensivo por todas partes
List<String> lista = new ArrayList<>();
if (lista != null && !lista.isEmpty() && lista.get(0) != null) { }

// Con List.of: si existe, no contiene null, punto
List<String> segura = List.of("a", "b");
```

---

## 3. La familia completa: Set.of y Map.of

### Set.of

```java
// Set inmutable: sin orden, sin duplicados
Set<String> roles = Set.of("ADMIN", "USER", "EDITOR");

// Duplicado: IllegalArgumentException en construccion
try {
    Set.of("A", "A");
} catch (IllegalArgumentException e) {
    System.out.println("Duplicado: " + e.getMessage());  // duplicate element: A
}

// Sin orden garantizado:
Set<Integer> numeros = Set.of(3, 1, 2);
// numeros puede iterar como 1,2,3 o cualquier orden
```

### Map.of y Map.ofEntries

```java
// Map.of: hasta 10 pares (20 argumentos)
Map<String, Integer> scores = Map.of(
    "Ana", 90,
    "Luis", 85,
    "Maria", 95
);

// Claves duplicadas: IllegalArgumentException
Map.of("a", 1, "a", 2);  // ERROR

// null prohibido en claves Y valores:
Map.of("a", null);  // NullPointerException

// Para mas de 10 pares:
Map<String, Integer> grande = Map.ofEntries(
    Map.entry("a", 1), Map.entry("b", 2),
    Map.entry("c", 3), Map.entry("d", 4)
    // ...
);
```

### copyOf

```java
List<String> mutable = new ArrayList<>(List.of("a", "b"));
List<String> copiaInmutable = List.copyOf(mutable);

Set<String> copiaSet = Set.copyOf(mutable);
Map<String, Integer> copiaMap = Map.copyOf(Map.of("a", 1));

// Reglas de copyOf:
// - NullPointerException si la fuente contiene null
// - Si la fuente ya es inmutable, puede devolverla misma (optimizacion)
List<String> yaInmutable = List.of("a");
List<String> devuelta = List.copyOf(yaInmutable);  // puede ser la misma referencia
```

---

## 4. List.of vs Arrays.asList vs ArrayList (en profundidad)

| Caracteristica | List.of | Arrays.asList | new ArrayList |
|----------------|---------|---------------|---------------|
| `add()` | No | No | Si |
| `remove()` | No | No | Si |
| `set()` | No | Si | Si |
| `clear()` | No | No | Si |
| Acepta null | No | Si | Si |
| Conectada al array original | No | Si | No |
| Tamano | Fijo | Fijo | Dinamico |
| Contenido | Copia de los elementos | Vista del array | Copia |
| Thread-safe | Si (inmutable) | No | No |

### La trampa clasica de Arrays.asList

```java
int[] numeros = {1, 2, 3};

// ERROR clasico: int[] NO es Integer[]
List<int[]> listaMal = Arrays.asList(numeros);   // lista de 1 elemento: el ARRAY
System.out.println(listaMal.size());             // 1 (no 3!)

// Correcto: usar Integer[] o List.of con boxed:
Integer[] wrappers = {1, 2, 3};
List<Integer> bien = Arrays.asList(wrappers);    // 3 elementos

// La forma moderna y segura:
List<Integer> mejor = List.of(1, 2, 3);
```

### Cuantas sobrecargas tiene List.of?

```java
// 11 sobrecargas especificas:
List.of();                 // 0 elementos
List.of(E e1);             // 1
List.of(E e1, E e2);       // 2
// ... hasta
List.of(E e1, ..., E e10); // 10
// Y una varargs:
List.of(E... elements);    // cualquier cantidad

// Diseño de rendimiento: los casos 0-10 evitan el array varargs
// (cada llamada no crea un array temporal innecesario)
```

---

## 5. Inmutabilidad: el porque

### Beneficios de las colecciones inmutables

1. **Thread-safety gratis**: nada puede cambiar, no hay condiciones de carrera
2. **Menos bugs**: nadie puede modificar accidentalmente una constante
3. **Predicibilidad**: una referencia segura puede compartirse sin miedo
4. **Rendimiento**: implementaciones compactas y sin metodos de escritura
5. **Diseño limpio**: comunican intencion ("esto no cambia")

### Copy-on-write mental

```java
// En lugar de modificar, creas una nueva version:
List<String> v1 = List.of("a", "b");
List<String> v2 = new ArrayList<>(v1);
v2.add("c");   // version nueva [a, b, c]
// v1 sigue siendo [a, b]
```

### Cuando NO usar colecciones inmutables

- Cuando necesitas añadir/quitar elementos frecuentemente
- Cuando la coleccion es el resultado de un proceso en construccion
- Cuando los datos provienen de usuario/BD y cambian constantemente

---

## 6. Casos de uso reales

### Constantes y configuracion

```java
public class Constantes {
    public static final List<String> CORS_ORIGINS = List.of("https://app.com", "https://admin.com");
    public static final Set<String> METODOS_PERMITIDOS = Set.of("GET", "POST", "PUT", "DELETE");
    public static final Map<String, Integer> CODIGOS_HTTP = Map.of(
        "OK", 200, "CREATED", 201, "NOT_FOUND", 404, "SERVER_ERROR", 500
    );
}
```

### Programacion defensiva en APIs

```java
public class Reporte {
    private final List<String> lineas;

    public Reporte(List<String> lineas) {
        // No confiamos en el que llama:
        this.lineas = List.copyOf(lineas);
    }

    public List<String> lineas() {
        // No exponemos nuestra copia interna (es inmutable, segura)
        return lineas;
    }
}
```

### Estado de dominio sin mutabilidad compartida

```java
// Lista de valores permitidos en un enum-like:
public record Pedido(String id, String estado) {
    public static final List<String> ESTADOS_VALIDOS = List.of(
        "PENDIENTE", "EN_PROCESO", "ENVIADO", "ENTREGADO", "CANCELADO"
    );

    public Pedido {
        if (!ESTADOS_VALIDOS.contains(estado)) {
            throw new IllegalArgumentException("Estado invalido: " + estado);
        }
    }
}
```

### Integracion con streams

```java
// Resultado de un pipeline -> inmutable directo:
List<String> nombresFiltrados = usuarios.stream()
    .filter(u -> u.edad() > 18)
    .map(Usuario::nombre)
    .toList();   // Java 16+: inmutable

// Si necesitas ordenar antes (los streams no pueden ordenar y devolver inmutable en un paso):
List<String> ordenados = usuarios.stream()
    .map(Usuario::nombre)
    .sorted()
    .toList();
```

---

## 7. Rendimiento y memoria

### Implementaciones internas (JDK 17+)

```java
// Las colecciones inmutables de Java usan implementaciones especializadas:

// List.of() -> ImmutableCollections.ListN (array de 0)
// List.of(a) -> ImmutableCollections.List12 (optimizado para 1-2)
// List.of(a, b) -> ImmutableCollections.List12
// List.of(a, b, c, ...) -> ImmutableCollections.ListN

// Beneficios vs ArrayList:
// - Sin campo "size" separado (o con formato compacto)
// - Sin capacidad reservada extra
// - Iteradores simples (sin modCount checks)
// - Sin metodos de escritura (menos codigo que cachear)

// Dato: List12 guarda 1 elemento como Object y 2 como Object[2] en el mismo objeto.
```

### Comparacion de memoria

```java
// ArrayList: objeto + array + capacity (puede reservar mas del necesario)
// List.of: un solo objeto con el array exacto

List<String> a = new ArrayList<>(List.of("x", "y"));  // sobrecarga de ArrayList
List<String> b = List.of("x", "y");                   // compacta
```

---

## 8. Trampas y casos limite

### Los elementos pueden seguir siendo mutables

```java
class Cuenta {
    double saldo;
}

Cuenta c1 = new Cuenta();
List<Cuenta> cuentas = List.of(c1);   // la lista es inmutable

c1.saldo = 1000;                      // PERO el objeto cambia
cuentas.get(0).saldo = -500;          // y esto tambien funciona

// La inmutabilidad es de la ESTRUCTURA, no del contenido.
// Para contenido inmutable: records, String, Wrapper, etc.
```

### varargs y el array compartido

```java
// TRAMPA: List.of(E... elements) recibe el array directamente
String[] datos = {"a", "b", "c"};
List<String> lista = List.of(datos);
datos[0] = "X";
// En la mayoria de las JDK, List.of COPIA el array internamente (seguro).
// Pero no dependas de ello: si necesitas protegerte, pasa copia:
List<String> segura = List.of(Arrays.copyOf(datos, datos.length));
```

### No se puede cambiar el tipo de elementos

```java
List<Object> lista = List.of("a", 1, 2.5);  // OK: Object

// Pero no puedes cambiar un List<Object> a List<String> sin re-empaquetar:
// List<String> s = (List<String>) lista;  // ClassCastException en el acceso
```

---

## 9. Integracion con Optional y streams funcionales

```java
// Combinar inmutables con flujos funcionales:

// Defaults con Optional:
List<String> config = Optional.ofNullable(getConfigExterna())
    .map(List::copyOf)
    .orElse(List.of());   // lista vacia inmutable

// Union de listas inmutables:
List<String> unidas = Stream.concat(List.of("a").stream(), List.of("b").stream())
    .toList();  // [a, b] inmutable

// Filtrado de una inmutable:
List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6);
List<Integer> pares = numeros.stream()
    .filter(n -> n % 2 == 0)
    .toList();  // [2, 4, 6] inmutable
```

---

## Errores Comunes

> Intentar modificar `List.of(...)` y esperar que funcione. Lanza `UnsupportedOperationException` — es inmutable por diseño. Crea una copia mutable con `new ArrayList<>(...)` si necesitas cambios.

> Pasar `null` a `List.of()`. Lanza `NullPointerException` inmediatamente. Si necesitas valores ausentes, usa `Optional` en vez de null.

> Confundir `List.of` con `Arrays.asList`. `Arrays.asList` devuelve una vista mutable (con `set`) del array original y acepta null; `List.of` copia, es inmutable y rechaza null.

> Asumir que `List.of` y `new ArrayList<>()` son intercambiables. Solo lo son para lectura. Para escritura necesitas ArrayList (u otra mutable).

> Usar `List.of(array)` con un `int[]` y esperar una lista de Integers. Obtienes una lista de 1 elemento (el array). Usa `Integer[]` o boxea.

> Olvidar que la inmutabilidad es estructural: los objetos dentro de la lista pueden ser mutables. Usa records/String/Wrapper para contenido verdaderamente inmutable.

> Confiar en el orden de `Set.of(...)`. El orden de iteracion no esta garantizado.

> Usar `Map.of` con mas de 10 pares sin `Map.ofEntries`. Error de compilacion. Usa `Map.ofEntries(Map.entry(k, v), ...)`.

---

## Buenas Practicas

1. **Usa `List.of()` para constantes y configuracion** — comunican que no cambian y son thread-safe.
2. **Usa `List.copyOf()` para copias defensivas** en constructores y getters.
3. **Usa `stream.toList()` (Java 16+)** para resultados de pipelines inmutables.
4. **No uses `Collections.unmodifiableList`** para datos nuevos — es un wrapper, `List.of` es la implementacion real.
5. **Maneja null con Optional**, no con null en colecciones (List.of te obliga a ser limpio).
6. **Distingue inmutabilidad estructural vs de contenido** — los elementos mutables siguen siendo un riesgo.
7. **Para mas de 10 elementos de Map** usa `Map.ofEntries`.
8. **Crea copias mutables cuando necesites construir**: `new ArrayList<>(List.of(...))`.
9. **Documenta la intencion**: una firma que devuelve `List<String>` sin decir "inmutable" pierde informacion. Si puedes, devuelve el tipo base.

---

## Conexiones

- [[15 - Listas y ArrayList]] - El tema base: este es su continuacion en profundidad (colecciones inmutables)
- [[03 - Tipos Primitivos y Referencia]] - Por que `List.of(int[])` no funciona como esperas (autoboxing de arrays)
- [[13 - Static vs Instancia]] - Metodos estaticos de fabrica como List.of
- [[16 - Bucles y Control de Flujo]] - Iterar colecciones inmutables
- [[23 - Metodos - Parametros, Retorno y Return]] - Metodos varargs de List.of
- [[24 - ArrayList - Metodos Avanzados]] - ArrayList mutable vs List.of inmutable
- [[31 - Curso Completo de .method()]] - Catalogo de metodos: of, copyOf, entry, toList
- [[34 - Clases Wrapper (envolventes)]] - Por que List.of rechaza null y sus Wrapper permiten
- [[37 - Record (y Value Objects)]] - Records + List.of = inmutabilidad completa en VO
- [[47 - List.copyOf() (inmutabilidad)]] - Curso completo de List.copyOf y copia defensiva
- [[99 - Glosario Rapido]] - Terminos: inmutable, factory method, varargs

---

## Tags
`#java #fundamentos #colecciones #list #set #map #inmutable #java9 #factory-methods`