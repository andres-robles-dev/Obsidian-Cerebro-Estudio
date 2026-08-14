---
tags: [java, fundamentos, generics, tipos-parametrizados, wildcards, erasure, pecs, java-moderno, profundidad]
---

# 45 - Genericos en Profundidad

Los genericos (tipos parametrizados) hacen que una clase, metodo o interfaz funcione con CUALQUIER tipo, pero con seguridad en tiempo de compilacion: `List<String>`, `Optional<Integer>`, `Map<String, List<Producto>>` son tipos genericos. Cuando escribes `Optional<T>` o `Caja<T>`, el `T` es un hueco que se llena con un tipo real al usarlo. Estables desde Java 5, son la base de las colecciones, Optional, los funcionales y la Stream API.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un tipo parametrizado?

Es un tipo que lleva OTRO tipo entre `< >`. El `<T>` dice "aqui va un tipo que se decide cuando se use".

```java
List<String>     nombres = new ArrayList<>();   // lista SOLO de String
Optional<Integer> edad   = Optional.empty();    // caja SOLO de Integer
Map<String, List<Producto>> porCategoria;        // K=String, V=List<Producto>

// El hueco <T> se llena en el momento de usarlo:
// List<T> con T="String"   -> List<String>
// Optional<T> con T="Integer" -> Optional<Integer>
```

### Por que existen: type safety

El error se detecta al COMPILAR, no cuando el programa corre:

```java
// SIN genericos (raw type): cualquier cosa entra, falla en runtime
List lista = new ArrayList();
lista.add("Hola");
lista.add(42);                         // un String y un int en la misma lista
String texto = (String) lista.get(1);  // ClassCastException en RUNTIME (era Integer)

// CON genericos: el compilador NO deja mezclar tipos
List<String> lista = new ArrayList<>();
lista.add("Hola");
lista.add(42);                         // ERROR DE COMPILACION: int no es String
// El compilador avisa ANTES de ejecutar
```

### El diamond <>

```java
// Java 7+: el tipo del lado derecho se infiere del izquierdo
List<String> nombres = new ArrayList<>();   // <> en vez de <String> otra vez
Optional<String> opt = Optional.of("Ana");
```

### Por que NO se usan primitivos (puente al tema 34)

```java
List<int>  numeros;   // ERROR: no compila
List<Integer> numeros = new ArrayList<>();   // OK: se usa el WRAPPER

// Los genericos solo aceptan tipos REFERENCIA (clases).
// El autoboxing convierte el int automaticamente al guardarlo/leerlo.
// Detalle completo en el tema 34 y 39.
```

---

## NIVEL 2 - Mid

### Clases genericas: la anatomia real de Optional<T>

```java
// La firma real simplificada de Optional te muestra TODO el patron:
public final class Optional<T> {                    // <T> en la clase

    private final T value;                          // T en un campo

    private Optional(T value) {                     // T en un constructor
        this.value = value;
    }

    public static <T> Optional<T> of(T value) {     // <T> antes del retorno en static
        return new Optional<>(value);               // T en parametro y retorno
    }

    public T get() {                                // T como retorno de instancia
        return value;
    }

    public T orElse(T otro) {                       // T en parametro y retorno
        return value != null ? value : otro;
    }
}

// El <T> viaja por TODA la clase: campos, constructores, parametros y retornos.
// Al hacer Optional<String>, el compilador "llena" T con String en todos lados.
```

### Clase generica propia: Caja<T>

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
Caja<String> cajaTexto = new Caja<>("Hola");
Caja<Integer> cajaNumero = new Caja<>(42);

String texto = cajaTexto.getContenido();   // sin cast: T ya es String
// cajaTexto.getContenido() da String; cajaNumero.getContenido() da Integer
```

### Metodos genericos: <T> antes del retorno

```java
// El <T> del METODO es independiente del de la clase:

public class Util {
    public static <T> List<T> paginar(List<T> lista, int pagina, int tamano) {
        int inicio = (pagina - 1) * tamano;
        return lista.stream()
            .skip(inicio)
            .limit(tamano)
            .toList();
    }
}

// El compilador INFIERE T desde los argumentos:
List<String> pagina = Util.paginar(nombres, 1, 10);   // T = String
List<Integer> paginaNumeros = Util.paginar(numeros, 2, 5);  // T = Integer
```

### Interfaces genericas: Repositorio<T>

```java
public interface Repositorio<T> {
    Optional<T> buscarPorId(long id);
    List<T> listarTodos();
    T guardar(T entidad);
}

