---
tags: [java, fundamentos, atributos, campos, estado, variables-instancia]
---

# 06 - Atributos y Campos

---

## NIVEL JUNIOR

### Que son los atributos?

Los atributos (o campos) son los datos que guarda un objeto. Son las variables que definen el estado de un objeto.

```java
public class Alumno {
    String nombre;      // Atributo
    int edad;           // Atributo
    double notaMedia;   // Atributo
}
```

Cada vez que creas un `Alumno`, ese objeto tiene su propio `nombre`, `edad` y `notaMedia`.

### Valores por defecto

Si no les das valor, Java les asigna uno automaticamente:

| Tipo | Valor por defecto |
|------|------------------|
| `int`, `long`, `byte`, `short` | `0` |
| `double`, `float` | `0.0` |
| `boolean` | `false` |
| `String` y otros objetos | `null` |

### Ejemplo basico

```java
public class Alumno {
    String nombre;
    int edad;
    double notaMedia;

    public void mostrarDatos() {
        System.out.println("Nombre: " + nombre);
        System.out.println("Edad: " + edad);
        System.out.println("Nota media: " + notaMedia);
    }
}

public class Principal {
    public static void main(String[] args) {
        Alumno a = new Alumno();
        a.nombre = "Lucia";
        a.edad = 20;
        a.notaMedia = 8.5;
        a.mostrarDatos();
    }
}
```

---

## NIVEL MID

### Inicializacion en la declaracion

Puedes dar valor inicial a los atributos directamente:

```java
public class Producto {
    String nombre;
    double precio = 0.0;           // Valor inicial
    int stock = 0;                 // Valor inicial
    boolean disponible = false;    // Valor inicial
    String codigo = "SIN-CODIGO";  // Valor inicial
}
```

### Campos final: que no cambian

Un campo `final` solo se asigna una vez:

```java
public class Persona {
    final String dni;       // Se asigna en el constructor y ya no cambia
    final int anioNacimiento;
    String nombre;

    public Persona(String dni, int anioNacimiento, String nombre) {
        this.dni = dni;
        this.anioNacimiento = anioNacimiento;
        this.nombre = nombre;
    }
}
```

### Campos static: compartidos

```java
public class Contador {
    static int total = 0;   // Compartido por todas las instancias
    int numero;              // Propio de cada instancia

    public Contador() {
        total++;
        numero = total;
    }
}
```

### Inicializacion en bloque

```java
public class Ejemplo {
    int x;
    String nombre;
    double precio;

    {
        // Bloque de inicializacion: se ejecuta antes del constructor
        x = 10;
        nombre = "Sin nombre";
    }

    public Ejemplo() {
        precio = 0.0;
    }
}
```

---

## NIVEL SENIOR

### Records: atributos simplificados

```java
public record Producto(String nombre, double precio, int stock) {
    // Los atributos son automaticamente private final
    // Los metodos de acceso son automaticos: producto.nombre(), producto.precio()
}

public record Pedido(int id, String cliente, List<Producto> productos, LocalDateTime fecha) {
    public Pedido {
        // Constructor compacto: validacion sin asignar manualmente
        if (id <= 0) {
            throw new IllegalArgumentException("Id invalido");
        }
        if (productos.isEmpty()) {
            throw new IllegalArgumentException("Pedido sin productos");
        }
    }
}
```

### Atributos con Optional

```java
import java.util.Optional;

public class Usuario {
    private final long id;
    private final String nombre;
    private final String email;
    private final Optional<String> telefono;  // Puede no tener telefono

    public Usuario(long id, String nombre, String email, String telefono) {
        this.id = id;
        this.nombre = nombre;
        this.email = email;
        this.telefono = Optional.ofNullable(telefono);
    }

    public Optional<String> getTelefono() {
        return telefono;
    }
}
```

### Inmutabilidad con copia defensiva

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Grupo {
    private final String nombre;
    private final List<String> miembros;

    public Grupo(String nombre, List<String> miembros) {
        this.nombre = nombre;
        // Copia defensiva: no confias en que la lista externa no cambie
        this.miembros = List.copyOf(miembros);
    }

    public List<String> getMiembros() {
        return miembros;  // Ya es inmutable por List.copyOf
    }
}
```

### Campos con patrones modernos

```java
public class Configuracion {
    private static final Configuracion INSTANCIA = new Configuracion();

    private String entorno = System.getenv("ENTORNO");
    private int puerto = Integer.getInteger("app.puerto", 8080);
    private List<String> origenesPermitidos = List.of("http://localhost");

    public static Configuracion obtener() {
        return INSTANCIA;
    }

    // getters...
}
```

---

## Errores Comunes

> Olvidar inicializar variables locales. Los atributos de clase se inicializan solos, pero las variables dentro de metodos NO.

> Poner atributos `public`. Rompe el encapsulamiento. Cualquiera puede modificarlos sin control.

> Usar `float` para dinero. Los decimales exactos se pierden. Usa `BigDecimal` para dinero.

> Asignar un array o lista sin copia defensiva. Quien te pasa la lista puede modificarla desde fuera.

> Confundir campos `static` con campos de instancia. `static` es compartido por todos los objetos.

---

## Buenas Practicas

1. Atributos `private` y acceso mediante [[21 - Getters y Setters]].
2. `final` para campos que no cambian despues de la creacion.
3. Inicializa en la declaracion si el valor por defecto tiene sentido.
4. Usa `record` para datos simples e inmutables.
5. Copia defensiva en atributos que reciben listas o arrays mutables.
6. `static final` para constantes: `public static final int MAX = 100;`.
7. No expongas colecciones directamente. Usa `List.copyOf()` o `Collections.unmodifiableList()`.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Los atributos son parte de la clase
- [[05 - Modificadores de Acceso]] - Los atributos suelen ser private
- [[07 - Constructores y this]] - Inicializar atributos en constructor
- [[08 - Instanciacion y new]] - Los atributos nacen con new
- [[09 - Multiples Objetos e Identidad]] - Cada objeto tiene sus propios atributos
- [[13 - Static vs Instancia]] - Campos static (compartidos) vs de instancia
- [[21 - Getters y Setters]] - Acceso controlado a atributos

---

## Tags
`#java #fundamentos #atributos #campos #estado #variables-instancia #campos-estaticos`
