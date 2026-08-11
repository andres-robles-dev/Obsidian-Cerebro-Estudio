---
tags: [java, fundamentos, streams, stream-api, funcional, pipelines, java-moderno, profundidad]
---

# 43 - Stream API en Profundidad

Un Stream es un flujo de datos sobre el que encadenas operaciones: filtras, transformas, ordenas y reduces. NO es una coleccion (no guarda datos): es una secuencia de elementos que se procesa en un pipeline. Es la forma moderna de Java para procesar colecciones sin bucles manuales.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un Stream?

Es una tuberia por la que pasan los elementos de una coleccion. Cada operacion que encadenas transforma la tuberia. El proceso tiene 3 partes: **fuente** (de donde salen los datos), **operaciones intermedias** (filtros y transformaciones) y **operacion terminal** (el resultado final).

```java
List<String> nombres = List.of("Ana", "Luis", "Paz", "Abril");

// Pipeline completo:
List<String> resultado = nombres.stream()      // 1. FUENTE
    .filter(n -> n.startsWith("A"))            // 2. INTERMEDIA: solo los que empiezan con A
    .map(String::toUpperCase)                  // 3. INTERMEDIA: transforma a mayusculas
    .toList();                                 // 4. TERMINAL: junta el resultado

// resultado = [ANA, ABRIL]
```

### La operacion estrella: filter

```java
// filter recibe un Predicate (lambda que devuelve boolean):
// deja pasar los elementos que cumplan la condicion

List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6);

List<Integer> pares = numeros.stream()
    .filter(n -> n % 2 == 0)      // solo pares
    .toList();
// [2, 4, 6]
```

### La operacion estrella 2: map

```java
// map transforma CADA elemento (recibe una Function):

List<String> nombres = List.of("ana", "luis");

List<Integer> longitudes = nombres.stream()
    .map(String::length)          // de String a int
    .toList();
// [3, 4]

List<String> mayusculas = nombres.stream()
    .map(String::toUpperCase)
    .toList();
// [ANA, LUIS]
```

### findFirst: buscar el primero que cumpla

```java
List<Producto> productos = catalogo.getProductos();

Optional<Producto> primero = productos.stream()
    .filter(p -> p.categoria().equals("Electronica"))
    .findFirst();                       // Optional: puede no haber ninguno

// Devuelve Optional: recuerda el tema 41
Producto resultado = primero.orElseThrow(() -> new ProductoNoEncontradoException());
```

---

## NIVEL 2 - Mid

### El pipeline completo

```java
List<Pedido> pedidos = repositorio.getPedidos();

// Cadena de operaciones:
double totalEnvios = pedidos.stream()
    .filter(Pedido::pagado)                       // solo pagados
    .filter(p -> !p.cancelado())                  // y no cancelados
    .map(Pedido::getTotal)                        // de Pedido a double
    .sorted(Comparator.reverseOrder())            // del mayor al menor
    .limit(3)                                     // solo los 3 primeros
    .mapToDouble(v -> v)                          // a stream primitivo
    .sum();                                       // suma total

// Filtros multiples + orden + limite + suma en una sola linea expresiva
```

### Operaciones intermedias mas usadas

```java
// distinct: elimina duplicados
List<String> unicos = lista.stream().distinct().toList();

// sorted: ordena (natural o con comparador)
List<String> ordenadas = lista.stream().sorted().toList();
List<Producto> porPrecio = productos.stream()
    .sorted(Comparator.comparing(Producto::getPrecio))
    .toList();

// limit: deja solo N elementos
List<String> primerosTres = lista.stream().limit(3).toList();

// skip: se salta N elementos
List<String> despuesDelPrimero = lista.stream().skip(1).toList();
```

### Operaciones terminales mas usadas

```java
long total = lista.stream().count();                    // cuantos hay

boolean hayAlguno = lista.stream().anyMatch(n -> n.length() > 5);  // alguno cumple?
boolean todos = lista.stream().allMatch(n -> n.length() > 0);      // todos cumplen?
boolean ninguno = lista.stream().noneMatch(n -> n.length() < 1);   // ninguno cumple?

Optional<String> primero = lista.stream().findFirst();   // el primero (Optional!)
Optional<String> cualquiera = lista.stream().findAny();  // cualquiera (paralelo)

Optional<Integer> max = numeros.stream().max(Integer::compareTo);
Optional<Integer> min = numeros.stream().min(Integer::compareTo);

// forEach: hacer algo con cada elemento (sin devolver lista)
lista.stream().forEach(System.out::println);

// toList: juntar en una lista inmutable (Java 16+)
List<String> resultado = lista.stream().filter(n -> n.length() > 2).toList();
```

