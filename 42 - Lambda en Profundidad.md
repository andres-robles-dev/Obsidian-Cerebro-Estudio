---
tags: [java, fundamentos, lambda, funcional, interfaces-funcionales, method-reference, profundidad]
---

# 42 - Lambda en Profundidad

Una lambda es una funcion anonima: codigo que se pasa como dato. En vez de escribir una clase completa con un metodo para una sola accion, escribes directamente la accion con la sintaxis `(parametros) -> expresion`. Son la base de la programacion funcional en Java y de la Stream API.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es una lambda?

Es una funcion SIN nombre que puedes guardar en una variable, pasar como argumento y ejecutar cuando quieras. Antes de las lambdas (Java 7) necesitabas escribir una clase anonima completa; ahora escribes la accion en una linea.

```java
// ANTES (Java 7): clase anonima
lista.sort(new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.length() - b.length();
    }
});

// AHORA (Java 8+): lambda
lista.sort((a, b) -> a.length() - b.length());
```

### Sintaxis basica

```java
// Sin parametros:
() -> System.out.println("Hola");

// Un parametro (los parentesis se pueden quitar):
nombre -> nombre.length();
(nombre) -> nombre.length();

// Varios parametros:
(a, b) -> a + b;

// La estructura general:
// (parametros) -> lo que devuelve o hace
```

### Primeros usos diarios

```java
// forEach: hacer algo con cada elemento
nombres.forEach(n -> System.out.println(n));

// sort con comparador
nombres.sort((a, b) -> a.compareTo(b));

// removeIf: quitar segun condicion
nombres.removeIf(n -> n.length() < 3);

// Runnable: una tarea para un hilo
Runnable tarea = () -> System.out.println("Ejecutando...");
```

---

## NIVEL 2 - Mid

### Las interfaces funcionales del JDK

Una lambda puede guardarse en una variable si existe una interfaz funcional (una interfaz con UN SOLO metodo abstracto) que le de tipo.

| Interfaz | Metodo | Recibe | Devuelve | Uso tipico |
|----------|--------|--------|----------|------------|
| `Predicate<T>` | `test(T)` | 1 valor | `boolean` | filtrar, validar |
| `Function<T,R>` | `apply(T)` | 1 valor | 1 valor | transformar |
| `Consumer<T>` | `accept(T)` | 1 valor | `void` | hacer algo (forEach) |
| `Supplier<T>` | `get()` | nada | 1 valor | fabricar valores |
| `BinaryOperator<T>` | `apply(T,T)` | 2 valores | 1 valor | combinar (reduce) |

```java
import java.util.function.*;

Predicate<String> esLargo = s -> s.length() > 5;         // boolean
Function<String, Integer> longitud = s -> s.length();    // transforma
Consumer<String> imprimir = s -> System.out.println(s);  // no devuelve
Supplier<String> generar = () -> "valor generado";       // no recibe
BinaryOperator<Integer> sumar = (a, b) -> a + b;         // combina

// Uso:
System.out.println(esLargo.test("hola mundo"));   // true
System.out.println(longitud.apply("java"));       // 4
```

### Bloques con llaves (varias sentencias)

```java
// Expresion simple: sin llaves, return implicito
nombres.forEach(n -> System.out.println(n));

// Bloque: con llaves, return explicito, logica extra
nombres.sort((a, b) -> {
    int resultado = a.length() - b.length();
    if (resultado == 0) {
        return a.compareTo(b);
    }
    return resultado;
});
```

### Variables "efectivamente finales"

Una lambda solo puede usar variables que no cambien despues de inicializarse:

```java
int factor = 2;              // nunca se reasigna: es "efectivamente final"

List<Integer> dobles = numeros.stream()
    .map(n -> n * factor)    // OK: factor no cambia
    .toList();

int contador = 0;            // se reasigna: NO es efectivamente final
numeros.forEach(n -> contador += n);   // ERROR de compilacion
```

---

## NIVEL 3 - Senior

### Method references: la forma corta de una lambda

| Sintaxis | Significa | Ejemplo |
|----------|-----------|---------|
| `Clase::metodoEstatico` | `x -> Clase.metodoEstatico(x)` | `Integer::parseInt` |
| `Clase::metodoInstancia` | `x -> x.metodoInstancia()` | `String::toUpperCase` |
| `objeto::metodo` | `x -> objeto.metodo(x)` | `sistema::println` |
| `Clase::new` | `() -> new Clase()` | `ArrayList::new` |

```java
// Las tres lineas hacen lo mismo:
nombres.stream().map(n -> n.toUpperCase()).toList();
nombres.stream().map(String::toUpperCase).toList();   // method reference
nombres.stream().map(String::toUpperCase).toList();

// Constructor:
Supplier<ArrayList<String>> crear = ArrayList::new;
```

### this en lambdas

