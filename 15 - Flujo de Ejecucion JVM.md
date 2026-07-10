---
tags: [java, fundamentos, jvm, flujo, ejecucion, classloading, stack, heap, gc, hilos]
---

# 15 - Flujo de Ejecución JVM

## Concepto Central

La **JVM (Java Virtual Machine)** orquesta el ciclo de vida de un programa Java: **carga clases** (Class Loading), **inicializa estáticos**, ejecuta **`main`** en hilo principal, gestiona **memoria** (Stack por hilo, Heap compartido), y **recolecta basura** (GC). Entender este flujo explica *cuándo* y *por qué* se ejecutan constructores, bloques static, inicializadores, y cómo interactúan hilos y memoria.

## Para Qué Sirve / Cuándo Usarlo

- Diagnosticar orden de inicialización (static vs instance)
- Entender `StackOverflowError` vs `OutOfMemoryError`
- Optimizar arranque (lazy loading, classloading)
- Depurar concurrencia (visibilidad, happens-before)
- Diseñar sistemas con classloaders personalizados (plugins, hot-reload)

## Sintaxis General: Fases de Arranque

```text
1. JVM START
   └─ Bootstrap ClassLoader (rt.jar / jrt-fs)
   └─ Platform / System ClassLoaders (classpath, modules)

2. CLASS LOADING (bajo demanda, primera referencia activa)
   ├─ Loading    → Lee .class → binary data → Method Area
   ├─ Linking    → Verification (bytecode válido)
   │             → Preparation (static fields = default values)
   │             → Resolution (symbolic refs → direct refs, opcional)
   └─ Initialization → <clinit> (static fields init + static blocks en orden)

3. MAIN THREAD START
   └─ Invoca public static void main(String[])

4. EXECUTION LOOP (por hilo)
   ├─ Stack Frame por llamada método
   │   ├─ Local Variables (parámetros, locales, this)
   │   ├─ Operand Stack (cálculos intermedios)
   │   └─ Frame Data (return addr, exception table)
   ├─ Heap: Objetos (new), Arrays, static fields
   └─ PC Register: instrucción actual

5. GARBAGE COLLECTION (Heap)
   ├─ Young Gen (Eden + S0/S1) → Minor GC frecuente
   ├─ Old Gen → Major/Full GC raro
   └─ Metaspace (Class metadata) → GC clases descargadas

6. JVM SHUTDOWN
   ├─ Shutdown Hooks (Runtime.addShutdownHook)
   ├─ Finalizers (deprecated) / Cleaners
   └─ Exit status
```

## Orden de Inicialización Detallado (Clave para Exámenes/Debug)

```java
class Padre {
    static { System.out.println("1. Static Padre"); }
    { System.out.println("3. Instance block Padre"); }
    Padre() { System.out.println("4. Constructor Padre"); }
    static int s = metodoStatic("2. Static field Padre");
    int i = metodoInstancia("5. Instance field Padre");
    static int metodoStatic(String s) { System.out.println(s); return 1; }
    int metodoInstancia(String s) { System.out.println(s); return 1; }
}

class Hijo extends Padre {
    static { System.out.println("6. Static Hijo"); }
    { System.out.println("8. Instance block Hijo"); }
    Hijo() { System.out.println("9. Constructor Hijo"); }
    static int s = metodoStatic("7. Static field Hijo");
    int i = metodoInstancia("10. Instance field Hijo");
}

// Main: new Hijo();
/*
SALIDA:
1. Static Padre
2. Static field Padre
6. Static Hijo
7. Static field Hijo
3. Instance block Padre
4. Constructor Padre
5. Instance field Padre
8. Instance block Hijo
9. Constructor Hijo
10. Instance field Hijo
*/

// REGLAS:
// 1. Static padre → Static hijo (jerarquía arriba-abajo)
// 2. Instance block + Constructor padre → Instance block + Constructor hijo
// 3. Campos se inicializan EN ORDEN DECLARACIÓN, intercalados con bloques instancia
// 4. Super() implícito primero en constructor hijo
```

## Ejemplo Propio: RastreoEjecucion

