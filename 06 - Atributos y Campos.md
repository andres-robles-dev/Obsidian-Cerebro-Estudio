---
tags: [java, fundamentos, atributos, campos, variables-instancia, estado, valores-defecto]
---

# 06 - Atributos y Campos

## Concepto Central

Un **atributo** (también llamado **campo** o *field* o *variable de instancia*) es una variable declarada **dentro de una clase pero fuera de cualquier método**. Define el **estado** que cada objeto (instancia) de esa clase tendrá de forma independiente. Cada `new Clase()` crea una **copia propia** de todos los campos no `static`.

## Para Qué Sirve / Cuándo Usarlo

- Modelar datos propios de cada entidad: `Persona.nombre`, `Cuenta.saldo`, `Coche.kilometraje`
- Mantener estado entre llamadas a métodos del mismo objeto
- Representar invariantes que la clase debe proteger (con `private` + validación)
- Diferenciar instancias: `libro1.titulo != libro2.titulo`

## Sintaxis General

```java
class NombreClase {
    // Campo de instancia (sin static)
    [modificadores] tipo nombreCampo;
    [modificadores] tipo nombreCampo = valorInicial;

    // Campo de clase (static) - ver [[13 - Static vs Instancia]]
    static tipo nombreCampoClase;
}
```

### Modificadores Comunes en Campos

| Modificador | Efecto |
|-------------|--------|
| `private` | Solo visible en la propia clase (estándar) |
| `public` | Visible en todas partes (evitar en campos mutables) |
| `protected` | Visible en paquete + subclases |
| `default` (ninguno) | Visible en paquete |
| `final` | Inmutable tras inicialización (constante de instancia) |
| `transient` | No serializado (ver serialización) |
| `volatile` | Visibilidad entre hilos (concurrencia) |

## Valores por Defecto (Inicialización Automática)

Java **garantiza** que todo campo se inicializa si no lo haces tú:

| Tipo | Valor por Defecto |
|------|-------------------|
| `byte`, `short`, `int`, `long` | `0` / `0L` |
| `float`, `double` | `0.0f` / `0.0` |
| `boolean` | `false` |
| `char` | `'\u0000'` (NUL) |
| **Referencia** (String, Objetos, Arrays) | `null` |

> **Nota**: Variables **locales** (en métodos) **NO** tienen valor por defecto → error si se leen sin inicializar.

## Ejemplo Propio: Empleado

