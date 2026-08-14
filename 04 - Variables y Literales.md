---
tags: [java, fundamentos, variables, literales, scope, declaracion, inicializacion]
---

# 04 - Variables y Literales

---

## NIVEL JUNIOR

### Que es una variable?

Una variable es una caja donde guardas un valor. Tiene un nombre, un tipo y un valor.

```java
String nombre = "Ana";
int edad = 30;
boolean activo = true;
```

- **Tipo**: que clase de dato guarda (`String`, `int`, `boolean`)
- **Nombre**: como la identificas (`nombre`, `edad`, `activo`)
- **Valor**: lo que tiene guardado (`"Ana"`, `30`, `true`)

### Declarar y asignar

```java
// Declarar (crear la caja)
int edad;

// Asignar (meter el valor)
edad = 25;

// O todo junto
int edad = 25;
```

### Que es un literal?

Un literal es el valor que escribes directamente en el codigo:

```java
int numero = 42;           // 42 es un literal entero
double precio = 19.99;     // 19.99 es un literal decimal
String saludo = "Hola";    // "Hola" es un literal de texto
char letra = 'A';          // 'A' es un literal de caracter
boolean cierto = true;     // true es un literal booleano
```

- **Definición:** un **literal** es un valor escrito directamente en el código fuente.
- Representa un **valor fijo** que Java puede utilizar.

**Ejemplos:**

```JAVA
int edad = 25;          // 25 → literal
String nombre = "Ana";  // "Ana" → literal
boolean activo = true;  // true → literal
```

**Idea clave:** el literal es **el valor escrito directamente**, no el nombre de la variable.
### Ejemplo basico

```java
public class EjemploVariables {
    public static void main(String[] args) {
        String producto = "Laptop";
        int cantidad = 3;
        double precioUnitario = 750.50;
        boolean disponible = true;

        double total = cantidad * precioUnitario;

        System.out.println("Producto: " + producto);
        System.out.println("Cantidad: " + cantidad);
        System.out.println("Total: " + total);
        System.out.println("Disponible: " + disponible);
    }
}
```

---

## NIVEL MID

### Tipos de literales

**Definición:**  
Los **tipos de literales** son las diferentes formas de representar valores directamente en el código.

**Notas clave:**

- Los literales representan **valores escritos directamente**.
- El tipo de literal determina qué tipo de dato representa.
- Los principales son: **enteros, decimales, caracteres, cadenas, booleanos y `null`**.
##### Ejemplos:

```JAVA
25          // entero
3.14        // decimal
'A'         // char
"Hola"      // String
true        // boolean
null        // nulo
```


```java
public class LiteralesEjemplos {
    public static void main(String[] args) {
        // Literales enteros
        int decimal = 42;
        int hexadecimal = 0xFF;  // 255 en decimal
        int binario = 0b1010;    // 10 en decimal (Java 7+)

        // Literales con sufijo
        long numeroGrande = 3_000_000_000L;     // L = long
        float precio = 19.99f;                   // f = float
        double grande = 1.5;                     // double por defecto

        // Literales caracter
        char letra = 'M';
        char nuevaLinea = '\n';
        char unicode = '\u00A9';  // (c) copyright

        // Literales especiales
        String salto = "Linea 1\nLinea 2\tTabulado";
        String nulo = null;  // Solo para tipos referencia
    }
}
```

**Idea clave:** `25` es un literal entero, `"Hola"` es un literal `String`, `'A'` es un literal `char`, etc.

### Ambito (scope) de variables

**Definición:**  
El **scope** es la zona del código donde una variable **puede ser accedida y utilizada**.

**Notas clave:**

- Una variable solo puede utilizarse dentro de su ámbito.
- Un **método**, un **bloque `{}`** o una **clase** pueden crear ámbitos.
- En ámbitos diferentes, se puede repetir el mismo nombre.
- En el mismo ámbito, no se puede declarar otra variable local con el mismo nombre.

**Ejemplo:**

```JAVA
void metodoA() {
    String buscar = "A";
}

void metodoB() {
    String buscar = "B";
}
```

Aquí no hay conflicto porque cada `buscar` pertenece a un **ámbito diferente**.

```java
public class AmbitoVariables {
    int campo = 1;  // Vive mientras exista el objeto

    public void metodo() {
        int local = 2;  // Vive solo dentro de este metodo
        if (local > 0) {
            int bloque = 3;  // Vive solo dentro del if
            System.out.println(bloque);
        }
        // System.out.println(bloque); // ERROR: no existe aqui
    }
}
```

**Idea clave:** **Scope = dónde puedo usar una variable.**
### Constantes con final

**Definición:**  
Una **constante** es una variable cuyo valor **no puede cambiar** después de ser asignado. Se declara con `final`.

**Notas clave:**

- `final` impide reasignar la variable.
- Por convención, las constantes se escriben en **MAYÚSCULAS**.
- Si tiene varias palabras, se separan con `_`.
- Las mayúsculas son una **convención**, no una obligación de Java.

**Ejemplo:**

```JAVA
final int DIAS_SEMANA = 7;

// DIAS_SEMANA = 5; ❌ Error

```

```java
public class Constantes {
    public static final double IVA = 0.21;
    public static final int MAXIMO_INTENTOS = 3;
    public static final String NOMBRE_APP = "MiPrograma";
}
```

