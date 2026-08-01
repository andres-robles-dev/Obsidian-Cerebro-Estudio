---
tags: [java, fundamentos, constructores, this, sobrecarga, inicializacion]
---

# 07 - Constructores y this

---

## NIVEL JUNIOR

### Que es un constructor?

Un constructor es un metodo especial que se ejecuta cuando creas un objeto con `new`. Su trabajo es preparar el objeto para que este listo para usar.

### Reglas del constructor

- Tiene el MISMO nombre que la clase
- NO lleva `void` ni ningun tipo de retorno
- Se llama automaticamente al hacer `new`

### Ejemplo minimo

```java
public class Perro {
    String nombre;
    int edad;

    // Constructor
    public Perro(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public void ladrar() {
        System.out.println(nombre + " dice: Guau!");
    }
}
```

### Como se usa

```java
public class Principal {
    public static void main(String[] args) {
        Perro p1 = new Perro("Max", 3);
        Perro p2 = new Perro("Luna", 1);

        p1.ladrar();
        p2.ladrar();
    }
}
```

### Que hace this?

`this` significa "este objeto". Sirve para diferenciar cuando el parametro y el atributo se llaman igual:

```java
public Perro(String nombre, int edad) {
    this.nombre = nombre;  // this.nombre = atributo, nombre = parametro
    this.edad = edad;      // this.edad = atributo, edad = parametro
}
```

### Constructor por defecto

Si NO escribes ningun constructor, Java pone uno vacio automaticamente:

```java
public class Gato {
    String nombre;
    // Java crea: public Gato() {}
}

// Puedes hacer: Gato g = new Gato();  // Funciona por el constructor por defecto
```

Pero si escribes UN constructor, el por defecto desaparece:

```java
public class Gato {
    String nombre;

    public Gato(String nombre) {
        this.nombre = nombre;
    }
}

// Gato g = new Gato();  // ERROR: ya no existe el constructor sin parametros
```

---

## NIVEL MID

### Sobrecarga de constructores

Puedes tener varios constructores con distintos parametros:

```java
public class Rectangulo {
    double ancho;
    double alto;
    String color;
    boolean relleno;

    // Constructor completo
    public Rectangulo(double ancho, double alto, String color, boolean relleno) {
        this.ancho = ancho;
        this.alto = alto;
        this.color = color;
        this.relleno = relleno;
    }

    // Constructor solo con medidas (color y relleno por defecto)
    public Rectangulo(double ancho, double alto) {
        this.ancho = ancho;
        this.alto = alto;
        this.color = "Negro";
        this.relleno = false;
    }

    // Constructor para cuadrado
    public Rectangulo(double lado) {
        this.ancho = lado;
        this.alto = lado;
        this.color = "Negro";
        this.relleno = false;
    }
}
```

### this() para encadenar constructores

Puedes llamar a un constructor desde otro con `this(...)`. Debe ser la PRIMERA linea:

```java
public class Rectangulo {
    double ancho;
    double alto;
    String color;
    boolean relleno;

    // Constructor canonico (el que hace todo el trabajo)
    public Rectangulo(double ancho, double alto, String color, boolean relleno) {
        this.ancho = ancho;
        this.alto = alto;
        this.color = color;
        this.relleno = relleno;
    }

    // Los demas constructores delegan en el canonico
    public Rectangulo(double ancho, double alto) {
        this(ancho, alto, "Negro", false);
    }

    public Rectangulo(double lado) {
        this(lado, lado, "Negro", false);
    }
}
```

### Validacion en constructor

```java
public class CuentaBancaria {
    private double saldo;

    public CuentaBancaria(double saldoInicial) {
        if (saldoInicial < 0) {
            throw new IllegalArgumentException(
                "El saldo inicial no puede ser negativo: " + saldoInicial
            );
        }
        this.saldo = saldoInicial;
    }
}
```

---

## NIVEL SENIOR

### Records: constructores compactos

```java
public record Libro(String titulo, String autor, int anio, String isbn) {
    // Constructor compacto: no hace falta asignar los campos
    public Libro {
        if (titulo == null || titulo.isBlank()) {
            throw new IllegalArgumentException("Titulo obligatorio");
        }
        if (anio < 1900 || anio > 2026) {
            throw new IllegalArgumentException("Anio fuera de rango: " + anio);
        }
    }

    // Constructor adicional
    public Libro(String titulo, String autor) {
        this(titulo, autor, 2026, "SIN-ISBN");
    }
}
```

