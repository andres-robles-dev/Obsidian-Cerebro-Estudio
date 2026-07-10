---
tags: [java, fundamentos, operador, ternario, condicional, expresion]
---

# 12 - Operador Ternario

## Concepto Central

El **operador ternario** (`? :`) es la **única expresión condicional** en Java: evalúa una condición booleana y devuelve **uno de dos valores** según sea `true` o `false`. Sintaxis: `condicion ? valorSiVerdadero : valorSiFalso`. Es una **expresión** (produce valor), no una sentencia (no termina en `;` por sí misma). Útil para asignaciones, argumentos, returns concisos.

## Para Qué Sirve / Cuándo Usarlo

- Asignación condicional compacta: `int x = (a > b) ? a : b;`
- Argumento condicional en llamada: `metodo(flag ? "A" : "B");`
- `return` expresivo: `return n % 2 == 0 ? "par" : "impar";`
- Inicialización de `final`: `final String msg = ok ? "OK" : "FAIL";`
- Evitar `if-else` verboso para lógica simple de **valor**

> **No usar** para lógica compleja, efectos secundarios, o anidados profundos (legibilidad).

## Sintaxis General

```java
// Expresión completa (produce valor)
tipo variable = condicionBooleana ? expresionTrue : expresionFalse;

// En return
return condicion ? valorTrue : valorFalse;

// En argumento
otroMetodo(condicion ? argTrue : argFalse);

// En impresión
System.out.println(condicion ? "Sí" : "No");

// Anidado (desaconsejado > 1 nivel)
String r = a > b ? "mayor" : (a < b ? "menor" : "igual");
```

### Reglas de Tipos

- Los dos ramas (`:` separados) deben ser **compatibles** en tipo.
- Java aplica **promoción numérica** / **tipo común superior** (LUB - Least Upper Bound).
- `String` + `Object` → `Object`; `int` + `double` → `double`; `Integer` + `Double` → `Number` (boxing).

## Ejemplo Propio: ControlAccesoEdad

