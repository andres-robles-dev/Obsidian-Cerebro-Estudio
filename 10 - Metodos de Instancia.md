---
tags: [java, fundamentos, metodos, instancia, void, return, parametros, sobrecarga, this]
---

# 10 - Métodos de Instancia

## Concepto Central

Un **método de instancia** es un bloque de código con nombre que pertenece a un **objeto** (instancia) y opera sobre **sus datos** (campos `this.campo`). Se invoca **sobre una referencia**: `objeto.metodo(args)`. Puede leer/modificar estado (`this`), recibir parámetros, devolver un valor (`return`) o solo realizar acción (`void`).

## Para Qué Sirve / Cuándo Usarlo

- Encapsular comportamiento asociado a un objeto: `cuenta.retirar(100)`, `rectangulo.area()`
- Reutilizar lógica sin duplicar código
- Exponer API pública controlada (ocultar implementación)
- Mantener invariantes validando en setters/métodos de negocio
- Polimorfismo (sobrescritura en herencia)

## Sintaxis General

```java
[modificadores] tipoRetorno nombreMetodo([listaParametros]) [throws Excepciones] {
    // Cuerpo: sentencias, uso de this.campo, llamadas a otros métodos
    return valor; // Obligatorio si tipoRetorno != void
}
```

| Parte | Descripción |
|-------|-------------|
| `modificadores` | `public`, `private`, `protected`, `static` (ver [[13]]), `final`, `abstract` |
| `tipoRetorno` | Tipo del valor devuelto (`int`, `String`, `void`, `MiClase`, `List<T>`...) |
| `nombreMetodo` | `camelCase`, verbo acción: `calcular`, `obtener`, `procesar`, `esValido` |
| `listaParametros` | `Tipo nombre, Tipo2 nombre2` — cero o más. Son **variables locales** al método. |
| `throws` | Excepciones checked que propaga (opcional) |
| `return` | Termina método y devuelve valor. En `void`: `return;` opcional al final. |

### Paso de Parámetros: **Valor de la Referencia**

| Tipo Parámetro | Qué se Copia | Efecto en Original |
|----------------|--------------|-------------------|
| **Primitivo** (`int`, `double`...) | Valor (bits) | **Ninguno** — método trabaja con copia |
| **Referencia** (`String`, `Objeto`, `Array`) | Dirección (referencia) | **Puede mutar objeto** apuntado — **no** reasignar parámetro |

```java
void primitivo(int x) { x = 10; }           // Copia: original intacto
void referencia(StringBuilder sb) { sb.append("X"); } // Misma referencia: MUTA original
void reasignar(StringBuilder sb) { sb = new StringBuilder(); } // Cambia ref LOCAL, original intacto
```

## Ejemplo Propio: Calculadora