Las constantes se escriben en MAYUSCULAS con guion bajo.

---

## NIVEL SENIOR

### Inferencia de tipo con var (Java 10+)

**Definición:**  
La **inferencia de tipo** permite que Java **deduzca automáticamente el tipo** de una variable a partir del valor que se le asigna.

**Notas clave:**

- `var` se introdujo en **Java 10**.
- El tipo lo determina el compilador.
- La variable sigue teniendo un **tipo fijo**; `var` no la convierte en dinámica.
- Se puede usar junto con variables que tienen el tipo escrito explícitamente.
- Se recomienda usarlo cuando el tipo sea **claro y mejore la legibilidad**.

**Ejemplo:**

```JAVA
var nombre = "Carlos";  // Java infiere String
var edad = 25;          // Java infiere int
var activo = true;      // Java infiere boolean

```

```java
public class DemoVar {
    public static void main(String[] args) {
        var nombre = "Ana";             // Java deduce String
        var edad = 30;                  // Java deduce int
        var precio = 19.99;             // Java deduce double
        var lista = new ArrayList<String>();  // Java deduce ArrayList<String>

        // var solo para variables locales, no para campos ni parametros
    }
}
```

**Idea clave:** `var` significa **“Java, deduce el tipo por mí”**.
### Efectivamente final

Desde Java 8, si una variable no cambia despues de inicializarse, es "efectivamente final". Puedes usarla en lambdas:

```java
public class EfectivamenteFinal {
    public static void main(String[] args) {
        int factor = 2;  // Nunca cambia, es efectivamente final

        var numeros = List.of(1, 2, 3);
        var resultado = numeros.stream()
            .map(n -> n * factor)  // factor es efectivamente final
            .toList();

        System.out.println(resultado); // [2, 4, 6]
    }
}
```

### Pattern matching con var y switch

```java
public class DemoPatternMatching {
    public static void main(String[] args) {
        Object valor = 42;

        // Java 16+ pattern matching
        if (valor instanceof String texto) {
            System.out.println("Texto: " + texto);
        } else if (valor instanceof Integer numero) {
            System.out.println("Numero: " + numero);
        }

        // Java 21+ pattern matching en switch
        String resultado = switch (valor) {
            case String s -> "Es texto: " + s;
            case Integer i when i > 0 -> "Es positivo: " + i;
            case Integer i -> "Es cero o negativo: " + i;
            default -> "Tipo desconocido";
        };
    }
}
```

### Text blocks con variables (Java 15+)

```java
public class DemoTextBlock {
    public static void main(String[] args) {
        String nombre = "Ana";
        int edad = 30;
        String ciudad = "Madrid";

        String perfil = """
            Perfil de usuario:
            -----------------
            Nombre: %s
            Edad:   %d
            Ciudad: %s
            """.formatted(nombre, edad, ciudad);

        System.out.println(perfil);
    }
}
```

### Variables locales con try-with-resources

```java
import java.io.*;
import java.nio.file.*;

public class DemoRecursos {
    public static void main(String[] args) throws IOException {
        var ruta = Path.of("datos.txt");
        Files.writeString(ruta, "Hola Java 2026");

        try (var lector = Files.newBufferedReader(ruta)) {
            String linea;
            while ((linea = lector.readLine()) != null) {
                System.out.println(linea);
            }
        }
        // lector se cierra automaticamente
    }
}
```

---

## Errores Comunes

> Usar variable local sin inicializarla. Java obliga a inicializar las variables locales antes de leerlas. Los campos de clase se inicializan solos (0, null, false).

> Olvidar la L en numeros largos. `long x = 3000000000;` no compila. Usa `3000000000L`.

> Olvidar la f en float. `float x = 3.14;` no compila. Usa `3.14f`.

> Confundir `=` (asignar) con `==` (comparar). `if (x = 5)` asigna 5 a x, no compara.

> Shadowing: declarar una variable local con el mismo nombre que un campo. El campo queda oculto dentro del metodo. Usa `this.campo` para acceder al campo.

---

## Buenas Practicas

1. Inicializa las variables en el mismo lugar donde las declaras.
2. Usa nombres descriptivos: `velocidadMaxima` no `v`.
3. Constantes con `static final` y nombre en `MAYUSCULAS`.
4. No uses literales magicos. `if (x > 86400)` deberia ser `if (x > SEGUNDOS_POR_DIA)`.
5. Ambito minimo: declara la variable lo mas cerca posible de donde la usas.
6. Una variable = un proposito. No reutilices la misma variable para distintas cosas.
7. Prefiere `var` cuando el tipo sea obvio por la derecha de la asignacion.

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Tipos validos para variables
- [[06 - Atributos y Campos]] - Variables como campos vs locales
- [[07 - Constructores y this]] - Inicializacion en constructor
- [[10 - Metodos de Instancia]] - Parametros y locales en metodos
- [[14 - Arrays Basicos y args]] - Arrays y args como variables
- [[20 - Javadoc y Documentacion]] - Documentar constantes
- [[23 - Metodos - Parametros, Retorno y Return]] - Return como expresion

---

## Tags
`#java #fundamentos #variables #literales #scope #declaracion #inicializacion #constantes`
