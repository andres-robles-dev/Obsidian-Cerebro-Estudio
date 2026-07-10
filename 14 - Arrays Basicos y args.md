---
tags: [java, fundamentos, arrays, args, main, indice, length, multidimensional, foreach]
---

# 14 - Arrays Básicos y args

## Concepto Central

Un **array** es un **objeto** contenedor de tamaño fijo que almacena **múltiples valores del mismo tipo** (primitivos o referencias) accesibles por **índice numérico base 0**. `String[] args` en `main` es el array que recibe los argumentos de línea de comandos al lanzar `java Clase arg1 arg2`.

## Para Qué Sirve / Cuándo Usarlo

- Colecciones ordenadas de tamaño conocido: días semana, notas alumnos, píxeles imagen
- `args` en `main`: parámetros externos (rutas, flags, configuración)
- Buffers temporales, tablas de búsqueda, matrices (2D)
- Base para `ArrayList`, `HashMap`, etc. (colecciones dinámicas)

## Sintaxis General

### Declaración, Creación, Inicialización

```java
// 1. Declarar + crear (tamaño fijo, valores por defecto)
tipo[] nombre = new tipo[tamaño];
int[] nums = new int[5]; // [0,0,0,0,0]

// 2. Declarar + inicializar con valores (literal array)
tipo[] nombre = {val1, val2, val3};
String[] dias = {"Lun", "Mar", "Mié"};

// 3. Crear anónimo (para pasar a método)
metodo(new int[]{1,2,3});

// 4. Array multidimensional (array de arrays)
tipo[][] matriz = new tipo[filas][columnas];
int[][] tablero = new int[8][8]; // Ajedrez
```

### Acceso y Propiedades

| Operación | Sintaxis | Notas |
|-----------|----------|-------|
| Leer elemento | `array[indice]` | `0 <= indice < array.length` |
| Escribir elemento | `array[indice] = valor` | Tipo compatible |
| Longitud | `array.length` | **Campo** (no método), `final`, tiempo O(1) |
| Recorrido índice | `for (int i=0; i<a.length; i++)` | Control total |
| Recorrido for-each | `for (Tipo e : array)` | Solo lectura, sin índice |
| Copia | `Arrays.copyOf(arr, n)` | `java.util.Arrays` |
| Ordenar | `Arrays.sort(arr)` | In-place, dual-pivot quicksort |
| Buscar | `Arrays.binarySearch(arr, key)` | Requiere ordenado |
| Comparar | `Arrays.equals(a1, a2)` | Elemento a elemento |
| String | `Arrays.toString(arr)` | `[1, 2, 3]` legible |

## Ejemplo Propio: RegistroNotas + DemoArgs

