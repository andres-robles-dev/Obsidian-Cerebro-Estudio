---
tags: [java, fundamentos, clases, estructura-basica]
---

# 01 - Clases y Estructura Basica

---

## NIVEL JUNIOR

### Que es una clase?

Una **clase** es como un molde o plantilla para crear cosas en Java. Piensa en una clase como el plano de una casa: el plano no es la casa, pero con ese plano puedes construir muchas casas iguales.

Un archivo `.java` tiene el mismo nombre que la clase publica que contiene.

### Regla mas importante

El archivo se llama igual que la clase publica:
- Archivo: `Persona.java`
- Clase: `public class Persona`

### Ejemplo minimo

```java
public class Persona {
    String nombre;

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}
```

### Como se usa?

```java
public class Principal {
    public static void main(String[] args) {
        Persona p = new Persona();
        p.nombre = "Ana";
        p.saludar();
    }
}
```

Esto imprime: `Hola, soy Ana`

### Que acabamos de hacer?

1. `public class Persona` - creamos el molde llamado Persona
2. `String nombre` - le dimos un espacio para guardar un texto
3. `public void saludar()` - le dimos una accion que puede hacer
4. `new Persona()` - creamos una persona de verdad (un objeto)
5. `p.nombre = "Ana"` - guardamos "Ana" en el espacio nombre
6. `p.saludar()` - le pedimos que salude

---

## NIVEL MID

### Estructura completa de una clase

Una clase puede tener tres tipos de miembros:

| Miembro | Que es | Ejemplo |
|---------|--------|---------|
| Atributos | Datos que guarda | `String nombre;` |
| Constructores | Como se crea | `public Persona(String n)` |
| Metodos | Cosas que hace | `public void saludar()` |

### Ejemplo con todo incluido

```java
public class Cancion {
    // Atributos
    String titulo;
    String artista;
    int duracionSegundos;

    // Constructor
    public Cancion(String titulo, String artista, int duracionSegundos) {
        this.titulo = titulo;
        this.artista = artista;
        this.duracionSegundos = duracionSegundos;
    }

    // Metodos
    public void reproducir() {
        System.out.println("Reproduciendo: " + titulo + " de " + artista);
    }

    public String obtenerInfo() {
        return titulo + " - " + artista + " (" + duracionSegundos + "s)";
    }
}
```

### Uso de la clase

```java
public class Reproductor {
    public static void main(String[] args) {
        Cancion c1 = new Cancion("Bohemian Rhapsody", "Queen", 354);
        Cancion c2 = new Cancion("Imagine", "John Lennon", 183);

        c1.reproducir();
        String info = c2.obtenerInfo();
        System.out.println(info);
    }
}
```

### this: la palabra clave

`this` significa "este objeto". Sirve para diferenciar entre el atributo y el parametro cuando tienen el mismo nombre:

```java
public Cancion(String titulo, String artista) {
    this.titulo = titulo;  // this.titulo = el atributo, titulo = el parametro
    this.artista = artista;
}
```

---

## NIVEL SENIOR

### Java moderno con records

Para clases simples que solo guardan datos, Java moderno ofrece los `record` (Java 16+):

```java
public record Cancion(String titulo, String artista, int duracionSegundos) {
    // Metodo adicional
    public String obtenerInfo() {
        return titulo + " - " + artista + " (" + duracionSegundos + "s)";
    }
}

public record Usuario(String nombre, String email, int edad) {}
```

Con `record` no necesitas escribir constructor, getters, `equals`, `hashCode` ni `toString`. Todo viene automaticamente.

### Clases selladas (sealed)

Cuando quieres controlar quien puede heredar de tu clase (Java 17+):

```java
public sealed class Figura permite Circulo, Rectangulo, Triangulo {
    // ...
}

public final class Circulo extends Figura {
    double radio;
}

public final class Rectangulo extends Figura {
    double ancho;
    double alto;
}
```

### Clases anidadas y locales

```java
public class Tienda {
    private String nombre;

    // Clase interna (inner class)
    public class Producto {
        String nombre;
        double precio;
    }

    // Clase local dentro de un metodo
    public void procesar() {
        class Factura {
            int numero;
        }
    }
}
```

### Buenas practicas profesionales

- Una clase = una responsabilidad. `Cancion` solo maneja canciones, no descargas ni reproduccion.
- Atributos privados con getters ([[21 - Getters y Setters]])
- Nombres en PascalCase: `MiClase`, `FacturaCliente`, `GestorArchivos`
- Paquete que refleje la funcionalidad: `com.musica.modelo`, `com.musica.servicios`
- Poner `@Override` en metodos que sobreescriben

### Diferencia entre clases y records

| Caracteristica | Clase normal | Record |
|----------------|-------------|--------|
| Estado mutable | Si | No (inmutable) |
| Constructor automatico | No | Si |
| `equals`/`hashCode` automatico | No | Si |
| `toString` automatico | No | Si |
| Herencia | Si | No (final) |
| Para que sirve | Logica compleja | Datos simples |

---

## Errores Comunes

> El archivo y la clase publica deben llamarse igual. Si el archivo es `Persona.java`, la clase debe ser `public class Persona`.

> Solo puede haber una clase `public` por archivo. Las demas deben ser sin modificador.

> Los constructores NO llevan `void` ni tipo de retorno. Se llaman igual que la clase y punto.

> No olvides las llaves `{ }` al abrir y cerrar la clase. Cada `{` debe tener su `}`.

> Toda variable necesita un tipo. No puedes escribir `nombre = "Ana"` sin declarar `String nombre;` primero.

---

## Buenas Practicas

1. Un archivo = una clase publica. Facil de encontrar y mantener.
2. Nombres descriptivos en PascalCase: `FacturaElectronica`, no `Fact` ni `FE`.
3. Un solo proposito por clase. Que no haga de todo.
4. Constructores que dejen el objeto listo para usar.
5. Atributos privados, acceso por [[21 - Getters y Setters]].
6. Metodos cortos con nombres verbo: `calcularTotal()`, `enviarEmail()`.
7. Documentar con [[20 - Javadoc y Documentacion]] la API publica.

---

## Conexiones

- [[02 - Punto de Entrada main]] - El `main` vive en una clase
- [[06 - Atributos y Campos]] - Los datos de la clase
- [[07 - Constructores y this]] - Como se crean los objetos
- [[08 - Instanciacion y new]] - `new` crea objetos desde la clase
- [[19 - Separacion de Responsabilidades]] - Una clase por proposito
- [[20 - Javadoc y Documentacion]] - Documentar la clase
- [[21 - Getters y Setters]] - Acceso controlado a atributos
- [[25 - Paquetes y Organizacion]] - Donde vive la clase

---

## Tags
`#java #fundamentos #clases #estructura-basica #poo`
