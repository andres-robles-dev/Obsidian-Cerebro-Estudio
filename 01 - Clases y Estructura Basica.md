---
tags: [java, fundamentos, clases, estructura-basica]
---

# 01 - Clases y Estructura Básica

## Concepto Central

Una **clase** es una plantilla o molde que define la estructura y comportamiento que tendrán los objetos creados a partir de ella. En Java, todo código vive dentro de una clase. Un archivo `.java` puede contener múltiples clases, pero solo **una** puede ser `public` y debe llamarse igual que el archivo.

## Para Qué Sirve / Cuándo Usarlo

- Agrupar datos y operaciones relacionadas en una sola unidad
- Crear tipos de datos personalizados (ej: `Persona`, `Producto`, `Vehiculo`)
- Organizar el código en módulos reutilizables y mantenibles
- Base de la Programación Orientada a Objetos (POO)
- Permitir instanciar múltiples objetos con la misma estructura

## Sintaxis General

```java
[modificadores] class NombreClase {
    // Atributos (campos / variables de instancia)
    tipo atributo1;
    tipo atributo2;

    // Constructores
    public NombreClase(parametros) {
        // Inicialización
    }

    // Métodos (comportamiento)
    [modificadores] tipoRetorno nombreMetodo(parametros) {
        // Cuerpo del método
    }
}
```

## Ejemplo Propio: Coche

```java
public class Coche {
    // Atributos - Estado del coche
    String marca;
    String modelo;
    int anioFabricacion;
    String color;
    double kilometraje;
    boolean encendido;

    // Constructor - Inicializa un coche nuevo
    public Coche(String marca, String modelo, int anioFabricacion, String color) {
        this.marca = marca;
        this.modelo = modelo;
        this.anioFabricacion = anioFabricacion;
        this.color = color;
        this.kilometraje = 0.0;
        this.encendido = false;
    }

    // Método - Comportamiento: encender el motor
    public void encender() {
        if (!encendido) {
            encendido = true;
            System.out.println(marca + " " + modelo + " encendido.");
        } else {
            System.out.println("El coche ya está encendido.");
        }
    }

    // Método - Comportamiento: conducir
    public void conducir(double kilometros) {
        if (encendido) {
            kilometraje += kilometros;
            System.out.println("Conducidos " + kilometros + " km. Total: " + kilometraje);
        } else {
            System.out.println("No se puede conducir: coche apagado.");
        }
    }

    // Método - Comportamiento: apagar
    public void apagar() {
        encendido = false;
        System.out.println("Coche apagado.");
    }

    // Método - Consulta: estado actual
    public void mostrarEstado() {
        System.out.println("=== Estado del Coche ===");
        System.out.println("Marca: " + marca);
        System.out.println("Modelo: " + modelo);
        System.out.println("Año: " + anioFabricacion);
        System.out.println("Color: " + color);
        System.out.println("Kilometraje: " + kilometraje + " km");
        System.out.println("Encendido: " + (encendido ? "Sí" : "No"));
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `public class Coche` | Clase pública accesible desde cualquier parte. Nombre en PascalCase. |
| `String marca;` | Atributo de tipo referencia (String). Valor por defecto: `null`. |
| `int anioFabricacion;` | Atributo primitivo. Valor por defecto: `0`. |
| `boolean encendido;` | Atributo primitivo. Valor por defecto: `false`. |
| `public Coche(...)` | Constructor público. Mismo nombre que la clase. Sin tipo de retorno. |
| `this.marca = marca;` | `this.` distingue el atributo de la clase del parámetro del constructor. |
| `public void encender()` | Método de instancia. `void` = no devuelve valor. Accede a `this.encendido`. |
| `if (!encendido)` | Control de flujo: solo enciende si está apagado. |
| `kilometraje += kilometros;` | Acumula kilómetros. Operador compuesto `+=`. |
| `mostrarEstado()` | Método de consulta (getter implícito). Muestra todos los atributos. |

## Errores Comunes

> [!warning] **Error 1: Clase pública con nombre distinto al archivo**
> ```java
> // Archivo: Coche.java
> public class Auto { } // ❌ Error: la clase pública debe llamarse Coche
> ```
> ✅ **Correcto**: `public class Coche` en archivo `Coche.java`

> [!warning] **Error 2: Múltiples clases públicas en un archivo**
> ```java
> public class Coche { }      // ❌ Solo una clase public por archivo
> public class Moto { }       // ❌ Error de compilación
> ```
> ✅ **Correcto**: Una `public`, las demás `package-private` (sin modificador) o en archivos separados.

> [!warning] **Error 3: Olvidar llaves de apertura/cierre**
> ```java
> public class Coche
>     String marca; // ❌ Falta { después de la declaración de clase
> ```
> ✅ **Correcto**: Siempre `{` después de `class Nombre` y `}` al final.

> [!warning] **Error 4: Atributos sin tipo**
> ```java
> marca = "Toyota"; // ❌ Falta declaración de tipo
> ```
> ✅ **Correcto**: `String marca = "Toyota";` o `String marca;` luego `marca = "Toyota";`

> [!warning] **Error 5: Constructor con tipo de retorno**
> ```java
> public void Coche() { } // ❌ Los constructores NO tienen tipo de retorno
> ```
> ✅ **Correcto**: `public Coche() { }` (sin `void`, sin `int`, nada).

## Buenas Prácticas

1. **Una responsabilidad por clase** — `Coche` gestiona estado de un coche, no de un garaje.
2. **Nombre en PascalCase** — `Coche`, `TarjetaCredito`, `GestorUsuarios`.
3. **Atributos `private` por defecto** — Encapsula el estado (ver `[[05 - Modificadores de Acceso]]`).
4. **Constructor para inicialización obligatoria** — Fuerza que el objeto nazca en estado válido.
5. **Métodos cortos y con nombre verbo** — `encender()`, `conducir()`, `calcularImpuesto()`.
6. **Comentarios Javadoc en API pública** — `/** ... */` para documentación automática.
7. **Archivo = una clase pública** — Facilita búsqueda y mantenimiento.

## Conexión con Otros Temas

- `[[02 - Punto de Entrada main]]` — El `main` vive en una clase (ej: `AppPrincipal`).
- `[[06 - Atributos y Campos]]` — Profundiza en declaración, valores por defecto, `static` fields.
- `[[07 - Constructores y this]]` — Detalle de constructores, sobrecarga, `this()` encadenado.
- `[[08 - Instanciacion y new]]` — Cómo `new Coche()` crea objetos desde esta plantilla.
- `[[17 - Separacion de Responsabilidades]]` — Clases como `Coche` (modelo) vs `Main` (arranque).

## Resumen en Una Frase

> **Una clase es un plano que define atributos y métodos; desde ella se crean objetos (instancias) con `new`.**

---

## Tags
`#java #fundamentos #clases #estructura-basica #poo`