### reduce: combinar todos en uno

```java
// reduce combina los elementos con una operacion binaria:

int suma = numeros.stream()
    .reduce(0, (acumulador, n) -> acumulador + n);

// reduce sin valor inicial devuelve Optional:
Optional<Integer> sumaOpcional = numeros.stream()
    .reduce((a, b) -> a + b);
```

---

## NIVEL 3 - Senior

### flatMap: aplanar listas de listas

```java
// Cada elemento se convierte en un stream que se "aplana":

List<List<String>> anidada = List.of(
    List.of("a", "b"),
    List.of("c"),
    List.of("d", "e", "f")
);

List<String> plana = anidada.stream()
    .flatMap(lista -> lista.stream())     // List<String> -> elementos sueltos
    .toList();
// [a, b, c, d, e, f]

// Caso real: todos los items de todos los pedidos:
List<Item> todosLosItems = pedidos.stream()
    .flatMap(p -> p.items().stream())
    .toList();
```

### Streams primitivos (sin autoboxing)

```java
// IntStream, LongStream, DoubleStream: numericos sin empaquetar

List<Integer> numeros = List.of(1, 2, 3, 4, 5);

int suma = numeros.stream()
    .mapToInt(Integer::intValue)     // Stream<Integer> -> IntStream
    .sum();

OptionalDouble promedio = numeros.stream()
    .mapToInt(Integer::intValue)
    .average();

int maximo = IntStream.rangeClosed(1, 100).max().orElse(0);

// Crear rangos:
IntStream.range(1, 5);      // 1, 2, 3, 4 (el fin se excluye)
IntStream.rangeClosed(1, 5); // 1, 2, 3, 4, 5
```

### Pereza (lazy evaluation): la clave de Streams

```java
// Los streams NO hacen nada hasta que llega una operacion TERMINAL:

List<String> nombres = List.of("Ana", "Luis", "Paz", "Abril");

nombres.stream()
    .filter(n -> {
        System.out.println("Filtrando: " + n);
        return n.startsWith("A");
    });
// NO imprime NADA: sin terminal, el stream ni se ejecuta

nombres.stream()
    .filter(n -> {
        System.out.println("Filtrando: " + n);
        return n.startsWith("A");
    })
    .findFirst();       // solo procesa hasta encontrar el primero (cortocircuito)
// Filtrando: Ana  -> encuentra y PARA. Luis/Paz/Abril ni se filtran

// Ventaja: cadenas largas procesan el MINIMO de elementos necesario
```

### takeWhile y dropWhile (Java 9+)

```java
// takeWhile: toma mientras se cumpla, PARANDO en el primero que falle
List<Integer> numeros = List.of(1, 2, 3, 4, 1, 2);

List<Integer> tomados = numeros.stream()
    .takeWhile(n -> n < 4)      // 1, 2, 3 (para en el 4)
    .toList();

// dropWhile: descarta mientras se cumpla, dejando el resto
List<Integer> restantes = numeros.stream()
    .dropWhile(n -> n < 4)      // 4, 1, 2
    .toList();
```

---

# METODO PROFUNDO (Curso completo de Stream API)

## 1. Anatomia del pipeline

```
Fuente                Intermedias                Terminal
list.stream()  ->   filter -> map -> sorted  ->  .toList()
Arrays.stream()                         ->  .count()
Stream.of(...)                          ->  .forEach(...)
IntStream.range(...)                    ->  .reduce(...)
```

- **Fuente**: de donde salen los datos (coleccion, array, rango, archivo, generator)
- **Intermedias**: devuelven OTRO Stream (se pueden encadenar infinitas). Son perezosas: no procesan nada hasta la terminal
- **Terminal**: produce el resultado (lista, numero, boolean, Optional) y CIERRA el stream. Un stream consumido no se reutiliza