```java
// En una clase anonima, this es la clase anonima.
// En una lambda, this es la clase CONTENEDORA:

public class Servicio {
    private String nombre = "servicio";

    public void ejecutar() {
        Runnable tarea = () -> System.out.println(this.nombre);
        //                              ^^^^ se refiere a Servicio, no a la lambda
        tarea.run();
    }
}
```

### Composicion funcional

```java
// Encadenar funciones: andThen
Function<Integer, Integer> duplicar = x -> x * 2;
Function<Integer, Integer> sumarUno = x -> x + 1;

Function<Integer, Integer> pipeline = duplicar.andThen(sumarUno);
System.out.println(pipeline.apply(5));   // 11 (duplica 10, luego +1 = 11)

// compose: orden inverso
Function<Integer, Integer> inverso = duplicar.compose(sumarUno);
System.out.println(inverso.apply(5));    // 12 (+1 = 6, duplica = 12)

// Negar predicados: negate
Predicate<String> esVacio = String::isEmpty;
Predicate<String> noVacio = esVacio.negate();

// Combinar predicados: and / or
Predicate<Integer> par = n -> n % 2 == 0;
Predicate<Integer> mayorQueDiez = n -> n > 10;
Predicate<Integer> parYMayor = par.and(mayorQueDiez);
```

---

# METODO PROFUNDO (Curso completo de Lambdas)

## 1. Sintaxis completa

```java
// Las 5 formas posibles:
// 1. Sin parametros:
() -> System.out.println("hola");

// 2. Un parametro sin parentesis:
s -> s.length();

// 3. Un parametro con parentesis:
(s) -> s.length();

// 4. Varios parametros con tipos declarados:
(String a, String b) -> a.length() - b.length();

// 5. Bloque con cuerpo:
(s) -> {
    System.out.println("Procesando: " + s);
    return s.toUpperCase();
};
```

Reglas:
- Los tipos de los parametros se pueden omitir (inferencia): `(a, b) -> a + b`
- Si el cuerpo es una expresion, el `return` es implicito
- Si el cuerpo tiene llaves, necesitas `return` explicito si debe devolver algo

## 2. @FunctionalInterface

```java
// Una interfaz con UN SOLO metodo abstracto es funcional:
@FunctionalInterface
public interface Descuento {
    double calcular(double precio);
    // Solo un metodo abstracto: las lambdas pueden implementarla
}

// La anotacion @FunctionalInterface hace que el compilador verifique
// que la interfaz solo tiene UN metodo abstracto (error si hay dos)

// Uso con lambda:
Descuento navidad = precio -> precio * 0.8;
Descuento verano = precio -> precio * 0.85;
System.out.println(navidad.calcular(100.0));   // 80.0

// Tambien puedes crear tus propias interfaces funcionales de dominio
// y usarlas como parametros:
public void aplicarDescuento(double precio, Descuento regla) {
    System.out.println(regla.calcular(precio));
}
aplicarDescuento(100.0, p -> p * 0.9);
```

## 3. Las interfaces funcionales del JDK (tabla completa)

| Interfaz | Metodo abstracto | Recibe | Devuelve |
|----------|------------------|--------|----------|
| `Predicate<T>` | `test` | T | boolean |
| `BiPredicate<T,U>` | `test` | T, U | boolean |
| `Function<T,R>` | `apply` | T | R |
| `BiFunction<T,U,R>` | `apply` | T, U | R |
| `UnaryOperator<T>` | `apply` | T | T (mismo tipo) |
| `BinaryOperator<T>` | `apply` | T, T | T |
| `Consumer<T>` | `accept` | T | void |
| `BiConsumer<T,U>` | `accept` | T, U | void |
| `Supplier<T>` | `get` | nada | T |
| `IntPredicate` | `test` | int | boolean |

```java
// Ejemplo con BiFunction:
BiFunction<Integer, Integer, String> formatear =
    (a, b) -> "(" + a + ", " + b + ")";
System.out.println(formatear.apply(1, 2));   // (1, 2)
```

## 4. Method references en profundidad

```java
// 1. Metodo estatico:
Function<String, Integer> f1 = Integer::parseInt;
// equivale a: (s) -> Integer.parseInt(s)

// 2. Metodo de instancia de UN argumento cualquiera:
Function<String, String> f2 = String::toLowerCase;
// equivale a: (s) -> s.toLowerCase()

// 3. Metodo de instancia de UN objeto capturado:
Consumer<String> f3 = System.out::println;
// equivale a: (s) -> System.out.println(s)

// 4. Constructor:
Supplier<List<String>> f4 = ArrayList::new;
// equivale a: () -> new ArrayList<>()

// Regla: usa method references cuando la lambda SOLO llama a un metodo.
// Si hay logica extra o transformacion, usa la lambda completa.
```

## 5. Captura de variables: la regla de "efectivamente final"

