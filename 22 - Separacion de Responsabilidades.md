---
tags: [java, fundamentos, separacion-responsabilidades, srp, arquitectura, capas, solid]
---

# 22 - Separacion de Responsabilidades (Avanzado)

---

## NIVEL JUNIOR

### Repaso rapido

Ya viste en [[19 - Separacion de Responsabilidades]] que cada clase debe tener una sola responsabilidad. Ahora veremos como aplicarlo a nivel arquitectura.

### La regla de oro

Una clase sabe hacer UNA cosa y la hace bien.

```java
// MAL: un metodo que hace de todo
public void procesarPedido() {
    // 1. Validar datos
    // 2. Guardar en BD
    // 3. Enviar email
    // 4. Generar factura
}

// BIEN: cada responsabilidad separada
public class Validador { ... }
public class Repositorio { ... }
public class EmailServicio { ... }
public class FacturaGenerador { ... }
```

---

## NIVEL MID

### Principios SOLID (los mas importantes)

**S** - Single Responsibility (una clase, una responsabilidad)
**O** - Open/Closed (abierto a extension, cerrado a modificacion)

### Single Responsibility en practica

```java
// MAL: la clase Usuario hace de todo
public class Usuario {
    private String nombre;
    private String email;

    public void guardar() { /* SQL */ }
    public void enviarEmail() { /* SMTP */ }
    public void validar() { /* reglas */ }
    public String formatoHTML() { /* HTML */ }
}

// BIEN: cada cosa en su clase
public record Usuario(String nombre, String email, boolean activo) {}

public class UsuarioRepositorio {
    public void guardar(Usuario u) { /* SQL */ }
}

public class EmailServicio {
    public void enviarBienvenida(Usuario u) { /* SMTP */ }
}

public class ValidadorUsuario {
    public boolean esValido(Usuario u) { /* reglas */ }
}
```

### Open/Closed

Las clases deben estar abiertas a extension pero cerradas a modificacion:

```java
// MAL: cada vez que anadimos un descuento nuevo, modificamos la clase
public class CalculadorDescuento {
    public double calcular(double precio, String tipo) {
        return switch (tipo) {
            case "NAVIDAD" -> precio * 0.2;
            case "VERANO" -> precio * 0.15;
            default -> 0;
        };
    }
}

// BIEN: usamos polimorfismo para extender
public interface Descuento {
    double aplicar(double precio);
}

public class DescuentoNavidad implements Descuento {
    public double aplicar(double precio) {
        return precio * 0.2;
    }
}

public class DescuentoVerano implements Descuento {
    public double aplicar(double precio) {
        return precio * 0.15;
    }
}

public class CalculadorDescuento {
    private final List<Descuento> descuentos;

    public CalculadorDescuento(List<Descuento> descuentos) {
        this.descuentos = descuentos;
    }

    public double calcular(double precio) {
        double total = precio;
        for (var d : descuentos) {
            total = d.aplicar(total);
        }
        return total;
    }
}
```

---

## NIVEL SENIOR

### Tell, Don't Ask

No le preguntes a un objeto por sus datos para luego hacer algo con ellos. Mejor dile que haga algo.

```java
// MAL: preguntar (Ask)
public void procesar(Usuario usuario) {
    if (usuario.edad() >= 18) {
        System.out.println(usuario.nombre() + " es mayor de edad");
    }
}

// BIEN: decir (Tell)
public record Usuario(String nombre, int edad) {
    public boolean esMayorDeEdad() {
        return edad >= 18;
    }

    public String estadoMayoria() {
        return esMayorDeEdad() ? "mayor" : "menor";
    }
}

public void procesar(Usuario usuario) {
    System.out.println(usuario.nombre() + " es " + usuario.estadoMayoria());
}
```

### Arquitectura en capas completa

```
/src/main/java/com/miempresa/
├── dominio/              -> Solo datos y reglas de negocio puras
│   ├── Pedido.java        (record)
│   ├── Producto.java      (record)
│   └── Usuario.java       (record)
├── repositorio/          -> Acceso a datos (interfaces)
│   ├── PedidoRepositorio.java
│   └── UsuarioRepositorio.java
├── repositorio.impl/     -> Implementaciones
│   ├── PedidoRepositorioMemoria.java
│   └── UsuarioRepositorioBD.java
├── servicio/             -> Casos de uso (orquesta repositorios + otras servicios)
│   ├── PedidoServicio.java
│   └── UsuarioServicio.java
├── servicio.notificacion/ -> Servicios externos
│   └── EmailServicio.java
└── aplicacion/           -> Punto de entrada, configuracion
    └── Principal.java
```