```java
public class Empleado {
    // --- CAMPOS DE INSTANCIA (estado único por empleado) ---
    private String nombre;           // null por defecto
    private String nif;              // null
    private int edad;                // 0
    private double salarioBase;      // 0.0
    private boolean activo;          // false
    private char categoria;          // '\u0000'
    private String[] habilidades;    // null (array es referencia)

    // Campo final: se inicializa UNA vez (constructor o declaración)
    private final String idEmpleado; // Debe asignarse en constructor

    // Contador compartido (static) - ver [[13 - Static vs Instancia]]
    private static int contadorIds = 0;

    // --- CONSTRUCTOR: Inicialización obligatoria de final y válidos ---
    public Empleado(String nombre, String nif, int edad, double salarioBase, char categoria) {
        // Validaciones (invariantes)
        if (nombre == null || nombre.isBlank()) throw new IllegalArgumentException("Nombre vacío");
        if (nif == null || nif.length() != 9) throw new IllegalArgumentException("NIF inválido");
        if (edad < 16 || edad > 70) throw new IllegalArgumentException("Edad 16-70");
        if (salarioBase < 0) throw new IllegalArgumentException("Salario >= 0");

        this.nombre = nombre;
        this.nif = nif;
        this.edad = edad;
        this.salarioBase = salarioBase;
        this.categoria = categoria;
        this.activo = true;                    // Default explícito
        this.habilidades = new String[0];      // Array vacío (no null)
        this.idEmpleado = "EMP-" + (++contadorIds); // Inicializa final
    }

    // --- GETTERS (lectura controlada) ---
    public String getNombre() { return nombre; }
    public String getNif() { return nif; }
    public int getEdad() { return edad; }
    public double getSalarioBase() { return salarioBase; }
    public boolean isActivo() { return activo; }
    public char getCategoria() { return categoria; }
    public String getIdEmpleado() { return idEmpleado; }
    public String[] getHabilidades() { return habilidades.clone(); } // Copia defensiva

    // --- SETTERS CON VALIDACIÓN (escritura controlada) ---
    public void setSalarioBase(double salarioBase) {
        if (salarioBase < 0) throw new IllegalArgumentException("Salario >= 0");
        this.salarioBase = salarioBase;
    }

    public void setActivo(boolean activo) {
        this.activo = activo;
    }

    public void addHabilidad(String habilidad) {
        if (habilidad == null || habilidad.isBlank()) return;
        String[] nuevo = new String[habilidades.length + 1];
        System.arraycopy(habilidades, 0, nuevo, 0, habilidades.length);
        nuevo[habilidades.length] = habilidad;
        this.habilidades = nuevo;
    }

    // --- MÉTODOS DE NEGOCIO (usan campos) ---
    public double calcularSalarioAnual() {
        return salarioBase * 14; // 14 pagas
    }

    public void cumpleanos() {
        edad++;
        if (edad > 65) categoria = 'J'; // Jubilación próxima
    }

    public void mostrarFicha() {
        System.out.println("=== Ficha Empleado ===");
        System.out.println("ID: " + idEmpleado);
        System.out.println("Nombre: " + nombre);
        System.out.println("NIF: " + nif);
        System.out.println("Edad: " + edad);
        System.out.println("Categoría: " + categoria);
        System.out.println("Salario base: " + salarioBase);
        System.out.println("Activo: " + activo);
        System.out.println("Habilidades: " + String.join(", ", habilidades));
    }

    // --- MAIN DE PRUEBA ---
    public static void main(String[] args) {
        Empleado e1 = new Empleado("Ana López", "12345678Z", 30, 25000, 'A');
        Empleado e2 = new Empleado("Carlos Ruiz", "87654321X", 45, 32000, 'B');

        e1.addHabilidad("Java");
        e1.addHabilidad("SQL");
        e2.addHabilidad("Python");

        e1.mostrarFicha();
        System.out.println("Salario anual: " + e1.calcularSalarioAnual());

        System.out.println("\n---\n");
        e2.mostrarFicha();

        // Demostración valores por defecto en nuevo objeto (sin setter)
        Empleado e3 = new Empleado("Test", "11111111A", 20, 1000, 'C');
        System.out.println("\nNuevo empleado - activo por defecto: " + e3.isActivo()); // true
        System.out.println("Habilidades vacías (no null): " + e3.getHabilidades().length); // 0
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `private String nombre;` | Campo instancia. `private` encapsula. Valor defecto `null`. |
| `private final String idEmpleado;` | `final` = asignación **una sola vez**. Obligatorio en constructor. |
| `private static int contadorIds = 0;` | **Campo de clase** (static). Compartido por todas las instancias. Ver `[[13 - Static vs Instancia]]`. |
| `this.nombre = nombre;` | `this.` distingue campo `this.nombre` del parámetro `nombre`. Ver `[[07 - Constructores y this]]`. |
| `this.habilidades = new String[0];` | Inicializa array vacío (evita `null` y `NullPointerException` posterior). |
| `this.idEmpleado = "EMP-" + (++contadorIds);` | Inicializa `final`. `++contadorIds` incrementa static y usa nuevo valor. |
| `public String[] getHabilidades() { return habilidades.clone(); }` | **Copia defensiva**: devuelve clon para que cliente no modifique array interno. |
| `public void addHabilidad(String habilidad)` | Método de mutación controlada. Crea array nuevo + copia + añade (inmutable por diseño). |
| `if (edad > 65) categoria = 'J';` | Mutación de campo `char` en método de negocio. |
| `Empleado e1 = new Empleado(...);` | Cada `new` crea objeto con **sus propios campos** (e1.nombre ≠ e2.nombre). |

## Errores Comunes

> [!warning] **Error 1: Campo `public` mutable (anemia)**
> ```java
> public class Cuenta { public double saldo; } // ❌
> // Cliente: cuenta.saldo = -1000; // Rompe invariante
> ```
> ✅ **Correcto**: `private double saldo;` + `public void ingresar(double c) { if(c>0) saldo+=c; }`

> [!warning] **Error 2: Devolver referencia interna mutable (fuga)**
> ```java
> private List<String> tags = new ArrayList<>();
> public List<String> getTags() { return tags; } // ❌
> // Cliente: obj.getTags().clear(); // Vacía lista interna!
> ```
> ✅ **Correcto**: `return Collections.unmodifiableList(tags);` o `return new ArrayList<>(tags);`

> [!warning] **Error 3: `final` en referencia ≠ objeto inmutable**
> ```java
> private final List<String> lista = new ArrayList<>(); // ✅ Referencia final
> lista.add("X"); // ✅ Permitido: muta el OBJETO, no la referencia
> lista = new ArrayList<>(); // ❌ Error: reasigna referencia
> ```
> ✅ **Entiende**: `final` en campo referencia = la variable no cambia de objeto, pero el objeto **sí** puede mutar.

> [!warning] **Error 4: Olvidar inicializar `final` en constructor**
> ```java
> class A { final int x; A() { } } // ❌ variable x might not have been initialized
> ```
> ✅ **Correcto**: `A(int v) { this.x = v; }` o `final int x = 10;` en declaración.

> [!warning] **Error 5: Shadowing accidental en setter**
> ```java
> void setNombre(String nombre) { nombre = nombre; } // ❌ Parámetro se asigna a sí mismo
> ```
> ✅ **Correcto**: `this.nombre = nombre;`

> [!warning] **Error 6: Array campo sin inicializar → NPE**
> ```java
> private String[] items;
> void add(String s) { items[0] = s; } // ❌ NullPointerException
> ```
> ✅ **Correcto**: `private String[] items = new String[0];` o inicializa en constructor.

## Buenas Prácticas

1. **`private` siempre** — Salvo DTOs/Records puros de datos sin lógica.
2. **`final` siempre que puedas** — Inmutabilidad = menos bugs. Campos `final` + constructor = objeto inmutable.
3. **Inicializa arrays/colecciones** — Evita `null` y NPE. `new String[0]`, `new ArrayList<>()`.
4. **Getters: copia defensiva o inmutable** — Nunca devuelvas array/colección interna directa.
5. **Setters: valida invariantes** — `saldo >= 0`, `edad > 0`, `email` contiene `@`.
6. **Nombres sustantivos** — `nombre`, `saldo`, `fechaNacimiento`. No `getNombre()` para campo (eso es método).
7. **Evita campos `static` mutables** — Estado global compartido = pesadilla concurrencia/testing. Ver `[[13 - Static vs Instancia]]`.
8. **Agrupa relacionados** — Si tienes `direccionCalle`, `direccionCiudad`, `direccionCp` → clase `Direccion`.

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Dónde se declaran los campos.
- `[[05 - Modificadores de Acceso]]` — `private`/`public`/`protected` en campos.
- `[[07 - Constructores y this]]` — Inicialización obligatoria de `final` y campos.
- `[[08 - Instanciacion y new]]` — Cada `new` crea copia propia de campos instancia.
- `[[09 - Multiples Objetos e Identidad]]` — e1.campo ≠ e2.campo.
- `[[10 - Metodos de Instancia]]` — Métodos leen/escriben `this.campo`.
- `[[13 - Static vs Instancia]]` — Diferencia campo `static` vs sin `static`.

## Resumen en Una Frase

> **Un atributo es una variable de instancia que define el estado único de cada objeto; encapsúlalo (`private`), válida en setters, y devuelve copias en getters.**

---

## Tags
`#java #fundamentos #atributos #campos #variables-instancia #estado #encapsulamiento #final #valores-defecto`