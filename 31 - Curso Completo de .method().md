---
tags: [java, fundamentos, metodos, string, stream, optional, list, api, curso]
---

# 31 - Curso Completo de .method()

Toda accion en Java se hace llamando metodos con el operador `.` (punto). Este archivo cataloga los metodos esenciales organizados por categoria, explicando **que hacen**, **cuando usarlos** y **con que usarlos**.

```java
objeto.metodo();  // La sintaxis universal
```

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### String — Los metodos que usaras todos los dias

`String` es el tipo mas usado en Java. Es inmutable: cada metodo devuelve un NUEVO String sin modificar el original.

```java
// Los strings se crean con literales o con text blocks
var nombre = "Ana";
var bloque = """
    Hola
    Mundo
    """;
```

#### Tabla de metodos esenciales de String

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `.length()` | Devuelve cuantos caracteres tiene | Saber el tamano del texto | String, StringBuilder |
| `.charAt(i)` | Devuelve el caracter en la posicion `i` | Leer una letra en posicion exacta | String |
| `.equals(otro)` | Compara si dos strings son exactamente iguales | Comparar textos (NUNCA `==`) | Cualquier String |
| `.equalsIgnoreCase(otro)` | Compara sin importar mayusculas/minusculas | Validar entradas del usuario | String |
| `.contains(sub)` | Dice si contiene una subcadena | Buscar si algo esta dentro del texto | String, StringBuilder |
| `.indexOf(sub)` | Devuelve la posicion donde empieza una subcadena (o -1) | Encontrar donde aparece algo | String |
| `.startsWith(pre)` | Dice si empieza con algo | Validar prefijos (ej: "http://") | String |
| `.endsWith(suf)` | Dice si termina con algo | Validar sufijos (ej: ".pdf") | String |
| `.isEmpty()` | Dice si la cadena tiene longitud 0 | Saber si esta vacio (`""`) | String, StringBuilder, Collection |
| `.isBlank()` | Dice si solo tiene espacios | Validar que el usuario escribio algo real | String |
| `.strip()` | Quita espacios al inicio y final | Limpiar entradas de usuario | String |
| `.toLowerCase()` | Convierte a minusculas | Normalizar textos para comparar | String |
| `.toUpperCase()` | Convierte a mayusculas | Mostrar en formato gritando | String |
| `.substring(desde, hasta)` | Extrae una parte del string | Obtener un fragmento | String |
| `.split(regex)` | Divide en un array por un separador | Separar palabras, CSV, rutas | String |
| `.replace(viejo, nuevo)` | Reemplaza todas las ocurrencias | Cambiar partes de un texto | String |
| `.repeat(n)` | Repite el string `n` veces | Crear separadores, relleno | String |
| `.formatted(args)` | Inserta valores en el string | Reemplazar `%s`, `%d` (moderno) | String |
| `.stripIndent()` | Quita indentacion de text blocks | Limpiar text blocks | String |
| `.lines()` | Devuelve un Stream de lineas (desde Java 11) | Procesar texto multilinea | String |
| `.trim()` | Quita espacios (solo ASCII) | Legacy, prefiere `.strip()` | String |
| `.toCharArray()` | Convierte a `char[]` | Cuando necesitas modificar caracteres | String |
| `.matches(regex)` | Verifica si todo el string coincide con una regex | Validar patrones (email, telefono) | String |

```java
// Ejemplos de String con Java moderno
public class EjemplosString {
    public static void main(String[] args) {
        var texto = "  Hola Mundo Java 2026  ";

        // .strip() — limpiar entrada
        var limpio = texto.strip();
        System.out.println(limpio);  // "Hola Mundo Java 2026"

        // .isBlank() — validar que no sea solo espacios
        System.out.println("   ".isBlank());   // true
        System.out.println("".isEmpty());      // true
        System.out.println("".isBlank());      // true

        // .formatted() — la forma moderna
        var msg = "Usuario %s tiene %d anios".formatted("Ana", 25);
        System.out.println(msg);

        // .contains() + .indexOf()
        var frase = "Java es el mejor lenguaje";
        System.out.println(frase.contains("mejor"));  // true
        System.out.println(frase.indexOf("mejor"));   // 12

        // .split()
        var csv = "perro,gato,loro";
        var animales = csv.split(",");
        System.out.println(Arrays.toString(animales));  // [perro, gato, loro]

        // .repeat()
        System.out.println("=".repeat(20));  // ====================

        // .lines() con text block
        var bloque = """
            linea 1
            linea 2
            linea 3
            """.stripIndent();
        bloque.lines().forEach(System.out::println);

        // .equalsIgnoreCase()
        var entrada = "Si";
        if (entrada.equalsIgnoreCase("si")) {
            System.out.println("El usuario dijo que si");
        }
    }
}
```

---

### Optional — El protector contra null

> Ver tambien: [[41 - Optional en Profundidad]] - curso completo de Optional

`Optional<T>` es un contenedor que puede tener un valor o no. Evita los `NullPointerException`.

```java
import java.util.Optional;
```

#### Tabla de metodos esenciales de Optional

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `.of(valor)` | Crea un Optional con un valor (NO acepta null) | Cuando SABES que hay valor | Cualquier tipo |
| `.ofNullable(valor)` | Crea un Optional que acepta null | Cuando NO SABES si hay valor | Cualquier tipo |
| `.empty()` | Crea un Optional vacio | Cuando devuelves "sin resultado" | Cualquier tipo |
| `.isPresent()` | Dice si HAY valor dentro | Preguntar antes de usar | Optional |
| `.isEmpty()` | Dice si NO hay valor | Lo contrario de isPresent | Optional |
| `.ifPresent(consumer)` | Ejecuta algo si hay valor | Hacer algo solo si existe | Optional |
| `.ifPresentOrElse(consumer, run)` | Ejecuta algo si hay o si no | Tiene ambas ramas | Optional |
| `.orElse(default)` | Devuelve el valor o un default | Dar un valor por defecto | Optional |
| `.orElseGet(supplier)` | Devuelve el valor o genera uno | Default que requiere calculo | Optional |
| `.orElseThrow()` | Devuelve el valor o lanza excepcion | Cuando DEBE haber valor | Optional |
| `.orElseThrow(supplier)` | Como el anterior con excepcion personalizada | Lanzar error si falta | Optional |
| `.map(funcion)` | Transforma el valor si presente | Encadenar transformaciones | Optional |
| `.filter(predicate)` | Filtra el valor si cumple condicion | Validar el valor interno | Optional |
| `.flatMap(funcion)` | Como map pero evita Optional anidados | Cuando la funcion ya devuelve Optional | Optional |
| `.stream()` | Convierte a Stream de 0 o 1 elemento | Usar con Stream API | Optional |

```java
public class EjemplosOptional {
    public static void main(String[] args) {
        // Crear optionals
        var conValor = Optional.of("Hola");
        var sinValor = Optional.empty();
        var talVez = Optional.ofNullable(podriaSerNull());

        // .isPresent() y .ifPresent()
        if (conValor.isPresent()) {
            System.out.println(conValor.orElseThrow());
        }

        conValor.ifPresent(s -> System.out.println("Tiene: " + s));

        // .orElse() y .orElseGet()
        var valor1 = sinValor.orElse("default");
        var valor2 = sinValor.orElseGet(() -> generarDefault());

        // .orElseThrow()
        // var valor3 = sinValor.orElseThrow();  // NoSuchElementException

        // .map() y .filter()
        var longitud = conValor
            .map(String::length)
            .orElse(0);
        System.out.println("Longitud: " + longitud);  // 4

        var filtrado = conValor
            .filter(s -> s.startsWith("H"))
            .orElse("No empieza con H");

        // .stream() para integrar con Stream API
        conValor.stream()
            .map(String::toUpperCase)
            .forEach(System.out::println);
    }

    static String podriaSerNull() {
        return Math.random() > 0.5 ? "Existo" : null;
    }

    static String generarDefault() {
        System.out.println("Generando default...");
        return "Generado";
    }
}
```