### Constructor con Optional

```java
import java.util.Optional;

public class Usuario {
    private final long id;
    private final String nombre;
    private final Optional<String> email;
    private final Optional<String> telefono;

    public Usuario(long id, String nombre, String email, String telefono) {
        this.id = id;
        this.nombre = nombre;
        this.email = Optional.ofNullable(email);
        this.telefono = Optional.ofNullable(telefono);
    }

    // Constructor con valores obligatorios
    public Usuario(long id, String nombre) {
        this(id, nombre, null, null);
    }
}
```

### Patron Builder con constructor privado

```java
public class Pedido {
    private final int id;
    private final String cliente;
    private final List<String> productos;
    private final String direccionEnvio;
    private final boolean urgente;

    private Pedido(Builder constructor) {
        this.id = constructor.id;
        this.cliente = constructor.cliente;
        this.productos = List.copyOf(constructor.productos);
        this.direccionEnvio = constructor.direccionEnvio;
        this.urgente = constructor.urgente;
    }

    public static class Builder {
        private int id;
        private String cliente;
        private List<String> productos = new ArrayList<>();
        private String direccionEnvio;
        private boolean urgente = false;

        public Builder conId(int id) {
            this.id = id;
            return this;
        }

        public Builder paraCliente(String cliente) {
            this.cliente = cliente;
            return this;
        }

        public Builder conProducto(String producto) {
            this.productos.add(producto);
            return this;
        }

        public Builder enviarA(String direccion) {
            this.direccionEnvio = direccion;
            return this;
        }

        public Builder urgente() {
            this.urgente = true;
            return this;
        }

        public Pedido construir() {
            return new Pedido(this);
        }
    }
}

// Uso:
// var pedido = new Pedido.Builder()
//     .conId(1)
//     .paraCliente("Ana")
//     .conProducto("Laptop")
//     .urgente()
//     .construir();
```

### this en lambdas y clases anidadas

```java
public class Servicio {
    private String nombre;

    public void ejecutar() {
        // En lambdas, this se refiere a la clase contenedora
        Runnable tarea = () -> {
            System.out.println(this.nombre);
        };

        // En clases anonimas, this se refiere a la clase anonima
        Runnable tarea2 = new Runnable() {
            @Override
            public void run() {
                System.out.println(Servicio.this.nombre);
            }
        };
    }
}
```

---

## Errores Comunes

> Poner `void` en el constructor. `public void Rectangulo() {}` es un metodo, no un constructor. El constructor no lleva tipo de retorno.

> Llamar a `this(...)` despues de otras instrucciones. `this()` debe ser la primera linea del constructor.

> Olvidar que al crear un constructor con parametros, desaparece el constructor sin parametros. Si lo necesitas, debes escribirlo explicitamente.

> Usar `this` en un metodo `static`. `this` no existe en contexto `static` porque no hay objeto.

> No validar parametros en el constructor. Un objeto puede nacer en estado invalido.

---

## Buenas Practicas

1. Un constructor canonico que recibe todos los parametros y hace la validacion.
2. Los demas constructores llaman al canonico con `this(...)` (encadenamiento).
3. Valida parametros en el constructor para que el objeto nazca valido.
4. Usa `final` en campos que se asignan en el constructor y nunca cambian.
5. Para muchos parametros, considera el patron Builder.
6. Documenta con Javadoc: que espera cada parametro y que excepciones lanza.
7. Records cuando la clase solo tiene datos, sin logica compleja.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - El constructor es parte de la clase
- [[06 - Atributos y Campos]] - El constructor inicializa los atributos
- [[08 - Instanciacion y new]] - `new` llama al constructor
- [[09 - Multiples Objetos e Identidad]] - Cada new ejecuta su propio constructor
- [[10 - Metodos de Instancia]] - Los constructores tambien usan this
- [[13 - Static vs Instancia]] - En static no existe this
- [[20 - Javadoc y Documentacion]] - Documentar parametros del constructor
- [[21 - Getters y Setters]] - Constructor puede usar setters para validar
- [[23 - Metodos - Parametros, Retorno y Return]] - Return en constructores

---

## Tags
`#java #fundamentos #constructores #this #sobrecarga #constructor-chaining #inicializacion`
