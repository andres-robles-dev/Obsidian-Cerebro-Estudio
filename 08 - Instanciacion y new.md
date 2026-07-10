---
tags: [java, fundamentos, instanciacion, new, heap, referencia, memoria]
---

# 08 - Instanciación y new

## Concepto Central

La **instanciación** es el proceso de crear un **objeto concreto** (instancia) a partir de una **clase** (plantilla). En Java, la palabra clave **`new`** es el operador que solicita memoria en el **Heap**, invoca al **constructor** y devuelve una **referencia** (dirección) al objeto recién creado. La variable que recibe esa referencia apunta al objeto, no lo contiene.

## Para Qué Sirve / Cuándo Usarlo

- Materializar la plantilla (`class`) en datos reales (`object`)
- Tener **múltiples copias independientes** con mismo comportamiento
- Inicializar estado vía constructor
- Pasar objetos a métodos, guardarlos en colecciones, devolverlos

## Sintaxis General

```java
// Expresión new: asigna memoria + llama constructor + devuelve referencia
TipoReferencia variable = new NombreClase(argumentosConstructor);

// DesConstructor);

// Pasos internos de new Clase(args):
// 1. Calcular tamaño (campos instancia + overhead)
// 2. Asignar memoria en Heap (inicializa a 0/false/null)
// 3. Ejecutar inicializadores de campo (int x = 5;)
// 4. Ejecutar bloques de inicialización de instancia { ... }
// 5. Ejecutar constructor (this.campo = arg; ...)
// 6. Devolver referencia (puntero) al objeto
```

### Diagrama Memoria (Stack vs Heap)

```
STACK (Hilo principal)                    HEAP (Memoria compartida)
┌─────────────────────┐                   ┌─────────────────────────────┐
│ main()              │                   │ Objeto Rectangulo @0x7F...  │
│  r ─────────────────┼──────────────────►│ ancho: 5.0                  │
│                     │    referencia     │ alto: 3.0                   │
│  r2 ────────────────┼──────┐            │ color: "Rojo"               │
│                     │      │             │ relleno: true               │
│                     │      │             └─────────────────────────────┘
│                     │      │             ┌─────────────────────────────┐
│                     │      └────────────►│ Objeto Rectangulo @0xA1...  │
│                     │                   │ ancho: 4.0                  │
│                     │                   │ alto: 4.0                   │
│                     │                   │ color: "Negro"              │
│                     │                   │ relleno: false              │
└─────────────────────┘                   └─────────────────────────────┘
```

- **Stack**: Variables locales (`r`, `r2`, parámetros, `this` dentro de métodos). Vida = scope del método.
- **Heap**: Objetos (`new`), arrays, campos `static`. Vida = mientras sean **alcanzables** (GC los limpia cuando no).

## Ejemplo Propio: Gato