```java
// Un stream solo se puede consumir UNA vez:
Stream<String> s = nombres.stream().filter(n -> n.length() > 2);
s.count();                    // OK: terminal, el stream queda consumido
s.count();                    // ERROR: IllegalStateException (stream ya operado)
```

## 2. Formas de crear un stream

```java
// Desde colecciones:
lista.stream();
lista.parallelStream();          // procesamiento en paralelo

// Desde arrays:
Arrays.stream(numeros);

// Desde valores directos:
Stream.of("a", "b", "c");

// Rangos numericos:
IntStream.range(1, 10);
LongStream.rangeClosed(1, 100);

// Desde una String (lineas del texto):
texto.lines();

// Infinitos (con limite para no explotar):
Stream.generate(() -> Math.random()).limit(5);
Stream.iterate(1, n -> n * 2).limit(10);
```

## 3. Operaciones intermedias (referencia completa)

| Operacion | Que hace | Recibe |
|-----------|----------|--------|
| `filter` | Deja pasar los que cumplen | `Predicate<T>` |
| `map` | Transforma cada elemento | `Function<T,R>` |
| `flatMap` | Cada elemento -> stream que aplana | `Function<T,Stream<R>>` |
| `distinct` | Quita duplicados (equals) | - |
| `sorted` | Ordena | `Comparator<T>` opcional |
| `limit(n)` | Deja solo los n primeros | int |
| `skip(n)` | Se salta los n primeros | int |
| `peek` | Observa (depurar) sin modificar | `Consumer<T>` |
| `takeWhile` | Toma mientras cumpla (para al fallar) | `Predicate<T>` |
| `dropWhile` | Descarta mientras cumpla | `Predicate<T>` |
| `mapToInt/mapToLong/mapToDouble` | Convierte a stream primitivo | `ToIntFunction<T>` |

```java
// peek para depurar:
lista.stream()
    .peek(System.out::println)              // mira lo que pasa
    .filter(n -> n.length() > 2)
    .peek(n -> System.out.println("Pasa: " + n))
    .toList();
```

## 4. Operaciones terminales (referencia completa)

| Terminal | Devuelve | Para que |
|----------|----------|----------|
| `toList()` | `List<T>` | juntar el resultado (inmutable, Java 16+) |
| `collect(Collectors.xxx)` | lo que pidas | agrupar, juntar en Map, etc. |
| `forEach` | void | hacer algo con cada elemento |
| `count()` | long | contar |
| `anyMatch/allMatch/noneMatch` | boolean | comprobar condiciones |
| `findFirst/findAny` | `Optional<T>` | buscar uno |
| `max/min` | `Optional<T>` | el mayor/menor |
| `reduce` | `T` u `Optional<T>` | combinar todos en uno |
| `sum/average` (primitivos) | int/long/`OptionalDouble` | sumar/promediar |

```java
// collect con Collectors (antes de toList):
List<String> lista = stream.collect(Collectors.toList());        // clasico
Set<String> conjunto = stream.collect(Collectors.toSet());       // a Set
Map<String, List<Producto>> porCategoria = productos.stream()
    .collect(Collectors.groupingBy(Producto::getCategoria));     // agrupar
```

## 5. Optional como terminal (puente al tema 41)

```java
// Los terminales de busqueda devuelven Optional porque PUEDE no haber resultado:

Optional<String> primero = lista.stream().findFirst();
Optional<Integer> mayor = numeros.stream().max(Integer::compareTo);
OptionalDouble promedio = numeros.stream().mapToInt(Integer::intValue).average();

// Manejalo con las reglas del tema 41:
String valor = primero.orElse("Sin resultados");
String exigido = primero.orElseThrow(() -> new SinResultadosException());
```

## 6. flatMap en profundidad

```java
// Cuando CADA elemento puede expandirse en varios:

// Ejemplo: todas las palabras de todos los textos:
List<String> textos = List.of("hola mundo", "java stream");

List<String> palabras = textos.stream()
    .flatMap(texto -> texto.lines().flatMap(l -> java.util.Arrays.stream(l.split(" "))))
    .toList();

// Caso tipico: Optional en map crea Optional<Optional>; flatMap aplana:
List<Optional<String>> opcionales = List.of(Optional.of("a"), Optional.empty());

List<String> presentes = opcionales.stream()
    .flatMap(Optional::stream)       // Optional -> stream de 0 o 1
    .toList();
// [a]
```