---

### List / ArrayList — La coleccion reina

`List` es una secuencia ordenada de elementos que puede crecer o encogerse.

```java
import java.util.List;
import java.util.ArrayList;
```

#### Tabla de metodos esenciales de List

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `List.of(elem...)` | Crea lista INMUTABLE con elementos | Crear listas rapido (Java 9+) | Cualquier tipo |
| `new ArrayList<>()` | Crea lista MUTABLE vacia | Cuando vas a modificar la lista | Cualquier tipo |
| `.add(elemento)` | Agrega al final | Anadir elementos | List mutable |
| `.add(posicion, elemento)` | Inserta en posicion especifica | Insertar en medio | List mutable |
| `.get(posicion)` | Obtiene elemento en posicion | Leer por indice | List |
| `.set(posicion, elemento)` | Reemplaza elemento en posicion | Actualizar un elemento | List mutable |
| `.remove(posicion)` | Elimina por indice | Borrar en posicion | List mutable |
| `.remove(objeto)` | Elimina por valor (primera ocurrencia) | Borrar un elemento especifico | List mutable |
| `.size()` | Cuantos elementos tiene | Saber el total | List |
| `.isEmpty()` | Si esta vacia | Preguntar si tiene elementos | List, Collection |
| `.contains(objeto)` | Si contiene el elemento | Saber si algo esta en la lista | List |
| `.indexOf(objeto)` | Posicion de la primera aparicion | Encontrar donde esta | List |
| `.clear()` | Vacia toda la lista | Reiniciar la lista | List mutable |
| `.sort(comparator)` | Ordena la lista | Ordenar elementos | List mutable |
| `.stream()` | Convierte a Stream | Usar Stream API | List, Collection |
| `.toArray()` | Convierte a array | Interoperar con APIs viejas | List |
| `.toArray(T[]::new)` | Convierte a array tipado | Array con el tipo correcto | List |
| `.subList(desde, hasta)` | Obtiene una vista de un rango | Trabajar con parte de la lista | List |
| `.addAll(coleccion)` | Agrega todos los elementos de otra coleccion | Fusionar listas | List mutable |
| `.removeAll(coleccion)` | Elimina todos los que esten en la coleccion | Restar listas | List mutable |
| `.retainAll(coleccion)` | Conserva solo los que esten en la coleccion | Interseccion de listas | List mutable |
| `.replaceAll(funcion)` | Reemplaza cada elemento con el resultado | Transformar in-place | List mutable |
| `.forEach(consumer)` | Ejecuta accion por cada elemento | Iterar (simplifica for-each) | List, Iterable |

```java
public class EjemplosList {
    public static void main(String[] args) {
        // Creacion moderna con List.of() (INMUTABLE)
        var frutas = List.of("manzana", "pera", "banana");
        // frutas.add("uva");  // ERROR: UnsupportedOperationException

        // Creacion mutable
        var lista = new ArrayList<>(List.of("rojo", "verde", "azul"));
        lista.add("amarillo");
        System.out.println(lista);  // [rojo, verde, azul, amarillo]

        // Operaciones basicas
        lista.add(1, "naranja");       // insertar en posicion
        lista.set(0, "morado");        // reemplazar
        lista.remove("verde");         // eliminar por objeto
        lista.remove(0);               // eliminar por indice

        System.out.println(lista.size());       // 2
        System.out.println(lista.contains("azul"));  // true
        System.out.println(lista.indexOf("azul"));   // 0

        // Recorrer con forEach (lambda)
        lista.forEach(System.out::println);

        // .stream() para operaciones funcionales
        var cuadrados = lista.stream()
            .map(s -> s + "!")
            .toList();
        System.out.println(cuadrados);

        // .sort() con lambda
        lista.sort((a, b) -> a.length() - b.length());

        // .addAll()
        var masColores = List.of("cian", "magenta");
        lista.addAll(masColores);

        // .replaceAll()
        lista.replaceAll(String::toUpperCase);

        // .subList()
        var primerosDos = lista.subList(0, 2);
        System.out.println(primerosDos);

        // .toArray()
        var array = lista.toArray(String[]::new);
        System.out.println(Arrays.toString(array));

        // .removeIf() (default method)
        lista.removeIf(s -> s.length() < 4);
    }
}
```

---

### StringBuilder — Construir textos eficientemente

Cuando concatenas muchos strings con `+`, Java crea objetos nuevos cada vez. `StringBuilder` los construye sin crear tantos objetos.

#### Tabla de metodos esenciales de StringBuilder

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `new StringBuilder()` | Crea un builder vacio | Empezar a construir | - |
| `new StringBuilder(capacidad)` | Crea con capacidad inicial | Cuando sabes el tamano aprox | - |
| `new StringBuilder(texto)` | Crea con un texto inicial | Partir de una base | - |
| `.append(texto)` | Agrega al final | Construir progresivamente | String, int, double, etc |
| `.insert(pos, texto)` | Inserta en posicion | Poner algo en medio | String, int, double |
| `.delete(desde, hasta)` | Elimina un rango | Quitar parte del texto | StringBuilder |
| `.deleteCharAt(pos)` | Elimina un caracter | Quitar un caracter | StringBuilder |
| `.replace(desde, hasta, texto)` | Reemplaza un rango | Cambiar parte del texto | StringBuilder |
| `.reverse()` | Invierte todo el texto | Dar vuelta al string | StringBuilder |
| `.toString()` | Convierte a String | Obtener el resultado final | StringBuilder |
| `.length()` | Longitud actual | Saber cuanto lleva | StringBuilder |
| `.charAt(pos)` | Caracter en posicion | Leer sin convertir | StringBuilder |
| `.setCharAt(pos, char)` | Cambia caracter en posicion | Modificar un caracter | StringBuilder |
| `.substring(desde, hasta)` | Extrae parte como String | Obtener fragmento | StringBuilder |
| `.indexOf(sub)` | Busca subcadena | Encontrar posicion | StringBuilder |
| `.capacity()` | Capacidad interna actual | Optimizar rendimiento | StringBuilder |

```java
public class EjemplosStringBuilder {
    public static void main(String[] args) {
        // MAL: concatenacion con + en bucle (crea muchos objetos)
        // String resultado = "";
        // for (int i = 0; i < 1000; i++) { resultado += i; }

        // BIEN: StringBuilder
        var sb = new StringBuilder();

        sb.append("Hola ");
        sb.append("mundo ");
        sb.append("desde ");
        sb.append("Java ");
        sb.append(2026);

        System.out.println(sb);              // "Hola mundo desde Java 2026"
        System.out.println(sb.toString());   // igual

        // Append encadenado
        var saludo = new StringBuilder()
            .append("Hola, ")
            .append("soy ")
            .append("Ana")
            .toString();
        System.out.println(saludo);

        // Insertar
        sb.insert(5, " querido");
        System.out.println(sb);  // "Hola querido mundo desde Java 2026"

        // Reemplazar
        sb.replace(5, 13, " estimado");
        System.out.println(sb);  // "Hola estimado mundo desde Java 2026"

        // Reverse
        sb.reverse();
        System.out.println(sb);  // "6202 avaJ edes odnum odimitse aloH"
        sb.reverse();  // Volver a la normalidad

        // Delete
        sb.delete(5, 14);  // borra " estimado"
        System.out.println(sb);  // "Hola mundo desde Java 2026"

        // Cuando usarlo
        // - Bucles que concatenan (SQL, JSON, XML)
        // - Procesar archivos linea por linea
        // - Cualquier construccion de string larga
    }
}
```