```java
public class Gato {
    // Campos de instancia (estado único por gato)
    private String nombre;
    private int edadMeses;
    private String raza;
    private boolean esterilizado;
    private double pesoKg;

    // Constructor
    public Gato(String nombre, int edadMeses, String raza, double pesoKg) {
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("Nombre obligatorio");
        if (edadMeses < 0) throw new IllegalArgumentException("Edad >= 0");
        if (pesoKg <= 0) throw new IllegalArgumentException("Peso > 0");

        this.nombre = nombre;
        this.edadMeses = edadMeses;
        this.raza = (raza != null) ? raza : "Común";
        this.pesoKg = pesoKg;
        this.esterilizado = false;
    }

    // Getters
    public String getNombre() { return nombre; }
    public int getEdadMeses() { return edadMeses; }
    public String getRaza() { return raza; }
    public double getPesoKg() { return pesoKg; }
    public boolean isEsterilizado() { return esterilizado; }

    // Setters con lógica
    public void cumplirMeses(int meses) {
        if (meses < 0) throw new IllegalArgumentException("Meses >= 0");
        this.edadMeses += meses;
        // Crecimiento simplificado
        if (this.edadMeses < 12) this.pesoKg += meses * 0.5;
    }

    public void esterilizar() { this.esterilizado = true; }

    public void mostrarFicha() {
        System.out.println("=== Ficha Gatuna ===");
        System.out.println("Nombre: " + nombre);
        System.out.println("Edad: " + edadMeses + " meses");
        System.out.println("Raza: " + raza);
        System.out.println("Peso: " + pesoKg + " kg");
        System.out.println("Esterilizado: " + (esterilizado ? "Sí" : "No"));
    }

    // MAIN: Demostración instanciación múltiple
    public static void main(String[] args) {
        System.out.println("--- Creando gatos con new ---\n");

        // 1. Instancia 1
        Gato g1 = new Gato("Michi", 3, "Siames", 1.2);
        // g1 es REFERENCIA en stack → objeto en heap

        // 2. Instancia 2 (distinta)
        Gato g2 = new Gato("Luna", 8, "Maine Coon", 4.5);

        // 3. Referencia a MISMO objeto (no new)
        Gato g3 = g1; // Copia REFERENCIA, no objeto

        // 4. new en expresión (anonimo, se usa y pierde si no se guarda)
        new Gato("Fantasma", 1, "Sphynx", 0.8).mostrarFicha(); // Válido pero referencia perdida

        System.out.println("\n--- Estado inicial ---");
        g1.mostrarFicha();
        System.out.println();
        g2.mostrarFicha();

        System.out.println("\n--- g3 apunta a g1 ---");
        System.out.println("g1 == g3 ? " + (g1 == g3));        // true (misma referencia)
        System.out.println("g1 == g2 ? " + (g1 == g2));        // false (objetos distintos)

        System.out.println("\n--- Modificando vía g3 (afecta a g1) ---");
        g3.cumplirMeses(6);
        g3.esterilizar();
        System.out.println("Edad g1: " + g1.getEdadMeses()); // 9 (era 3 + 6)
        System.out.println("Esterilizado g1: " + g1.isEsterilizado()); // true

        System.out.println("\n--- g2 independiente ---");
        System.out.println("Edad g2: " + g2.getEdadMeses()); // 8 (inalterado)
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `Gato g1 = new Gato(...);` | `new` reserva memoria en Heap, ejecuta constructor, devuelve ref. `g1` (en Stack) guarda esa ref. |
| `Gato g2 = new Gato(...);` | **Segundo** `new` → **segundo** objeto en Heap. `g2` apunta a sitio distinto. |
| `Gato g3 = g1;` | **Asignación de referencia**. `g3` copia el valor de `g1` (la dirección). **Mismo objeto**. |
| `new Gato(...).mostrarFicha();` | `new` crea objeto, llama método, **referencia se pierde** (no guardada). Objeto elegible para GC tras línea. |
| `g1 == g3` | Compara **referencias** (direcciones). `true` porque ambas apuntan al mismo objeto. |
| `g1 == g2` | `false` — `new` llamado dos veces = dos objetos distintos en Heap. |
| `g3.cumplirMeses(6);` | Mutación vía `g3`. Como `g3` y `g1` son mismo objeto, `g1` ve el cambio. |
| `g2` inalterado | `g2` apunta a **otro** objeto en Heap. Cambios en `g1`/`g3` no afectan a `g2`. |

## Errores Comunes

> [!warning] **Error 1: Olvidar `new`**
> ```java
> Gato g = Gato("Michi", 3, "Siames", 1.2); // ❌ Error: método no encontrado
> ```
> ✅ **Correcto**: `Gato g = new Gato("Michi", 3, "Siames", 1.2);`

> [!warning] **Error 2: Confundir `==` con `.equals()`**
> ```java
> Gato a = new Gato("Michi", 3, "Siames", 1.2);
> Gato b = new Gato("Michi", 3, "Siames", 1.2);
> if (a == b) { } // ❌ false: son dos objetos distintos
> if (a.equals(b)) { } // ✅ Si Gato sobrescribe equals() comparando campos
> ```

> [!warning] **Error 3: Usar referencia antes de `new` (NPE)**
> ```java
> Gato g;
> g.mostrarFicha(); // ❌ NullPointerException: g es null
> ```
> ✅ **Correcto**: `Gato g = new Gato(...);` antes de usar.

> [!warning] **Error 4: Pasar objeto a método esperando copia**
> ```java
> void cambiarNombre(Gato g, String n) { g = new Gato(n, 0, "", 1); } // ❌ Cambia ref LOCAL
> Gato miGato = new Gato("Michi", 3, "Siames", 1.2);
> cambiarNombre(miGato, "Luna"); // miGato sigue siendo "Michi"
> ```
> ✅ **Correcto**: Mutar el objeto recibido: `g.setNombre(n);` (si existe setter) o `g.nombre = n;` si campo accesible. Java pasa **referencia por valor**.

> [!warning] **Error 5: `new` en bucle innecesario (presión GC)**
> ```java
> for (int i=0; i<1_000_000; i++) {
>     String s = new String("x"); // ❌ Crea 1M objetos basura
> }
> ```
> ✅ **Correcto**: Reutiliza o usa literales/pool. `String s = "x";`

## Buenas Prácticas

1. **`new` solo donde necesites nueva identidad** — No crees objetos si vas a reutilizar uno existente.
2. **Inmutables preferibles** — `String`, `LocalDate`, `BigDecimal` evitan aliases problemáticos. Ver `[[09 - Multiples Objetos e Identidad]]`.
3. **Constructor válido = objeto válido** — No dejes objeto a medio construir. Ver `[[07 - Constructores y this]]`.
4. **Evita `new` en bucles calientes** — Reusa, usa `StringBuilder`, pools, flyweight.
5. **Referencia nula explícita si necesario** — `g = null;` para ayudar GC si objeto grande y ya no se usa (raro).
6. **`try-with-resources` para recursos** — `new FileInputStream(...)` → `try (var in = new FileInputStream(...)) { }` cierra auto.
7. **Factory methods en lugar de `new` público** — `Gato.createCachorro("Michi")` encapsula lógica creación.

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Clase = molde, `new` = crea objeto.
- `[[07 - Constructores y this]]` — `new` invoca constructor.
- `[[09 - Multiples Objetos e Identidad]]` — Cada `new` = identidad distinta; asignación = alias.
- `[[10 - Metodos de Instancia]]` — Métodos operan sobre `this` (objeto al que apunta ref).
- `[[13 - Static vs Instancia]]` — `new` crea campos instancia; `static` no se toca.
- `[[15 - Flujo de Ejecucion JVM]]` — Heap, Stack, GC, ciclo de vida objeto.

## Resumen en Una Frase

> **`new Clase()` reserva memoria en Heap, ejecuta el constructor y devuelve una referencia (puntero) al objeto; la variable guarda esa referencia, no el objeto.**

---

## Tags
`#java #fundamentos #instanciacion #new #heap #stack #referencia #memoria #garbage-collector`