```java
public class ControlAccesoEdad {
    // Constantes de negocio
    private static final int EDAD_MINIMA = 18;
    private static final int EDAD_JUBILACION = 65;
    private static final int EDAD_MAXIMA_ACCESO = 120;

    // --- MÉTODO PRINCIPAL: usa ternario en return ---
    public static String evaluarAcceso(int edad, boolean esVip) {
        // Validación rangos (expresión booleana compuesta)
        boolean edadValida = edad >= 0 && edad <= EDAD_MAXIMA_ACCESO;

        // Ternario anidado simple: 3 casos
        String base = edadValida
            ? (edad >= EDAD_JUBILACION ? "Jubilado" : (edad >= EDAD_MINIMA ? "Adulto" : "Menor"))
            : "Edad inválida";

        // Ternario para sufijo VIP
        String vipTag = esVip ? " [VIP]" : "";

        return base + vipTag;
    }

    // --- MÉTODO: cálculo precio entrada con ternario ---
    public static double calcularPrecio(int edad, boolean esVip, boolean esEstudiante) {
        double base = 10.0;

        // Descuentos acumulativos con ternarios encadenados (legibles en líneas)
        double precio = base
            - (edad < 12           ? 5.0   : 0)   // Niño
            - (edad >= 65          ? 3.0   : 0)   // Jubilado
            - (esEstudiante        ? 2.0   : 0)   // Estudiante
            - (esVip               ? 1.0   : 0);  // VIP

        // Suelo a 0 (ternario)
        return precio > 0 ? precio : 0;
    }

    // --- MÉTODO: mensaje personalizado con ternario en argumento ---
    public static void saludar(String nombre, int edad, boolean vip) {
        String titulo = vip ? "Estimado" : "Hola";
        String segmento = edad < 18 ? "joven" : (edad < 65 ? "adulto" : "senior");
        // Ternario directo en println
        System.out.println(titulo + " " + nombre + ", bienvenido " + segmento + (vip ? " VIP" : "") + "!");
    }

    // --- MAIN DE PRUEBA ---
    public static void main(String[] args) {
        System.out.println("=== Evaluación Acceso ===");
        int[] edades = {10, 17, 18, 25, 65, 70, 130};
        boolean[] vip = {false, true, false, true, false, true, false};

        for (int i = 0; i < edades.length; i++) {
            String resultado = evaluarAcceso(edades[i], vip[i]);
            double precio = calcularPrecio(edades[i], vip[i], edades[i] <= 25);
            System.out.printf("Edad %3d | VIP:%-5s → %-18s | Precio: %.2f€%n",
                edades[i], vip[i], resultado, precio);
        }

        System.out.println("\n=== Saludos ===");
        saludar("Ana", 16, false);
        saludar("Carlos", 30, true);
        saludar("María", 70, false);

        // Demostración tipos resultado ternario
        System.out.println("\n=== Tipos en ternario ===");
        int a = 5, b = 10;
        double d = a > b ? a : b;           // int → double (promoción)
        Number n = a > b ? Integer.valueOf(a) : Double.valueOf(b); // Integer|Double → Number
        Object o = a > b ? "mayor" : 99;    // String|Integer → Object
        System.out.println("double: " + d + ", Number: " + n + ", Object: " + o);
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `boolean edadValida = edad >= 0 && edad <= EDAD_MAXIMA_ACCESO;` | Variable intermedia legible. Evita repetir condición compleja en ternario. |
| `String base = edadValida ? (edad >= 65 ? ... ) : "Edad inválida";` | **Ternario anidado 2 niveles**. Paréntesis obligatorios para precedencia. |
| `String vipTag = esVip ? " [VIP]" : "";` | Ternario simple para sufijo. `""` (String vacío) compatible con `" [VIP]"`. |
| `double precio = base - (cond ? desc : 0) - ...` | **Ternarios en expresión aritmética**. Cada uno devuelve `double` (promoción `int`→`double`). |
| `return precio > 0 ? precio : 0;` | **Guarda** (clamp) con ternario. Evita `if (precio < 0) precio = 0;`. |
| `String titulo = vip ? "Estimado" : "Hola";` | Asignación condicional simple. |
| `String segmento = edad < 18 ? "joven" : (edad < 65 ? "adulto" : "senior");` | Ternario anidado 2 niveles para 3 rangos. |
| `System.out.println(... (vip ? " VIP" : "") ...)` | Ternario **inline** en concatenación. |
| `double d = a > b ? a : b;` | `a,b` son `int`, resultado `double` por **promoción numérica** (asignación a `double`). |
| `Number n = ... ? Integer.valueOf(a) : Double.valueOf(b);` | Tipos wrapper distintos → **LUB = Number**. Boxing automático. |
| `Object o = ... ? "mayor" : 99;` | `String` e `Integer` → **LUB = Object**. Boxing del `int`. |

## Errores Comunes

> [!warning] **Error 1: Usar ternario como sentencia (sin asignar/retornar)**
> ```java
> edad >= 18 ? System.out.println("Adulto") : System.out.println("Menor"); // ❌ void no válido en expresión
> ```
> ✅ **Correcto**: `System.out.println(edad >= 18 ? "Adulto" : "Menor");` o `if-else`.

> [!warning] **Error 2: Tipos incompatibles en ramas**
> ```java
> Object x = true ? "texto" : 123; // ✅ Object (LUB)
> String s = true ? "texto" : 123; // ❌ String vs Integer incompatible
> ```
> ✅ **Correcto**: Asegura tipo común o castea: `(String) (cond ? "a" : "b")`.

> [!warning] **Error 3: Anidamiento ilegible**
> ```java
> String r = a>b?"A":a<b?"B":a==c?"C":"D"; // ❌ Imposible de leer
> ```
> ✅ **Correcto**: `if-else if-else` o `switch` / extraer a método.

> [!warning] **Error 4: Efectos secundarios en ramas**
> ```java
> int x = cond ? (metodoConSideEffect(), 1) : 0; // ❌ Coma en expresión: confuso
> ```
> ✅ **Correcto**: Separa lógica. Ternario = **valor**, no acción.

> [!warning] **Error 5: `null` en una rama sin tipo objetivo claro**
> ```java
> String s = cond ? "valor" : null; // ✅ OK (null compatible con String)
> // Pero si variable es `var`:
> var x = cond ? "valor" : null; // ❌ Error: tipo inferido = String, null ok pero confuso
> ```

## Buenas Prácticas

1. **Una línea, simple** — `cond ? A : B` donde A,B son expresiones puras.
2. **Paréntesis en anidados** — `(cond1 ? A : (cond2 ? B : C))`.
3. **Extrae a variable** si condición compleja: `boolean ok = ...; String r = ok ? A : B;`.
4. **Prefiere `if-else`** para >2 casos, lógica con efectos, o bloques >1 línea.
5. **`switch` expression (Java 12+)** para múltiples casos limpios:
   ```java
   String tipo = switch (edad) {
       case int e when e < 18 -> "Menor";
       case int e when e < 65 -> "Adulto";
       default -> "Senior";
   };
   ```
6. **`Objects.toString(obj, "default")`** para null-safe en ternario: `cond ? obj.toString() : "n/a"`.

## Conexión con Otros Temas

- `[[04 - Variables y Literales]]` — Asignación a `final` con ternario.
- `[[10 - Metodos de Instancia]]` — `return` con ternario.
- `[[13 - Static vs Instancia]]` — Métodos `static` usan ternario sin `this`.
- `[[16 - Convenciones de Nombrado]]` — Nombres booleanos claros (`esVip`, `edadValida`).

## Resumen en Una Frase

> **El ternario `cond ? A : B` es una expresión que devuelve `A` si `cond` es true, `B` si false; úsalo para valores simples, no para flujo de control complejo.**

---

## Tags
`#java #fundamentos #ternario #operador-condicional #expresion #asignacion-condicional`