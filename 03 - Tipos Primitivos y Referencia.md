---
tags: [java, fundamentos, tipos, primitivos, referencia, string]
---

# 03 - Tipos Primitivos y Referencia

## Concepto Central

Java distingue dos categorías de tipos de datos: **primitivos** (valores simples, almacenados directamente en la variable) y **referencia** (objetos, la variable guarda una dirección de memoria / referencia al objeto en el Heap). Esta distinción afecta asignación, comparación, paso a métodos y rendimiento.

## Para Qué Sirve / Cuándo Usarlo

| Categoría | Uso Típico |
|-----------|------------|
| **Primitivos** | Cálculos numéricos, contadores, banderas booleanas, caracteres sueltos, alto rendimiento, arrays grandes de números |
| **Referencia** | Texto (`String`), colecciones, objetos de dominio (`Usuario`, `Pedido`), APIs, cualquier cosa con identidad y estado complejo |

## Sintaxis General

### Primitivos (8 tipos)

| Tipo | Tamaño | Rango / Valores | Literal Ejemplo | Valor Defecto |
|------|--------|-----------------|-----------------|---------------|
| `byte` | 8 bits | -128 a 127 | `(byte)100` | `0` |
| `short` | 16 bits | -32,768 a 32,767 | `(short)30000` | `0` |
| **`int`** | **32 bits** | **-2.147M a 2.147M** | **`42`** | **`0`** |
| **`long`** | **64 bits** | **±9.22 quintillones** | **`42L`** | **`0L`** |
| `float` | 32 bits | ~7 dígitos precisión | `3.14f` | `0.0f` |
| **`double`** | **64 bits** | **~15 dígitos precisión** | **`3.14`** | **`0.0`** |
| **`boolean`** | **1 bit (JVM)** | **`true` / `false`** | **`true`** | **`false`** |
| **`char`** | **16 bits (Unicode)** | **`\u0000` a `\uFFFF`** | **`'A'`** | **`'\u0000'`** |

> **Negrita** = los más usados en práctica diaria.

### Referencia

```java
// Clases, interfaces, arrays, String
String nombre = "Ana";           // String (inmutable)
int[] numeros = {1, 2, 3};       // Array de primitivos
Usuario usuario = new Usuario(); // Objeto propio
List<String> lista = new ArrayList<>(); // Colección
```

## Ejemplo Propio: MedidasFisicas