---

### Scanner — Leer datos de entrada

```java
import java.util.Scanner;
```

#### Tabla de metodos esenciales de Scanner

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `new Scanner(System.in)` | Lee de teclado | Entrada de consola | System.in |
| `new Scanner(archivo)` | Lee de archivo | Leer archivos de texto | File, Path |
| `new Scanner(string)` | Lee de un String | Parsear texto | String |
| `.nextLine()` | Lee una linea entera | Texto con espacios | Teclado, archivo |
| `.next()` | Lee una palabra (hasta espacio) | Tokens individuales | Teclado, archivo |
| `.nextInt()` | Lee un entero | Numeros enteros | Teclado, archivo |
| `.nextDouble()` | Lee un decimal | Numeros con decimales | Teclado, archivo |
| `.nextBoolean()` | Lee true/false | Banderas booleanas | Teclado, archivo |
| `.hasNext()` | Si hay mas tokens | Bucle de lectura | Scanner |
| `.hasNextInt()` | Si el siguiente token es entero | Validar antes de leer | Scanner |
| `.hasNextDouble()` | Si el siguiente token es decimal | Validar antes de leer | Scanner |
| `.useDelimiter(patron)` | Cambia el separador de tokens | CSV, datos con formato | Scanner |
| `.close()` | Cierra el scanner | Liberar recurso (SIEMPRE) | Scanner |

```java
public class EjemplosScanner {
    public static void main(String[] args) {
        // Lectura de teclado con try-with-resources (cierre automatico)
        try (var entrada = new Scanner(System.in)) {
            System.out.print("Nombre: ");
            var nombre = entrada.nextLine();

            System.out.print("Edad: ");
            var edad = entrada.nextInt();
            entrada.nextLine();  // consumir el salto de linea

            System.out.println("%s tiene %d anios".formatted(nombre, edad));

        } // .close() automatico por try-with-resources

        // Leer archivo (Java moderno con Path)
        try (var sc = new Scanner(java.nio.file.Path.of("datos.txt"))) {
            while (sc.hasNextLine()) {
                var linea = sc.nextLine();
                System.out.println(linea);
            }
        } catch (java.io.IOException e) {
            System.err.println("Error: " + e.getMessage());
        }

        // Parsear un String
        var datos = "10 20 30 40 50";
        try (var sc = new Scanner(datos)) {
            while (sc.hasNextInt()) {
                System.out.println(sc.nextInt());
            }
        }
    }
}
```

---

## NIVEL 2 - Mid

### Stream API — La forma funcional de procesar datos

Un `Stream` es una secuencia de elementos que puedes transformar, filtrar y recolectar sin modificar la fuente original.

```java
import java.util.stream.Stream;
```

#### Tabla de metodos esenciales de Stream

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `Stream.of(elem...)` | Crea un Stream con elementos | Stream pequeño directo | Cualquier tipo |
| `.stream()` | Convierte coleccion a Stream | Empezar a procesar | Collection, List, Set |
| `.filter(predicate)` | Filtra los que cumplen condicion | Quitar lo que no sirve | Stream |
| `.map(funcion)` | Transforma cada elemento | Cambiar A -> B | Stream |
| `.flatMap(funcion)` | Aplana Stream de Streams en uno solo | Unir listas anidadas | Stream |
| `.forEach(consumer)` | Ejecuta por cada elemento | Accion final (side-effect) | Stream |
| `.collect(collector)` | Recoge en una estructura | Convertir a List, Set, Map | Stream |
| `.toList()` | Recoge en List inmutable (Java 16+) | El mas usado, simple | Stream |
| `.sorted()` | Ordena naturalmente | Ordenar elementos | Stream |
| `.sorted(comparator)` | Ordena con criterio personalizado | Orden personalizado | Stream |
| `.distinct()` | Elimina duplicados | Valores unicos | Stream |
| `.limit(n)` | Toma solo los primeros n | Paginar, limitar | Stream |
| `.skip(n)` | Salta los primeros n | Paginar, saltar | Stream |
| `.reduce(inicial, acumulador)` | Reduce a un solo valor | Sumar, concatenar, combinar | Stream |
| `.anyMatch(predicate)` | Si ALGUNO cumple | Pregunta existencial | Stream |
| `.allMatch(predicate)` | Si TODOS cumplen | Validacion total | Stream |
| `.noneMatch(predicate)` | Si NINGUNO cumple | Negacion total | Stream |
| `.findFirst()` | Primer elemento del Stream | Obtener uno | Stream |
| `.findAny()` | Cualquier elemento (paralelo) | Obtener uno rapido | Stream |
| `.count()` | Cuantos elementos quedan | Conteo | Stream |
| `.min(comparator)` | El menor segun criterio | Minimo | Stream |
| `.max(comparator)` | El mayor segun criterio | Maximo | Stream |
| `.peek(consumer)` | Ver cada elemento (debug) | Depuracion | Stream |
| `.mapToInt()` | Convierte a IntStream | Operaciones numericas | Stream |
| `.mapToDouble()` | Convierte a DoubleStream | Operaciones decimales | Stream |
| `.concat(a, b)` | Combina dos Streams (static) | Unir dos secuencias | Stream |
| `.takeWhile(predicate)` | Toma mientras cumpla (Java 9+) | Tomar hasta que falle | Stream |
| `.dropWhile(predicate)` | Salta mientras cumpla (Java 9+) | Saltar hasta que falle | Stream |