// La implementacion decide el tipo:
public class RepositorioUsuarios implements Repositorio<Usuario> {
    public Optional<Usuario> buscarPorId(long id) { ... }
    public List<Usuario> listarTodos() { ... }
    public Usuario guardar(Usuario u) { ... }
}
```

### Tipos acotados: T extends ...

```java
// T solo puede ser Number o una subclase (Integer, Double, Long...):
public class MathBox<T extends Number> {
    private final T valor;

    public MathBox(T valor) {
        this.valor = valor;
    }

    public double duplicar() {
        return valor.doubleValue() * 2;    // doubleValue() existe por ser Number
    }
}

new MathBox<>(21);        // OK: Integer es Number
new MathBox<>(3.14);      // OK: Double es Number
// new MathBox<>("Hola"); // ERROR: String no es Number

// Con interfaz: T extends Comparable<T> (como en el tema 37):
public record Cajon<T extends Comparable<T>>(T contenido) { }
```

---

## NIVEL 3 - Senior

### Wildcards: el signo ?

```java
// ? significa "tipo desconocido": limita lo que puedes hacer, con seguridad:

// 1. ? a secas: acepta cualquier tipo, pero NO puedes leer como T ni escribir nada
public static void imprimir(Optional<?> opt) {
    System.out.println(opt.isPresent());        // OK: metodos sin T
    // opt.get()   -> da Object (el tipo concreto se perdio)
}

// 2. ? extends T: acepta T o SUBTIPOS. SOLO lecturas seguras (producer).
public static double sumar(List<? extends Number> numeros) {
    return numeros.stream().mapToDouble(Number::doubleValue).sum();
    // numeros.add(5);  // ERROR: no sabes si la lista real es de Double, Integer...
}

// 3. ? super T: acepta T o SUPERTIPOS. SOLO escrituras seguras (consumer).
public static void agregarTodo(List<? super Integer> destino, List<Integer> fuente) {
    destino.addAll(fuente);   // OK: Integer llega a List<Integer>, List<Number>...
    // Integer x = destino.get(0);  // ERROR: el tipo real podria ser Number/Object
}
```

### PECS: la regla de oro

> **P**roducer **E**xtends, **C**onsumer **S**uper

```java
// Si SOLO PRODUCES (lees) valores de T      -> <? extends T>
// Si SOLO CONSUMES (escribes) valores de T  -> <? super T>
// Si haces ambas -> no uses wildcard, usa T directo

// Ejemplo real: Collections.copy(dest, src) de la JDK:
public static <T> void copy(List<? super T> destino, List<? extends T> origen) {
    // destino CONSUME (escribe) -> super T
    // origen PRODUCE (lee)      -> extends T
    for (T item : origen) {
        destino.add(item);
    }
}

// Uso flexible:
List<Number> dest   = new ArrayList<>();
List<Integer> src   = List.of(1, 2, 3);
copy(dest, src);    // OK: Integer produce hacia Number
```

### Type erasure: que pasa en runtime

```java
// El compilador BORRA los <T>. En runtime NO existen:

List<String> textos   = new ArrayList<>();
List<Integer> numeros = new ArrayList<>();

System.out.println(textos.getClass() == numeros.getClass()); // TRUE
// Ambos son java.util.ArrayList a secas: la JVM no sabe nada de <T>

// Consecuencias PRACTICAS:
// 1. NO se puede new T():
public class Caja<T> {
    public T crear() {
        return new T();              // ERROR: T no existe en runtime
    }
}

// 2. NO se puede instanceof con tipo parametrizado:
lista instanceof List<String>;       // ERROR: no compila
lista instanceof List<?>;            // OK: wildcard si funciona

// 3. NO se puede crear T[]:
T[] array = new T[10];               // ERROR
// Alternativa: usar List<T> (o crear Object[] y castear con @SuppressWarnings)
```

### Generic Record Patterns (Java 21+)

```java
// Puedes hacer pattern matching con records genericos,
// y el compilador infiere los type arguments:

record Box<T>(T contenido) { }

public static void inspeccionar(Object obj) {
    if (obj instanceof Box<String>(String s)) {
        System.out.println("Caja de texto: " + s);
    } else if (obj instanceof Box<?> b) {
        System.out.println("Caja de otro tipo");
    }
}

inspeccionar(new Box<>("Hola"));   // Caja de texto: Hola
```

---

# METODO PROFUNDO (Curso completo de Genericos)

## 1. Anatomia: declaracion vs uso

```java
// DECLARACION: defines el parametro <T> (la clase NO sabe que sera)
public class Caja<T> { ... }

// USO: llenas el parametro con un tipo real
Caja<String> caja = new Caja<>("Hola");

