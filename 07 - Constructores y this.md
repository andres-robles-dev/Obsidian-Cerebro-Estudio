---
tags: [java, fundamentos, constructores, this, sobrecarga, inicializacion]
---

# 07 - Constructores y this

## Concepto Central

Un **constructor** es un bloque de código especial que se ejecuta **automáticamente** al crear un objeto con `new`. Su única misión: **inicializar el estado del objeto** (asignar valores a sus campos) para que nazca en un estado válido y usable. El nombre **debe** coincidir exactamente con el de la clase y **no tiene tipo de retorno** (ni `void`).

La palabra clave **`this`** es una referencia al **objeto actual** (la instancia en ejecución). Sirve para:
1. Desambiguar **campo vs parámetro** (`this.nombre = nombre`)
2. Llamar a **otro constructor** de la misma clase (`this(...)`) — *constructor chaining*
3. Pasar **la propia instancia** a otro método/objeto (`otro.metodo(this)`)

## Para Qué Sirve / Cuándo Usarlo

- Garantizar que **todo objeto nace válido** (invariantes cumplidos)
- Centralizar lógica de inicialización (evita duplicar código en setters)
- Permitir **múltiples formas de crear** un objeto (sobrecarga)
- Inyectar dependencias obligatorias (constructor = contrato de creación)
- `this`: resolver colisión nombres, encadenar constructores, pasar `this` como callback

## Sintaxis General

```java
class NombreClase {
    // Constructor sin parámetros (por defecto si no hay otros)
    public NombreClase() {
        // Inicialización básica
    }

    // Constructor con parámetros (común)
    public NombreClase(Tipo1 param1, Tipo2 param2) {
        this.campo1 = param1;      // this.campo = parámetro
        this.campo2 = param2;
    }

    // Constructor encadenado (llama a otro constructor de MISMA clase)
    public NombreClase(Tipo1 param1) {
        this(param1, valorPorDefecto); // DEBE ser PRIMERA línea
    }

    // Bloque de inicialización de instancia (opcional, antes de constructor)
    {
        // Código común a TODOS los constructores
    }
}
```

### Reglas Clave

| Regla | Detalle |
|-------|---------|
| **Nombre** | Igual a la clase (`Coche` → `public Coche()`) |
| **Sin retorno** | Ni `void`, ni `int`, ni nada. Solo nombre. |
| **`this(...)`** | Solo en **primera línea** de constructor. Llama a otro constructor de la misma clase. |
| **`super(...)`** | Solo en **primera línea**. Llama a constructor padre. Si no pones `this()` ni `super()`, Java inserta `super()` implícito. |
| **Constructor por defecto** | Si **no escribes ningún** constructor, Java genera `public Clase() { super(); }`. Si escribes **cualquiera**, **no** genera el sin-argumentos. |

## Ejemplo Propio: Rectangulo

