---
tags: [java, fundamentos, system-out, print, println, printf, concatenacion, stringbuilder]
---

# 11 - System.out y Concatenación

## Concepto Central

`System.out` es un `PrintStream` conectado a la **salida estándar** (consola). Proporciona métodos para escribir texto: `print()`, `println()`, `printf()`. La **concatenación** con `+` une `String` y otros tipos convirtiéndolos a `String` automáticamente (llamando a `toString()` / `valueOf()`). Internamente, Java usa `StringBuilder` para optimizar cadenas `+` en una sola expresión.

## Para Qué Sirve / Cuándo Usarlo

- Depuración rápida (*logging* básico)
- Interfaz de línea de comandos (CLI) simple
- Mostrar estado de objetos (`toString()`)
- Formateo controlado (`printf` estilo C)
- Construir mensajes dinámicos

## Sintaxis General

### Métodos de PrintStream

| Método | Comportamiento | Uso Típico |
|--------|----------------|------------|
| `print(x)` | Escribe `x` **sin** salto de línea | Prompts, partes de línea |
| `println(x)` | Escribe `x` **con** salto de línea (`\n`) | Líneas completas, logs |
| `println()` | Solo salto de línea | Línea en blanco |
| `printf(fmt, args...)` | Formato estilo C (`%d`, `%s`, `%f`...) | Columnas, decimales, padding |
| `format(fmt, args...)` | Alias de `printf` (devuelve `this`) | Encadenable |

### Concatenación con `+`

```java
// Reglas:
// 1. Si alguno es String → concatena (convierte otro a String)
// 2. Objetos → llama toString()
// 3. Primitivos → valueOf() / Boxing + toString()
// 4. null → "null" (String literal)

String s = "Edad: " + 25;           // "Edad: 25"
String s = "Valor: " + 3.14;        // "Valor: 3.14"
String s = "Activo: " + true;       // "Activo: true"
String s = "Obj: " + new Object();  // "Obj: java.lang.Object@15db9742"
String s = "Null: " + null;         // "Null: null"

// Múltiples + en una línea → UN StringBuilder interno (optimizado)
String r = "A" + "B" + "C";         // En compile-time: "ABC" (constant folding)
String r = a + b + c;               // Runtime: new StringBuilder().append(a).append(b).append(c).toString()
```

### Formato `printf` / `format` (Especificadores Comunes)

| Especificador | Tipo | Ejemplo |
|---------------|------|---------|
| `%s` | String / Object (toString) | `printf("Nombre: %s", "Ana")` |
| `%d` | Entero (int, long, byte, short) | `printf("Edad: %d", 25)` |
| `%f` | Decimal (float, double) | `printf("Precio: %.2f", 19.99)` |
| `%n` | Salto de línea **portable** | `printf("Línea 1%nLínea 2")` |
| `%c` | Char | `printf("Inicial: %c", 'A')` |
| `%b` | Boolean | `printf("Ok: %b", true)` |
| `%x` / `%X` | Hex (minúscula/mayúscula) | `printf("Color: #%06X", 0xFF5500)` |
| `%t` / `%T` | Fecha/Hora (compuesto) | `printf("%tF %tT", now, now)` |

**Banderas (flags)**: `%-10s` (izquierda), `%05d` (ceros izq), `%+d` (signo), `%,d` (separador miles), `%(d` (paréntesis negativos).

