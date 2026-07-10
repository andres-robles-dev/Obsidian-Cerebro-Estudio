---
tags: [java, fundamentos, variables, literales, scope, declaracion, inicializacion]
---

# 04 - Variables y Literales

## Concepto Central

Una **variable** es un nombre asociado a una posición de memoria que almacena un valor de un tipo concreto. En Java, toda variable debe **declararse** (tipo + nombre) antes de usarse, y puede **inicializarse** en la declaración o después. Un **literal** es la representación textual de un valor fijo en código (ej: `42`, `3.14`, `'A'`, `"Hola"`, `true`).

## Para Qué Sirve / Cuándo Usarlo

- Guardar estado temporal o permanente (campos)
- Pasar datos a métodos (parámetros)
- Recibir resultados de expresiones
- Configurar comportamiento (constantes)
- Leer argumentos de línea de comandos

## Sintaxis General

### Declaración e Inicialización

```java
// Declaración simple
tipo nombre;
// Declaración + inicialización
tipo nombre = valor;
// Múltiples mismo tipo
tipo a = 1, b = 2, c;
// Inicialización posterior (definida antes de uso)
tipo x;
x = calculaValor();
```

### Literales por Tipo

| Tipo | Literal Ejemplo | Notas |
|------|-----------------|-------|
| `int` | `42`, `-7`, `0b1010` (binario), `0x2A` (hex) | Por defecto `int` |
| `long` | `42L`, `0L`, `-3000000000L` | **Sufijo `L`/`l` obligatorio si > 2^31-1** |
| `float` | `3.14f`, `1e-3f`, `.5f` | **Sufijo `f`/`F` obligatorio** |
| `double` | `3.14`, `1e-3`, `.5` | Por defecto `double` |
| `boolean` | `true`, `false` | Solo dos valores |
| `char` | `'A'`, `'\n'`, `'\u0041'`, `'\t'` | Comillas simples, 1 char (16 bits Unicode) |
| `String` | `"Hola"`, `""`, `"Línea\nOtra"` | Comillas dobles, **objeto** inmutable |
| `null` | `null` | Referencia vacía (solo tipos referencia) |

> **Underscores en numéricos** (Java 7+): `1_000_000`, `0xFF_EC_DE_5E`, `3.14_15_92f` — solo legibilidad, ignorados por compilador.

## Ejemplo Propio: ConfiguracionJuego