## 7. Reducciones: reduce y collect

```java
// reduce: plegar todos los elementos en uno:
int suma = numeros.stream().reduce(0, Integer::sum);
int maximo = numeros.stream().reduce(Integer.MIN_VALUE, Math::max);
String concatenado = palabras.stream().reduce("", (a, b) -> a + " " + b);

// collect: juntar en estructuras:
Set<String> unicos = palabras.stream().collect(Collectors.toSet());
Map<Boolean, List<Integer>> particion = numeros.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));
String join = palabras.stream().collect(Collectors.joining(", "));
```

## 8. Anti-patrones y errores comunes

| Anti-patron | Problema | Solucion |
|-------------|----------|----------|
| Usar streams para todo (incluso bucles simples) | Ilegible | `for` simple si es solo iterar |
| Consumir el mismo stream dos veces | IllegalStateException | Crear uno nuevo cada vez |
| Esperar resultados sin terminal | Nada se ejecuta (pereza) | Siempre terminar el pipeline |
| Modificar la coleccion mientras se procesa | ConcurrentModificationException | Filtrar y crear nueva lista |
| Lambdas gigantes dentro de map/filter | Ilegible | Extraer metodos |
| Hacer trabajo costoso en streams paralelos sin medirlo | Mas lento de lo esperado | Solo paralelo con datos grandes |
| `findFirst()` sobre listas no ordenadas | Resultado arbitrario | Ordenar antes o usar findAny |

## 9. Buenas practicas definitivas

1. **Pipeline legible**: una operacion por linea, fuente arriba, terminal abajo.
2. **Prefiere `toList()`** sobre `collect(Collectors.toList())` (mas corto y claro).
3. **Usa streams primitivos** (`mapToInt`) para operaciones numericas: evitas autoboxing.
4. **Encadena filtros en vez de combinar condiciones** cuando ayuda a la lectura.
5. **`peek` solo para depurar**, no para efectos secundarios.
6. **For simple cuando no transformas nada**: recorrer e imprimir con for puede ser mas claro.
7. **Los streams NO reemplazan todo**: son una herramienta mas; elige la mas clara.
8. **Cuidado con NPE en streams**: `filter(Objects::nonNull)` cuando puede haber nulls.

---

## Errores Comunes

> Usar un stream sin operacion terminal: nada se ejecuta (pereza). Todo pipeline termina con toList, count, forEach, findFirst...

> `Optional.get()` sobre el resultado de findFirst sin verificar. Usa orElse/orElseThrow (tema 41).

> Modificar la lista original dentro de un stream. Los streams procesan una vista; mutar la fuente rompe el comportamiento.

> Combinar filter con condiciones monstruosas: `filter(n -> n > 0 && n < 10 && ...)`. Filtros separados leen mejor.

> Convertir a primitivo con boxed() sin necesidad, o quedarse en Stream<Integer> para sumas. Usa mapToInt + sum.

> Consumir el mismo stream dos veces: IllegalStateException. Crea uno nuevo por operacion.

> Usar streams paralelos en colecciones pequenas: el overhead supera el beneficio.

---

## Conexiones

- [[15 - Listas y ArrayList]] - stream() como fuente de datos desde listas
- [[24 - ArrayList - Metodos Avanzados]] - Operaciones funcionales sobre ArrayList
- [[31 - Curso Completo de .method()]] - .method() con lambdas (map, filter, sorted)
- [[34 - Clases Wrapper (envolventes)]] - Streams primitivos y boxing
- [[41 - Optional en Profundidad]] - Los terminales de busqueda devuelven Optional
- [[42 - Lambda en Profundidad]] - Las lambdas son el combustible de los pipelines
- [[44 - Patron Catalogo Inmutable con Enum]] - Busquedas con stream + filter + findFirst
- [[Plantillas de Codigo/23 - Optional]] - Manejando Optional de findFirst

---

## Tags
`#java #fundamentos #streams #stream-api #funcional #pipelines #java-moderno`