```java
public class RastreoEjecucion {
    // --- STATIC FIELDS + BLOQUES ---
    static { log("1. [STATIC BLOCK] Inicio carga clase"); }
    static int contadorClase = initContador(); // Inicializador campo static
    static String config = cargarConfig();     // Otro campo static

    static int initContador() {
        log("2. [STATIC FIELD] initContador()");
        return 0;
    }

    static String cargarConfig() {
        log("3. [STATIC FIELD] cargarConfig()");
        return "PROD";
    }

    // --- INSTANCE FIELDS + BLOQUES ---
    { log("7. [INSTANCE BLOCK] Bloque instancia"); }
    int idInstancia = asignarId();              // Campo instancia
    String nombre = "Obj-" + idInstancia;       // Otro campo instancia

    int asignarId() {
        log("8. [INSTANCE FIELD] asignarId()");
        return ++contadorClase; // Accede a static
    }

    // --- CONSTRUCTOR ---
    public RastreoEjecucion() {
        log("9. [CONSTRUCTOR] this=" + this + ", id=" + idInstancia);
    }

    // --- MÉTODOS ---
    static void log(String msg) {
        System.out.printf("[%s] %s%n", Thread.currentThread().getName(), msg);
    }

    void metodoInstancia() {
        log("10. [METODO INSTANCIA] invocado");
    }

    static void metodoStatic() {
        log("11. [METODO STATIC] invocado");
    }

    // --- MAIN: Demostración flujo completo ---
    public static void main(String[] args) {
        log("=== MAIN INICIO ===");
        log("5. [MAIN] Antes de primer new");
        metodoStatic(); // 11

        log("6. [MAIN] Creando obj1...");
        RastreoEjecucion obj1 = new RastreoEjecucion(); // 7,8,9
        obj1.metodoInstancia(); // 10

        log("12. [MAIN] Creando obj2...");
        RastreoEjecucion obj2 = new RastreoEjecucion(); // 7,8,9 (static NO repite)
        obj2.metodoInstancia();

        log("13. [MAIN] Acceso static: contadorClase=" + contadorClase);

        // Demo Stack vs Heap
        demoMemoria();
    }

    static void demoMemoria() {
        log("\n=== MEMORIA: Stack vs Heap ===");
        // Stack: variables locales, parámetros, frames
        int a = 10;                // Stack (primitivo)
        RastreoEjecucion r = new RastreoEjecucion(); // r en Stack, OBJETO en Heap
        int[] arr = new int[1000]; // arr ref en Stack, array en Heap

        log("a (primitivo) en Stack: " + a);
        log("r (ref) en Stack -> Objeto en Heap: " + r);
        log("arr (ref) en Stack -> Array en Heap: " + arr.length + " elementos");

        // Llamada método → nuevo Frame en Stack
        calcular(a, r, arr);
    }

    static void calcular(int x, RastreoEjecucion ref, int[] array) {
        log("  >> calcular() nuevo Stack Frame");
        log("  x (copia valor) = " + x);
        log("  ref (copia referencia) -> mismo objeto: " + (ref == array)); // false tipos dif
        // array es int[], ref es RastreoEjecucion
        log("  << calcular() return, frame POPPED");
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `static { log("1..."); }` | **Bloque static**: ejecuta **una vez** al inicializar clase (antes de `main`). |
| `static int contadorClase = initContador();` | **Campo static con inicializador**: llama método static. Orden = declaración. |
| `{ log("7..."); }` | **Bloque instancia**: ejecuta **cada `new`**, **antes** del constructor. |
| `int idInstancia = asignarId();` | **Campo instancia**: inicializador ejecuta en cada `new`, tras bloque instancia. |
| `public RastreoEjecucion() { ... }` | **Constructor**: corre **después** de bloques/campos instancia. `this` ya válido. |
| `RastreoEjecucion obj1 = new RastreoEjecucion();` | **`new`**: 1) Asigna Heap 2) Inicializa campos a default 3) Bloques instancia + campos 4) Constructor. |
| `obj1.metodoInstancia();` | Llamada **virtual** (dispatch por tipo real). Nuevo **Stack Frame**. |
| `static void log(...)` | Método **static**: no `this`. Accede solo a static. Llamado `Clase.log()` o `obj.log()` (warning). |
| `int a = 10;` | **Variable local primitiva** → **Stack** (frame actual). |
| `RastreoEjecucion r = new ...;` | `r` (referencia) en **Stack**; **Objeto** en **Heap**. |
| `calcular(a, r, arr);` | **Nuevo Frame** en Stack. Parámetros: `x` (copia valor `a`), `ref` (copia referencia `r`), `array` (copia ref `arr`). |

## Memoria: Stack vs Heap

| Característica | **Stack (Pila)** | **Heap (Montículo)** |
|----------------|------------------|----------------------|
| **Dueño** | Cada **hilo** (Thread) | **Compartido** (todos hilos) |
| **Contenido** | Frames de métodos: locales, parámetros, `this`, return addr, operand stack | **Objetos** (`new`), **Arrays**, **static fields** |
| **Vida** | LIFO: `push` al llamar, `pop` al return | Hasta **inalcanzable** (GC) |
| **Tamaño** | Pequeño (~1MB/hilo configurable `-Xss`) | Grande (`-Xmx`) |
| **Error** | `StackOverflowError` (recursión infinita, frames profundos) | `OutOfMemoryError: Java heap space` |
| **Acceso** | Muy rápido (puntero stack pointer) | Más lento (gestión alloc/GC) |
| **Thread-safety** | **Inherente** (cada hilo su stack) | **Requiere sincronización** |

## Errores Comunes

> [!warning] **Error 1: `StackOverflowError` por recursión sin base**
> ```java
> void f() { f(); } // ❌ Llena Stack frames hasta overflow
> ```
> ✅ **Correcto**: Caso base `if (n==0) return; f(n-1);` o usa iteración.

> [!warning] **Error 2: `OutOfMemoryError` reteniendo referencias**
> ```java
> static List<Object> cache = new ArrayList<>();
> void add(Object o) { cache.add(o); } // ❌ Nunca libera, GC no limpia
> ```
> ✅ **Correcto**: `WeakReference`, `SoftReference`, `Cache` con expiración, `Map.computeIfAbsent`.

> [!warning] **Error 3: Acceso campo instancia desde static**
> ```java
> class A { int x; static void f() { System.out.println(x); } } // ❌
> ```
> ✅ **Correcto**: `static void f(A a) { System.out.println(a.x); }`

> [!warning] **Error 4: Bloque static lanza excepción → `ExceptionInInitializerError`**
> ```java
> static { throw new RuntimeException("fail"); } // Clase inutilizable
> ```
> ✅ **Correcto**: Try-catch en bloque static, o lazy initialization holder.

> [!warning] **Error 5: Orden inicialización campos static depende de declaración**
> ```java
> static int a = b + 1; // ❌ b aún 0 (default)
> static int b = 10;
> ```
> ✅ **Correcto**: Declarar `b` antes de `a`, o usar método `static int initA() { return b+1; }`.

## Buenas Prácticas

1. **Minimiza static mutable** — Estado global = acoplamiento, tests difíciles, bugs concurrencia.
2. **Lazy initialization holder** para singletons costosos:
   ```java
   class Singleton { private Singleton() {} static class Holder { static final Singleton INST = new Singleton(); } static Singleton get() { return Holder.INST; } }
   ```
3. **Evita objetos grandes en Stack** — Solo primitivos y referencias. Arrays/objetos → Heap.
4. **`-Xms -Xmx` sizing** — Heap fijo evita pausas de resize. `-Xms2g -Xmx2g`.
5. **Perfila antes de tunear GC** — `jcmd <pid> GC.heap_info`, `jvisualvm`, `async-profiler`.
6. **`finally` / `try-with-resources`** — Libera recursos (sockets, files) aunque haya excepción. Stack se limpia automático.
7. **Shutdown hooks** para cleanup ordenado: `Runtime.getRuntime().addShutdownHook(new Thread(() -> ...))`.

## Conexión con Otros Temas

- `[[07 - Constructores y this]]` — Orden: static → instance blocks → campos → constructor.
- `[[08 - Instanciacion y new]]` — `new` dispara asignación Heap + inicialización instancia.
- `[[09 - Multiples Objetos e Identidad]]` — Cada `new` = identidad distinta en Heap.
- `[[13 - Static vs Instancia]]` — Static en Method Area / Metaspace; Instancia en Heap.
- `[[14 - Arrays Basicos y args]]` — Arrays en Heap, `args` creado por JVM antes de `main`.
- `[[17 - Separacion de Responsabilidades]]` — Clases stateless (static utils) vs stateful (instancia).

## Resumen en Una Frase

> **JVM: ClassLoad → Static Init (1 vez) → Main Thread (Stack) → new (Heap + Instance Init) → GC limpia Heap inalcanzable; Stack = frames LIFO por hilo, Heap = compartido.**

---

## Tags
`#java #fundamentos #jvm #flujo-ejecucion #classloading #stack #heap #garbage-collector #memoria #inicializacion`