// Convenciones de nombre del parametro (por convencion, una letra):
// T  -> Type (tipo)
// E  -> Element (elementos de colecciones)
// K  -> Key (clave de Map)
// V  -> Value (valor de Map)
// N  -> Number
// S, U -> tipos segundo/tercero (Pair<S, U>)
```

## 2. Optional<T> leido como generico (paso a paso)

```java
// Cuando escribes: Optional<String> opt = Optional.of("Ana");

// 1. El compilador llena T = String en la clase Optional<T>
// 2. of(String valor) ahora recibe String y devuelve Optional<String>
// 3. get() ahora devuelve String (no hace falta cast)
// 4. Cualquier error de tipo se detecta AL COMPILAR

// Cuando escribes: String s = opt.orElse("x");
// -> orElse(String otro) recibe String, devuelve String. Todo tipo-seguro.

// Este es el mismo patron que usarias en tus propias clases: Caja<T>, Resultado<T>...
```

## 3. Clases con multiples parametros: Map<K, V>

```java
Map<String, List<Producto>> porCategoria = new HashMap<>();
//                     K = String
//                           V = List<Producto>

// Dos parametros en tu propia clase:
public class Par<K, V> {
    private final K clave;
    private final V valor;

    public Par(K clave, V valor) {
        this.clave = clave;
        this.valor = valor;
    }

    public K getClave() {
        return clave;
    }

    public V getValor() {
        return valor;
    }
}

Par<String, Integer> par = new Par<>("edad", 30);
```

## 4. Metodo generico vs clase generica (cuando usar cada uno)

| Situacion | Usa |
|-----------|-----|
| El tipo es compartido por campos Y metodos de la clase | Clase generica `Caja<T>` |
| El tipo solo afecta A UN metodos | Metodo generico `<T> List<T> paginar(...)` |
| Utilidades estaticas que sirven para cualquier tipo | Metodo generico static |
| Un repositorio que trabaja con distintas entidades | Interfaz generica `Repositorio<T>` |

```java
// Metodo generico en clase NO generica (lo normal para utilidades):
public class Util {
    public static <T> T primero(List<T> lista) {
        return lista.isEmpty() ? null : lista.get(0);
    }
}
```

## 5. Tipos acotados en profundidad

```java
// Una cota: solo superclase o interfaz
<T extends Number>
<T extends Comparable<T>>

// Dos cotas: clase + interfaces (la clase PRIMERO, luego &):
<T extends Number & Comparable<T>>

// La forma de Collections.sort (mas flexible):
// T extends Comparable<? super T>
// Acepta tipos cuyo PARENT implementa Comparable (ej: Dog hereda Animal implements Comparable<Animal>)
public static <T extends Comparable<? super T>> void ordenar(List<T> lista) { ... }
```

## 6. Wildcards: tabla completa

| Wildcard | Significa | Puedes leer como | Puedes escribir |
|----------|-----------|------------------|-----------------|
| `List<?>` | cualquier tipo | `Object` | NADA (ni null es seguro) |
| `List<? extends Number>` | Number o subtipos | `Number` | NADA |
| `List<? super Integer>` | Integer o supertipos | `Object` | `Integer` (y subtipos) |
| `List<T>` (sin ?) | exactamente T | `T` | `T` |

```java
// La regla mental:
// extends = LIMITE SUPERIOR (hacia arriba en la herencia)
// super   = LIMITE INFERIOR (hacia abajo en la herencia)
```

## 7. PECS con ejemplo de negocio

```java
// Regla: Producer Extends, Consumer Super

// Producer (lees): metodo que recibe datos
public static double total(List<? extends Producto> productos) {
    return productos.stream().mapToDouble(Producto::getPrecio).sum();
    // Solo lee: acepta List<Producto>, List<Alimento>, List<Electronico>...
}

// Consumer (escribes): metodo que agrega datos
public static void agregar(List<? super Producto> destino, Producto producto) {
    destino.add(producto);
    // Solo escribe: acepta List<Producto>, List<Object>...
}

// Ambos casos reales:
List<Alimento> alimentos = ...;
double t = total(alimentos);                     // extends: List<Alimento> OK
agregar(new ArrayList<Object>(), unProducto);    // super: List<Object> OK
```

## 8. Type erasure en detalle

```java
// Borrado de tipos (compilador):
// Caja<T>        -> Caja (T se borra a Object o a su cota)
// Caja<T extends Number> -> Caja (T se borra a Number)
// Caja<? extends Number> -> Caja (borrado a Number)

// Warning unchecked: el compilador NO puede verificar los casts genericos
@SuppressWarnings("unchecked")   // ultimo recurso, nunca la primera opcion
public <T> T convertir(Object objeto) {
    return (T) objeto;           // cast que el runtime no puede verificar
}

