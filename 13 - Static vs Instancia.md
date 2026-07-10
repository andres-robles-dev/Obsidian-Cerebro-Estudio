---
tags: [java, fundamentos, static, instancia, clase, objeto, campos, metodos, memoria]
---

# 13 - Static vs Instancia

## Concepto Central

**`static`** significa **"pertenece a la CLASE"**, no a una instancia. Un miembro `static` (campo o método) existe **una sola vez** en memoria (área *Method Area* / *Metaspace*), compartido por **todas** las instancias. Un miembro **de instancia** (sin `static`) pertenece a **cada objeto** creado con `new`; cada uno tiene su **copia propia** en el Heap. `this` **no existe** en contexto `static`.

## Para Qué Sirve / Cuándo Usarlo

| `static` (Clase) | Instancia (Objeto) |
|------------------|-------------------|
| Constantes universales (`Math.PI`, `Color.RED`) | Estado único por entidad (`cuenta.saldo`) |
| Utilidades sin estado (`Math.max`, `String.valueOf`) | Comportamiento con `this` (`rectangulo.area()`) |
| Contadores globales (`Contador.instancias`) | Identidad propia (`usuario.id`) |
| Factorías / Singletons (`Logger.getLogger()`) | Mutación de estado propio |
| Configuración global (`Config.debug`) | Datos de negocio |

## Sintaxis General

```java
class MiClase {
    // CAMPO STATIC (variable de clase)
    static tipo nombreCampo = valorInicial;
    static final tipo CONSTANTE = valor; // Constante de clase

    // CAMPO INSTANCIA (variable de objeto)
    tipo nombreCampo;

    // MÉTODO STATIC (método de clase)
    static tipoRetorno nombreMetodo(params) {
        // Solo puede acceder a: static fields, static methods, parámetros
        // NO this, NO campos instancia, NO métodos instancia
    }

    // MÉTODO INSTANCIA
    tipoRetorno nombreMetodo(params) {
        // Accede a: this.campoInstancia, this.metodoInstancia(), static fields/methods
    }

    // BLOQUE STATIC (inicialización clase, 1 vez al cargar)
    static { /* código */ }

    // BLOQUE INSTANCIA (ejecuta en cada new, antes de constructor)
    { /* código */ }
}
```

### Acceso

```java
// Static: via CLASE (recomendado) o instancia (desaconsejado, warning)
MiClase.campoStatic;
MiClase.metodoStatic();
obj.campoStatic;      // Funciona pero confunde
obj.metodoStatic();   // Funciona pero confunde

// Instancia: SOLO via referencia (objeto)
obj.campoInstancia;
obj.metodoInstancia();
// MiClase.campoInstancia; // ❌ Error: non-static field cannot be referenced from static context
```

## Ejemplo Propio: ContadorInstancias + UtilidadesMatematicas

