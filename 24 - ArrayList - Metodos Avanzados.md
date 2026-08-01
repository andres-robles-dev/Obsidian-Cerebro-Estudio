---
tags: [java, fundamentos, arraylist, colecciones, list, stream, metodos-avanzados]
---

# 24 - ArrayList - Metodos Avanzados

---

## NIVEL JUNIOR

### Repaso rapido

```java
import java.util.ArrayList;

ArrayList<String> lista = new ArrayList<>();
lista.add("A");
lista.add("B");
lista.add("C");

System.out.println(lista.size());     // 3
System.out.println(lista.get(0));     // "A"
System.out.println(lista.contains("B")); // true
lista.remove(1);                      // Elimina "B"
```

### Metodos basicos que ya conoces

| Metodo | Que hace |
|--------|----------|
| `add(elemento)` | Anade al final |
| `get(indice)` | Obtiene por posicion |
| `set(indice, elemento)` | Reemplaza en posicion |
| `remove(indice)` | Elimina por posicion |
| `remove(elemento)` | Elimina el primero igual |
| `size()` | Numero de elementos |
| `contains(elemento)` | Si existe o no |
| `indexOf(elemento)` | Posicion del elemento (-1 si no) |
| `isEmpty()` | Si esta vacia |
| `clear()` | Vacia la lista |

---

## NIVEL MID

### Recorrer y modificar

```java
import java.util.*;

public class DemoMedio {
    public static void main(String[] args) {
        List<String> palabras = new ArrayList<>(List.of("sol", "luna", "mar", "arbol", "rio"));

        // Recorrer con for-each
        for (String p : palabras) {
            System.out.println(p);
        }

        // contains e indexOf
        System.out.println(palabras.contains("mar"));    // true
        System.out.println(palabras.indexOf("mar"));     // 2
        System.out.println(palabras.lastIndexOf("mar"));  // 2 (solo aparece una vez)

        // subList: obtener una parte
        List<String> primeras = palabras.subList(0, 3);  // [sol, luna, mar]

        // toArray: convertir a array
        String[] array = palabras.toArray(new String[0]);
    }
}
```

### removeIf: eliminar con condicion

```java
List<Integer> numeros = new ArrayList<>(List.of(1, 2, 3, 4, 5, 6));

numeros.removeIf(n -> n % 2 == 0);  // Elimina pares
System.out.println(numeros);  // [1, 3, 5]
```

### replaceAll: transformar todos

```java
List<String> nombres = new ArrayList<>(List.of("ana", "luis", "carlos"));
nombres.replaceAll(n -> n.toUpperCase());
System.out.println(nombres);  // [ANA, LUIS, CARLOS]
```

---

## NIVEL SENIOR

### Stream API

La forma mas potente de trabajar con listas:

```java
import java.util.*;
import java.util.stream.*;

public class DemoStream {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Filtrar, transformar y recolectar
        List<Integer> paresDoblados = numeros.stream()
            .filter(n -> n % 2 == 0)
            .map(n -> n * 2)
            .toList();  // Java 16+
        System.out.println(paresDoblados);  // [4, 8, 12, 16, 20]

        // Sumar
        int suma = numeros.stream()
            .mapToInt(Integer::intValue)
            .sum();

        // Media
        double media = numeros.stream()
            .mapToInt(Integer::intValue)
            .average()
            .orElse(0);

        // Maximo
        int max = numeros.stream()
            .max(Integer::compareTo)
            .orElseThrow();

        // Agrupar
        Map<Boolean, List<Integer>> grupos = numeros.stream()
            .collect(Collectors.partitioningBy(n -> n > 5));
    }
}
```

### Stream con objetos propios