// El compilador inserta CASTS automaticos en el bytecode (bridge methods):
List<String> lista = ...;
String s = lista.get(0);
// -> en bytecode: String s = (String) lista.get(0);  // cast puesto por el compilador
```

## 9. Genericos y Java moderno

```java
// Records genericos (tema 37):
public record Resultado<T>(boolean exito, T dato, String error) {
    public static <T> Resultado<T> ok(T dato) {
        return new Resultado<>(true, dato, null);
    }
    public static <T> Resultado<T> fallo(String error) {
        return new Resultado<>(false, null, error);
    }
}

// Funcionales genericos (temas 42 y 43):
Predicate<String> esLargo = s -> s.length() > 5;      // Predicate<T>
Function<String, Integer> longitud = String::length;   // Function<T,R>
Optional<Producto> producto = repo.buscar(id);         // Optional<T>

// Optional en la practica cotidiana es UN generico: cada vez que lo usas
// estas llenando T con el tipo real (tema 41).
```

## 10. Checklist: cuando crear tu propia clase generica

- [ ] La clase sirve para VARIOS tipos sin cambiar la logica (Caja, Resultado, Par)
- [ ] Vas a garantizar type safety (que el error aparezca en compilacion)
- [ ] El tipo se comparte entre campos y metodos -> clase generica
- [ ] El tipo solo afecta a un metodo -> metodo generico
- [ ] Necesitas restringir tipos -> `<T extends TipoBase>`
- [ ] Solo lees -> `? extends`; solo escribes -> `? super` (PECS)
- [ ] Evitar raw types siempre: `private List lista;` -> `private List<String> lista;`

**Cuando NO crear una clase generica:**
- Si solo trabajas con un tipo concreto, genericos = complejidad innecesaria
- Si la "genericidad" es real: no sobregeneralices `T extends Object`
- Prefiere las genericas del JDK ya probadas (List, Optional, Map) cuando sirvan

---

## Errores Comunes

> Usar raw types: `List lista = new ArrayList();` pierde toda la seguridad de tipos. Siempre escribe `List<String> lista = ...`.

> `new T()`: no compila porque T se borra en runtime. Usa `Supplier<T>` o `Class<T>` como parametro.

> `lista instanceof List<String>`: no compila. Los genericos no existen en runtime; usa `instanceof List<?>` o revisa el elemento con get(0) instanceof String.

> Mezclar `List<String>` con `List<Object>`: `List<String>` NO es subtipo de `List<Object>`. Asignar uno al otro da error de compilacion.

> Abusar de `?` cuando basta un tipo concreto: `List<?>` no te deja escribir nada. Si solo necesitas un tipo, usa T.

> Wildcards en RETORNOS: `public List<? extends Producto> get()` obliga al llamador a perder informacion de tipo. Devuelve tipos concretos; usa wildcards en PARAMETROS.

> Casts de Object cuando los genericos lo evitarian: `(String) lista.get(0)` con `List<String>` es redundante.

> Ignorar warnings de unchecked: son avisos de que el compilador no puede verificar. Arregla la causa, no los silencies (salvo ultimo recurso).

> Crear clases genericas sin necesidad: si la clase solo maneja un tipo, `<T>` es ruido innecesario.

---

## Conexiones

- [[13 - Static vs Instancia]] - Metodo estatico generico filtrarNoNulos
- [[15 - Listas y ArrayList]] - List<T>: el generico mas usado del dia a dia
- [[20 - Javadoc y Documentacion]] - Interfaz generica Repositorio<T>
- [[23 - Metodos - Parametros, Retorno y Return]] - Metodo generico paginar<T>
- [[34 - Clases Wrapper (envolventes)]] - Por que List<int> no compila: solo tipos referencia
- [[37 - Record (y Value Objects)]] - Records genericos Resultado<T> y Cajon<T extends Comparable<T>>
- [[41 - Optional en Profundidad]] - Optional<T>: la clase generica del JDK como ejemplo real
- [[42 - Lambda en Profundidad]] - Predicate<T>, Function<T,R>, Consumer<T> y Supplier<T>
- [[43 - Stream API en Profundidad]] - Operaciones con tipos genericos en los pipelines
- [[99 - Glosario Rapido]] - Generics: tipos parametrizados List<T>, Map<K,V>
- [[Plantillas de Codigo/29 - Clase Generica]] - Plantilla de clase generica con ejemplo

---

## Tags
`#java #fundamentos #generics #tipos-parametrizados #wildcards #erasure #pecs #java-moderno`