```java
public class Calculadora {
    // --- ESTADO (historial simple) ---
    private final List<String> historial = new ArrayList<>();
    private static final int MAX_HISTORIAL = 100;

    // --- OPERACIONES BÁSICAS (void: acción, no devuelven) ---
    public void sumar(double a, double b) {
        double r = a + b;
        registrar("SUM", a, b, r);
        System.out.println(a + " + " + b + " = " + r);
    }

    public void restar(double a, double b) {
        double r = a - b;
        registrar("RES", a, b, r);
        System.out.println(a + " - " + b + " = " + r);
    }

    public void multiplicar(double a, double b) {
        double r = a * b;
        registrar("MUL", a, b, r);
        System.out.println(a + " × " + b + " = " + r);
    }

    public void dividir(double a, double b) {
        if (b == 0) {
            System.out.println("Error: división por cero");
            return; // Salida anticipada en void
        }
        double r = a / b;
        registrar("DIV", a, b, r);
        System.out.println(a + " ÷ " + b + " = " + r);
    }

    // --- OPERACIONES CON RETORNO (expresiones, composables) ---
    public double suma(double a, double b) { return a + b; }
    public double resta(double a, double b) { return a - b; }
    public double multiplicacion(double a, double b) { return a * b; }
    public double division(double a, double b) {
        if (b == 0) throw new ArithmeticException("División por cero");
        return a / b;
    }

    // --- MÉTODOS QUE USAN ESTADO (this) ---
    private void registrar(String op, double a, double b, double r) {
        if (historial.size() >= MAX_HISTORIAL) historial.remove(0); // FIFO
        historial.add(String.format("%s: %.2f %s %.2f = %.2f", op, a, opChar(op), b, r));
    }

    private char opChar(String op) {
        return switch (op) { case "SUM" -> '+'; case "RES" -> '-'; case "MUL" -> '×'; case "DIV" -> '÷'; default -> '?'; };
    }

    public void mostrarHistorial() {
        System.out.println("\n=== Historial (" + historial.size() + ") ===");
        for (String h : historial) System.out.println(h);
    }

    public void limpiarHistorial() { historial.clear(); }

    // --- SOBRECARGA: mismo nombre, distinta firma ---
    public double sumar(double... valores) { // Varargs
        double s = 0;
        for (double v : valores) s += v;
        registrar("SUM", 0, 0, s); // Simplificado
        return s;
    }

    public int sumar(int a, int b) { // Sobrecarga por tipos
        return a + b;
    }

    // --- MAIN DE PRUEBA ---
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();

        // Void methods (acción)
        calc.sumar(10, 5);
        calc.restar(20, 8);
        calc.multiplicar(6, 7);
        calc.dividir(100, 4);
        calc.dividir(10, 0); // Manejo error

        // Return methods (expresión)
        double r = calc.suma(3.5, 2.1) * calc.multiplicacion(2, 2);
        System.out.println("\nExpresión: (3.5+2.1) * (2*2) = " + r);

        // Varargs
        System.out.println("Suma varargs: " + calc.sumar(1,2,3,4,5,6));

        // Sobrecarga int
        System.out.println("Suma int: " + calc.sumar(10, 20));

        // Historial (estado this)
        calc.mostrarHistorial();
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `private final List<String> historial` | Campo de instancia `final` (referencia inmutable). Inicializado en línea. |
| `public void sumar(double a, double b)` | Método **void**: realiza acción (imprime, registra), **no devuelve** valor. |
| `return;` en `dividir` | Salida anticipada en `void`. Válido. Sin valor. |
| `public double suma(double a, double b) { return a + b; }` | Método **con retorno**: calcula y devuelve. **Componible**: `calc.suma(...) * calc.multiplicacion(...)`. |
| `throw new ArithmeticException(...)` | Lanza excepción *unchecked*. Cliente debe `try-catch` o propagar. |
| `private void registrar(...)` | Método **privado** (helper interno). Usa `this.historial` (estado instancia). |
| `for (String h : historial)` | *Enhanced for* (for-each) sobre colección. Lee `this.historial`. |
| `public double sumar(double... valores)` | **Varargs**: 0..N `double`. Internamente array `double[]`. Sobrecarga de `sumar(double,double)`. |
| `public int sumar(int a, int b)` | **Sobrecarga**: mismo nombre, **firma distinta** (tipos parámetros). Resuelta en compile-time. |
| `calc.sumar(10, 5)` | Invocación: `objeto.metodo(args)`. `this` dentro = `calc`. |

## Errores Comunes

> [!warning] **Error 1: Olvidar `return` en método no-void**
> ```java
> int suma(int a, int b) { a + b; } // ❌ missing return statement
> ```
> ✅ **Correcto**: `return a + b;` o `int r = a+b; return r;`

> [!warning] **Error 2: `return` con valor en `void`**
> ```java
> void saludar() { return "Hola"; } // ❌ incompatible types
> ```
> ✅ **Correcto**: `void` → `return;` o nada al final.

> [!warning] **Error 3: Confundir paso por valor (referencia)**
> ```java
> void cambiar(String s) { s = "Nuevo"; }
> String original = "Viejo"; cambiar(original); // original sigue "Viejo"
> ```
> ✅ **Entiende**: Parámetro `s` copia referencia. `s = ...` cambia copia local. Para mutar: `StringBuilder sb = new StringBuilder(); cambiar(sb);` donde `cambiar` hace `sb.append(...)`.

> [!warning] **Error 4: Sobrescritura accidental (overriding) vs sobrecarga (overloading)**
> ```java
> class Padre { void m(int x) {} }
> class Hijo { void m(double x) {} } // ❌ Sobrecarga, no override
> ```
> ✅ **Override**: Mismo nombre, **mismos parámetros**, mismo retorno (o covariante), `@Override` annotation.

> [!warning] **Error 5: Llamar método instancia sin objeto**
> ```java
> class A { void m() {} }
> A.m(); // ❌ Non-static method cannot be referenced from static context
> ```
> ✅ **Correcto**: `new A().m();` o `A a = new A(); a.m();`

> [!warning] **Error 6: `this` en método `static`**
> ```java
> static void m() { this.x = 1; } // ❌ static context has no this
> ```
> ✅ **Correcto**: `this` solo en instancia.

## Buenas Prácticas

1. **Un método, una responsabilidad** — `calcular()` calcula, `guardar()` persiste, `imprimir()` imprime.
2. **`void` para comandos, `return` para consultas** — *Command Query Separation*. `cuenta.retirar(100)` (void), `cuenta.getSaldo()` (return).
3. **Parámetros `final`** — `void m(final int x)` evita reasignación accidental.
4. **Valida temprano** — `if (b == 0) throw ...` al inicio (fail-fast).
5. **Evita `boolean` flags como parámetros** — `procesar(true, false)` ilegible. Mejor dos métodos: `procesarRapido()`, `procesarCompleto()`.
6. **Sobrecarga con cuidado** — Solo si semántica idéntica. `sumar(int,int)` y `sumar(double,double)` OK. `procesar(int)` y `procesar(String)` confuso.
7. **Varargs al final** — `void m(int fijo, String... vars)` válido. `void m(String... vars, int fijo)` ❌.
8. **`@Override` siempre** — Compilador avisa si firma no coincide con padre/interfaz.
9. **Documenta contrato** — Javadoc: qué hace, parámetros, retorno, excepciones, efectos colaterales (mutación).

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Métodos son miembros de la clase.
- `[[06 - Atributos y Campos]]` — Métodos leen/escriben `this.campo`.
- `[[07 - Constructores y this]]` — Constructores son métodos especiales de inicialización.
- `[[08 - Instanciacion y new]]` — `new Clase()` crea objeto para llamar métodos.
- `[[09 - Multiples Objetos e Identidad]]` — `this` = identidad del objeto receptor.
- `[[13 - Static vs Instancia]]` — Diferencia método `static` (clase) vs instancia.
- `[[17 - Separacion de Responsabilidades]]` — Métodos cohesivos, clase con una razón de cambio.

## Resumen en Una Frase

> **Un método de instancia es una operación con nombre que se ejecuta sobre un objeto (`this`), recibe parámetros (por valor), puede leer/escribir sus campos, y opcionalmente devuelve un resultado.**

---

## Tags
`#java #fundamentos #metodos #instancia #void #return #parametros #sobrecarga #varargs #this`