```java
public record Producto(String nombre, String categoria, double precio) {}

List<Producto> productos = List.of(
    new Producto("Laptop", "Electronica", 1200.00),
    new Producto("Mouse", "Electronica", 25.99),
    new Producto("Camiseta", "Ropa", 35.00),
    new Producto("Pantalon", "Ropa", 55.00),
    new Producto("Teclado", "Electronica", 89.90)
);

// Productos de electronica que cuestan menos de 100
var baratos = productos.stream()
    .filter(p -> p.categoria().equals("Electronica"))
    .filter(p -> p.precio() < 100)
    .map(Producto::nombre)
    .toList();
System.out.println(baratos);  // [Mouse, Teclado]

// Precio medio por categoria
var mediaPorCategoria = productos.stream()
    .collect(Collectors.groupingBy(
        Producto::categoria,
        Collectors.averagingDouble(Producto::precio)
    ));
```

### Listas inmutables con copyOf

```java
List<String> mutable = new ArrayList<>(List.of("A", "B", "C"));
List<String> inmutable = List.copyOf(mutable);  // No se puede modificar

// mutable.add("D");  // OK, mutable cambia
// inmutable.add("D"); // ERROR: inmutable
```

### Ordenar con Comparator

```java
List<String> nombres = new ArrayList<>(List.of("Carlos", "Ana", "Luis", "Beatriz"));

// Orden natural (alfabetico)
Collections.sort(nombres);
System.out.println(nombres);  // [Ana, Beatriz, Carlos, Luis]

// Orden inverso
nombres.sort(Comparator.reverseOrder());
System.out.println(nombres);  // [Luis, Carlos, Beatriz, Ana]

// Ordenar por longitud
nombres.sort(Comparator.comparing(String::length));
System.out.println(nombres);  // [Ana, Luis, Carlos, Beatriz]

// Con objetos:
// productos.sort(Comparator.comparing(Producto::precio));
// productos.sort(Comparator.comparing(Producto::precio).reversed());
```

### Collectors avanzados

```java
var resultado = productos.stream()
    .collect(Collectors.toMap(
        Producto::nombre,   // clave
        p -> p,              // valor
        (a, b) -> a          // si hay duplicados, quedate con el primero
    ));

// String.join con stream
String concatenado = nombres.stream()
    .collect(Collectors.joining(", ", "[", "]"));
System.out.println(concatenado);  // [Ana, Luis, Carlos, Beatriz]
```

---

## Errores Comunes

> Modificar la lista mientras la recorres con for-each. Lanza `ConcurrentModificationException`. Usa `removeIf` o `Iterator.remove()`.

> Usar `==` para buscar elementos. `contains()`, `indexOf()` y `remove()` usan `.equals()` internamente. Asegurate de que tu objeto tenga `equals()` bien implementado.

> Asumir que `subList()` es independiente. `subList()` devuelve una **vista** de la lista original. Modificar la vista modifica la original.

> Usar `toArray(new T[size])` con size 0 o con size fijo. El metodo optimo es `toArray(new T[0])` (Java 6+).

> Olvidar que `List.of()` devuelve listas inmutables. Si necesitas modificarla, usa `new ArrayList<>(List.of(...))`.

---

## Buenas Practicas

1. Declara como `List<T>` no como `ArrayList<T>`.
2. Usa `stream().filter().map().toList()` para transformaciones.
3. Usa `removeIf` en vez de iterar manualmente.
4. Usa `List.copyOf()` para exposicion defensiva de datos internos.
5. Usa `Comparator.comparing()` para ordenar objetos.
6. Con `toList()` (Java 16+) en vez de `collect(Collectors.toList())`.
7. Para colecciones grandes, considera `parallelStream()`.

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Autoboxing con colecciones
- [[08 - Instanciacion y new]] - Instanciacion de colecciones
- [[14 - Arrays Basicos y args]] - Arrays vs ArrayList
- [[15 - Listas y ArrayList]] - Nivel basico de listas
- [[16 - Bucles y Control de Flujo]] - Recorrer listas
- [[21 - Getters y Setters]] - Copia defensiva con List.copyOf
- [[23 - Metodos - Parametros, Retorno y Return]] - List como parametro y retorno

---

## Tags
`#java #fundamentos #arraylist #colecciones #list #stream #metodos-avanzados #collectors`