```java
import java.util.Arrays;

public class RegistroNotas {
    // Constantes
    private static final double NOTA_MIN = 0.0;
    private static final double NOTA_MAX = 10.0;

    // Estado: array de notas (referencia)
    private double[] notas;
    private String asignatura;

    // Constructor con array (copia defensiva)
    public RegistroNotas(String asignatura, double[] notasIniciales) {
        this.asignatura = asignatura;
        this.notas = (notasIniciales != null) ? Arrays.copyOf(notasIniciales, notasIniciales.length) : new double[0];
    }

    // Añadir nota (crea array mayor + copia) — ineficiente para muchas inserciones, usa ArrayList en real
    public void addNota(double nota) {
        validar(nota);
        notas = Arrays.copyOf(notas, notas.length + 1);
        notas[notas.length - 1] = nota;
    }

    // Estadísticas
    public double media() {
        if (notas.length == 0) return 0;
        double sum = 0;
        for (double n : notas) sum += n; // for-each
        return sum / notas.length;
    }

    public double maxima() {
        if (notas.length == 0) return 0;
        double max = notas[0];
        for (int i = 1; i < notas.length; i++) if (notas[i] > max) max = notas[i]; // índice
        return max;
    }

    public double minima() {
        if (notas.length == 0) return 0;
        double min = notas[0];
        for (double n : notas) if (n < min) min = n;
        return min;
    }

    public void mostrar() {
        System.out.println("=== " + asignatura + " ===");
        System.out.println("Notas: " + Arrays.toString(notas));
        System.out.printf("Media: %.2f | Min: %.2f | Max: %.2f | N: %d%n",
            media(), minima(), maxima(), notas.length);
    }

    private void validar(double n) {
        if (n < NOTA_MIN || n > NOTA_MAX)
            throw new IllegalArgumentException("Nota " + n + " fuera [" + NOTA_MIN + "," + NOTA_MAX + "]");
    }

    // --- MAIN: procesa args como notas ---
    public static void main(String[] args) {
        System.out.println("=== Demo args ===");
        System.out.println("args.length = " + args.length);
        System.out.println("args = " + Arrays.toString(args));

        // args son Strings → parsear a double
        double[] notasArgs = new double[args.length];
        for (int i = 0; i < args.length; i++) {
            try {
                notasArgs[i] = Double.parseDouble(args[i]);
            } catch (NumberFormatException e) {
                System.err.println("Argumento inválido (no número): '" + args[i] + "'. Se ignora.");
                notasArgs[i] = -1; // Marcador
            }
        }

        // Filtrar válidas (>=0)
        double[] validas = Arrays.stream(notasArgs).filter(n -> n >= 0).toArray();

        RegistroNotas reg = new RegistroNotas("Desde CLI", validas);
        reg.mostrar();

        // Demo array 2D
        demoMatriz();
    }

    private static void demoMatriz() {
        System.out.println("\n=== Matriz 3x3 (tablero) ===");
        int[][] tablero = new int[3][3];
        int val = 1;
        for (int i = 0; i < tablero.length; i++) {
            for (int j = 0; j < tablero[i].length; j++) {
                tablero[i][j] = val++;
            }
        }
        // Imprimir matriz
        for (int[] fila : tablero) {
            System.out.println(Arrays.toString(fila));
        }
        // Acceso: tablero[fila][col]
        System.out.println("Centro: " + tablero[1][1]); // 5
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `private double[] notas;` | Campo referencia a array. Inicialmente `null`. |
| `Arrays.copyOf(notasIniciales, ...)` | **Copia defensiva**: evita aliasing con array pasado por cliente. |
| `notas = Arrays.copyOf(notas, notas.length + 1);` | **Redimensionar**: crea array nuevo +1, copia contenido. O(n). |
| `for (double n : notas)` | **For-each**: itera valores, sin índice. No permite borrar/insertar. |
| `for (int i = 1; i < notas.length; i++)` | **For índice**: necesita índice para comparar `notas[i] > max`. |
| `Double.parseDouble(args[i])` | Convierte `String` → `double`. Lanza `NumberFormatException` si inválido. |
| `Arrays.stream(...).filter(...).toArray()` | **Stream API (Java 8+)**: filtrado funcional. Requiere `import java.util.Arrays;`. |
| `int[][] tablero = new int[3][3];` | **Array 2D**: array de 3 arrays de 3 ints cada uno. `tablero.length=3`, `tablero[0].length=3`. |
| `for (int[] fila : tablero)` | For-each sobre filas (cada fila es `int[]`). |

## Errores Comunes

> [!warning] **Error 1: Índice fuera de rango**
> ```java
> int[] a = new int[3];
> a[3] = 10; // ❌ ArrayIndexOutOfBoundsException (índices 0,1,2)
> ```
> ✅ **Correcto**: `if (i >= 0 && i < a.length) a[i] = 10;`

> [!warning] **Error 2: `length()` con paréntesis**
> ```java
> int len = a.length(); // ❌ length es CAMPO, no método
> ```
> ✅ **Correcto**: `a.length`

> [!warning] **Error 3: Array no inicializado (null)**
> ```java
> int[] a; System.out.println(a.length); // ❌ NPE
> ```
> ✅ **Correcto**: `int[] a = new int[0];` o `= {}`.

> [!warning] **Error 4: For-each no permite modificar array**
> ```java
> for (int n : a) n = 5; // ❌ Modifica variable local n, NO a[i]
> ```
> ✅ **Correcto**: `for (int i=0; i<a.length; i++) a[i] = 5;`

> [!warning] **Error 5: `==` compara referencias, no contenido**
> ```java
> int[] a = {1,2}; int[] b = {1,2};
> if (a == b) ... // ❌ false (distintos objetos)
> ```
> ✅ **Correcto**: `Arrays.equals(a, b)` o `Arrays.deepEquals` para 2D.

> [!warning] **Error 6: `args` en main siempre existe (length 0 si vacío)**
> ```java
> // java MiClase → args.length == 0, args != null
> // java MiClase a b → args = ["a", "b"]
> ```

## Buenas Prácticas

1. **Prefiere `ArrayList`/`List`** para tamaño variable — `add()` O(1) amortizado vs `Arrays.copyOf` O(n).
2. **Copia defensiva** en constructores/setters que reciben arrays — `Arrays.copyOf(arr, arr.length)`.
3. **`Arrays.toString()` / `deepToString()`** para depuración — no `System.out.println(arr)` (imprime hash).
4. **For-each por defecto** — Índice solo si necesitas posición o mutar.
5. **Valida `args` en main** — `if (args.length < esperados) { usage(); return; }`.
6. **`Arrays.sort()` muta original** — Copia si necesitas conservar orden: `Arrays.sort(Arrays.copyOf(a, a.length))`.
7. **Arrays primitivos vs wrappers** — `int[]` más memoria/rendimiento que `Integer[]`.
8. **Varargs = array** — `void m(int... nums)` recibe `int[]`. `m(1,2,3)` o `m(new int[]{1,2,3})`.

## Conexión con Otros Temas

- `[[03 - Tipos Primitivos y Referencia]]` — Array es objeto (referencia), elementos pueden ser primitivos.
- `[[04 - Variables y Literales]]` — Literal array `{1,2,3}`, variable `int[] a`.
- `[[08 - Instanciacion y new]]` — `new int[5]` crea objeto en Heap.
- `[[09 - Multiples Objetos e Identidad]]` — Dos arrays con mismo contenido ≠ mismo objeto.
- `[[10 - Metodos de Instancia]]` — Métodos reciben/retornan arrays.
- `[[13 - Static vs Instancia]]` — `Arrays` clase utilitaria `static`.
- `[[15 - Flujo de Ejecucion JVM]]` — `args` creado por JVM antes de `main`.

## Resumen en Una Frase

> **Array = objeto contenedor tamaño fijo, índice 0..length-1, `length` campo; `args` en main = `String[]` con parámetros CLI; usa `Arrays` para utilidades y `ArrayList` si tamaño cambia.**

---

## Tags
`#java #fundamentos #arrays #args #main #indice #length #foreach #multidimensional #arrays-util`