```java
public class EjemplosStream {
    public static void main(String[] args) {
        var nombres = List.of("Ana", "Carlos", "Beatriz", "David", "Elena");

        // .filter() + .map() + .toList()
        var nombresLargos = nombres.stream()
            .filter(n -> n.length() > 4)
            .map(String::toUpperCase)
            .sorted()
            .toList();
        System.out.println(nombresLargos);  // [BEATRIZ, CARLOS, ELENA]

        // .flatMap() — aplanar listas anidadas
        var listaDeListas = List.of(
            List.of("a", "b"),
            List.of("c", "d", "e"),
            List.of("f")
        );
        var plano = listaDeListas.stream()
            .flatMap(List::stream)
            .toList();
        System.out.println(plano);  // [a, b, c, d, e, f]

        // .reduce() — combinar en un solo valor
        var numeros = List.of(1, 2, 3, 4, 5);
        var suma = numeros.stream()
            .reduce(0, Integer::sum);
        System.out.println(suma);  // 15

        var concatenado = nombres.stream()
            .reduce("", (a, b) -> a + " " + b);
        System.out.println(concatenado.strip());

        // .anyMatch(), .allMatch(), .noneMatch()
        System.out.println(numeros.stream().anyMatch(n -> n > 4));   // true
        System.out.println(numeros.stream().allMatch(n -> n > 0));   // true
        System.out.println(numeros.stream().noneMatch(n -> n > 10)); // true

        // .distinct()
        var duplicados = List.of(1, 1, 2, 2, 3, 3, 4);
        var unicos = duplicados.stream().distinct().toList();
        System.out.println(unicos);  // [1, 2, 3, 4]

        // .limit() y .skip()
        var primeros3 = numeros.stream().limit(3).toList();
        var saltando2 = numeros.stream().skip(2).toList();
        System.out.println(primeros3);  // [1, 2, 3]
        System.out.println(saltando2);  // [3, 4, 5]

        // .takeWhile() y .dropWhile()
        var tomados = numeros.stream().takeWhile(n -> n < 4).toList();
        var saltados = numeros.stream().dropWhile(n -> n < 4).toList();
        System.out.println(tomados);   // [1, 2, 3]
        System.out.println(saltados);  // [4, 5]

        // .findFirst()
        var primero = nombres.stream()
            .filter(n -> n.startsWith("C"))
            .findFirst();
        primero.ifPresent(System.out::println);  // Carlos

        // .mapToInt() para sumar
        var sumaTotal = numeros.stream()
            .mapToInt(Integer::intValue)
            .sum();
        System.out.println(sumaTotal);  // 15

        // .peek() para debug
        var resultado = nombres.stream()
            .peek(n -> System.out.println("Original: " + n))
            .filter(n -> n.length() > 4)
            .peek(n -> System.out.println("Filtrado: " + n))
            .map(String::toUpperCase)
            .toList();
    }
}
```

---

### Map / HashMap — Pares clave-valor

Un `Map` asocia una clave unica con un valor. Como un diccionario: buscas por palabra (clave) y obtienes la definicion (valor).

```java
import java.util.Map;
import java.util.HashMap;
```

#### Tabla de metodos esenciales de Map

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `Map.of(k, v, k, v...)` | Crea Map INMUTABLE (Java 9+) | Map pequeño constante | Cualquier par |
| `new HashMap<>()` | Crea Map MUTABLE vacio | Map que cambiara | Cualquier par |
| `.put(clave, valor)` | Inserta o actualiza | Anadir o modificar entrada | Map mutable |
| `.putIfAbsent(clave, valor)` | Inserta solo si no existe | Evitar sobrescribir | Map mutable |
| `.get(clave)` | Obtiene valor (o null si no existe) | Buscar por clave | Map |
| `.getOrDefault(clave, default)` | Obtiene valor o default | Buscar sin null | Map |
| `.remove(clave)` | Elimina entrada por clave | Borrar del mapa | Map mutable |
| `.containsKey(clave)` | Si la clave existe | Preguntar antes de get | Map |
| `.containsValue(valor)` | Si el valor existe | Busqueda inversa | Map |
| `.keySet()` | Conjunto de claves | Iterar claves | Map |
| `.values()` | Coleccion de valores | Iterar valores | Map |
| `.entrySet()` | Conjunto de entradas (clave=valor) | Iterar ambos | Map |
| `.size()` | Cuantas entradas tiene | Contar elementos | Map |
| `.isEmpty()` | Si esta vacio | Verificar si hay datos | Map |
| `.clear()` | Vacia todo el mapa | Reiniciar | Map mutable |
| `.forEach(biConsumer)` | Iterar clave+valor | Recorrer entero | Map |
| `.computeIfAbsent(clave, funcion)` | Calcula valor si clave no existe | Cache, inicializacion perezosa | Map mutable |
| `.computeIfPresent(clave, funcion)` | Calcula nuevo valor si clave existe | Actualizar si existe | Map mutable |
| `.merge(clave, valor, funcion)` | Combina valor nuevo con existente | Acumular valores | Map mutable |
| `.putAll(map)` | Copia todas las entradas de otro mapa | Fusionar maps | Map mutable |
| `.replace(clave, valor)` | Reemplaza si existe (no inserta) | Actualizar solo existentes | Map mutable |

```java
public class EjemplosMap {
    public static void main(String[] args) {
        // Creacion con Map.of() (INMUTABLE, max 10 entradas)
        var colores = Map.of(
            "rojo", "#FF0000",
            "verde", "#00FF00",
            "azul", "#0000FF"
        );

        // Creacion mutable
        var edades = new HashMap<String, Integer>();
        edades.put("Ana", 25);
        edades.put("Carlos", 30);
        edades.put("Beatriz", 28);

        // .get() y .getOrDefault()
        System.out.println(edades.get("Ana"));           // 25
        System.out.println(edades.getOrDefault("David", 0));  // 0

        // .putIfAbsent()
        edades.putIfAbsent("Ana", 99);  // No sobrescribe, Ana ya existe
        System.out.println(edades.get("Ana"));  // Sigue siendo 25

        // .containsKey()
        if (edades.containsKey("Carlos")) {
            System.out.println("Carlos tiene " + edades.get("Carlos"));
        }

        // Iterar con .forEach()
        edades.forEach((nombre, edad) ->
            System.out.println(nombre + " -> " + edad)
        );

        // .entrySet() para Stream API
        edades.entrySet().stream()
            .filter(e -> e.getValue() > 26)
            .forEach(e -> System.out.println(e.getKey()));

        // .computeIfAbsent() — inicializar si no existe
        var conteoPalabras = new HashMap<String, Integer>();
        var texto = "hola mundo hola java hola";
        for (var palabra : texto.split(" ")) {
            conteoPalabras.computeIfAbsent(palabra, k -> 0);
            conteoPalabras.merge(palabra, 1, Integer::sum);
        }
        System.out.println(conteoPalabras);
        // {mundo=1, java=1, hola=3}

        // .keySet() y .values()
        System.out.println(edades.keySet());   // [Ana, Carlos, Beatriz]
        System.out.println(edades.values());   // [25, 30, 28]

        // Map.ofEntries() para mas de 10 entradas
        var masColores = Map.ofEntries(
            Map.entry("blanco", "#FFFFFF"),
            Map.entry("negro", "#000000"),
            Map.entry("gris", "#808080")
        );
    }
}
```

---

### Arrays — Utilidades para arreglos

La clase `java.util.Arrays` tiene metodos ESTATICOS para trabajar con arrays clasicos.

#### Tabla de metodos esenciales de Arrays (util)

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `Arrays.toString(array)` | Convierte array a String legible | Mostrar array en consola | Cualquier tipo |
| `Arrays.sort(array)` | Ordena el array (ascendente) | Ordenar elementos | Primitivos, objetos |
| `Arrays.sort(array, comparador)` | Ordena con criterio | Orden personalizado | Objetos |
| `Arrays.binarySearch(array, clave)` | Busca en array ORDENADO (indice o -1) | Busqueda rapida | Primitivos, objetos |
| `Arrays.copyOf(original, nuevaLong)` | Copia con nueva longitud | Redimensionar array | Cualquier tipo |
| `Arrays.copyOfRange(original, desde, hasta)` | Copia un rango | Extraer parte del array | Cualquier tipo |
| `Arrays.equals(a, b)` | Compara dos arrays | Saber si son iguales | Cualquier tipo |
| `Arrays.fill(array, valor)` | Llena todo con un valor | Inicializar | Primitivos, objetos |
| `Arrays.asList(array)` | Convierte a List (respalda el array!) | Bridge a Collection API | Objetos |
| `Arrays.stream(array)` | Convierte a Stream | Procesar con Stream API | Primitivos, objetos |
| `Arrays.deepToString(array)` | String de arrays multidimensionales | Mostrar matrices | Object[] |
| `Arrays.parallelSort(array)` | Ordena en paralelo (mas rapido) | Arrays GRANDES | Primitivos, objetos |
| `Arrays.mismatch(a, b)` | Primera posicion donde difieren (Java 9+) | Encontrar diferencia | Primitivos, objetos |
| `Arrays.compare(a, b)` | Compara lexicograficamente (Java 9+) | Orden entre arrays | Primitivos, objetos |

