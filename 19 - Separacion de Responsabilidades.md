---
tags: [java, fundamentos, separacion-responsabilidades, srp, cohesion, arquitectura]
---

# 19 - Separacion de Responsabilidades

---

## NIVEL JUNIOR

### El problema

Meter todo en un solo metodo o clase:

```java
// MAL: todo mezclado
public class TodoEnUno {
    public static void main(String[] args) {
        String nombre = "Ana";
        int edad = 25;
        boolean mayorEdad = edad >= 18;
        System.out.println("Nombre: " + nombre);
        System.out.println("Mayor de edad: " + mayorEdad);

        double precio = 100;
        double iva = precio * 0.21;
        double total = precio + iva;
        System.out.println("Total: " + total);
    }
}
```

### La solucion: separar

Cada cosa en su lugar:

```java
// Clase que solo guarda datos de persona
public class Persona {
    String nombre;
    int edad;
}

// Clase que solo calcula precios
public class CalculadoraPrecios {
    public double calcularTotal(double precio) {
        return precio + (precio * 0.21);
    }
}

// Clase que solo muestra informacion
public class Mostrador {
    public void mostrarPersona(Persona p) {
        System.out.println("Nombre: " + p.nombre);
        String estado = p.edad >= 18 ? "Mayor de edad" : "Menor";
        System.out.println(estado);
    }
}
```

### El main solo arranca

```java
public class Principal {
    public static void main(String[] args) {
        Persona p = new Persona();
        p.nombre = "Ana";
        p.edad = 25;

        Mostrador m = new Mostrador();
        m.mostrarPersona(p);

        CalculadoraPrecios calc = new CalculadoraPrecios();
        System.out.println("Total: " + calc.calcularTotal(100));
    }
}
```

---

## NIVEL MID

### Principio de Responsabilidad Unica (SRP)

Cada clase debe tener UNA sola razon para cambiar:

```java
// BIEN: cada clase tiene una responsabilidad

public class Usuario {
    private String nombre;
    private String email;
    // Solo guarda datos de usuario
}

public class RepositorioUsuarios {
    public void guardar(Usuario u) {
        // Solo guarda en base de datos
    }

    public Usuario buscarPorEmail(String email) {
        // Solo busca en base de datos
    }
}

public class ValidadorUsuario {
    public boolean esValido(Usuario u) {
        // Solo valida datos
        return u.email.contains("@") && u.nombre != null;
    }
}

public class EmailServicio {
    public void enviarBienvenida(Usuario u) {
        // Solo envia emails
    }
}
```

### Cohesion alta, acoplamiento bajo

- **Cohesion alta**: los metodos de una clase estan relacionados entre si
- **Acoplamiento bajo**: las clases dependen lo minimo posible unas de otras

```java
// Alta cohesion: todos los metodos hablan de facturas
public class Factura {
    private double total;
    private List<String> lineas;

    public void agregarLinea(String producto, double precio) { }
    public double calcularTotal() { }
    public void mostrarFactura() { }
}
```

---

## NIVEL SENIOR

### Separacion en capas

Arquitectura tipica en proyectos profesionales:

```
src/main/java/com/miempresa/
├── dominio/          -> Clases que representan el negocio
│   ├── Usuario.java
│   └── Pedido.java
├── repositorio/      -> Acceso a datos
│   ├── UsuarioRepositorio.java
│   └── PedidoRepositorio.java
├── servicios/        -> Logica de negocio
│   ├── UsuarioServicio.java
│   └── PedidoServicio.java
└── aplicacion/       -> Punto de entrada
    └── Main.java
```

### Ejemplo de capas

```java
// CAPA DOMINIO: datos puros
public record Usuario(String nombre, String email, boolean activo) {}

// CAPA REPOSITORIO: acceso a datos
public class UsuarioRepositorio {
    private final List<Usuario> usuarios = new ArrayList<>();

    public void guardar(Usuario usuario) {
        usuarios.add(usuario);
    }

    public Optional<Usuario> buscarPorEmail(String email) {
        return usuarios.stream()
            .filter(u -> u.email().equalsIgnoreCase(email))
            .findFirst();
    }
}

// CAPA SERVICIO: logica de negocio
public class UsuarioServicio {
    private final UsuarioRepositorio repositorio;
    private final EmailServicio emailServicio;

    public UsuarioServicio(UsuarioRepositorio repositorio, EmailServicio emailServicio) {
        this.repositorio = repositorio;
        this.emailServicio = emailServicio;
    }

    public Usuario registrar(String nombre, String email) {
        if (!email.contains("@")) {
            throw new IllegalArgumentException("Email invalido");
        }

        var usuario = new Usuario(nombre, email, true);
        repositorio.guardar(usuario);
        emailServicio.enviarBienvenida(usuario);
        return usuario;
    }
}

// CAPA APLICACION: arranque
public class Principal {
    public static void main(String[] args) {
        var repositorio = new UsuarioRepositorio();
        var emailServicio = new EmailServicio();
        var servicio = new UsuarioServicio(repositorio, emailServicio);

        var usuario = servicio.registrar("Ana", "ana@email.com");
        System.out.println("Usuario registrado: " + usuario.nombre());
    }
}
```

### Inyeccion de dependencias

En vez de crear dependencias dentro de la clase, las recibes por constructor:

```java
// MAL: la clase crea sus dependencias
public class ServicioMalo {
    private Repositorio repositorio = new Repositorio();  // Acoplamiento fuerte
}

// BIEN: recibe las dependencias
public class ServicioBueno {
    private final Repositorio repositorio;

    public ServicioBueno(Repositorio repositorio) {
        this.repositorio = repositorio;  // Inyeccion por constructor
    }
}
```

---

## Errores Comunes

> Clases上帝 (todopoderosas) que hacen de todo. Una clase que calcula, guarda en BD, envia emails y muestra en pantalla tiene muchas responsabilidades.

> El main con 200 lineas de codigo. El main solo debe arrancar y delegar.

> Metodos de 100+ lineas. Un metodo deberia hacer una sola cosa y ser corto (< 20 lineas).

> Clases que dependen directamente de implementaciones concretas en vez de interfaces.

---

## Buenas Practicas

1. Una clase = una responsabilidad. Si una clase hace "y ademas", dividela.
2. Metodos cortos (< 20 lineas). Si es mas largo, extrae a otro metodo.
3. Inyeccion de dependencias por constructor. No crees dependencias dentro de la clase.
4. El main solo arranca. Que cree los objetos principales y llame al metodo que inicia.
5. Separa en capas: dominio (datos), repositorio (acceso a datos), servicio (logica), aplicacion (arranque).

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Clases con proposito unico
- [[05 - Modificadores de Acceso]] - Encapsulamiento para separar
- [[10 - Metodos de Instancia]] - Metodos con una responsabilidad
- [[20 - Javadoc y Documentacion]] - Documentar responsabilidades
- [[21 - Getters y Setters]] - Encapsulamiento de datos
- [[22 - Separacion de Responsabilidades]] - Nivel avanzado
- [[25 - Paquetes y Organizacion]] - Organizar por capas

---

## Tags
`#java #fundamentos #separacion-responsabilidades #srp #cohesion #arquitectura #capas`