```java
public class DemoStaticVsInstancia {
    // --- CAMPOS STATIC (compartidos) ---
    private static int totalObjetosCreados = 0;      // Contador global
    private static final String NOMBRE_APP = "DemoApp"; // Constante (convención UPPER_SNAKE)
    private static boolean modoDebug = false;        // Config global mutable

    // --- CAMPOS INSTANCIA (propios) ---
    private final int id;                    // Identidad única (final)
    private String nombre;                   // Estado mutable
    private double valor;                    // Estado mutable

    // --- BLOQUE STATIC: inicialización compleja de static (1 vez) ---
    static {
        System.out.println("[STATIC] Clase cargada. Inicializando...");
        // Podría leer config archivo, conectar pool, etc.
    }

    // --- BLOQUE INSTANCIA: código común a TODOS los constructores ---
    {
        totalObjetosCreados++;               // Incrementa static
        this.id = totalObjetosCreados;       // Asigna id único
        System.out.println("[INSTANCE] Bloque instancia para id=" + id);
    }

    // --- CONSTRUCTORES ---
    public DemoStaticVsInstancia() {
        this("Objeto-" + id, 0.0);
    }

    public DemoStaticVsInstancia(String nombre, double valor) {
        this.nombre = nombre;
        this.valor = valor;
        System.out.println("[CTOR] " + this); // this.toString()
    }

    // --- MÉTODO STATIC: utilidad, factoría, acceso static ---
    public static int getTotalObjetosCreados() { return totalObjetosCreados; }

    public static void setModoDebug(boolean debug) { modoDebug = debug; }

    public static DemoStaticVsInstancia crearPorDefecto() {
        return new DemoStaticVsInstancia("Default", 1.0);
    }

    // Método static que USA otro static
    public static String infoGlobal() {
        return NOMBRE_APP + " | Objetos: " + totalObjetosCreados + " | Debug: " + modoDebug;
    }

    // ❌ ERROR: static no puede acceder a instancia
    // public static void imprimirNombre() { System.out.println(this.nombre); } // COMPILE ERROR

    // --- MÉTODOS INSTANCIA: usan this, estado propio ---
    public void setNombre(String nombre) { this.nombre = nombre; }
    public void setValor(double valor) { this.valor = valor; }
    public void incrementarValor(double delta) { this.valor += delta; }

    // Instancia PUEDE leer static
    public void imprimirConContexto() {
        System.out.println("[" + id + "] " + nombre + " = " + valor
            + " | Global: " + DemoStaticVsInstancia.infoGlobal());
    }

    @Override
    public String toString() {
        return "DemoStaticVsInstancia{id=" + id + ", nombre='" + nombre + "', valor=" + valor + "}";
    }

    // --- MAIN ---
    public static void main(String[] args) {
        System.out.println("=== Inicio ===");
        System.out.println(infoGlobal()); // Llamada static sin objeto

        DemoStaticVsInstancia.setModoDebug(true); // Cambia static

        DemoStaticVsInstancia o1 = new DemoStaticVsInstancia("Primero", 10);
        DemoStaticVsInstancia o2 = new DemoStaticVsInstancia("Segundo", 20);
        DemoStaticVsInstancia o3 = DemoStaticVsInstancia.crearPorDefecto(); // Factory static

        System.out.println("\n--- Estado tras crear 3 ---");
        o1.imprimirConContexto();
        o2.imprimirConContexto();
        o3.imprimirConContexto();

        System.out.println("\n--- Modificando instancia o1 ---");
        o1.incrementarValor(5);
        o1.setNombre("Primero-Modificado");
        o1.imprimirConContexto();
        o2.imprimirConContexto(); // o2 inalterado (instancia distinta)

        System.out.println("\n--- Acceso static via clase (recomendado) ---");
        System.out.println("Total: " + DemoStaticVsInstancia.getTotalObjetosCreados());

        System.out.println("\n--- Acceso static via instancia (desaconsejado) ---");
        System.out.println("o1.getTotalObjetosCreados() = " + o1.getTotalObjetosCreados()); // Warning IDE

        System.out.println("\n=== Fin ===");
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `private static int totalObjetosCreados = 0;` | Un solo `int` en Method Area. Todas las instancias ven/modifican **el mismo**. |
| `private static final String NOMBRE_APP` | Constante de clase. Inmutable, compartida. Convención `UPPER_SNAKE_CASE`. |
| `static { ... }` | **Bloque estático**. Ejecuta **una vez** al cargar clase (antes de cualquier `new` o `static` method). |
| `{ totalObjetosCreados++; this.id = ... }` | **Bloque de instancia**. Ejecuta **cada `new`**, **antes** del constructor. Útil para lógica común a todos ctors. |
| `public static int getTotalObjetosCreados()` | Método de clase. Accede a `static` fields. **No** usa `this`. |
| `public static DemoStaticVsInstancia crearPorDefecto()` | **Factory method** static. Crea y devuelve instancia. Patrón común. |
| `// public static void imprimirNombre() { this.nombre; }` | **Error compile-time**: `static` no tiene `this`. No ve campos instancia. |
| `public void imprimirConContexto()` | Método instancia. Usa `this.id`, `this.nombre` **y** `DemoStaticVsInstancia.infoGlobal()` (static). |
| `DemoStaticVsInstancia.setModoDebug(true);` | Llamada static **via clase**. Correcto. |
| `o1.getTotalObjetosCreados()` | Llamada static **via instancia**. Funciona pero **warning**: oculta que es miembro de clase. |