```java
public class EjemplosArraysUtil {
    public static void main(String[] args) {
        var numeros = new int[]{5, 2, 8, 1, 9, 3};

        // .toString()
        System.out.println(Arrays.toString(numeros));  // [5, 2, 8, 1, 9, 3]

        // .sort()
        Arrays.sort(numeros);
        System.out.println(Arrays.toString(numeros));  // [1, 2, 3, 5, 8, 9]

        // .binarySearch() (SOLO en array ordenado)
        var indice = Arrays.binarySearch(numeros, 5);
        System.out.println("El 5 esta en posicion: " + indice);  // 3

        // .copyOf() — "redimensionar"
        var masGrande = Arrays.copyOf(numeros, 10);
        System.out.println(Arrays.toString(masGrande));  // [1, 2, 3, 5, 8, 9, 0, 0, 0, 0]

        // .copyOfRange()
        var rango = Arrays.copyOfRange(numeros, 1, 4);
        System.out.println(Arrays.toString(rango));  // [2, 3, 5]

        // .fill()
        var relleno = new int[5];
        Arrays.fill(relleno, 42);
        System.out.println(Arrays.toString(relleno));  // [42, 42, 42, 42, 42]

        // .asList() (CUIDADO: respalda el array!)
        var lista = Arrays.asList(numeros);  // solo para Integer[], no int[]
        // Para primitivos, usa stream():
        var listaStream = Arrays.stream(numeros).boxed().toList();

        // .stream()
        var suma = Arrays.stream(numeros).sum();
        var promedio = Arrays.stream(numeros).average().orElse(0);
        System.out.println("Suma: " + suma + ", Promedio: " + promedio);

        // .deepToString() para matrices
        var matriz = new int[][]{{1, 2}, {3, 4}};
        System.out.println(Arrays.deepToString(matriz));  // [[1, 2], [3, 4]]
    }
}
```

---

### Integer, Double, Long — Wrappers y metodos utiles

Las clases envolventes (`Integer`, `Double`, `Long`, etc.) tienen metodos ESTATICOS para convertir y operar con numeros.

#### Tabla de metodos esenciales de wrappers

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `Integer.parseInt(texto)` | Convierte String a int | Leer numeros de texto | String |
| `Integer.valueOf(texto)` | Convierte String a Integer | Preferir sobre parseInt (cachea -128 a 127) | String |
| `Integer.valueOf(int)` | Boxea un int a Integer | Crear Integer | int |
| `.intValue()` | Extrae el int del Integer | Desboxeo manual | Integer |
| `.toString()` | Convierte numero a String | Mostrar el numero | Integer, Double |
| `Integer.toString(int)` | Version estatica | Convertir int a String | int |
| `Integer.compare(a, b)` | Compara dos ints (-1, 0, 1) | Ordenar, comparar | int |
| `Integer.sum(a, b)` | Suma (functional-style) | Method reference | int |
| `Integer.max(a, b)` | Mayor de dos | Maximo | int |
| `Integer.min(a, b)` | Menor de dos | Minimo | int |
| `Double.parseDouble(texto)` | Convierte String a double | Leer decimales de texto | String |
| `Integer.toBinaryString(n)` | Convierte a binario | Representacion binaria | int |
| `Integer.toHexString(n)` | Convierte a hexadecimal | Representacion hex | int |
| `.isNaN()` | Si es NaN (Double/Float) | Validar calculos matematicos | Double, Float |
| `.isInfinite()` | Si es infinito (Double/Float) | Validar calculos | Double, Float |
| `Integer.MAX_VALUE` | Constante: valor maximo | Limites de tipo | - |
| `Integer.MIN_VALUE` | Constante: valor minimo | Limites de tipo | - |

```java
public class EjemplosWrappers {
    public static void main(String[] args) {
        // Conversion desde String
        var numero = Integer.parseInt("42");
        var decimal = Double.parseDouble("3.14");
        var logico = Boolean.parseBoolean("true");

        System.out.println(numero + 10);  // 52

        // .valueOf() con cache
        var a = Integer.valueOf(127);
        var b = Integer.valueOf(127);
        System.out.println(a == b);  // true (cacheado)

        var c = Integer.valueOf(200);
        var d = Integer.valueOf(200);
        System.out.println(c == d);  // false (no cacheado, usa .equals())

        // Method references con Integer::sum
        var lista = List.of(1, 2, 3, 4, 5);
        var suma = lista.stream()
            .reduce(0, Integer::sum);
        System.out.println(suma);  // 15

        // Integer.compare para ordenar
        var numeros = new int[]{5, 2, 8, 1};
        var ordenados = Arrays.stream(numeros)
            .boxed()
            .sorted((x, y) -> Integer.compare(x, y))
            .toList();
        System.out.println(ordenados);

        // Representaciones
        System.out.println(Integer.toBinaryString(42));  // 101010
        System.out.println(Integer.toHexString(255));    // ff

        // Limites
        System.out.println(Integer.MAX_VALUE);  // 2147483647
        System.out.println(Integer.MIN_VALUE);  // -2147483648
    }
}
```

---

## NIVEL 3 - Senior

### Object — Los metodos que TODA clase hereda

`Object` es la clase raiz de Java. Todo objeto hereda estos metodos automaticamente.

```java
// Toda clase es hija de Object aunque no lo escribas
public class MiClase /* extends Object implicito */ {
}
```

#### Tabla de metodos de Object

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `.equals(otro)` | Compara si dos objetos son IGUALES (por valor) | Comparar objetos (NUNCA `==`) | Toda clase |
| `.hashCode()` | Numero entero que representa al objeto | HashMap, HashSet, busqueda rapida | Toda clase |
| `.toString()` | Representacion del objeto como String | Mostrar informacion del objeto | Toda clase |
| `.getClass()` | Devuelve el objeto Class del tipo en runtime | Reflexion, instanceof alternativo | Toda clase |
| `.clone()` | Crea una copia del objeto (solo si implements Cloneable) | Clonacion superficial | Clases que implementen Cloneable |
| `.finalize()` | Llamado por GC antes de destruir (DEPRECATED desde Java 9) | **NO USAR** | Toda clase |
| `.notify()` | Despierta un hilo esperando en este monitor | Sincronizacion de hilos | Toda clase |
| `.notifyAll()` | Despierta todos los hilos esperando | Sincronizacion de hilos | Toda clase |
| `.wait()` | Espera hasta que otro hilo notifique | Sincronizacion de hilos | Toda clase |

**Regla de oro**: Si sobreescribes `equals()`, SIEMPRE sobreescribe `hashCode()`. Juntos garantizan que dos objetos iguales tengan el mismo hash.