```java
public class Rectangulo {
    // --- CAMPOS ---
    private double ancho;
    private double alto;
    private String color;
    private boolean relleno;

    // Contador estático de instancias creadas
    private static int contador = 0;

    // --- CONSTRUCTOR COMPLETO (canónico) ---
    public Rectangulo(double ancho, double alto, String color, boolean relleno) {
        validarPositivo(ancho, "Ancho");
        validarPositivo(alto, "Alto");
        this.ancho = ancho;
        this.alto = alto;
        this.color = (color != null && !color.isBlank()) ? color : "Negro";
        this.relleno = relleno;
        contador++; // Incrementa static
    }

    // --- CONSTRUCTOR SOBRECARGADO: solo dimensiones (defaults color/relleno) ---
    public Rectangulo(double ancho, double alto) {
        this(ancho, alto, "Negro", false); // Encadena al canónico (PRIMERA LÍNEA)
    }

    // --- CONSTRUCTOR SOBRECARGADO: cuadrado (ancho=alto) ---
    public Rectangulo(double lado) {
        this(lado, lado); // Encadena al de 2 params → ese encadena al canónico
    }

    // --- CONSTRUCTOR COPIA (crea nuevo rectángulo igual a otro) ---
    public Rectangulo(Rectangulo otro) {
        this(otro.ancho, otro.alto, otro.color, otro.relleno);
    }

    // --- MÉTODO PRIVADO: validación compartida ---
    private void validarPositivo(double valor, String nombre) {
        if (valor <= 0) {
            throw new IllegalArgumentException(nombre + " debe ser > 0, recibido: " + valor);
        }
    }

    // --- GETTERS ---
    public double getAncho() { return ancho; }
    public double getAlto() { return alto; }
    public String getColor() { return color; }
    public boolean isRelleno() { return relleno; }

    // --- SETTERS CON VALIDACIÓN ---
    public void setAncho(double ancho) {
        validarPositivo(ancho, "Ancho");
        this.ancho = ancho;
    }

    public void setAlto(double alto) {
        validarPositivo(alto, "Alto");
        this.alto = alto;
    }

    public void setColor(String color) {
        this.color = (color != null && !color.isBlank()) ? color : "Negro";
    }

    public void setRelleno(boolean relleno) {
        this.relleno = relleno;
    }

    // --- MÉTODOS DE NEGOCIO ---
    public double area() { return ancho * alto; }
    public double perimetro() { return 2 * (ancho + alto); }
    public boolean esCuadrado() { return Math.abs(ancho - alto) < 0.0001; }

    public void mostrar() {
        System.out.println("=== Rectángulo ===");
        System.out.println("Ancho: " + ancho);
        System.out.println("Alto: " + alto);
        System.out.println("Color: " + color);
        System.out.println("Relleno: " + relleno);
        System.out.println("Área: " + area());
        System.out.println("Perímetro: " + perimetro());
        System.out.println("Es cuadrado: " + esCuadrado());
    }

    // --- MÉTODO ESTÁTICO: acceso a contador ---
    public static int getContador() { return contador; }

    // --- MAIN DE PRUEBA ---
    public static void main(String[] args) {
        // Usos de constructores sobrecargados
        Rectangulo r1 = new Rectangulo(5, 3, "Rojo", true);  // Canónico
        Rectangulo r2 = new Rectangulo(4, 4);                // 2 params → defaults
        Rectangulo r3 = new Rectangulo(6);                   // 1 param (cuadrado)
        Rectangulo r4 = new Rectangulo(r1);                  // Copia de r1

        r1.mostrar();
        System.out.println();
        r2.mostrar();
        System.out.println();
        r3.mostrar();
        System.out.println();
        r4.mostrar();

        System.out.println("\nTotal instancias creadas: " + Rectangulo.getContador());

        // Demostración this en setter
        r1.setAncho(10); // this.ancho = 10
        System.out.println("\nTras setAncho(10): área = " + r1.area());
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `public Rectangulo(double ancho, double alto, String color, boolean relleno)` | Constructor **canónico** (el más completo). Valida, asigna con `this.`, incrementa `static`. |
| `this.ancho = ancho;` | `this.ancho` = campo de instancia. `ancho` = parámetro. Sin `this.`, `ancho = ancho` asigna parámetro a sí mismo. |
| `this.color = (color != null ...) ? color : "Negro";` | Operador ternario para default si `null`/vacío. |
| `public Rectangulo(double ancho, double alto) { this(ancho, alto, "Negro", false); }` | **Constructor chaining**: `this(...)` llama a otro constructor. **Debe ser primera línea**. |
| `public Rectangulo(double lado) { this(lado, lado); }` | Encadena al de 2 params → ese encadena al canónico. Evita duplicar validación/asignación. |
| `public Rectangulo(Rectangulo otro)` | **Constructor copia**. Crea nueva instancia con mismos valores. |
| `private void validarPositivo(...)` | Lógica compartida. `private` = solo esta clase. |
| `contador++` | Modifica campo `static`. Ver `[[13 - Static vs Instancia]]`. |
| `r1.setAncho(10);` | En setter: `this.ancho = ancho;` desambigua. |

## Errores Comunes

> [!warning] **Error 1: Poner `void` o tipo en constructor**
> ```java
> public void Rectangulo(double a, double b) { } // ❌ Método, no constructor
> public int Rectangulo() { return 0; }          // ❌
> ```
> ✅ **Correcto**: `public Rectangulo(double a, double b) { }` (sin tipo).

> [!warning] **Error 2: `this(...)` no en primera línea**
> ```java
> public Rectangulo(double a) {
>     System.out.println("Creando"); // ❌ Error: this() must be first statement
>     this(a, a);
> }
> ```
> ✅ **Correcto**: `this(a, a);` en línea 1. Si necesitas lógica antes, usa método `init()` llamado desde canónico.

> [!warning] **Error 3: Llamar `this()` y `super()` en mismo constructor**
> ```java
> public Rectangulo(double a) {
>     this(a, a);
>     super(); // ❌ Solo uno permitido, y en primera línea
> }
> ```
> ✅ **Correcto**: El canónico hace `super()` implícito (o explícito). Los encadenados `this()` delegan.

> [!warning] **Error 4: Olvidar constructor sin args tras definir uno con args**
> ```java
> class A { public A(int x) {} }
> new A(); // ❌ Error: no constructor sin argumentos
> ```
> ✅ **Correcto**: Añade `public A() { this(0); }` o `public A() { }` explícito.

> [!warning] **Error 5: `this` en contexto `static`**
> ```java
> static void metodo() { this.ancho = 5; } // ❌ static no tiene this
> ```
> ✅ **Correcto**: `this` solo en métodos/constructores de instancia.

> [!warning] **Error 6: Shadowing sin `this` en setter**
> ```java
> void setAncho(double ancho) { ancho = ancho; } // ❌ No hace nada al campo
> ```
> ✅ **Correcto**: `this.ancho = ancho;`

## Buenas Prácticas

1. **Un constructor canónico** — El que tiene todos los parámetros y hace validación/asignación real.
2. **Encadena (`this(...)`) hacia el canónico** — Los demás constructores delegan. Un solo punto de verdad.
3. **Valida en el canónico** — Los encadenados confían en él.
4. **`final` en campos inmutables** — Asigna en constructor (o declaración). Ver `[[06 - Atributos y Campos]]`.
5. **Constructor copia defensivo** — Si campos son mutables (arrays, listas), copia profunda en constructor copia.
6. **Documenta contrato** — Javadoc en constructor público: qué parámetros, qué excepciones lanza.
7. **Evita lógica pesada en constructor** — Solo inicialización. Cosas complejas → factoría/builder.
8. **`this` consistente** — Úsalo **siempre** en setters y constructores para campos, aunque no haya colisión (claridad).

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Constructores son miembros de la clase.
- `[[06 - Atributos y Campos]]` — Inicialización de campos (`final`, valores defecto).
- `[[08 - Instanciacion y new]]` — `new Rectangulo(...)` invoca constructor.
- `[[09 - Multiples Objetos e Identidad]]` — Cada `new` llama constructor → objeto distinto.
- `[[10 - Metodos de Instancia]]` — Setters usan `this` igual que constructores.
- `[[13 - Static vs Instancia]]` — `contador++` en constructor modifica `static`.

## Resumen en Una Frase

> **El constructor inicializa el objeto para que nazca válido; `this` desambigua campo/parámetro y encadena constructores hacia uno canónico único.**

---

## Tags
`#java #fundamentos #constructores #this #sobrecarga #constructor-chaining #inicializacion #constructor-copia`