```java
public class ConfiguracionJuego {
    // --- CONSTANTES (static final) - Convención UPPER_SNAKE_CASE ---
    public static final int MAX_JUGADORES = 4;
    public static final double GRAVEDAD = 9.81;
    public static final String VERSION = "1.0.0";
    public static final char TECLA_SALTO_DEFAULT = ' ';

    // --- CAMPOS DE INSTANCIA (estado configurable por partida) ---
    private String nombrePartida;
    private int numJugadores;
    private double velocidadJuego;
    private boolean modoHardcore;
    private char teclaSalto;

    // --- CONSTRUCTOR CON VALORES POR DEFECTO ---
    public ConfiguracionJuego(String nombrePartida) {
        this.nombrePartida = nombrePartida;      // Requerido
        this.numJugadores = 1;                   // Default
        this.velocidadJuego = 1.0;               // Default
        this.modoHardcore = false;               // Default
        this.teclaSalto = TECLA_SALTO_DEFAULT;   // Default desde constante
    }

    // --- SETTERS CON VALIDACIÓN (literales en condiciones) ---
    public void setNumJugadores(int numJugadores) {
        if (numJugadores < 1 || numJugadores > MAX_JUGADORES) {
            throw new IllegalArgumentException(
                "Jugadores debe ser 1-" + MAX_JUGADORES + ", recibido: " + numJugadores
            );
        }
        this.numJugadores = numJugadores;
    }

    public void setVelocidadJuego(double velocidad) {
        if (velocidad <= 0.0 || velocidad > 5.0) {
            throw new IllegalArgumentException("Velocidad 0.1-5.0, recibido: " + velocidad);
        }
        this.velocidadJuego = velocidad;
    }

    public void setModoHardcore(boolean modoHardcore) {
        this.modoHardcore = modoHardcore;
    }

    public void setTeclaSalto(char tecla) {
        // Validar imprimible (rango ASCII básico)
        if (tecla < ' ' || tecla > '~') {
            throw new IllegalArgumentException("Tecla no imprimible: " + (int)tecla);
        }
        this.teclaSalto = tecla;
    }

    // --- GETTERS ---
    public String getNombrePartida() { return nombrePartida; }
    public int getNumJugadores() { return numJugadores; }
    public double getVelocidadJuego() { return velocidadJuego; }
    public boolean isModoHardcore() { return modoHardcore; }
    public char getTeclaSalto() { return teclaSalto; }

    // --- MÉTODO DE VISUALIZACIÓN (concatenación de literales y vars) ---
    public void mostrarConfig() {
        System.out.println("=== Configuración: " + nombrePartida + " ===");
        System.out.println("Versión: " + VERSION);
        System.out.println("Jugadores: " + numJugadores + " / " + MAX_JUGADORES);
        System.out.println("Velocidad: " + velocidadJuego + "x");
        System.out.println("Hardcore: " + (modoHardcore ? "SÍ" : "NO"));
        System.out.println("Tecla salto: '" + teclaSalto + "' (ASCII: " + (int)teclaSalto + ")");
        System.out.println("Gravedad: " + GRAVEDAD + " m/s²");
    }

    // --- MAIN DE PRUEBA ---
    public static void main(String[] args) {
        ConfiguracionJuego cfg = new ConfiguracionJuego("Partida Épica");

        // Literales en llamadas
        cfg.setNumJugadores(3);
        cfg.setVelocidadJuego(1.5);
        cfg.setModoHardcore(true);
        cfg.setTeclaSalto('W');

        cfg.mostrarConfig();

        // Demostración literales varios
        System.out.println("\n--- Literales demo ---");
        int binario = 0b1010;      // 10 decimal
        int hexa = 0xFF;           // 255 decimal
        long grande = 9_223_372_036_854_775_807L; // Long.MAX_VALUE
        float prec = 1.23456789f;  // 7 dígitos precisión
        double alta = 1.23456789012345; // 15 dígitos
        char salto = '\n';         // Escape
        char unicode = '\u00A9';   // ©
        String multilinea = "Línea 1\nLínea 2\tTabulado";

        System.out.println("Binario 0b1010 = " + binario);
        System.out.println("Hex 0xFF = " + hexa);
        System.out.println("Long con _ : " + grande);
        System.out.println("Float: " + prec);
        System.out.println("Double: " + alta);
        System.out.println("Char \\n = " + (int)salto);
        System.out.println("Unicode © = " + unicode);
        System.out.println("String multilinea:\n" + multilinea);
    }
}
```

## Explicación Detallada Línea a Línea

| Sección | Explicación |
|---------|-------------|
| `static final int MAX_JUGADORES = 4;` | Constante de clase. `static` = una copia compartida. `final` = inmutable. Nombre `UPPER_SNAKE_CASE`. |
| `private String nombrePartida;` | Campo de instancia. `private` encapsula. Valor por defecto `null`. |
| `this.nombrePartida = nombrePartida;` | `this.` desambigua campo vs parámetro. Ver `[[07 - Constructores y this]]`. |
| `if (numJugadores < 1 || numJugadores > MAX_JUGADORES)` | Uso de constante en validación. Literal `1` y `MAX_JUGADORES` (int). |
| `throw new IllegalArgumentException(...)` | Lanza excepción con mensaje construido concatenando literal + variable. |
| `cfg.setNumJugadores(3);` | Literal `3` (int) pasado como argumento. |
| `cfg.setModoHardcore(true);` | Literal `true` (boolean). |
| `cfg.setTeclaSalto('W');` | Literal `char` con comillas simples. |
| `int binario = 0b1010;` | Literal binario (prefijo `0b`/`0B`, Java 7+). |
| `int hexa = 0xFF;` | Literal hexadecimal (prefijo `0x`/`0X`). |
| `long grande = 9_223_372_036_854_775_807L;` | Underscores legibilidad + sufijo `L` obligatorio. |
| `float prec = 1.23456789f;` | Sufijo `f` obligatorio para `float`. |
| `char salto = '\n';` | Secuencia escape: nueva línea. Otras: `\t`, `\r`, `\\`, `\'`, `\"`. |
| `char unicode = '\u00A9';` | Unicode de 4 hex dígitos: ©. |
| `String multilinea = "Línea 1\nLínea 2\tTabulado";` | `\n` y `\t` dentro de String se interpretan al imprimir. |