```java
import java.util.Objects;

public class Persona {
    private String nombre;
    private int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    // equals() generado con Objects (Java 7+, moderno)
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        var persona = (Persona) o;
        return edad == persona.edad && Objects.equals(nombre, persona.nombre);
    }

    // hashCode() generado con Objects
    @Override
    public int hashCode() {
        return Objects.hash(nombre, edad);
    }

    // toString() generado con String.formatted()
    @Override
    public String toString() {
        return "Persona[nombre=%s, edad=%d]".formatted(nombre, edad);
    }
}
```

```java
public class EjemplosObject {
    public static void main(String[] args) {
        var p1 = new Persona("Ana", 25);
        var p2 = new Persona("Ana", 25);
        var p3 = new Persona("Carlos", 30);

        // .equals() compara por VALOR
        System.out.println(p1.equals(p2));  // true
        System.out.println(p1.equals(p3));  // false

        // .hashCode()
        System.out.println(p1.hashCode() == p2.hashCode());  // true (iguales = mismo hash)

        // .toString()
        System.out.println(p1);  // Persona[nombre=Ana, edad=25]
        System.out.println(p3);  // Persona[nombre=Carlos, edad=30]

        // .getClass()
        System.out.println(p1.getClass());           // class Persona
        System.out.println(p1.getClass().getSimpleName());  // Persona

        // Record NO necesitan equals/hashCode/toString manuales (vienen automaticos)
    }
}
```

---

### Records — Los metodos vienen incluidos

Los Records son clases inmutables donde el compilador genera TODO automaticamente: constructor, accesores, `equals()`, `hashCode()`, `toString()`.

```java
// Esto solo:
public record Usuario(String nombre, int edad) {}

// Genera automaticamente:
// - constructor: Usuario(String nombre, int edad)
// - accesor: .nombre() (NO getNombre(), solo nombre())
// - accesor: .edad()
// - .equals(), .hashCode(), .toString()
```

#### Tabla de metodos de Record

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `.componente()` (ej: `.nombre()`) | Accede al componente | Leer el valor automatico | Record |
| `.equals(otro)` | Compara TODOS los componentes | Comparar records | Record |
| `.hashCode()` | Hash basado en componentes | HashMap/HashSet con records | Record |
| `.toString()` | Muestra todos los componentes | Mostrar record | Record |
| `.getClass()` | Heredado de Object | Informacion de tipo | Record |
| `RecordComponent[] getRecordComponents()` | Reflexion: componentes del record | Frameworks, serializacion | Record |

```java
// Definicion compacta con validacion (Java moderno)
public record Producto(String nombre, double precio) {
    // Constructor compacto (no necesita parametros)
    public Producto {
        if (precio < 0) {
            throw new IllegalArgumentException("Precio no puede ser negativo");
        }
        // nombre y precio se asignan automaticamente
    }

    // Puedes anadir metodos adicionales
    public String precioFormateado() {
        return "$ %.2f".formatted(precio);
    }
}

public class EjemplosRecord {
    public static void main(String[] args) {
        var p1 = new Producto("Laptop", 999.99);
        var p2 = new Producto("Laptop", 999.99);
        var p3 = new Producto("Mouse", 25.50);

        // Accesores (SIN get)
        System.out.println(p1.nombre());   // Laptop
        System.out.println(p1.precio());   // 999.99

        // equals y hashCode automaticos
        System.out.println(p1.equals(p2));  // true
        System.out.println(p1.equals(p3));  // false

        // toString automatico
        System.out.println(p1);  // Producto[nombre=Laptop, precio=999.99]

        // Metodo extra del record
        System.out.println(p1.precioFormateado());  // $ 999.99

        // Usar en colecciones
        var productos = List.of(p1, p3);
        var precios = productos.stream()
            .map(Producto::precio)
            .toList();
        System.out.println(precios);
    }
}
```

---

### Path / Files — El sistema de archivos moderno

Desde Java 7 (NIO.2), la forma moderna de trabajar con archivos es `java.nio.file.Path` y `java.nio.file.Files`.

```java
import java.nio.file.Path;
import java.nio.file.Files;
```

#### Tabla de metodos de Path y Files

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `Path.of(ruta)` | Crea un Path desde String | Trabajar con rutas de archivos | String, URI |
| `Path.of(partes...)` | Crea Path desde partes | Construir rutas | Varargs String |
| `.resolve(otro)` | Resuelve una ruta relativa contra esta | Combinar directorios | Path |
| `.getParent()` | Directorio padre | Navegar hacia arriba | Path |
| `.getFileName()` | Nombre del archivo/carpeta | Extraer nombre | Path |
| `.toAbsolutePath()` | Convierte a ruta absoluta | Obtener ruta completa | Path |
| `.normalize()` | Elimina `.` y `..` | Limpiar la ruta | Path |
| `Files.exists(path)` | Si el archivo/carpeta existe | Verificar existencia | Path |
| `Files.readString(path)` | Lee todo el archivo como String | Archivos pequenos | Path |
| `Files.writeString(path, texto)` | Escribe texto en archivo | Archivos pequenos | Path |
| `Files.readAllLines(path)` | Lee todas las lineas como List\<String\> | Procesar lineas | Path |
| `Files.write(path, lineas)` | Escribe lineas en archivo | Guardar lineas | Path, Iterable |
| `Files.lines(path)` | Stream de lineas (lazy, hay que cerrarlo) | Archivos GRANDES | Path |
| `Files.createFile(path)` | Crea archivo fisico | Crear archivo vacio | Path |
| `Files.createDirectory(path)` | Crea una carpeta | Crear directorio | Path |
| `Files.createDirectories(path)` | Crea toda la estructura de carpetas | mkdir -p | Path |
| `Files.delete(path)` | Borra archivo o carpeta VACIA | Eliminar | Path |
| `Files.deleteIfExists(path)` | Borra si existe (sin error si no) | Eliminar seguro | Path |
| `Files.copy(origen, destino)` | Copia archivo | Duplicar | Path, Path |
| `Files.move(origen, destino)` | Mueve o renombra | Trasladar | Path, Path |
| `Files.size(path)` | Tamano del archivo en bytes | Saber el peso | Path |
| `Files.isDirectory(path)` | Si es una carpeta | Verificar tipo | Path |
| `Files.isRegularFile(path)` | Si es un archivo normal | Verificar tipo | Path |
| `Files.list(path)` | Stream de archivos en directorio (hay que cerrarlo) | Listar contenido | Path |
| `Files.walk(path)` | Recorre recursivamente | Buscar en subdirectorios | Path |
| `Files.find(path, maxDepth, matcher)` | Busca archivos con filtro | Busqueda avanzada | Path |
| `Files.probeContentType(path)` | Detecta tipo MIME | Saber si es imagen, PDF, etc | Path |
| `Files.getLastModifiedTime(path)` | Ultima modificacion | Saber cuando se modifico | Path |
| `Files.createTempFile(pref, suf)` | Archivo temporal | Archivos descartables | String |
| `File.mkdir()` | Legacy: crear carpeta | NO USAR, usar Files | - |

