---
tags: [java, fundamentos, objetos, identidad, igualdad, aliasing, referencia, heap]
---

# 09 - Múltiples Objetos e Identidad

## Concepto Central

Cada ejecución de `new Clase()` produce un **objeto distinto** con su **propia identidad** (dirección en memoria Heap). Dos variables pueden referenciar el **mismo objeto** (aliasing) u **objetos distintos** con igual contenido. Java distingue:

- **Identidad** (`==`): ¿Son la **misma** entidad en memoria? (misma dirección)
- **Igualdad** (`.equals()`): ¿Tienen **mismo contenido/significado**? (lógica de negocio)

Entender esto es crítico para evitar bugs sutiles de estado compartido inesperado.

## Para Qué Sirve / Cuándo Usarlo

- Modelar entidades del mundo real (cada `Usuario` es único aunque se llamen igual)
- Detectar aliasing (dos variables tocando mismo objeto)
- Implementar `equals()`/`hashCode()` correctos para colecciones (`Set`, `Map`)
- Evitar mutaciones accidentales vía referencias compartidas
- Diseñar APIs que aclaren si devuelven objeto nuevo o compartido

## Sintaxis General

```java
// Creación: cada new = nueva identidad
Objeto a = new Objeto();
Objeto b = new Objeto();        // a != b (identidad distinta)
Objeto c = a;                   // c == a (misma identidad, alias)

// Comparación identidad
if (a == b) { }                 // Compara direcciones (bits de referencia)

// Comparación igualdad (requiere override en la clase)
if (a.equals(b)) { }            // Compara contenido (si bien implementado)

// Copia defensiva (crear nuevo objeto con mismo contenido)
Objeto copia = new Objeto(a);   // Constructor copia
// o
Objeto copia = a.clone();       // Si implementa Cloneable
// o
Objeto copia = a.toBuilder().build(); // Patrón Builder
```

## Ejemplo Propio: TarjetaCredito

