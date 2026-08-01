---
tags: [java, fundamentos, listas, arraylist, colecciones, generics]
---

# 15 - Listas y ArrayList

---

## NIVEL JUNIOR

### Que es ArrayList?

`ArrayList` es como un array pero que puede crecer y encogerse. No tienes que decirle el tamanio de antemano.

### Importar ArrayList

```java
import java.util.ArrayList;
```

### Crear y anadir elementos

```java
ArrayList<String> nombres = new ArrayList<>();
nombres.add("Ana");
nombres.add("Luis");
nombres.add("Carlos");

System.out.println(nombres);  // [Ana, Luis, Carlos]
```

### Metodos basicos

```java
ArrayList<String> lista = new ArrayList<>();

lista.add("Elemento");        // Anadir al final
lista.add(0, "Primero");      // Anadir en posicion especifica
String elem = lista.get(0);   // Obtener por posicion
int tam = lista.size();       // Cuantos elementos tiene
lista.remove(0);              // Eliminar por posicion
lista.remove("Elemento");     // Eliminar por valor
boolean existe = lista.contains("Ana");  // Comprobar si existe
```

### Recorrer una lista

```java
ArrayList<String> nombres = new ArrayList<>();
nombres.add("Ana");
nombres.add("Luis");

for (String nombre : nombres) {
    System.out.println(nombre);
}
```

---

## NIVEL MID

### List vs ArrayList

Es mejor declarar como `List` (la interfaz) y crear como `ArrayList`:

```java
import java.util.List;
import java.util.ArrayList;

List<String> nombres = new ArrayList<>();
```

Esto te permite cambiar de implementacion sin tocar el resto del codigo.

### ArrayList con objetos propios

```java
public class Alumno {
    String nombre;
    double nota;

    public Alumno(String nombre, double nota) {
        this.nombre = nombre;
        this.nota = nota;
    }
}

List<Alumno> alumnos = new ArrayList<>();
alumnos.add(new Alumno("Ana", 8.5));
alumnos.add(new Alumno("Luis", 7.0));

for (Alumno a : alumnos) {
    System.out.println(a.nombre + ": " + a.nota);
}
```

### Operaciones comunes

```java
import java.util.*;

public class DemoListas {
    public static void main(String[] args) {
        List<Integer> numeros = new ArrayList<>();
        numeros.addAll(List.of(5, 2, 8, 1, 9, 3));

        // Ordenar
        Collections.sort(numeros);
        System.out.println(numeros);  // [1, 2, 3, 5, 8, 9]

        // Reverso
        Collections.reverse(numeros);
        System.out.println(numeros);  // [9, 8, 5, 3, 2, 1]

        // Maximo y minimo
        int max = Collections.max(numeros);
        int min = Collections.min(numeros);

        // Mezclar aleatoriamente
        Collections.shuffle(numeros);
    }
}
```

### List.of: lista inmutable

```java
List<String> colores = List.of("Rojo", "Verde", "Azul");
// colores.add("Amarillo");  // ERROR: es inmutable
```

---

## NIVEL SENIOR

### ArrayList con Stream

```java
import java.util.*;

public class DemoStreamListas {
    public static void main(String[] args) {
        List<Integer> numeros = new ArrayList<>(List.of(1, 2, 3, 4, 5, 6));

        // Filtrar pares y multiplicar por 2
        List<Integer> resultado = numeros.stream()
            .filter(n -> n % 2 == 0)
            .map(n -> n * 2)
            .toList();  // Java 16+ directo a lista inmutable

        System.out.println(resultado);  // [4, 8, 12]

        // Sumar todos
        int suma = numeros.stream()
            .mapToInt(Integer::intValue)
            .sum();

        // Agrupar por condicion
        var grupos = numeros.stream()
            .collect(Collectors.partitioningBy(n -> n > 3));
    }
}
```

### removeIf y replaceAll

```java
List<String> palabras = new ArrayList<>(List.of("hola", "adios", "sol", "luna", "mar"));

// Eliminar las que tengan menos de 3 letras
palabras.removeIf(p -> p.length() < 3);
System.out.println(palabras);  // [hola, adios, luna]

// Transformar todas
palabras.replaceAll(String::toUpperCase);
System.out.println(palabras);  // [HOLA, ADIOS, LUNA]
```

### Lista inmutable con defensa

```java
import java.util.*;

public class DemoInmutable {
    public static void main(String[] args) {
        List<String> original = new ArrayList<>();
        original.add("A");
        original.add("B");
        original.add("C");

        // Copia defensiva inmutable
        List<String> protegida = List.copyOf(original);

        // original.add("D");  // Esto NO afecta a protegida
        // protegida.add("D"); // ERROR: inmutable
    }
}
```

### Records en listas

```java
public record Producto(String nombre, double precio) {}

List<Producto> productos = List.of(
    new Producto("Laptop", 1200.50),
    new Producto("Mouse", 25.99),
    new Producto("Teclado", 89.90)
);

// Buscar producto mas caro
var masCaro = productos.stream()
    .max(Comparator.comparing(Producto::precio))
    .orElseThrow();

System.out.println(masCaro);  // Producto[nombre=Laptop, precio=1200.5]
```

---

## Errores Comunes

> Usar `==` para comparar elementos de la lista. Usa `.equals()`. Los metodos como `contains()` y `indexOf()` ya usan `.equals()` internamente.

> Modificar la lista mientras la recorres con for-each. Lanza `ConcurrentModificationException`. Usa `removeIf` o `Iterator`.

> Olvidar el tipo generico `<T>`. No hagas `ArrayList lista = new ArrayList();`. Usa siempre `ArrayList<String>` o `ArrayList<Integer>`.

> Asumir que `List.of()` es mutable. `List.of()` crea listas inmutables. Usa `new ArrayList<>(List.of(...))` si necesitas modificarla.

---

## Buenas Practicas

1. Declara como `List<T>` no como `ArrayList<T>`. Programa contra la interfaz.
2. Usa `List.of()` para listas inmutables pequenas.
3. Usa `Stream.toList()` en vez de `.collect(Collectors.toList())` (Java 16+).
4. Usa `removeIf` en vez de iterar manualmente para eliminar elementos.
5. Para listas que no cambian, prefiere `List.of()` o `List.copyOf()` (inmutables).
6. Especifica el tipo generico: `List<String>` no `List` a secas.

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Generics con wrappers
- [[14 - Arrays Basicos y args]] - Arrays vs ArrayList
- [[16 - Bucles y Control de Flujo]] - Recorrer listas con for-each
- [[21 - Getters y Setters]] - Copia defensiva de listas
- [[23 - Metodos - Parametros, Retorno y Return]] - List como parametro y retorno
- [[24 - ArrayList - Metodos Avanzados]] - Stream y metodos avanzados

---

## Tags
`#java #fundamentos #listas #arraylist #colecciones #generics #list`