## Scope (Ámbito) de Variables

| Tipo | Declaración | Vida | Visibilidad |
|------|-------------|------|-------------|
| **Campo (instance field)** | En clase, fuera de métodos | Mientras exista el objeto | Según modificador (`private`, etc.) |
| **Variable local** | Dentro de método/bloque | Desde declaración hasta fin de bloque `{}` | Solo dentro de ese bloque |
| **Parámetro** | En firma método | Durante ejecución del método | Dentro del método |
| **Variable de clase (`static`)** | En clase con `static` | Mientras clase cargada (JVM viva) | Según modificador |

> **Regla**: Variable local **debe** inicializarse antes de leerla. Campos se inicializan solos (0, null, false).

## Errores Comunes

> [!warning] **Error 1: Leer variable local sin inicializar**
> ```java
> void metodo() {
>     int x;
>     System.out.println(x); // ❌ Error: variable might not have been initialized
> }
> ```
> ✅ **Correcto**: `int x = 0;` o inicializar en todas las ramas antes de usar.

> [!warning] **Error 2: `long` sin sufijo `L` para valor grande**
> ```java
> long x = 3000000000; // ❌ Error: integer number too large (se ve como int)
> ```
> ✅ **Correcto**: `long x = 3000000000L;`

> [!warning] **Error 3: `float` sin sufijo `f`**
> ```java
> float x = 3.14; // ❌ Error: incompatible types (double -> float)
> ```
> ✅ **Correcto**: `float x = 3.14f;`

> [!warning] **Error 4: `char` con comillas dobles o múltiples caracteres**
> ```java
> char c = "A";   // ❌ String, no char
> char c = 'AB';  // ❌ Más de un char
> ```
> ✅ **Correcto**: `char c = 'A';`

> [!warning] **Error 5: Confundir `=` (asignación) con `==` (comparación)**
> ```java
> if (x = 5) { } // ❌ Asigna 5 a x, resultado 5 (int) no boolean
> ```
> ✅ **Correcto**: `if (x == 5) { }`

> [!warning] **Error 6: Variable local oculta campo (shadowing) sin querer**
> ```java
> class A { int x = 1; void m() { int x = 2; System.out.println(x); } } // Imprime 2, campo inaccesible
> ```
> ✅ **Correcto**: Usa `this.x` para campo, o nombres distintos.

## Buenas Prácticas

1. **Inicializa en la declaración** cuando sea posible (`private int contador = 0;`).
2. **`final` por defecto** en locales y parámetros si no cambian (`final int max = 100;`).
3. **Nombres descriptivos** — `velocidadMaxima` no `v` ni `vm`.
4. **Constantes `static final`** para valores fijos compartidos (`Math.PI`, `Config.MAX`).
5. **Evita literales mágicos** — `if (x > 86400)` → `if (x > SEGUNDOS_POR_DIA)`.
6. **Scope mínimo** — Declara variables lo más cerca posible de su uso.
7. **Una variable, un propósito** — No reutilices `temp` para cosas distintas.
8. **Prefiere primitivos a wrappers** (`int` vs `Integer`) salvo nulabilidad o colecciones.

## Conexión con Otros Temas

- `[[03 - Tipos Primitivos y Referencia]]` — Tipos válidos para variables.
- `[[06 - Atributos y Campos]]` — Variables como campos de instancia vs locales.
- `[[07 - Constructores y this]]` — Inicialización de campos en constructor.
- `[[08 - Instanciacion y new]]` — Campos nacen con `new`.
- `[[10 - Metodos de Instancia]]` — Parámetros y locales en métodos.
- `[[14 - Arrays Basicos y args]]` — Array es variable referencia; `args` en main.

## Resumen en Una Frase

> **Variable = nombre + tipo + valor; literal = valor escrito en código; scope = dónde vive y muere la variable.**

---

## Tags
`#java #fundamentos #variables #literales #scope #declaracion #inicializacion #constantes`