### Ejemplo completo con inyeccion

```java
// DOMINIO
public record Producto(String codigo, String nombre, double precio) {}
public record LineaPedido(Producto producto, int cantidad) {}
public record Pedido(String id, String cliente, List<LineaPedido> lineas) {}

// REPOSITORIO (interfaz)
public interface ProductoRepositorio {
    Optional<Producto> buscarPorCodigo(String codigo);
    List<Producto> buscarTodos();
}

// REPOSITORIO (implementacion)
public class ProductoRepositorioMemoria implements ProductoRepositorio {
    private final Map<String, Producto> productos = new HashMap<>();

    public void agregar(Producto p) {
        productos.put(p.codigo(), p);
    }

    @Override
    public Optional<Producto> buscarPorCodigo(String codigo) {
        return Optional.ofNullable(productos.get(codigo));
    }

    @Override
    public List<Producto> buscarTodos() {
        return List.copyOf(productos.values());
    }
}

// SERVICIO
public class PedidoServicio {
    private final ProductoRepositorio productoRepositorio;

    public PedidoServicio(ProductoRepositorio productoRepositorio) {
        this.productoRepositorio = productoRepositorio;
    }

    public double calcularTotal(String codigoProducto, int cantidad) {
        var producto = productoRepositorio.buscarPorCodigo(codigoProducto)
            .orElseThrow(() -> new IllegalArgumentException("Producto no encontrado: " + codigoProducto));

        if (cantidad <= 0) {
            throw new IllegalArgumentException("Cantidad invalida: " + cantidad);
        }

        return producto.precio() * cantidad;
    }
}

// APLICACION
public class Principal {
    public static void main(String[] args) {
        // Cableado manual (sin framework)
        var repositorio = new ProductoRepositorioMemoria();
        repositorio.agregar(new Producto("LAP001", "Laptop", 1200.00));
        repositorio.agregar(new Producto("MOU002", "Mouse", 25.99));

        var servicio = new PedidoServicio(repositorio);

        var total = servicio.calcularTotal("LAP001", 2);
        System.out.println("Total: " + total + " euros");
    }
}
```

### Dependency Inversion

Las clases de alto nivel no deben depender de clases de bajo nivel. Ambas deben depender de abstracciones:

```java
// MAL: PedidoServicio depende directamente de ProductoRepositorioMemoria
public class PedidoServicioMal {
    private ProductoRepositorioMemoria repositorio = new ProductoRepositorioMemoria();
}

// BIEN: PedidoServicio depende de la interfaz ProductoRepositorio
public class PedidoServicioBien {
    private final ProductoRepositorio repositorio;

    public PedidoServicioBien(ProductoRepositorio repositorio) {
        this.repositorio = repositorio;
    }
}
```

---

## Errores Comunes

> Clases de 500+ lineas. Signo claro de multiples responsabilidades mezcladas.

> Metodos que hacen "y ademas". `guardarYEnviarEmailYGenerarFactura()` es tres responsabilidades en un metodo.

> Dependencias directas a implementaciones concretas. Siempre programa contra interfaces.

> El patron "God Object" (objeto dios): una clase que lo sabe y hace todo.

> Mezclar logica de negocio con codigo de infraestructura (SQL, HTTP, archivos) en la misma clase.

---

## Buenas Practicas

1. Programa contra interfaces, no contra implementaciones.
2. Inyeccion de dependencias por constructor.
3. Una clase = una responsabilidad. Si no puedes explicar que hace en una frase, dividela.
4. Metodos cortos (< 20 lineas).
5. Tell, Don't Ask: dile al objeto que haga algo, no le preguntes por sus datos.
6. Capas: dominio (datos puros) -> repositorio (acceso datos) -> servicio (logica) -> aplicacion (arranque).

---

## Conexiones

- [[05 - Modificadores de Acceso]] - Encapsulamiento
- [[19 - Separacion de Responsabilidades]] - Nivel basico de separacion
- [[20 - Javadoc y Documentacion]] - Documentar la arquitectura
- [[21 - Getters y Setters]] - Encapsulamiento
- [[25 - Paquetes y Organizacion]] - Organizar por capas

---

## Tags
`#java #fundamentos #separacion-responsabilidades #srp #solid #arquitectura #capas #inyeccion-dependencias`