```java
// La lambda "captura" las variables del entorno:

int descuento = 10;      // efectivamente final (no cambia nunca)
Function<Integer, Integer> precioConDescuento = p -> p - descuento;

// El compilador NO permite capturar variables que cambian:
int total = 0;
lista.forEach(n -> total += n);   // ERROR: total no es efectivamente final

// Por que? Porque la lambda podria ejecutarse en otro hilo.
// Garantizar inmutabilidad de la captura evita carreras de datos.

// Solucion para acumular: usa streams (reduce) o una variable mutable externa
// controlada (ej: int[] acumulador = {0}; - pero prefiere streams).
```

## 6. Lambdas en colecciones (las herramientas de cada dia)

```java
List<String> nombres = new ArrayList<>(List.of("Ana", "Luis", "Paz", "Sol"));

nombres.forEach(n -> System.out.println(n));        // recorrer

nombres.removeIf(n -> n.length() < 3);              // quitar "Sol"

nombres.replaceAll(String::toUpperCase);            // transformar cada elemento

nombres.sort((a, b) -> a.compareTo(b));             // ordenar

// Map:
Map<String, Integer> edades = new HashMap<>();
edades.computeIfAbsent("Ana", k -> 30);             // si falta, calcular
edades.merge("Ana", 1, Integer::sum);               // acumular valores
```

## 7. Composicion y currying (funciones que crean funciones)

```java
// Una lambda que devuelve otra lambda = fabrica de comportamiento:

public Function<Integer, Integer> crearMultiplicador(int factor) {
    return x -> x * factor;    // devuelve una lambda que captura factor
}

Function<Integer, Integer> triple = crearMultiplicador(3);
System.out.println(triple.apply(5));   // 15

// Composicion con andThen/compose (ver NIVEL 3):
Function<Integer, Integer> cuadrado = x -> x * x;
Function<Integer, String> imprimir = x -> "Resultado: " + x;

Function<Integer, String> pipeline = cuadrado.andThen(imprimir);
System.out.println(pipeline.apply(4));   // Resultado: 16
```

## 8. Buenas practicas y anti-patrones

| Anti-patron | Problema | Solucion |
|-------------|----------|----------|
| Lambda con demasiada logica | Ilegible | Extrae un metodo y usa method reference |
| Capturar variables mutables | Error de compilacion | Redisenar con streams |
| Uso excesivo de lambdas donde un for lee mejor | Menos claro | Prefiere for cuando el flujo es simple |
| Lambda con efecto secundario en map() | Violacion del modelo funcional | Usa forEach o revisa el diseno |
| Nombres de una letra siempre | Confuso | Nombres cortos y claros (n, i, p ok para colecciones) |

```java
// MAL: lambda gigante
pedidos.forEach(p -> {
    double total = p.items().stream().mapToDouble(Item::precio).sum();
    if (total > 100 && p.urgente()) {
        System.out.println("Enviar urgente: " + p.id());
    } else if (total > 100) {
        System.out.println("Enviar normal: " + p.id());
    }
});

// BIEN: extraer metodo y usar method reference
pedidos.forEach(Pedido::imprimirEnvio);
```

---

## Errores Comunes

> Usar una variable no efectivamente final dentro de la lambda. El compilador lo rechaza; las variables capturadas no pueden cambiar.

> Escribir `return` en una lambda de expresion: `x -> return x + 1;` es error. La expresion ya devuelve, sin `return` (solo en bloques `{ }`).

> Confundir las interfaces funcionales: usar Predicate donde va Consumer, o Function donde va BiFunction. Revisa que recibe y que devuelve cada una.

> Crear clases anonimas cuando una lambda basta: 5 lineas por 1. Solo usa clase anonima si necesitas variables de instancia propias.

> Lambdas largas e ilegibles. Si el cuerpo pasa de 3-4 sentencias, extrae un metodo.

> Olvidar @FunctionalInterface en tus interfaces: sin ella el compilador no avisa si accidentalmente agregas un segundo metodo abstracto.

---

## Conexiones

- [[04 - Variables y Literales]] - Variables efectivamente finales en lambdas
- [[07 - Constructores y this]] - this en lambdas vs clases anidadas
- [[10 - Metodos de Instancia]] - Metodos con Stream y lambdas internas
- [[15 - Listas y ArrayList]] - forEach, removeIf, replaceAll con lambdas
- [[18 - Convenciones de Nombrado]] - Convenciones en lambdas (p -> p.precio())
- [[24 - ArrayList - Metodos Avanzados]] - Lambdas en operaciones funcionales de listas
- [[31 - Curso Completo de .method()]] - .method() con argumentos lambda
- [[41 - Optional en Profundidad]] - Lambdas en ifPresent, orElseGet, orElseThrow
- [[43 - Stream API en Profundidad]] - Las lambdas son el combustible de los streams
- [[44 - Patron Catalogo Inmutable con Enum]] - Lambdas y streams en catalogos de busqueda

---

## Tags
`#java #fundamentos #lambda #funcional #interfaces-funcionales #method-reference #java-moderno`