## Errores Comunes

> [!warning] **Error 1: Referenciar instancia desde static**
> ```java
> class A { int x; static void f() { System.out.println(x); } } // ❌ non-static field x cannot be referenced from static context
> ```
> ✅ **Correcto**: Hacer `f` instancia, o pasar `A a` como parámetro: `static void f(A a) { a.x; }`.

> [!warning] **Error 2: Llamar método instancia desde static sin objeto**
> ```java
> class A { void m() {} static void f() { m(); } } // ❌
> ```
> ✅ **Correcto**: `new A().m();` o `A a = ...; a.m();`.

> [!warning] **Error 3: `this` en método static**
> ```java
> static void f() { this.x = 1; } // ❌
> ```
> ✅ **Correcto**: `this` solo en instancia.

> [!warning] **Error 4: Shadowing static field con variable local/parámetro**
> ```java
> static int cuenta = 0;
> static void inc(int cuenta) { cuenta++; } // ❌ Incrementa parámetro, no static field
> ```
> ✅ **Correcto**: `Demo.cuenta++;` o `this.cuenta++` (si fuera instancia) — en static: `Clase.cuenta++`.

> [!warning] **Error 5: Mutar static mutable desde múltiples hilos (race condition)**
> ```java
> static int contador = 0;
> void inc() { contador++; } // ❌ No atómico en multihilo
> ```
> ✅ **Correcto**: `AtomicInteger`, `synchronized`, `LongAdder`.

> [!warning] **Error 6: Static field mutable en clase serializable (fuga)**
> ```java
> class A implements Serializable { static List<String> cache = new ArrayList(); } // ❌ Static no se serializa, pero estado global persiste en JVM
> ```

## Buenas Prácticas

1. **`static final` para constantes** — `public static final int MAX = 100;` (inlining compile-time si primitivo/String).
2. **Accede static via `Clase.miembro`** — Nunca `obj.miembroStatic`. Legibilidad.
3. **Evita `static` mutable** — Estado global = acoplamiento oculto, tests frágiles, problemas concurrencia. Usa inyección de dependencias / singletons controlados.
4. **Métodos utilitarios puros → `static`** — `Math`, `Collections`, `Objects`, `StringUtils`. Sin efectos laterales, solo params → return.
5. **Factory methods `static`** — `List.of()`, `LocalDate.now()`, `Optional.of()`.
6. **Bloque `static` para init costosa** — Cargar driver JDBC, leer config, init pool.
7. **Bloque instancia `{}` para código común constructores** — DRY en validación/logging.
8. **`main` es `static`** — Punto entrada sin objeto. Crea primer objeto y arranca.

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Miembros de clase.
- `[[06 - Atributos y Campos]]` — Static vs instance fields, valores por defecto.
- `[[07 - Constructores y this]]` — Bloque instancia vs constructor, `this` no existe en static.
- `[[08 - Instanciacion y new]]` — `new` crea campos instancia; static ya existe.
- `[[09 - Multiples Objetos e Identidad]]` — Static compartido = misma identidad para todas instancias.
- `[[10 - Metodos de Instancia]]` — Instancia puede llamar static; static no puede llamar instancia sin ref.
- `[[15 - Flujo de Ejecucion JVM]]` — Class loading → static init → main → new → instance init → ctor.

## Resumen en Una Frase

> **`static` = uno solo para toda la clase (memoria clase), sin `this`; instancia = uno por objeto (Heap), con `this`; static ve static, instancia ve todo.**

---

## Tags
`#java #fundamentos #static #instancia #clase #objeto #memoria #metodo-estatico #campo-estatico #factory`