```java
public class EjemplosPathFiles {
    public static void main(String[] args) throws java.io.IOException {
        // Crear Path
        var ruta = Path.of("C:", "Users", "arobl", "Desktop", "datos.txt");
        var relativa = Path.of("datos", "entrada.txt");

        // Operaciones con Path
        System.out.println(ruta.getFileName());        // datos.txt
        System.out.println(ruta.getParent());          // C:\Users\arobl\Desktop
        System.out.println(relativa.toAbsolutePath()); // C:\...\datos\entrada.txt

        // Leer archivo completo (Java 11+)
        var contenido = Files.readString(ruta);
        System.out.println(contenido);

        // Escribir archivo (Java 11+)
        var salida = Path.of("output.txt");
        Files.writeString(salida, "Hola Mundo desde Java 2026");

        // Leer todas las lineas
        var lineas = Files.readAllLines(ruta);
        lineas.forEach(System.out::println);

        // Stream de lineas (lazy, eficiente para archivos grandes)
        try (var stream = Files.lines(ruta)) {
            stream
                .filter(l -> l.contains("error"))
                .forEach(System.out::println);
        }

        // Crear directorios
        var carpeta = Path.of("backup", "2026", "julio");
        Files.createDirectories(carpeta);  // Crea toda la estructura

        // Listar directorio
        try (var archivos = Files.list(Path.of("."))) {
            archivos
                .filter(Files::isRegularFile)
                .forEach(System.out::println);
        }

        // Recorrer recursivamente
        try (var arbol = Files.walk(Path.of("src"))) {
            arbol
                .filter(p -> p.toString().endsWith(".java"))
                .forEach(System.out::println);
        }

        // Operaciones condicionales
        var destino = Path.of("copia.txt");
        if (Files.exists(ruta)) {
            Files.copy(ruta, destino, java.nio.file.StandardCopyOption.REPLACE_EXISTING);
        }

        // Archivo temporal
        var temp = Files.createTempFile("reporte-", ".txt");
        Files.writeString(temp, "Datos temporales");
        System.out.println("Temp: " + temp);
    }
}
```

---

### Collectors — Como recoger los streams

`Collectors` es una clase con metodos ESTATICOS que se usan con `.collect(Collectors.xxx)` o directamente con `.toList()`.

```java
import java.util.stream.Collectors;
```

#### Tabla de metodos esenciales de Collectors

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `.toList()` | Recoge en List inmutable (directo en Stream) | CASI SIEMPRE, es el default | Stream |
| `Collectors.toSet()` | Recoge en Set (sin duplicados) | Valores unicos | Stream |
| `Collectors.toMap(k, v)` | Recoge en Map | Agrupar como pares | Stream |
| `Collectors.joining()` | Une Strings sin separador | Concatenar texto | Stream de String |
| `Collectors.joining(sep)` | Une Strings con separador | CSV, listas legibles | Stream de String |
| `Collectors.joining(sep, pre, suf)` | Une con prefijo y sufijo | "[a, b, c]" | Stream de String |
| `Collectors.groupingBy(funcion)` | Agrupa por clave -> Map<K, List<V>> | Categorizar elementos | Stream |
| `Collectors.partitioningBy(pred)` | Divide en dos grupos (true/false) | Separar por condicion | Stream |
| `Collectors.counting()` | Cuenta elementos | Conteo (mas claro que .count()) | Stream |
| `Collectors.summingInt(func)` | Suma de valores int | Sumar atributos | Stream |
| `Collectors.averagingInt(func)` | Promedio de valores int | Calcular media | Stream |
| `Collectors.summarizingInt(func)` | Estadisticas: count, sum, min, avg, max | Resumen completo | Stream |
| `Collectors.mapping(func, downstream)` | Mapea y recoge | Transformar antes de agrupar | Stream |
| `Collectors.filtering(pred, downstream)` | Filtra y recoge | Filtrar antes de recolectar | Stream |
| `Collectors.reducing(inicial, func)` | Reduccion personalizada | Reducir con custom | Stream |
| `Collectors.collectingAndThen(down, finisher)` | Recoge y luego transforma | Post-procesar coleccion | Stream |
| `Collectors.teeing(c1, c2, merge)` | Divide en dos collectors y combina (Java 12+) | Dos operaciones en un pase | Stream |

```java
public class EjemplosCollectors {
    public static void main(String[] args) {
        var personas = List.of(
            new Persona("Ana", 25),
            new Persona("Carlos", 30),
            new Persona("Beatriz", 25),
            new Persona("David", 30),
            new Persona("Elena", 28)
        );

        // .toList() — el mas comun (inmutable)
        var nombres = personas.stream()
            .map(Persona::toString)
            .toList();

        // groupingBy() — agrupar por edad
        var porEdad = personas.stream()
            .collect(Collectors.groupingBy(Persona::edad));
        System.out.println(porEdad);
        // {25=[Ana, Beatriz], 28=[Elena], 30=[Carlos, David]}

        // partitioningBy() — dos grupos
        var mayoresDe27 = personas.stream()
            .collect(Collectors.partitioningBy(p -> p.edad() > 27));
        System.out.println(mayoresDe27);
        // {false=[Ana, Beatriz], true=[Carlos, David, Elena]}

        // joining()
        var listaNombres = personas.stream()
            .map(Persona::nombre)
            .collect(Collectors.joining(", "));
        System.out.println("Nombres: " + listaNombres);
        // Nombres: Ana, Carlos, Beatriz, David, Elena

        // summarizingInt() — estadisticas completas
        var stats = personas.stream()
            .collect(Collectors.summarizingInt(Persona::edad));
        System.out.println("Count: " + stats.getCount());   // 5
        System.out.println("Sum: " + stats.getSum());       // 138
        System.out.println("Avg: " + stats.getAverage());   // 27.6
        System.out.println("Min: " + stats.getMin());       // 25
        System.out.println("Max: " + stats.getMax());       // 30

        // groupingBy() con downstream — agrupar y contar
        var cuantosPorEdad = personas.stream()
            .collect(Collectors.groupingBy(
                Persona::edad,
                Collectors.counting()
            ));
        System.out.println(cuantosPorEdad);
        // {25=2, 28=1, 30=2}

        // groupingBy() con mapping — agrupar y extraer campo
        var nombresPorEdad = personas.stream()
            .collect(Collectors.groupingBy(
                Persona::edad,
                Collectors.mapping(Persona::nombre, Collectors.toList())
            ));
        System.out.println(nombresPorEdad);
        // {25=[Ana, Beatriz], 28=[Elena], 30=[Carlos, David]}

        // teeing() — dos resultados en un solo pase
        var sumaYConteo = personas.stream()
            .collect(Collectors.teeing(
                Collectors.summingInt(Persona::edad),
                Collectors.counting(),
                (suma, conteo) -> "Suma: " + suma + ", Conteo: " + conteo
            ));
        System.out.println(sumaYConteo);

        // filtering() — filtrar antes de recolectar
        var nombresFiltrados = personas.stream()
            .collect(Collectors.filtering(
                p -> p.edad() > 27,
                Collectors.mapping(Persona::nombre, Collectors.joining(", "))
            ));
        System.out.println(nombresFiltrados);  // Carlos, David, Elena
    }
}

// Necesario para los ejemplos
record Persona(String nombre, int edad) {}
```

---

### Comparator — Ordenar con criterio

`Comparator` es una interfaz funcional que define como ordenar objetos.

#### Tabla de metodos de Comparator

