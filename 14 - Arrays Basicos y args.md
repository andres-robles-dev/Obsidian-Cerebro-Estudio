---
tags: [java, fundamentos, arrays, args, arreglos, indice]
---

# 14 - Arrays Basicos y args

---

## NIVEL JUNIOR

### Que es un array?

Un array es una coleccion de elementos del mismo tipo, guardados en una sola variable. Piensa en un array como una fila de casilleros numerados.

### Como se declara

```java
// Tipo[] nombre = new Tipo[cantidad];
int[] edades = new int[5];  // Array de 5 enteros
String[] nombres = new String[3];  // Array de 3 textos
```

### Indices: empiezan en 0

Los casilleros se numeran desde 0, no desde 1:

```java
int[] numeros = new int[3];
numeros[0] = 10;  // Primer elemento
numeros[1] = 20;  // Segundo elemento
numeros[2] = 30;  // Tercer elemento

System.out.println(numeros[0]);  // 10
System.out.println(numeros[1]);  // 20
System.out.println(numeros[2]);  // 30
```

### Array con valores iniciales

```java
int[] numeros = {10, 20, 30, 40, 50};
String[] colores = {"Rojo", "Verde", "Azul"};
```

### length: cuantos elementos tiene

```java
int[] numeros = {10, 20, 30};
System.out.println(numeros.length);  // 3
```

### Recorrer un array con for

```java
int[] numeros = {10, 20, 30, 40, 50};
for (int i = 0; i < numeros.length; i++) {
    System.out.println("Posicion " + i + ": " + numeros[i]);
}
```

### String[] args en main

El parametro `args` del main es un array de String. Recibe los argumentos al ejecutar el programa:

```java
public class Saludar {
    public static void main(String[] args) {
        System.out.println("Hola " + args[0]);
    }
}
```

Ejecutar: `java Saludar Ana` -> Imprime "Hola Ana"

---

## NIVEL MID

### Recorrer con for-each

```java
int[] numeros = {10, 20, 30, 40, 50};
for (int numero : numeros) {
    System.out.println(numero);
}
```

Mas sencillo que el for tradicional. No necesitas el indice.

### Array de objetos

```java
public class Alumno {
    String nombre;
    double nota;
}

Alumno[] alumnos = new Alumno[3];
alumnos[0] = new Alumno();
alumnos[0].nombre = "Ana";
alumnos[0].nota = 8.5;

alumnos[1] = new Alumno();
alumnos[1].nombre = "Luis";
alumnos[1].nota = 7.0;

for (Alumno a : alumnos) {
    System.out.println(a.nombre + ": " + a.nota);
}
```

### Matriz (array bidimensional)

```java
int[][] matriz = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

System.out.println(matriz[0][0]);  // 1 (fila 0, columna 0)
System.out.println(matriz[1][2]);  // 6 (fila 1, columna 2)
```

### Args con validacion

```java
public class Calculadora {
    public static void main(String[] args) {
        if (args.length < 2) {
            System.out.println("Uso: java Calculadora <numero1> <numero2>");
            return;
        }

        double a = Double.parseDouble(args[0]);
        double b = Double.parseDouble(args[1]);
        System.out.println("Suma: " + (a + b));
    }
}
```

---

## NIVEL SENIOR

### Stream con arrays

```java
import java.util.Arrays;

public class DemoStreamArrays {
    public static void main(String[] args) {
        int[] numeros = {5, 2, 8, 1, 9, 3};

        // Filtrar, ordenar y convertir
        var resultado = Arrays.stream(numeros)
            .filter(n -> n > 4)
            .sorted()
            .toArray();

        System.out.println(Arrays.toString(resultado));  // [5, 8, 9]

        // Sumar todos
        int suma = Arrays.stream(numeros).sum();

        // Media
        double media = Arrays.stream(numeros).average().orElse(0);
    }
}
```

### List.of y arrays

```java
import java.util.List;

public class DemoArraysModerno {
    public static void main(String[] args) {
        // Array a lista inmutable
        String[] colores = {"Rojo", "Verde", "Azul"};
        List<String> listaColores = List.of(colores);

        // Lista a array
        var lista = List.of("A", "B", "C");
        String[] array = lista.toArray(String[]::new);
    }
}
```

### Arrays.copyOf y System.arraycopy

```java
import java.util.Arrays;

public class DemoCopias {
    public static void main(String[] args) {
        int[] original = {1, 2, 3, 4, 5};

        // Copia con tamanio diferente
        int[] copia = Arrays.copyOf(original, 10);  // {1,2,3,4,5,0,0,0,0,0}
        int[] recorte = Arrays.copyOfRange(original, 1, 4);  // {2,3,4}

        System.out.println(Arrays.toString(copia));
        System.out.println(Arrays.toString(recorte));
    }
}
```

### Varargs: array implicito

```java
public class Utiles {
    public static void imprimirTodo(String... elementos) {
        // elementos es un String[] internamente
        for (String e : elementos) {
            System.out.println(e);
        }
    }

    public static double media(double... numeros) {
        return Arrays.stream(numeros).average().orElse(0);
    }
}

// Uso:
// Utiles.imprimirTodo("A", "B", "C");
// double m = Utiles.media(1, 2, 3, 4);
```

---

## Errores Comunes

> Acceder a un indice que no existe. `int[] arr = new int[3]; arr[5] = 10;` lanza `ArrayIndexOutOfBoundsException`. Los indices van de 0 a `length-1`.

> Confundir `length` (atributo del array) con `length()` (metodo de String). Arrays usan `length` sin parentesis.

> Olvidar que los arrays tienen TAMANIO FIJO. No puedes anadir ni quitar elementos. Para eso usa [[15 - Listas y ArrayList]].

> Pasar un indice negativo. `arr[-1]` tambien lanza `ArrayIndexOutOfBoundsException`.

> No comprobar `args.length` antes de usar `args[0]`. Si no hay argumentos, lanza excepcion.

---

## Buenas Practicas

1. Prefiere `List` sobre arrays para colecciones que cambian de tamanio.
2. Usa `Arrays.toString()` o `Arrays.deepToString()` para imprimir arrays.
3. Usa `Arrays.stream()` para operaciones funcionales sobre arrays.
4. Para copiar arrays, usa `Arrays.copyOf()` en vez de bucles manuales.
5. Valida siempre `args.length` antes de acceder a `args[i]`.
6. Usa varargs (`Tipo...`) en vez de arrays como parametros si esperas 0..N elementos.

---

## Conexiones

- [[02 - Punto de Entrada main]] - String[] args del main
- [[03 - Tipos Primitivos y Referencia]] - Arrays de primitivos vs objetos
- [[15 - Listas y ArrayList]] - Alternativa moderna a arrays
- [[16 - Bucles y Control de Flujo]] - Recorrer arrays con for y for-each
- [[24 - ArrayList - Metodos Avanzados]] - Stream y colecciones

---

## Tags
`#java #fundamentos #arrays #args #arreglos #indice #varargs`