```java
public class TarjetaCredito {
    private final String numero;       // Inmutable (final)
    private final String titular;      // Inmutable
    private double limiteCredito;      // Mutable
    private double saldoDisponible;    // Mutable
    private boolean activa;            // Mutable

    // Constructor canónico
    public TarjetaCredito(String numero, String titular, double limiteCredito) {
        if (numero == null || !numero.matches("\\d{16}")) {
            throw new IllegalArgumentException("Número 16 dígitos");
        }
        if (titular == null || titular.isBlank()) {
            throw new IllegalArgumentException("Titular obligatorio");
        }
        if (limiteCredito <= 0) {
            throw new IllegalArgumentException("Límite > 0");
        }
        this.numero = numero;
        this.titular = titular;
        this.limiteCredito = limiteCredito;
        this.saldoDisponible = limiteCredito;
        this.activa = true;
    }

    // Constructor copia (copia defensiva de campos mutables)
    public TarjetaCredito(TarjetaCredito otra) {
        this.numero = otra.numero;
        this.titular = otra.titular;
        this.limiteCredito = otra.limiteCredito;
        this.saldoDisponible = otra.saldoDisponible;
        this.activa = otra.activa;
    }

    // Getters
    public String getNumero() { return numero; }
    public String getTitular() { return titular; }
    public double getLimiteCredito() { return limiteCredito; }
    public double getSaldoDisponible() { return saldoDisponible; }
    public boolean isActiva() { return activa; }

    // Operaciones mutan ESTE objeto
    public boolean cargar(double importe) {
        if (!activa) return false;
        if (importe <= 0) throw new IllegalArgumentException("Importe > 0");
        if (importe > saldoDisponible) return false;
        saldoDisponible -= importe;
        return true;
    }

    public void pagar(double importe) {
        if (importe <= 0) throw new IllegalArgumentException("Importe > 0");
        saldoDisponible = Math.min(limiteCredito, saldoDisponible + importe);
    }

    public void bloquear() { this.activa = false; }
    public void activar() { this.activa = true; }

    // SOBRESCRITURA IGUALDAD: dos tarjetas son "iguales" si mismo número
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;                    // Identidad (rápido)
        if (obj == null || getClass() != obj.getClass()) return false;
        TarjetaCredito that = (TarjetaCredito) obj;
        return this.numero.equals(that.numero);          // Igualdad por número
    }

    @Override
    public int hashCode() { return numero.hashCode(); }  // Consistente con equals

    @Override
    public String toString() {
        return "Tarjeta{" + numero.substring(12) + " | " + titular + " | Disp: " + saldoDisponible + "}";
    }

    // MAIN: Demostración identidad vs igualdad vs aliasing
    public static void main(String[] args) {
        System.out.println("=== IDENTIDAD vs IGUALDAD ===\n");

        // 1. Dos objetos DISTINTOS con MISMO contenido (número)
        TarjetaCredito t1 = new TarjetaCredito("1234567812345678", "Ana", 1000);
        TarjetaCredito t2 = new TarjetaCredito("1234567812345678", "Ana", 1000);

        System.out.println("t1: " + t1);
        System.out.println("t2: " + t2);
        System.out.println("t1 == t2 (identidad): " + (t1 == t2));       // FALSE
        System.out.println("t1.equals(t2) (igualdad): " + t1.equals(t2)); // TRUE

        // 2. ALIASING: dos referencias, MISMO objeto
        TarjetaCredito t3 = t1;  // t3 apunta a MISMO objeto que t1
        System.out.println("\n--- Aliasing: t3 = t1 ---");
        System.out.println("t1 == t3: " + (t1 == t3)); // TRUE
        System.out.println("Antes: t1.saldo=" + t1.getSaldoDisponible() + ", t3.saldo=" + t3.getSaldoDisponible());
        t3.cargar(200); // Mutación vía t3
        System.out.println("Tras t3.cargar(200):");
        System.out.println("t1.saldo=" + t1.getSaldoDisponible() + ", t3.saldo=" + t3.getSaldoDisponible()); // AMBOS 800

        // 3. COPIA DEFENSIVA: nuevo objeto independiente
        TarjetaCredito t4 = new TarjetaCredito(t1); // Constructor copia
        System.out.println("\n--- Copia defensiva: t4 = new TarjetaCredito(t1) ---");
        System.out.println("t1 == t4: " + (t1 == t4));             // FALSE (objeto nuevo)
        System.out.println("t1.equals(t4): " + t1.equals(t4));     // TRUE (mismo número)
        t4.cargar(300); // Mutación vía t4
        System.out.println("Tras t4.cargar(300):");
        System.out.println("t1.saldo=" + t1.getSaldoDisponible() + " (inalterado)");
        System.out.println("t4.saldo=" + t4.getSaldoDisponible()); // 500

        // 4. USO EN COLECCIONES (requiere equals/hashCode correctos)
        System.out.println("\n--- En HashSet (usa equals/hashCode) ---");
        java.util.Set<TarjetaCredito> cartera = new java.util.HashSet<>();
        cartera.add(t1);
        cartera.add(t2); // Duplicado lógico (mismo número) → NO se añade
        cartera.add(t4); // Duplicado lógico → NO se añade
        System.out.println("Tamaño cartera: " + cartera.size()); // 1

        // 5. MUTACIÓN EN COLECCIÓN (peligro si campo usado en equals cambia)
        System.out.println("\n--- Peligro: mutar campo usado en equals ---");
        // TarjetaCredito usa 'numero' (final) en equals → SEGURO
        // Si usara 'titular' (mutable) en equals → rompería HashSet
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `private final String numero;` | `final` = inmutable. Ideal para `equals`/`hashCode`. |
| `public TarjetaCredito(TarjetaCredito otra)` | **Constructor copia**. Crea **nueva identidad** con mismo contenido. |
| `t2 = new TarjetaCredito(...)` | Segundo `new` → **segunda identidad** distinta en Heap. |
| `t1 == t2` | Compara **bits de referencia** (dirección). `false` → objetos distintos. |
| `t1.equals(t2)` | Llama `equals` sobrescrito. Compara `numero`. `true` → mismo significado. |
| `TarjetaCredito t3 = t1;` | **Asignación de referencia**. `t3` y `t1` guardan **misma dirección**. |
| `t3.cargar(200);` | Mutación via `t3`. Como `t3 == t1`, `t1` ve el cambio. **Aliasing**. |
| `t4 = new TarjetaCredito(t1)` | **Copia defensiva**. Nuevo objeto (`t1 != t4`), mismo número (`equals=true`). |
| `t4.cargar(300);` | Mutación via `t4`. `t1` **no** afectado (objetos independientes). |
| `Set.add(t2)` | `HashSet` usa `hashCode()` → bucket → `equals()` → detecta duplicado lógico → rechaza. |
| `numero` final en `equals` | **Seguro**: campo usado en `equals` no muta. Si mutara campo usado en `equals` → rompe `HashSet`/`HashMap`. |

## Errores Comunes

> [!warning] **Error 1: Usar `==` para comparar contenido**
> ```java
> String a = new String("Hola");
> String b = new String("Hola");
> if (a == b) { } // ❌ false (distintos objetos)
> ```
> ✅ **Correcto**: `a.equals(b)` (o `Objects.equals(a,b)` para null-safe).

> [!warning] **Error 2: No sobrescribir `equals` + `hashCode` juntos**
> ```java
> class Usuario { String email; }
> Set<Usuario> set = new HashSet<>();
> set.add(new Usuario("a@b.com"));
> set.add(new Usuario("a@b.com")); // ❌ Dos entradas! equals usa Object.equals (identidad)
> ```
> ✅ **Correcto**: Sobrescribe **ambos** consistentemente.

> [!warning] **Error 3: `equals` usa campo mutable**
> ```java
> class Cuenta { String iban; double saldo; // mutable
>   boolean equals(Object o) { return this.saldo == ((Cuenta)o).saldo; } // ❌
> }
> Set<Cuenta> s = new HashSet<>();
> s.add(c); c.saldo = 100; // ¡Rompe HashSet! hashCode cambió, bucket equivocado.
> ```
> ✅ **Correcto**: `equals`/`hashCode` solo sobre campos **inmutables** (idealmente `final`).

> [!warning] **Error 4: Aliasing accidental en getters**
> ```java
> class Agenda { private List<String> contactos = new ArrayList<>();
>   public List<String> getContactos() { return contactos; } // ❌ Fuga referencia interna
> }
> Agenda a = new Agenda(); a.getContactos().clear(); // Vacía agenda por sorpresa
> ```
> ✅ **Correcto**: `return Collections.unmodifiableList(contactos);` o `new ArrayList<>(contactos)`.

> [!warning] **Error 5: `==` con wrappers/autoboxing (cache)**
> ```java
> Integer a = 127, b = 127; a == b → true (cache -128 a 127)
> Integer c = 200, d = 200; c == d → false (nuevos objetos)
> ```
> ✅ **Correcto**: Siempre `.equals()` para wrappers, `==` solo para primitivos.

> [!warning] **Error 6: Asignar `null` y luego dereferenciar**
> ```java
> TarjetaCredito t = null; t.getNumero(); // ❌ NPE
> ```
> ✅ **Correcto**: Validar `if (t != null)` u `Optional<TarjetaCredito>`.

## Buenas Prácticas

1. **`equals`/`hashCode` solo en clases de valor/entidad** — DTOs, entidades JPA, Value Objects. No en servicios, controllers, etc.
2. **Campos en `equals` = `final` inmutables** — `id`, `numero`, `email`, `isbn`.
3. **Constructor copia para defensiva** — `new TarjetaCredito(otra)` devuelve objeto independiente.
4. **Getters devuelven copias/inmutables** — Evita aliasing accidental.
5. **Documenta si método devuelve misma instancia o nueva** — `@return nueva instancia` vs `@return this (fluent)`.
6. **`==` para: primitivos, `enum`, `null`, same-instance check (`this == obj` en `equals`)**.
7. **`Objects.equals(a,b)` / `Objects.hash(...)`** — Null-safe, menos boilerplate.
8. **`record` (Java 14+) para datos puros** — Genera `equals`/`hashCode`/`toString`/`constructor`/`getters` automáticos sobre campos `final`.

## Conexión con Otros Temas

- `[[08 - Instanciacion y new]]` — Cada `new` = nueva identidad.
- `[[06 - Atributos y Campos]]` — `final` en campos usados en `equals`.
- `[[07 - Constructores y this]]` — Constructor copia usa `this(campo1, campo2...)`.
- `[[10 - Metodos de Instancia]]` — Mutadores (`cargar`, `pagar`) cambian estado de `this`.
- `[[13 - Static vs Instancia]]` — Identidad es por instancia, no clase.
- `[[14 - Arrays Basicos y args]]` — Arrays usan `==` para identidad, `Arrays.equals` para contenido.
- `[[17 - Separacion de Responsabilidades]]` — Entidades con identidad clara vs Value Objects.

## Resumen en Una Frase

> **Cada `new` crea una identidad única; `==` compara identidades, `.equals()` compara significado; aliasing comparte identidad, copia defensiva crea nueva identidad con mismo contenido.**

---

## Tags
`#java #fundamentos #identidad #igualdad #aliasing #equals #hashcode #copia-defensiva #referencia #objetos`