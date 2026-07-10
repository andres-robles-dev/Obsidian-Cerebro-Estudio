---
tags: [java, fundamentos, main, punto-entrada, jvm]
---

# 02 - Punto de Entrada main

## Concepto Central

El método `main` es el **punto de arranque** de toda aplicación Java standalone. La JVM (Máquina Virtual de Java) lo busca específicamente con esta firma exacta: `public static void main(String[] args)`. Sin él, la aplicación no sabe por dónde empezar a ejecutarse.

## Para Qué Sirve / Cuándo Usarlo

- Iniciar la ejecución del programa
- Recibir argumentos de línea de comandos (`args`)
- Crear los primeros objetos y lanzar la lógica principal
- Configurar entorno inicial (logging, conexión BD, hilos, etc.)
- Todo programa Java autónomo necesita **exactamente uno** (o varios con clases distintas, pero la JVM elige uno al lanzar)

## Sintaxis General

```java
public class NombreClase {
    public static void main(String[] args) {
        // Código de arranque
        // 1. Configuración inicial
        // 2. Creación de objetos principales
        // 3. Llamada a métodos que inician la lógica
    }
}
```

### Desglose de la Firma

| Parte | Significado |
|-------|-------------|
| `public` | Accesible desde fuera (la JVM la llama desde el exterior) |
| `static` | Pertenece a la **clase**, no a una instancia. No hace falta `new NombreClase()` |
| `void` | No devuelve valor. La JVM no espera retorno. |
| `main` | Nombre **obligatorio** y exacto (minúsculas). La JVM busca este nombre. |
| `String[] args` | Array de `String` con argumentos pasados al lanzar: `java MiClase arg1 arg2` |

## Ejemplo Propio: AppPrincipal

```java
public class AppPrincipal {
    public static void main(String[] args) {
        System.out.println("=== Iniciando Aplicación ===");

        // 1. Mostrar argumentos recibidos (si los hay)
        if (args.length > 0) {
            System.out.println("Argumentos recibidos: " + args.length);
            for (int i = 0; i < args.length; i++) {
                System.out.println("  args[" + i + "] = " + args[i]);
            }
        } else {
            System.out.println("Sin argumentos de línea de comandos.");
        }

        // 2. Crear objeto principal del dominio
        Calculadora calc = new Calculadora();

        // 3. Usar el objeto
        int suma = calc.sumar(10, 5);
        System.out.println("10 + 5 = " + suma);

        // 4. Llamar a método que inicia flujo completo (opcional)
        // iniciarSistema();

        System.out.println("=== Aplicación Finalizada ===");
    }
}

// Clase auxiliar en mismo archivo (package-private)
class Calculadora {
    public int sumar(int a, int b) {
        return a + b;
    }

    public int restar(int a, int b) {
        return a - b;
    }

    public int multiplicar(int a, int b) {
        return a * b;
    }

    public double dividir(double a, double b) {
        if (b == 0) {
            throw new IllegalArgumentException("No se puede dividir por cero");
        }
        return a / b;
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `public class AppPrincipal` | Clase contenedora. `public` para que JVM la encuentre. |
| `public static void main(String[] args)` | Firma exacta obligatoria. `static` = se llama sin instanciar. |
| `System.out.println(...)` | Salida a consola. Ver `[[11 - System.out y Concatenacion]]`. |
| `args.length` | Propiedad del array: número de elementos. Ver `[[14 - Arrays Basicos y args]]`. |
| `for (int i = 0; i < args.length; i++)` | Recorrido clásico por índice. `i` empieza en 0. |
| `Calculadora calc = new Calculadora();` | Instanciación. Ver `[[08 - Instanciacion y new]]`. |
| `int suma = calc.sumar(10, 5);` | Llamada a método de instancia. Ver `[[10 - Metodos de Instancia]]`. |
| `throw new IllegalArgumentException(...)` | Lanzamiento de excepción. Control de errores. |

## Errores Comunes

> [!warning] **Error 1: Falta `static`**
> ```java
> public void main(String[] args) { } // ❌ No es static
> ```
> ✅ **Correcto**: `public static void main(String[] args)`

> [!warning] **Error 2: Nombre incorrecto (mayúsculas)**
> ```java
> public static void Main(String[] args) { } // ❌ Main ≠ main
> public static void MAIN(String[] args) { } // ❌
> ```
> ✅ **Correcto**: `main` todo minúsculas.

> [!warning] **Error 3: Parámetro incorrecto**
> ```java
> public static void main(String args) { }        // ❌ No es array
> public static void main(String... args) { }     // ✅ Varargs válido (equivale a array)
> public static void main(String[] args) { }      // ✅ Forma canónica
> ```

> [!warning] **Error 4: Clase no `public`**
> ```java
> class MiApp { // ❌ Sin public, JVM no la ve como punto de entrada
>     public static void main(String[] args) { }
> }
> ```
> ✅ **Correcto**: `public class MiApp { ... }`

> [!warning] **Error 5: `main` dentro de método o constructor**
> ```java
> public class App {
>     public App() {
>         public static void main(String[] args) { } // ❌ Ilegal: método dentro de método
>     }
> }
> ```
> ✅ **Correcto**: `main` a nivel de clase, no anidado.

> [!warning] **Error 6: `return` con valor**
> ```java
> public static int main(String[] args) { return 0; } // ❌ void no devuelve nada
> ```
> ✅ **Correcto**: `void` sin `return` o `return;` solo para salir antes.

## Buenas Prácticas

1. **`main` ligero** — Solo arranque: crear objetos, delegar a métodos/servicios.
2. **Una clase `main` por módulo ejecutable** — `AppWeb`, `AppBatch`, `AppCli`.
3. **Validar `args` temprano** — Si esperas argumentos, comprueba `args.length` al inicio.
4. **Manejo de excepciones global** — `try-catch` en `main` para log final y salida limpia.
5. **Logging en lugar de `println`** — En producción usa SLF4J/Logback, no `System.out`.
6. **Código de salida** — `System.exit(codigo)` para scripts/batch (0=ok, !=0=error).

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — `main` vive dentro de una clase.
- `[[08 - Instanciacion y new]]` — `main` suele ser donde se hace el primer `new`.
- `[[13 - Static vs Instancia]]` — `main` es `static`, no puede acceder a `this` ni a campos de instancia directamente.
- `[[14 - Arrays Basicos y args]]` — `args` es un array `String[]`.
- `[[15 - Flujo de Ejecucion JVM]]` — La JVM carga la clase, verifica `main`, crea hilo principal, ejecuta.

## Resumen en Una Frase

> **`public static void main(String[] args)` es la firma exacta que la JVM busca para arrancar tu programa; debe ser `static` para llamarse sin instanciar la clase.**

---

## Tags
`#java #fundamentos #main #punto-entrada #jvm #static`