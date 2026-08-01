---
tags: [java, fundamentos, instanciacion, new, heap, memoria, referencia]
---

# 08 - Instanciacion y new

---

## NIVEL JUNIOR

### Que es instanciar?

Instanciar es crear un objeto a partir de una clase. Usas la palabra `new` para hacerlo.

```java
Clase objeto = new Clase();
```

- La **clase** es el molde (el plano)
- El **objeto** es la instancia (la casa construida)
- `new` es la maquina que construye el objeto

### Ejemplo basico

```java
public class Juguete {
    String nombre;
    double precio;

    public void mostrarInfo() {
        System.out.println(nombre + " - " + precio + " euros");
    }
}

public class Tienda {
    public static void main(String[] args) {
        Juguete j1 = new Juguete();
        j1.nombre = "Peluche";
        j1.precio = 15.50;

        Juguete j2 = new Juguete();
        j2.nombre = "Coche RC";
        j2.precio = 45.00;

        j1.mostrarInfo();
        j2.mostrarInfo();
    }
}
```

### Anatomia de una instanciacion

```java
Juguete j1 = new Juguete();
```

1. `Juguete j1` - declara una variable que puede guardar objetos Juguete
2. `new` - pide memoria para crear el objeto
3. `Juguete()` - llama al constructor para inicializar el objeto
4. `=` - asigna la direccion del nuevo objeto a la variable `j1`

---

## NIVEL MID

### Que pasa en memoria?

Cuando haces `new`, pasan varias cosas:

```
Memoria:
_____________________________
|         STACK             |
|                           |
| j1 = @1234  -------|      |
|____________________|______|
                     |
                     v
_____________________|_______
|         HEAP              |
|                           |
| @1234: Juguete           |
|   nombre = "Peluche"     |
|   precio = 15.50         |
|___________________________|
```

- La variable `j1` esta en el **Stack** y guarda una direccion (referencia)
- El objeto en si esta en el **Heap**
- La direccion apunta al objeto en el Heap

### Dos objetos, misma clase

```java
Coche c1 = new Coche("Rojo");
Coche c2 = new Coche("Azul");
```

Ahora tienes dos objetos independientes en el Heap. `c1` y `c2` son referencias distintas que apuntan a objetos distintos.

### Pasar objetos a metodos

Cuando pasas un objeto a un metodo, pasas la referencia (la direccion), no el objeto entero:

```java
public class Taller {
    public static void pintar(Coche c, String nuevoColor) {
        c.color = nuevoColor;  // Modifica el objeto original
    }

    public static void main(String[] args) {
        Coche miCoche = new Coche("Rojo");
        pintar(miCoche, "Azul");
        System.out.println(miCoche.color);  // "Azul" - se modifico el original
    }
}
```

---

## NIVEL SENIOR

### Factory methods en vez de new

En Java moderno se recomienda usar metodos estaticos factory en vez de `new` directamente:

```java
import java.time.LocalDate;

public class Usuario {
    private final String nombre;
    private final String email;

    // Constructor privado: solo se usa desde los factory methods
    private Usuario(String nombre, String email) {
        this.nombre = nombre;
        this.email = email;
    }

    // Factory methods
    public static Usuario crearConEmail(String nombre, String email) {
        if (!email.contains("@")) {
            throw new IllegalArgumentException("Email invalido");
        }
        return new Usuario(nombre, email);
    }

    public static Usuario crearInvitado(String nombre) {
        return new Usuario(nombre, "invitado@" + nombre.toLowerCase() + ".com");
    }

    public static Usuario crearAdmin(String nombre) {
        return new Usuario(nombre, "admin@" + nombre.toLowerCase() + ".com");
    }
}
```

### Instanciacion con records

Los `record` se instancian igual pero con menos codigo:

```java
public record Punto(double x, double y) {
    public static Punto origen() {
        return new Punto(0, 0);
    }

    public Punto desplazar(double dx, double dy) {
        return new Punto(this.x + dx, this.y + dy);  // Nuevo objeto inmutable
    }
}

// Uso
var p1 = new Punto(3, 4);
var origen = Punto.origen();
var p2 = p1.desplazar(1, 1);
```

### Copia defensiva al instanciar

Cuando recibes datos mutables en el constructor, haz una copia para proteger tu objeto:

```java
public class Grupo {
    private final String nombre;
    private final List<String> miembros;

    public Grupo(String nombre, List<String> miembros) {
        this.nombre = nombre;
        // Copia defensiva: si alguien modifica la lista original, este grupo no se entera
        this.miembros = List.copyOf(miembros);
    }

    public List<String> obtenerMiembros() {
        return miembros;  // Ya es inmutable
    }
}
```

### Pool de objetos con Flyweight

Para objetos que se repiten mucho, puedes reutilizar instancias:

```java
public class Color {
    private static final Map<String, Color> POOL = new HashMap<>();

    private final String nombre;
    private final String hex;

    private Color(String nombre, String hex) {
        this.nombre = nombre;
        this.hex = hex;
    }

    public static Color de(String nombre, String hex) {
        return POOL.computeIfAbsent(nombre, n -> new Color(n, hex));
    }
}

// Uso: Color.de("rojo", "#FF0000") devuelve la misma instancia si ya se creo
```

---

## Errores Comunes

> Olvidar el `new`. `Coche c = Coche("Rojo");` no compila. Debe ser `new Coche("Rojo")`.

> Confundir la referencia con el objeto. `Coche c1 = c2;` no copia el objeto, copia la referencia. Ambos apuntan al mismo objeto.

> Crear objetos dentro de un bucle sin necesidad. Cada `new` consume memoria. A veces es mejor reutilizar.

> No validar en el constructor. El objeto puede nacer en un estado invalido.

> Pasar `this` en el constructor (dejando escapar la referencia antes de terminar de construir).

---

## Buenas Practicas

1. Prefiere factory methods sobre `new` directo cuando la creacion tiene logica.
2. Usa `record` para objetos de datos: instanciacion simple e inmutable.
3. Copia defensiva de colecciones y arrays recibidos en el constructor.
4. No crees objetos innecesariamente dentro de bucles. Reutiliza cuando puedas.
5. Usa `List.of()`, `Map.of()`, `Set.of()` para colecciones pequenas e inmutables.
6. Documenta el constructor con Javadoc explicando que hace cada parametro.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Las clases son el molde para instanciar
- [[06 - Atributos y Campos]] - Atributos = estado del objeto instanciado
- [[07 - Constructores y this]] - `new` llama al constructor
- [[09 - Multiples Objetos e Identidad]] - Cada new = objeto distinto
- [[13 - Static vs Instancia]] - Static existe sin new, instancia necesita new
- [[17 - Flujo de Ejecucion JVM]] - Memoria Heap y Stack
- [[23 - Metodos - Parametros, Retorno y Return]] - Paso por valor de referencias

---

## Tags
`#java #fundamentos #instanciacion #new #heap #memoria #referencia #factory`