## Ejemplo Propio: LoggerSimple

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class LoggerSimple {
    // Niveles de log
    public enum Nivel { DEBUG, INFO, WARN, ERROR }

    // Configuración
    private final String nombre;
    private Nivel nivelMinimo = Nivel.INFO;
    private boolean mostrarTimestamp = true;
    private boolean mostrarNivel = true;

    // Formateador fecha
    private static final DateTimeFormatter FMT = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

    public LoggerSimple(String nombre) {
        this.nombre = nombre;
    }

    public void setNivelMinimo(Nivel n) { this.nivelMinimo = n; }
    public void setMostrarTimestamp(boolean b) { this.mostrarTimestamp = b; }
    public void setMostrarNivel(boolean b) { this.mostrarNivel = b; }

    // API pública
    public void debug(String msg) { log(Nivel.DEBUG, msg); }
    public void info(String msg)  { log(Nivel.INFO, msg); }
    public void warn(String msg)  { log(Nivel.WARN, msg); }
    public void error(String msg) { log(Nivel.ERROR, msg); }

    // Método central con concatenación y printf
    private void log(Nivel nivel, String mensaje) {
        if (nivel.ordinal() < nivelMinimo.ordinal()) return; // Filtro

        StringBuilder sb = new StringBuilder(); // Evita concatenación + en logs frecuentes

        if (mostrarTimestamp) {
            sb.append('[').append(LocalDateTime.now().format(FMT)).append(']');
        }
        if (mostrarNivel) {
            sb.append(' ').append(String.format("%-5s", nivel.name())); // %-5s = padding derecha 5
        }
        sb.append(' ').append(nombre).append(": ").append(mensaje);

        // println para salto de línea automático
        System.out.println(sb.toString());
    }

    // Método con printf para tabla formateada
    public void tablaResumen(String[] claves, double[] valores) {
        if (claves.length != valores.length) throw new IllegalArgumentException("Longitudes difieren");
        System.out.printf("%n=== Resumen: %s ===%n", nombre);
        System.out.printf("%-20s %12s%n", "Concepto", "Valor");
        System.out.printf("%-20s %12s%n", "--------------------", "------------");
        for (int i = 0; i < claves.length; i++) {
            System.out.printf("%-20s %12.2f%n", claves[i], valores[i]);
        }
        System.out.println();
    }

    // Demostración concatenación vs StringBuilder
    public void demoConcatenacion() {
        String a = "Hola";
        String b = "Mundo";

        // 1. Concatenación simple (optimizada a StringBuilder)
        String r1 = a + " " + b + "!";
        System.out.println("Concatenación +: " + r1);

        // 2. StringBuilder explícito (rendimiento en bucles)
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 5; i++) sb.append(i).append(",");
        System.out.println("StringBuilder bucle: " + sb); // "0,1,2,3,4,"

        // 3. String.join (Java 8+)
        String r2 = String.join("-", "Uno", "Dos", "Tres");
        System.out.println("String.join: " + r2);

        // 4. printf demo
        System.out.printf("Entero: %d | Hex: %X | Float: %.3f | Bool: %b%n", 42, 42, 3.14159, true);
    }

    // MAIN
    public static void main(String[] args) {
        LoggerSimple log = new LoggerSimple("MiApp");
        log.setNivelMinimo(Nivel.DEBUG);

        log.debug("Iniciando aplicación...");
        log.info("Configuración cargada");
        log.warn("Memoria baja: 15%");
        log.error("Fallo conexión BD");

        log.tablaResumen(
            new String[]{"Ingresos", "Gastos", "Beneficio", "IVA"},
            new double[]{12500.50, 8200.75, 4299.75, 902.95}
        );

        log.demoConcatenacion();
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `System.out.println(sb.toString())` | `println` = `print` + `\n`. `StringBuilder` → `String` eficiente. |
| `String.format("%-5s", nivel.name())` | Formatea string ancho 5, alineado izquierda (`-`). `DEBUG` → `"DEBUG "`, `INFO` → `"INFO  "`. |
| `System.out.printf("%-20s %12.2f%n", ...)` | `%-20s`: string 20 chars izq. `%12.2f`: float 12 chars ancho, 2 decimales, der. `%n`: salto portable. |
| `a + " " + b + "!"` | Concatenación `+`. Compilador → `new StringBuilder().append(a).append(" ").append(b).append("!").toString()`. |
| `StringBuilder sb = new StringBuilder(); for... sb.append(i).append(",")` | **Explícito** en bucle: evita crear `StringBuilder` por iteración (como haría `+`). |
| `String.join("-", "Uno", "Dos", "Tres")` | Java 8+. Une con delimitador. Null-safe (lanza NPE si elemento null). |
| `LocalDateTime.now().format(FMT)` | Fecha actual formateada. `FMT` = `DateTimeFormatter` thread-safe (inmutable). |

## Errores Comunes

> [!warning] **Error 1: Concatenación `+` en bucle (rendimiento)**
> ```java
> String s = "";
> for (int i=0; i<10000; i++) s += i + ","; // ❌ Crea 10k StringBuilder + 10k Strings intermedios
> ```
> ✅ **Correcto**: `StringBuilder sb = new StringBuilder(); for(...) sb.append(i).append(','); String s = sb.toString();`

> [!warning] **Error 2: `println` vs `print` confusión**
> ```java
> System.print("Hola"); System.print("Mundo"); // ❌ "HolaMundo" en misma línea
> ```
> ✅ **Correcto**: `println` para líneas, `print` para continuar línea.

> [!warning] **Error 3: `printf` sin argumentos coincidentes**
> ```java
> System.out.printf("%d %s", 42); // ❌ MissingFormatArgumentException
> ```
> ✅ **Correcto**: `printf("%d %s", 42, "hola")` o `printf("%d", 42)`.

> [!warning] **Error 4: `%n` vs `\n`**
> ```java
> System.out.printf("L1\nL2"); // Windows: \n ≠ \r\n (puede verse mal en notepad)
> System.out.printf("L1%nL2"); // ✅ Portable (JVM traduce a separador SO)
> ```

> [!warning] **Error 5: `toString()` por defecto inútil**
> ```java
> class Usuario { String nombre; }
> System.out.println(new Usuario("Ana")); // ❌ Usuario@15db9742
> ```
> ✅ **Correcto**: Sobrescribe `toString()` en tu clase: `return "Usuario{" + nombre + "}";`

> [!warning] **Error 6: `System.out` no sincronizado en hilos (mezcla salida)**
> ```java
> // Múltiples hilos imprimiendo a la vez → líneas entrelazadas
> ```
> ✅ **Correcto**: `synchronized(System.out) { System.out.println(...); }` o usa logger (SLF4J/Logback).

## Buenas Prácticas

1. **`System.out` solo para CLI/debug** — En producción: **SLF4J + Logback/Log4j2** (niveles, appenders, async, rotación).
2. **`StringBuilder` en bucles / logs frecuentes** — Evita basura GC.
3. **`printf`/`format` para columnas, decimales, padding** — Más legible que concatenar `+ " " +`.
4. **`%n` siempre** — Portable entre Windows/Linux/Mac.
5. **Sobrescribe `toString()` en tus clases** — `System.out.println(objeto)` útil al instante.
6. **Evita `+` en logging condicional** — `log.debug("User: " + user.getName() + " id: " + user.getId())` evalúa **siempre** la concatenación aunque nivel sea INFO. Usa `log.debug("User: {} id: {}", user::getName, user::getId)` (lambda, lazy) con SLF4J.
7. **`String.join` / `String.format` / `MessageFormat`** — Para plantillas complejas.

## Conexión con Otros Temas

- `[[03 - Tipos Primitivos y Referencia]]` — Conversión automática primitivo→String en `+`.
- `[[06 - Atributos y Campos]]` — `toString()` usa campos.
- `[[10 - Metodos de Instancia]]` — `println(objeto.metodo())`.
- `[[14 - Arrays Basicos y args]]` — `Arrays.toString(array)` para imprimir arrays.
- `[[15 - Flujo de Ejecucion JVM]]` — `System` clase final, `out` estático `PrintStream`.

## Resumen en Una Frase

> **`System.out.println()` imprime con salto; `printf()` formatea con `%d`/`%s`/`%f`/`%n`; `+` concatena (usa `StringBuilder` interno); en bucles/logs usa `StringBuilder` explícito.**

---

## Tags
`#java #fundamentos #system-out #print #println #printf #concatenacion #stringbuilder #formato #logging`