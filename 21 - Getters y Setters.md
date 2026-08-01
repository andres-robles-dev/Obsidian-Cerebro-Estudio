---
tags: [java, fundamentos, getters, setters, encapsulamiento, javaBeans, records]
---

# 21 - Getters y Setters

---

## NIVEL JUNIOR

### Por que hacen falta?

Los atributos deben ser `private` para protegerlos. Pero... como accedes a ellos desde fuera? Con getters y setters.

```java
public class Persona {
    private String nombre;

    // Getter: obtener el valor
    public String getNombre() {
        return nombre;
    }

    // Setter: cambiar el valor
    public void setNombre(String nombre) {
        this.nombre = nombre;
    }
}
```

### Como se usa

```java
Persona p = new Persona();
p.setNombre("Ana");                    // Escribir
System.out.println(p.getNombre());     // Leer
```

### Convencion de nombres

| Tipo de atributo | Getter | Setter |
|-----------------|--------|--------|
| `String nombre` | `getNombre()` | `setNombre(String)` |
| `int edad` | `getEdad()` | `setEdad(int)` |
| `boolean activo` | `isActivo()` | `setActivo(boolean)` |

Para `boolean`, el getter se llama `isX()` en vez de `getX()`.

---

## NIVEL MID

### Validacion en setters

Lo bueno de los setters es que puedes controlar **que valores son validos**:

```java
public class CuentaBancaria {
    private double saldo;
    private String titular;

    public void setSaldo(double saldo) {
        if (saldo < 0) {
            throw new IllegalArgumentException("El saldo no puede ser negativo");
        }
        this.saldo = saldo;
    }

    public void setTitular(String titular) {
        if (titular == null || titular.isBlank()) {
            throw new IllegalArgumentException("El titular es obligatorio");
        }
        this.titular = titular.trim();
    }

    public double getSaldo() {
        return saldo;
    }

    public String getTitular() {
        return titular;
    }
}
```

### Getter que devuelve copia (copia defensiva)

Cuando tienes una lista o array, devolverla directamente permite que la modifiquen desde fuera:

```java
import java.util.*;

public class Grupo {
    private List<String> miembros = new ArrayList<>();

    public void agregarMiembro(String nombre) {
        miembros.add(nombre);
    }

    // MAL: devuelve la lista original (pueden modificarla fuera)
    public List<String> getMiembrosInseguro() {
        return miembros;
    }

    // BIEN: devuelve una copia inmutable
    public List<String> getMiembros() {
        return List.copyOf(miembros);
    }
}
```

### Setter con logica

```java
public class Producto {
    private String nombre;
    private double precio;

    public void setPrecio(double precio) {
        if (precio <= 0) {
            throw new IllegalArgumentException("El precio debe ser positivo");
        }
        this.precio = Math.round(precio * 100.0) / 100.0;  // Redondear a 2 decimales
    }
}
```

---

## NIVEL SENIOR

### Records: getters automaticos

Los `record` tienen getters incorporados sin escribirlos:

```java
public record Usuario(String nombre, String email, boolean activo) {
    // Los getters son automaticos: usuario.nombre(), usuario.email(), usuario.activo()
    // No hay setters: el record es inmutable
}

public class Demo {
    public static void main(String[] args) {
        var usuario = new Usuario("Ana", "ana@email.com", true);

        System.out.println(usuario.nombre());   // getter automatico
        System.out.println(usuario.email());    // getter automatico
        System.out.println(usuario.activo());   // isActivo() pero se llama activo()
    }
}
```

### Objetos inmutables sin setters

Para objetos que no deben cambiar despues de creados:

```java
public class Configuracion {
    private final String host;
    private final int puerto;
    private final boolean debug;

    public Configuracion(String host, int puerto, boolean debug) {
        this.host = host;
        this.puerto = puerto;
        this.debug = debug;
    }

    // Solo getters, NO setters
    public String getHost() { return host; }
    public int getPuerto() { return puerto; }
    public boolean isDebug() { return debug; }
}
```

### Builder con getters para datos opcionales

```java
public class PedidoBuilder {
    private int id;
    private String cliente;
    private String direccion;
    private boolean urgente;
    private List<String> productos = new ArrayList<>();

    public PedidoBuilder conId(int id) {
        this.id = id;
        return this;
    }

    public PedidoBuilder paraCliente(String cliente) {
        this.cliente = cliente;
        return this;
    }

    public PedidoBuilder urgente() {
        this.urgente = true;
        return this;
    }

    public Pedido construir() {
        // Validar datos obligatorios
        if (cliente == null || cliente.isBlank()) {
            throw new IllegalStateException("Cliente obligatorio");
        }
        return new Pedido(id, cliente, direccion, urgente, productos);
    }
}
```

### CopyOf en vez de getter mutable

```java
public class Almacen {
    private final Map<String, Integer> stock = new HashMap<>();

    public void agregarProducto(String codigo, int cantidad) {
        stock.merge(codigo, cantidad, Integer::sum);
    }

    // Devuelve copia inmutable del mapa interno
    public Map<String, Integer> obtenerStock() {
        return Map.copyOf(stock);
    }
}
```

---

## Errores Comunes

> Poner getters y setters en records. Los records ya tienen getters y no tienen setters porque son inmutables.

> Getter que devuelve la coleccion interna directamente. Permite que modifiquen la lista desde fuera. Usa `List.copyOf()` o `Collections.unmodifiableList()`.

> Setter sin validacion. Si aceptas cualquier valor, el objeto puede quedar en estado invalido.

> Getter y setter para todo. No todos los atributos necesitan getter y setter. Algunos son solo internos.

> Poner logica pesada en getters. Los getters deberian ser rapidos y sin efectos secundarios.

---

## Buenas Practicas

1. Atributos `private`, acceso por getters y setters.
2. Getters para `boolean`: `isActivo()` en vez de `getActivo()`.
3. Valida en setters, no dejes el objeto en estado invalido.
4. Copia defensiva para colecciones en getters: `List.copyOf(miembros)`.
5. Prefiere `record` para datos simples: getters ya incluidos, sin setters (inmutable).
6. No expongas arrays mutables. Devuelve copias o `Arrays.copyOf()`.
7. Considera objetos inmutables sin setters cuando el estado no deba cambiar.

---

## Conexiones

- [[05 - Modificadores de Acceso]] - Atributos private con getters publicos
- [[06 - Atributos y Campos]] - Campos final y encapsulamiento
- [[07 - Constructores y this]] - Constructor con parametros vs setters
- [[08 - Instanciacion y new]] - Creacion de objetos encapsulados
- [[18 - Convenciones de Nombrado]] - Convencion getX/isX/setX
- [[19 - Separacion de Responsabilidades]] - Encapsulamiento como separacion

---

## Tags
`#java #fundamentos #getters #setters #encapsulamiento #javaBeans #records #inmutabilidad`