| Metodo | Que hace | Cuando usarlo | Compatible con |
|--------|----------|---------------|----------------|
| `Comparator.naturalOrder()` | Orden natural (ascendente) | Ordenar por Comparable | Cualquier Comparable |
| `Comparator.reverseOrder()` | Orden inverso (descendente) | Ordenar al reves | Cualquier Comparable |
| `.reversed()` | Invierte el comparador actual | Dar vuelta el orden | Comparator |
| `Comparator.comparing(extractor)` | Ordena por un campo | Ordenar por atributo especifico | Function<T, R> |
| `Comparator.comparingInt(extractor)` | Ordena por int (evita boxeo) | Campos int | ToIntFunction |
| `.thenComparing(otro)` | Segundo criterio si el primero empata | Orden compuesto | Comparator |
| `.thenComparingInt(extractor)` | Segundo criterio int | Orden compuesto con int | Comparator |
| `Comparator.nullsFirst(comp)` | Pone nulls al inicio | Manejar valores null | Comparator |
| `Comparator.nullsLast(comp)` | Pone nulls al final | Manejar valores null | Comparator |
| `Comparator.comparing(extractor, keyComp)` | Ordena con comparador para la clave | Orden personalizado por campo | Function, Comparator |

```java
public class EjemplosComparator {
    public static void main(String[] args) {
        var personas = List.of(
            new Persona("Carlos", 30),
            new Persona("Ana", 25),
            new Persona("Beatriz", 30),
            new Persona("David", 25)
        );

        // Ordenar por nombre
        var porNombre = personas.stream()
            .sorted(Comparator.comparing(Persona::nombre))
            .toList();
        System.out.println(porNombre);
        // [Ana, Beatriz, Carlos, David]

        // Ordenar por edad descendente, luego nombre ascendente
        var porEdadLuegoNombre = personas.stream()
            .sorted(Comparator
                .comparingInt(Persona::edad)
                .reversed()
                .thenComparing(Persona::nombre))
            .toList();
        System.out.println(porEdadLuegoNombre);
        // [Beatriz(30), Carlos(30), Ana(25), David(25)]

        // Orden natural (si Persona implementara Comparable)
        // .sorted(Comparator.naturalOrder())

        // Comparador inline con lambda
        var porLongitudNombre = personas.stream()
            .sorted((a, b) -> Integer.compare(
                a.nombre().length(),
                b.nombre().length()))
            .toList();
    }
}
```

---

## Errores Comunes

### 1. Usar `==` en vez de `.equals()` para comparar Strings

```java
var a = "Hola";
var b = new String("Hola");
System.out.println(a == b);        // false (son objetos distintos)
System.out.println(a.equals(b));   // true (son textualmente iguales)
```

### 2. No cerrar Scanner

```java
var sc = new Scanner(System.in);
// ... usarlo ...
// sc.close();  // Si no lo cierras, el programa mantiene el recurso

// SOLUCION: try-with-resources
try (var sc = new Scanner(System.in)) {
    // se cierra solo
}
```

### 3. Modificar una lista creada con `List.of()`

```java
var lista = List.of("a", "b");
// lista.add("c");  // UnsupportedOperationException
```

### 4. Usar `Optional.get()` sin verificar

```java
var opt = Optional.empty();
// var valor = opt.get();  // NoSuchElementException
// SIEMPRE usa .orElse(), .orElseThrow() o .ifPresent()
```

### 5. Olvidar que StringBuilder no es String

```java
var sb = new StringBuilder("Hola");
// if (sb.equals("Hola")) {}  // FALSE! sb es StringBuilder, no String
if (sb.toString().equals("Hola")) {}  // Correcto
```

### 6. Usar Stream despues de cerrarlo

```java
var stream = lista.stream();
stream.forEach(System.out::println);
// stream.forEach(System.out::println);  // IllegalStateException: stream has already been operated upon
```

### 7. Olvidar equals + hashCode juntos

```java
public class Punto {
    int x, y;
    // equals() pero NO hashCode() -> HashMap no funciona bien
}
```

### 8. Usar `.collect(Collectors.toList())` en vez de `.toList()`

```java
// VIEJO (Java 8-15):
var lista = stream.collect(Collectors.toList());  // mutable

// NUEVO (Java 16+):
var lista = stream.toList();  // inmutable, mas simple
```

---

## Buenas Practicas

1. **Encadena metodos cortos** en vez de muchos pasos separados
   ```java
   var resultado = lista.stream()
       .filter(x -> x > 0)
       .map(String::valueOf)
       .sorted()
       .toList();
   ```

2. **Elige el metodo correcto para cada caso**
   - `.strip()` > `.trim()` (soporta Unicode)
   - `.toList()` > `.collect(Collectors.toList())` (inmutable)
   - `.orElseThrow()` > `.get()` (mas seguro)
   - `.formatted()` > `String.format()` (mas legible)

3. **Prefiere metodos sin side-effects** en Stream API
   - `.filter()` `.map()` `.sorted()` — bien
   - `.forEach()` solo al final — bien
   - `.peek()` solo para debug — no lo uses en produccion

4. **Usa method references** cuando sea claro
   ```java
   // Bien: .map(Persona::nombre)
   // Menos bien: .map(p -> p.nombre())
   ```

5. **Cierra recursos manuales** o usa try-with-resources
   ```java
   try (var lines = Files.lines(path)) { ... }
   ```

6. **Sobreescribe equals SOLO si tiene sentido logico**
   - Dos objetos son iguales si representan lo mismo, no si son la misma direccion de memoria.

7. **Usa Records para datos inmutables** — ya vienen con equals, hashCode, toString.

8. **No abuses de Optional**
   - Optional es para RETORNO de metodos, no para campos, parametros, o colecciones.

9. **Conoce tu Stream**
   - Operaciones intermedias (`.filter()`, `.map()`) son **lazy** — no se ejecutan hasta que hay una terminal (`.toList()`, `.forEach()`, `.reduce()`).

10. **Usa `Map.of()` y `List.of()` para constantes**
    ```java
    private static final List<String> DIAS = List.of("Lun", "Mar", "Mie", "Jue", "Vie");
    private static final Map<String, String> COLORS = Map.of("rojo", "#FF0000", "verde", "#00FF00");
    ```

---

## Conexiones

- [[01 - Clases y Estructura Basica]] — Los metodos viven dentro de clases
- [[04 - Variables y Literales]] — Los Strings son literales
- [[06 - Atributos y Campos]] — Atributos almacenan datos que los metodos procesan
- [[07 - Constructores y this]] — Los constructores son metodos especiales
- [[08 - Instanciacion y new]] — Creas objetos, luego llamas sus metodos
- [[10 - Metodos de Instancia]] — Base conceptual de los metodos
- [[11 - System.out y Concatenacion]] — System.out.println() tambien es un .method()
- [[13 - Static vs Instancia]] — Metodos estaticos vs de instancia
- [[15 - Listas y ArrayList]] — Las listas son el primer lugar donde usaras muchos .method()
- [[16 - Bucles y Control de Flujo]] — Recorres colecciones con .method()
- [[21 - Getters y Setters]] — Los metodos mas repetitivos (y como evitarlos con Records)
- [[22 - Separacion de Responsabilidades]] — Cada metodo hace una cosa
- [[24 - ArrayList - Metodos Avanzados]] — Metodos especificos de ArrayList
- [[26 - Private en Profundidad]] — Private protege los metodos internos
- [[27 - Singleton en Profundidad]] — Metodo estatico obtener()
- [[28 - Static en Profundidad]] — Metodos sin objeto
- [[29 - Scanner en Profundidad]] — Scanner tiene sus propios .method()
- [[30 - Principio fundamental de Java (y POO)]] — Los metodos son el comportamiento de los objetos
- [[99 - Glosario Rapido]] — Definiciones rapidas de cada metodo