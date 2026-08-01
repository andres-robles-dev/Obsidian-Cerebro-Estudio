---
tags: [java, fundamentos, tipos, primitivos, referencia, string]
---

# 03 - Tipos Primitivos y Referencia

---

## NIVEL JUNIOR

### Los dos mundos de Java

Java tiene dos tipos de datos:

**Primitivos**: guardan el valor directamente (numeros, letras, booleanos)
**Referencia**: guardan una direccion hacia un objeto (textos, listas, objetos propios)

### Los 8 primitivos (solo recuerda los 4 mas usados)

| Tipo | Guarda | Ejemplo |
|------|--------|---------|
| `int` | Numeros enteros | `int edad = 25;` |
| `double` | Numeros decimales | `double precio = 19.99;` |
| `boolean` | Verdadero o falso | `boolean activo = true;` |
| `char` | Un solo caracter | `char letra = 'A';` |

Los otros 4 (`byte`, `short`, `long`, `float`) se usan en casos especiales.

### Tipos referencia (los mas comunes)

```java
String nombre = "Maria";        // Texto
int[] numeros = {1, 2, 3};      // Lista de numeros
```

### Ejemplo basico

```java
public class TiposBasicos {
    public static void main(String[] args) {
        // Primitivos
        int edad = 30;
        double altura = 1.75;
        boolean esEstudiante = true;
        char inicial = 'C';

        // Referencia
        String nombre = "Carlos";

        System.out.println("Nombre: " + nombre);
        System.out.println("Edad: " + edad);
        System.out.println("Altura: " + altura);
        System.out.println("Estudiante: " + esEstudiante);
        System.out.println("Inicial: " + inicial);
    }
}
```

---

## NIVEL MID

### Diferencia clave: valor vs referencia

Cuando asignas una variable a otra:

```java
public class DiferenciaValorReferencia {
    public static void main(String[] args) {
        // Con primitivos: se COPIA el valor
        int a = 10;
        int b = a;   // b recibe una copia de 10
        b = 20;
        System.out.println("a = " + a); // 10 (no cambio)
        System.out.println("b = " + b); // 20

        // Con referencia: se COPIA la direccion
        int[] arr1 = {1, 2, 3};
        int[] arr2 = arr1;   // arr2 apunta al MISMO array
        arr2[0] = 99;
        System.out.println("arr1[0] = " + arr1[0]); // 99 (cambio!)
    }
}
```

### Tipo por defecto segun el contexto

```java
public class ValoresPorDefecto {
    int entero;        // 0 por defecto
    double decimal;    // 0.0 por defecto
    boolean bandera;   // false por defecto
    String texto;      // null por defecto
}
```

### Underscores en numeros grandes

```java
int unMillon = 1_000_000;
long distanciaAlSol = 149_600_000_000L;
```

---

## NIVEL SENIOR

### Records para datos inmutables

Con `record` los datos son inmutables y el codigo es minimo:

```java
public record Coordenadas(double latitud, double longitud) {
    public double distanciaA(Coordenadas otra) {
        var dLat = Math.toRadians(otra.latitud - this.latitud);
        var dLon = Math.toRadians(otra.longitud - this.longitud);
        return Math.sqrt(dLat * dLat + dLon * dLon) * 111_320;
    }
}
```

### Wrappers: primitivos como objetos

Java tiene versiones objeto de los primitivos para usarlos en colecciones:

```java
import java.util.ArrayList;

public class DemoWrappers {
    public static void main(String[] args) {
        // Autoboxing: Java convierte automaticamente
        int numero = 42;
        Integer numeroObjeto = numero; // autoboxing

        ArrayList<Integer> lista = new ArrayList<>();
        lista.add(10);  // autoboxing de int a Integer
        lista.add(20);

        int suma = lista.get(0) + lista.get(1); // unboxing automatico
        System.out.println("Suma: " + suma);
    }
}
```

### Text blocks (Java 15+)

Para escribir texto multilinea sin concatenar:

```java
public class TextBlocks {
    public static void main(String[] args) {
        String mensaje = """
            Hola %s,
            Bienvenido a Java moderno.
            Tu codigo es: %d
            """.formatted("Ana", 42);

        System.out.println(mensaje);
    }
}
```

### Pattern matching con instanceof (Java 16+)

```java
public class PatternMatching {
    public static void main(String[] args) {
        Object valor = "Hola Java";

        if (valor instanceof String texto) {
            System.out.println("Longitud: " + texto.length());
        }
    }
}
```

### Conversion moderna con Optional

```java
import java.util.Optional;

public class ConversionSegura {
    public Optional<Integer> convertirANumero(String texto) {
        try {
            return Optional.of(Integer.parseInt(texto));
        } catch (NumberFormatException e) {
            return Optional.empty();
        }
    }
}
```

---

## Errores Comunes

> Usar `==` para comparar Strings. Con objetos usa `.equals()`, no `==`. `==` compara direcciones de memoria, no contenido.

> Asignar `null` a un primitivo. Solo los tipos referencia aceptan `null`. `int x = null;` da error.

> Poner `L` en numeros grandes. `long x = 3000000000;` da error porque el literal es `int` y no cabe. Usa `3000000000L`.

> Olvidar la `f` en float. `float x = 3.14;` da error porque `3.14` es `double`. Usa `3.14f`.

> Comillas dobles para char. `char c = "A";` es String, no char. Usa comillas simples: `char c = 'A';`.

---

## Buenas Practicas

1. Usa `int` para enteros y `double` para decimales por defecto. Solo usa `long` si el numero no cabe en `int`.
2. Usa `boolean` para banderas con nombres positivos: `estaActivo`, no `noActivo`.
3. Usa `String` para texto. No uses `char[]` ni `byte[]`.
4. Evita `new String("texto")`. Usa literal `"texto"` que es mas eficiente (String Pool).
5. Usa underscores en numeros grandes: `1_000_000` es mas legible.
6. Usa `record` para grupos de datos simples en vez de clases con boilerplate.

---

## Conexiones

- [[04 - Variables y Literales]] - Como declarar y dar valor a las variables
- [[06 - Atributos y Campos]] - Tipos de atributos y valores por defecto
- [[08 - Instanciacion y new]] - `new` crea objetos de tipos referencia
- [[10 - Metodos de Instancia]] - Paso por valor en metodos
- [[14 - Arrays Basicos y args]] - Arrays de primitivos vs arrays de objetos
- [[23 - Metodos - Parametros, Retorno y Return]] - Paso por valor siempre en Java
- [[24 - ArrayList - Metodos Avanzados]] - Autoboxing con colecciones

---

## Tags
`#java #fundamentos #tipos #primitivos #referencia #string #wrappers`