```java
public class MedidasFisicas {
    public static void main(String[] args) {
        // --- PRIMITIVOS ---
        // Enteros
        byte temperaturaByte = -10;        // -128 a 127
        short altitudShort = 3000;         // -32k a 32k
        int poblacionInt = 47_000_000;     // Underscores para legibilidad (Java 7+)
        long distanciaLuzLong = 9_460_730_472_580_800L; // Sufijo L obligatorio

        // Decimales
        float precioFloat = 19.99f;        // Sufijo f obligatorio
        double piDouble = 3.141592653589793; // Por defecto double

        // Lógico y caracter
        boolean esDia = true;
        char inicial = 'M';                // Comillas simples, UN carácter
        char emoji = '\u1F600';            // Unicode: 😀

        // --- REFERENCIA ---
        String nombre = "Madrid";          // Objeto String (inmutable)
        String saludo = new String("Hola"); // Explícito new (raro en String)
        int[] codigosPostales = {28001, 28002, 28003}; // Array de primitivos
        Medida medida = new Medida(10.5, "cm"); // Objeto propio

        // --- DEMOSTRACIÓN VALOR VS REFERENCIA ---
        int a = 10;
        int b = a;           // Copia VALOR
        b = 20;
        System.out.println("a=" + a + ", b=" + b); // a=10, b=20 (independientes)

        int[] arr1 = {1, 2, 3};
        int[] arr2 = arr1;   // Copia REFERENCIA (apuntan al MISMO array)
        arr2[0] = 99;
        System.out.println("arr1[0]=" + arr1[0]); // 99 ! (compartido)

        // String: inmutable, parece valor pero es referencia
        String s1 = "Hola";
        String s2 = s1;
        s2 = "Adiós";        // s2 apunta a NUEVO objeto, s1 intacto
        System.out.println("s1=" + s1 + ", s2=" + s2); // s1=Hola, s2=Adiós
    }
}

// Clase de referencia propia
class Medida {
    double valor;
    String unidad;

    public Medida(double valor, String unidad) {
        this.valor = valor;
        this.unidad = unidad;
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `int poblacionInt = 47_000_000;` | `_` separador visual (Java 7+), ignorado por compilador. |
| `long distanciaLuzLong = ...L` | Sufijo `L` obligatorio si literal > `Integer.MAX_VALUE`. |
| `float precioFloat = 19.99f;` | Sufijo `f` obligatorio. Sin `f` → `double` por defecto. |
| `char inicial = 'M';` | Comillas **simples**. Un solo `char` (16 bits Unicode). |
| `char emoji = '\u1F600';` | Escape Unicode hexadecimal (4 dígitos). |
| `String nombre = "Madrid";` | Literal String → objeto en **String Pool** (área especial Heap). |
| `int[] codigosPostales = {...}` | Array: objeto en Heap, `length` fijo. Ver `[[14 - Arrays Basicos y args]]`. |
| `int b = a;` | **Primitivo**: copia el valor (bits). Cambiar `b` no afecta `a`. |
| `int[] arr2 = arr1;` | **Referencia**: copia la dirección. Ambos apuntan al mismo objeto. |
| `s2 = "Adiós";` | `String` inmutable: reasignar crea nuevo objeto, original intacto. |

## Errores Comunes

> [!warning] **Error 1: `long` sin sufijo `L`**
> ```java
> long grande = 3000000000; // ❌ Error: literal int fuera de rango
> long grande = 3000000000L; // ✅
> ```

> [!warning] **Error 2: `float` sin sufijo `f`**
> ```java
> float pi = 3.14; // ❌ Error: literal double incompatible
> float pi = 3.14f; // ✅
> ```

> [!warning] **Error 3: Comillas dobles en `char`**
> ```java
> char c = "A"; // ❌ String, no char
> char c = 'A'; // ✅
> ```

> [!warning] **Error 4: `==` en objetos (referencia)**
> ```java
> String s1 = new String("Hola");
> String s2 = new String("Hola");
> if (s1 == s2) { } // ❌ Compara referencias (false)
> if (s1.equals(s2)) { } // ✅ Compara contenido
> ```

> [!warning] **Error 5: `boolean` con 0/1**
> ```java
> boolean flag = 1; // ❌ Java no usa 0/1 para boolean
> boolean flag = true; // ✅
> ```

> [!warning] **Error 6: Asignar `null` a primitivo**
> ```java
> int n = null; // ❌ Solo tipos referencia aceptan null
> Integer n = null; // ✅ Wrapper (ver boxing)
> ```

## Buenas Prácticas

1. **`int` por defecto para enteros** — Suficiente 99% casos. `long` solo si necesitas > 2.147M.
2. **`double` por defecto para decimales** — `float` solo si memoria crítica (arrays masivos, GPU).
3. **`boolean` para banderas** — Nombres positivos: `estaActivo`, `tienePermiso` (no `noActivo`).
4. **`String` para texto** — Nunca `char[]` ni `byte[]` salvo I/O de bajo nivel.
5. **Evita `new String("...")`** — Literal `"..."` usa String Pool, más eficiente.
6. **Wrappers (`Integer`, `Double`, `Boolean`)** — Solo para colecciones, genéricos, `null` permitido. Ver *Boxing/Unboxing*.
7. **Underscores en literales largos** — `1_000_000` más legible que `1000000`.

## Conexión con Otros Temas

- `[[04 - Variables y Literales]]` — Declaración, inicialización, literales, scope.
- `[[06 - Atributos y Campos]]` — Tipos de atributos, valores por defecto (0, false, null).
- `[[08 - Instanciacion y new]]` — `new` crea objetos de tipos referencia en Heap.
- `[[10 - Metodos de Instancia]]` — Paso por valor (primitivos) vs paso por referencia (objetos).
- `[[13 - Static vs Instancia]]` — Campos `static` de tipos primitivos vs referencia.
- `[[14 - Arrays Basicos y args]]` — Arrays de primitivos vs arrays de objetos.

## Resumen en Una Frase

> **Primitivos guardan el valor directamente (copia al asignar); referencia guardan dirección al objeto en Heap (comparten objeto al asignar).**

---

## Tags
`#java #fundamentos #tipos #primitivos #referencia #string #literales`