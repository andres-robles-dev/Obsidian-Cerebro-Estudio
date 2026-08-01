---
tags: [java, fundamentos, private, encapsulamiento, profundidad]
---

# 26 - Private en Profundidad

Private es el modificador de acceso mas restrictivo de Java. Solo la clase donde se declara puede verlo. Este archivo lo explora desde lo mas simple hasta lo mas profundo.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es private?

`private` significa que solo la clase donde esta declarado puede acceder a ello. Nadie mas. Ni otras clases del mismo paquete, ni subclases, ni nadie.

```java
public class CuentaBancaria {
    private double saldo;  // Solo CuentaBancaria puede ver saldo

    public void depositar(double cantidad) {
        if (cantidad > 0) {
            saldo = saldo + cantidad;
        }
    }

    public double obtenerSaldo() {
        return saldo;
    }
}
```

```java
public class Principal {
    public static void main(String[] args) {
        CuentaBancaria c = new CuentaBancaria();
        c.depositar(100);
        System.out.println(c.obtenerSaldo());  // OK, metodo public
        // c.saldo = 500;  // ERROR: saldo es private
    }
}
```

### La regla de oro

Los ATRIBUTOS siempre van `private`. Siempre. Sin excepcion hasta que sepas muy bien cuando no hacerlo.

```java
// BIEN:
public class Persona {
    private String nombre;
    private int edad;
}

// MAL:
public class Persona {
    public String nombre;  // Cualquiera puede cambiarlo sin control
    public int edad;
}
```

### Private en metodos

Los metodos que solo usa la clase internamente van `private`:

```java
public class ProcesadorPagos {
    public void procesar(double importe) {
        if (!validar(importe)) {
            return;
        }
        cobrar(importe);
        enviarConfirmacion(importe);
    }

    private boolean validar(double importe) {
        return importe > 0;
    }

    private void cobrar(double importe) {
        System.out.println("Cobrando " + importe);
    }

    private void enviarConfirmacion(double importe) {
        System.out.println("Confirmacion enviada");
    }
}
```

`validar`, `cobrar` y `enviarConfirmacion` son detalles internos. Nadie necesita saber que existen.

---

## NIVEL 2 - Mid

### Private en constructores

Los constructores `private` impiden que cualquiera cree objetos de esa clase. Se usa para:

**Singleton** (una sola instancia):

```java
public class ConexionBaseDatos {
    private static final ConexionBaseDatos INSTANCIA = new ConexionBaseDatos();

    private ConexionBaseDatos() {
        // Solo se llama una vez
        System.out.println("Conectando a base de datos...");
    }

    public static ConexionBaseDatos obtener() {
        return INSTANCIA;
    }

    public void ejecutarConsulta(String sql) {
        System.out.println("Ejecutando: " + sql);
    }
}
```

```java
// Uso:
ConexionBaseDatos db = ConexionBaseDatos.obtener();
db.ejecutarConsulta("SELECT * FROM usuarios");
// ConexionBaseDatos db2 = new ConexionBaseDatos();  // ERROR: constructor private
```

**Factory Method** (controlas como se crea):

```java
public class Usuario {
    private String nombre;
    private String email;

    private Usuario(String nombre, String email) {
        this.nombre = nombre;
        this.email = email;
    }

    public static Usuario crearConEmail(String nombre, String email) {
        if (!email.contains("@")) {
            throw new IllegalArgumentException("Email invalido");
        }
        return new Usuario(nombre, email);
    }

    public static Usuario crearInvitado(String nombre) {
        return new Usuario(nombre, "invitado@" + nombre.toLowerCase() + ".com");
    }
}
```

### Private en clases anidadas (inner classes)

Una clase interna puede acceder a los miembros `private` de la clase que la contiene:

```java
public class Agenda {
    private List<Contacto> contactos = new ArrayList<>();

    public void agregarContacto(String nombre, String telefono) {
        contactos.add(new Contacto(nombre, telefono));
    }

    private class Contacto {
        private String nombre;
        private String telefono;

        public Contacto(String nombre, String telefono) {
            this.nombre = nombre;
            this.telefono = telefono;
        }

        public void mostrar() {
            System.out.println(nombre + ": " + telefono);
            // Puede acceder a contactos (private de Agenda)
            System.out.println("Total contactos: " + contactos.size());
        }
    }
}
```

La inner class `Contacto` ve los `private` de `Agenda` porque es como si formara parte de la misma clase.

### Private vs package-private (default)

| Modificador | Lo ve la misma clase | Lo ve el mismo paquete | Lo ven subclases | Lo ve todo el mundo |
|-------------|:---:|:---:|:---:|:---:|
| `private` | Si | No | No | No |
| *default* | Si | Si | No | No |
| `protected` | Si | Si | Si | No |
| `public` | Si | Si | Si | Si |

```java
package modelo;

public class Ejemplo {
    private int a;     // Solo Ejemplo
    int b;             // Solo modelo (package-private)
    protected int c;   // modelo + subclases
    public int d;      // Todos
}
```

La diferencia clave: `private` ni siquiera las subclases lo ven. `package-private` (default) lo ven todas las clases del mismo paquete.

---

## NIVEL 3 - Senior

### Private en interfaces (Java 9+)

Desde Java 9, las interfaces pueden tener metodos `private` para compartir codigo entre metodos `default`:

```java
public interface Repositorio {

    Usuario buscarPorId(long id);

    default Usuario buscarOCrear(long id, String nombre) {
        log("Buscando usuario " + id);
        var encontrado = buscarPorId(id);
        if (encontrado != null) {
            log("Usuario encontrado");
            return encontrado;
        }
        log("Usuario no encontrado, creando...");
        return crear(id, nombre);
    }

    private Usuario crear(long id, String nombre) {
        log("Creando usuario " + nombre);
        return new Usuario(id, nombre);
    }

    private void log(String mensaje) {
        System.out.println("[LOG] " + mensaje);
    }
}
```

Estos metodos `private` solo existen para que los metodos `default` no repitan codigo. Nadie que implemente la interfaz los ve.

### Private en records

```java
public record Producto(String nombre, double precio) {
    // Los campos son automaticamente private final
    // No puedes cambiarlo. Siempre son private.

    private static final double IVA = 0.21;

    public double calcularPrecioConIVA() {
        return precio + (precio * IVA);
    }

    private void validarPrecio() {
        if (precio <= 0) {
            throw new IllegalArgumentException("Precio invalido: " + precio);
        }
    }

    public Producto {
        validarPrecio();
    }
}
```

En un `record`:
- Los campos son `private final` y no se puede cambiar
- Puedes tener metodos `private` normales
- Puedes tener constantes `private static final`

### Private en enums

Los constructores de los `enum` son `private` implicitamente. No puedes crear un enum con `new`:

```java
public enum EstadoPedido {
    PENDIENTE("En espera"),
    EN_PROCESO("Preparando"),
    ENVIADO("En camino"),
    ENTREGADO("Completado"),
    CANCELADO("Anulado");

    private final String descripcion;

    // Este constructor es private aunque no pongas private
    EstadoPedido(String descripcion) {
        this.descripcion = descripcion;
    }

    public String obtenerDescripcion() {
        return descripcion;
    }
}
```

```java
// EstadoPedido e = new EstadoPedido("algo");  // ERROR
EstadoPedido e = EstadoPedido.PENDIENTE;        // OK
```

### Private y herencia

`private` no se hereda. Una subclase no puede ver los miembros `private` de la superclase:

```java
public class Animal {
    private String nombre;
    private int edad;

    public Animal(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public void mostrar() {
        System.out.println(nombre + ", " + edad + " anios");
    }
}

public class Perro extends Animal {
    public Perro(String nombre, int edad) {
        super(nombre, edad);
    }

    public void ladrar() {
        // System.out.println(nombre);  // ERROR: nombre es private en Animal
        mostrar();  // OK: mostrar() es public y usa nombre internamente
        System.out.println("Guau!");
    }
}
```

Tampoco se puede sobreescribir un metodo `private`:

```java
public class Base {
    private void hacerAlgo() {
        System.out.println("Base");
    }
}

public class Hija extends Base {
    // Esto NO es sobreescribir. Es un metodo NUEVO y propio de Hija
    private void hacerAlgo() {
        System.out.println("Hija");
    }
}
```

### Private con reflection

Tecnicamente se puede acceder a `private` desde fuera usando `setAccessible(true)`:

```java
import java.lang.reflect.Field;

public class DemoReflection {
    public static void main(String[] args) throws Exception {
        Persona p = new Persona("Ana", 30);

        Field campoNombre = Persona.class.getDeclaredField("nombre");
        campoNombre.setAccessible(true);  // Saltamos el private

        String nombre = (String) campoNombre.get(p);
        System.out.println(nombre);  // "Ana"

        campoNombre.set(p, "Maria");  // Modificamos private
    }
}

class Persona {
    private String nombre;
    private int edad;

    public Persona(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }
}
```

**Esto NO deberias hacerlo.** Rompe el encapsulamiento. Solo tiene sentido en:
- Frameworks como Spring o Hibernate
- Librerias de testing como JUnit
- Serializacion personalizada

En codigo de aplicacion normal, jamas uses `setAccessible(true)`.

### Private y modulos (Java 9+)

Con el sistema de modulos, puedes controlar que paquetes se ven desde fuera. Lo que no se exporta, es como si fuera `private` a nivel de modulo:

```java
// module-info.java
module com.miempresa.musica {
    // Solo esto es visible fuera del modulo
    exports com.miempresa.musica.modelo;
    exports com.miempresa.musica.servicio;

    // com.miempresa.musica.repositorio no se exporta
    // Es "private" a nivel de modulo
}
```

```java
// Dentro del modulo, la clase es public
// Pero fuera del modulo, no existe
package com.miempresa.musica.repositorio;

public class RepositorioUsuarios {
    // Esta clase solo es accesible dentro del modulo
}
```

---

# METODO PROFUNDO (Curso completo de private)

## 1. Que es exactamente private?

### Definicion del lenguaje

Segun la especificacion de Java (JLS), `private` significa que el miembro solo es accesible desde el cuerpo de la **clase** donde se declara. Ni siquiera desde clases hijas o clases del mismo paquete.

```java
public class Ejemplo {
    private int numero;

    public void metodo() {
        System.out.println(numero);  // OK: misma clase
    }
}

class OtraClase {
    void test() {
        Ejemplo e = new Ejemplo();
        // System.out.println(e.numero);  // ERROR: otra clase
    }
}
```

### Por que importa?

Porque asi puedes cambiar la implementacion interna sin que nadie se entere. Los `private` son detalles que pueden cambiar; los `public` son contratos que deben mantenerse.

---

## 2. Private en atributos (encapsulamiento)

### La forma correcta

```java
public class CuentaBancaria {
    private double saldo;          // Nadie toca esto directamente
    private String titular;
    private String numeroCuenta;

    public void depositar(double cantidad) {
        if (cantidad > 0) {
            saldo += cantidad;
        }
    }

    public boolean retirar(double cantidad) {
        if (cantidad > 0 && saldo >= cantidad) {
            saldo -= cantidad;
            return true;
        }
        return false;
    }

    public double obtenerSaldo() {
        return saldo;
    }
}
```

### Copia defensiva en atributos private

Cuando un atributo es una coleccion o array, devolver la referencia directamente permite que la modifiquen:

```java
public class Grupo {
    private List<String> miembros = new ArrayList<>();

    public void agregarMiembro(String nombre) {
        miembros.add(nombre);
    }

    // MAL: devuelves la lista interna, pueden modificarla
    public List<String> obtenerMiembrosInseguro() {
        return miembros;
    }

    // BIEN: devuelves copia inmutable
    public List<String> obtenerMiembros() {
        return List.copyOf(miembros);
    }
}
```

```java
Grupo g = new Grupo();
g.agregarMiembro("Ana");

// Con la version insegura:
List<String> lista = g.obtenerMiembrosInseguro();
lista.add("Luis");  // Modificaron la lista interna del grupo!

// Con la version segura:
List<String> lista = g.obtenerMiembros();
// lista.add("Luis");  // ERROR: es inmutable
```

### Por que importa?

El `private` en atributos no es solo "ocultar". Es "controlar". Tu decides como y cuando se modifican los datos. Sin `private`, cualquiera puede poner tu objeto en un estado invalido.

---

## 3. Private en metodos (ayuda interna)

### Para que sirven

Los metodos `private` existen para extraer logica repetida y mantener el codigo limpio:

```java
public class ValidadorDocumentos {
    public boolean validarDNI(String dni) {
        if (esNuloOVacio(dni)) return false;
        if (!tieneLongitudCorrecta(dni, 9)) return false;
        if (!contieneSoloDigitosYLetra(dni)) return false;
        return letraCoincide(dni);
    }

    public boolean validarPasaporte(String pasaporte) {
        if (esNuloOVacio(pasaporte)) return false;
        if (!tieneLongitudCorrecta(pasaporte, 6)) return false;
        return soloLetrasMayusculas(pasaporte);
    }

    // Metodos private: solo usados internamente
    private boolean esNuloOVacio(String texto) {
        return texto == null || texto.isBlank();
    }

    private boolean tieneLongitudCorrecta(String texto, int longitud) {
        return texto.length() == longitud;
    }

    private boolean contieneSoloDigitosYLetra(String texto) {
        String digitos = texto.substring(0, 8);
        char letra = texto.charAt(8);
        return digitos.matches("\\d{8}") && Character.isLetter(letra);
    }

    private boolean letraCoincide(String dni) {
        // Logica de validacion de letra del DNI
        return true;
    }

    private boolean soloLetrasMayusculas(String texto) {
        return texto.matches("[A-Z]+");
    }
}
```

### Metodo privado que llama a otro privado

```java
public class ProcesadorArchivos {
    public void procesar(String ruta) {
        var contenido = leerArchivo(ruta);
        var datos = parsear(contenido);
        guardar(datos);
    }

    private String leerArchivo(String ruta) {
        var rutaPath = Path.of(ruta);
        if (!rutaPath.toFile().exists()) {
            return "";
        }
        return Files.readString(rutaPath);
    }

    private List<String> parsear(String contenido) {
        return contenido.lines()
            .filter(linea -> !linea.isBlank())
            .map(String::trim)
            .toList();
    }

    private void guardar(List<String> datos) {
        datos.forEach(System.out::println);
    }
}
```

### Por que importa?

Los metodos `private` te permiten:
- Dividir problemas grandes en piezas pequenas
- Reutilizar codigo dentro de la misma clase
- Ocultar detalles de implementacion
- Probar la logica a traves de los metodos publicos que los usan

---

## 4. Private en constructores

### Singleton clasico

Cuando quieres que solo exista UNA instancia de una clase:

```java
public class GestorConfiguracion {
    private static final GestorConfiguracion INSTANCIA = new GestorConfiguracion();
    private Properties propiedades;

    private GestorConfiguracion() {
        propiedades = new Properties();
        try (var input = Files.newInputStream(Path.of("config.properties"))) {
            propiedades.load(input);
        } catch (IOException e) {
            System.out.println("Usando configuracion por defecto");
        }
    }

    public static GestorConfiguracion obtener() {
        return INSTANCIA;
    }

    public String get(String clave) {
        return propiedades.getProperty(clave, "");
    }
}
```

### Builder pattern

Cuando el constructor tiene muchos parametros opcionales:

```java
public class Pedido {
    private int id;
    private String cliente;
    private List<String> productos;
    private String direccionEnvio;
    private boolean urgente;

    private Pedido(Builder builder) {
        this.id = builder.id;
        this.cliente = builder.cliente;
        this.productos = List.copyOf(builder.productos);
        this.direccionEnvio = builder.direccionEnvio;
        this.urgente = builder.urgente;
    }

    public static class Builder {
        private int id;
        private String cliente;
        private List<String> productos = new ArrayList<>();
        private String direccionEnvio = "";
        private boolean urgente = false;

        public Builder(int id, String cliente) {
            this.id = id;
            this.cliente = cliente;
        }

        public Builder conDireccion(String direccion) {
            this.direccionEnvio = direccion;
            return this;
        }

        public Builder conProducto(String producto) {
            this.productos.add(producto);
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
```

```java
// Uso:
Pedido pedido = new Pedido.Builder(1, "Ana")
    .conDireccion("Calle Mayor 10")
    .conProducto("Laptop")
    .conProducto("Mouse")
    .urgente()
    .construir();
```

### Utility class (clase de solo metodos estaticos)

```java
public class UtilidadesMatematicas {
    private UtilidadesMatematicas() {
        // Constructor private para que nadie instancie esta clase
        throw new UnsupportedOperationException("Clase de utilidades");
    }

    public static double media(double... numeros) {
        return Arrays.stream(numeros).average().orElse(0);
    }

    public static double maximo(double... numeros) {
        return Arrays.stream(numeros).max().orElseThrow();
    }
}
```

```java
// UtilidadesMatematicas u = new UtilidadesMatematicas();  // ERROR: constructor private
double m = UtilidadesMatematicas.media(1, 2, 3, 4);        // OK: metodo static
```

### Por que importa?

El constructor `private` es la forma mas limpia de controlar como y cuando se crean objetos. Sin el, cualquiera podria crear instancias de clases que no deberian instanciarse.

---

## 5. Private en clases anidadas

### Inner class (no static)

Una inner class (`class` dentro de otra, sin `static`) puede ver todos los `private` de la clase contenedora:

```java
public class Coche {
    private String marca;
    private String modelo;
    private boolean encendido = false;
    private Motor motor;

    public Coche(String marca, String modelo) {
        this.marca = marca;
        this.modelo = modelo;
        this.motor = new Motor(0);
    }

    public void arrancar() {
        motor.encender();
        encendido = true;
    }

    // Inner class: puede acceder a private de Coche
    private class Motor {
        private int revoluciones;

        public Motor(int revoluciones) {
            this.revoluciones = revoluciones;
        }

        public void encender() {
            revoluciones = 1000;
            System.out.println("Motor de " + modelo + " encendido");
            // Puede acceder a 'modelo' que es private de Coche
        }
    }
}
```

### Static nested class

Una clase anidada `static` NO puede acceder a los `private` de la contenedora directamente:

```java
public class Factura {
    private int numero;
    private double total;

    // Static nested class: NO ve los private de Factura
    public static class LineaFactura {
        private String producto;
        private double precio;
        private int cantidad;

        public LineaFactura(String producto, double precio, int cantidad) {
            this.producto = producto;
            this.precio = precio;
            this.cantidad = cantidad;
        }

        public double subtotal() {
            return precio * cantidad;
        }
    }
}
```

### Por que importa?

Las inner classes son la unica excepcion donde algo fuera de la clase puede ver sus `private`. Es util para modelar partes que realmente forman parte de la clase (como `Motor` es parte de `Coche`).

---

## 6. Private en interfaces (Java 9+)

### Codigo compartido entre metodos default

```java
public interface Logger {
    default void info(String mensaje) {
        formatearYMostrar("INFO", mensaje);
    }

    default void error(String mensaje) {
        formatearYMostrar("ERROR", mensaje);
    }

    default void debug(String mensaje) {
        if (isDebug()) {
            formatearYMostrar("DEBUG", mensaje);
        }
    }

    private void formatearYMostrar(String nivel, String mensaje) {
        var linea = "[%s] %s - %s".formatted(
            LocalDateTime.now(),
            nivel,
            mensaje
        );
        System.out.println(linea);
    }

    private boolean isDebug() {
        return "true".equalsIgnoreCase(System.getenv("DEBUG"));
    }
}
```

```java
public class MiServicio implements Logger {
    public void hacerAlgo() {
        info("Empezando proceso");
        // ...
        error("Algo fallo");
    }
}
```

La clase `MiServicio` solo ve los metodos `default`. No ve `formatearYMostrar` ni `isDebug`. Son detalles internos de la interfaz.

### Por que importa?

Sin metodos `private` en interfaces, los metodos `default` tenian que repetir codigo o usar metodos `static` (que son publicos). Ahora puedes tener ayuda interna sin contaminar la API.

---

## 7. Private en records

### Todo es private final

```java
public record Direccion(String calle, String ciudad, int codigoPostal) {
    // Los campos calle, ciudad, codigoPostal son:
    // - private: solo accesibles dentro del record
    // - final: no se pueden reasignar

    // Los getters son automaticos: direccion.calle(), direccion.ciudad()
    // Pero NO son campos publicos. Son metodos publicos que devuelven el valor private.

    public boolean esValida() {
        return calle != null && !calle.isBlank()
            && ciudad != null && !ciudad.isBlank()
            && codigoPostal >= 1000 && codigoPostal <= 52999;
    }
}
```

```java
Direccion d = new Direccion("Calle Mayor", "Madrid", 28001);
System.out.println(d.calle());          // OK: metodo public
// System.out.println(d.calle);         // ERROR: el campo es private
// d.calle = "Otra calle";             // ERROR: el campo es final
```

Puedes tener metodos `private` en records:

```java
public record Email(String direccion) {
    public Email {
        if (!esValido(direccion)) {
            throw new IllegalArgumentException("Email invalido: " + direccion);
        }
    }

    private static boolean esValido(String email) {
        return email != null && email.contains("@") && email.contains(".");
    }

    public String dominio() {
        return extraerDominio(direccion);
    }

    private String extraerDominio(String email) {
        return email.substring(email.indexOf("@") + 1);
    }
}
```

### Por que importa?

Los records simplifican el encapsulamiento: los campos son `private final` sin que tu escribas una linea. El acceso es solo por metodos, nunca directo.

---

## 8. Private en enums

### Constructor private implicito

En los `enum`, el constructor es `private` aunque no pongas la palabra:

```java
public enum Talla {
    S("Pequena", 1),
    M("Mediana", 2),
    L("Grande", 3),
    XL("Extra grande", 4);

    private final String descripcion;
    private final int orden;

    // Este constructor es private automaticamente
    Talla(String descripcion, int orden) {
        this.descripcion = descripcion;
        this.orden = orden;
    }

    public String obtenerDescripcion() {
        return descripcion;
    }

    public boolean esMayorQue(Talla otra) {
        return this.orden > otra.orden;
    }
}
```

### Campos y metodos private en enum

```java
public enum DiaSemana {
    LUNES(true),
    MARTES(true),
    MIERCOLES(true),
    JUEVES(true),
    VIERNES(true),
    SABADO(false),
    DOMINGO(false);

    private final boolean laborable;
    private static final List<DiaSemana> FINDE = List.of(SABADO, DOMINGO);

    DiaSemana(boolean laborable) {
        this.laborable = laborable;
    }

    public boolean esLaborable() {
        return laborable;
    }

    public static boolean esFinde(DiaSemana dia) {
        return dia == SABADO || dia == DOMINGO;
    }

    private static void validarDia(DiaSemana dia) {
        if (dia == null) {
            throw new IllegalArgumentException("El dia no puede ser null");
        }
    }
}
```

### Por que importa?

Los enums son la unica excepcion donde el constructor `private` viene por defecto. No puedes hacer `new Talla()`. Solo existen las constantes que tu defines.

---

## 9. Private vs package-private

### Cuando usar cada uno

```java
package modelo;

public class ServicioUsuarios {
    // private: solo esta clase
    private ValidadorEmail validador;

    // package-private: lo ven clases del mismo paquete (modelo)
    RepositorioUsuarios repositorio;

    // public: lo ve todo el mundo
    public void registrar(String email) {
        // ...
    }
}

class RepositorioUsuarios {
    // package-private: solo visible en modelo (no es public)
    void guardar() { }
}

class ValidadorEmail {
    // private: solo ValidadorEmail
    private String patron = "^[A-Za-z0-9+_.-]+@(.+)$";

    boolean esValido(String email) {  // package-private
        return email.matches(patron);
    }
}
```

### La regla practica

```java
public class ClasePublica {
    // Atributos: siempre private
    private String nombre;

    // Constantes: pueden ser public (si son parte de la API)
    public static final int MAXIMO = 100;

    // Metodos public: los que forman parte del contrato
    public void hacerAlgo() { }

    // Metodos private: ayuda interna
    private void validar() { }

    // package-private: para testing interno del paquete
    void metodoInterno() { }
}
```

### Por que importa?

- Usa `private` cuando solo la clase debe saberlo (casi siempre)
- Usa `package-private` (default) cuando necesitas que otras clases del mismo paquete accedan pero no quieres que sea parte de la API publica
- Usa `public` solo cuando realmente necesitas que sea parte del contrato
- Usa `protected` solo si tienes herencia y las subclases necesitan acceder

---

## 10. Private y herencia

### Los private no se heredan

```java
public class Vehiculo {
    private String marca;
    private String modelo;
    private int velocidad;

    public Vehiculo(String marca, String modelo) {
        this.marca = marca;
        this.modelo = modelo;
        this.velocidad = 0;
    }

    // La subclase accede a traves de metodos public/protected
    public void acelerar(int incremento) {
        velocidad += incremento;
    }

    public void mostrarEstado() {
        System.out.println(marca + " " + modelo + " - " + velocidad + " km/h");
    }
}

public class Coche extends Vehiculo {
    private int numPuertas;

    public Coche(String marca, String modelo, int numPuertas) {
        super(marca, modelo);
        this.numPuertas = numPuertas;
    }

    public void mostrarCompleto() {
        // System.out.println(marca);     // ERROR: marca es private en Vehiculo
        // System.out.println(velocidad); // ERROR: velocidad es private en Vehiculo
        mostrarEstado();  // OK: mostrarEstado es public
        System.out.println("Puertas: " + numPuertas);
    }
}
```

### Los metodos private no se sobreescriben

```java
public class Base {
    private void hacer() {
        System.out.println("Base.hacer()");
    }

    public void ejecutar() {
        hacer();  // Llama a Base.hacer(), siempre
    }
}

public class Derivada extends Base {
    // Esto NO sobreescribe hacer(). Es un metodo NUEVO.
    private void hacer() {
        System.out.println("Derivada.hacer()");
    }
}

public class Demo {
    public static void main(String[] args) {
        Derivada d = new Derivada();
        d.ejecutar();  // Imprime "Base.hacer()", NO "Derivada.hacer()"
    }
}
```

Aunque los dos metodos se llamen `hacer()`, el de `Base` es private y no se ve en `Derivada`. El `hacer()` de `Derivada` es otro metodo completamente distinto.

### Por que importa?

Si quieres que una subclase pueda acceder o sobreescribir, usa `protected` en vez de `private`. Si quieres que nadie (ni subclases) acceda, usa `private`.

---

## 11. Private con reflection (por que no usarlo)

### Como se hace (aunque no deberias)

```java
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class DemoReflection {
    public static void main(String[] args) throws Exception {
        var usuario = new UsuarioPrivado("Ana", "clave123");

        // Acceder a campo private
        Field campoContrasenia = UsuarioPrivado.class
            .getDeclaredField("contrasenia");
        campoContrasenia.setAccessible(true);

        String contrasenia = (String) campoContrasenia.get(usuario);
        System.out.println("Contrasenia: " + contrasenia);

        // Modificar campo private
        campoContrasenia.set(usuario, "nuevaClave");

        // Llamar a metodo private
        Method metodo = UsuarioPrivado.class
            .getDeclaredMethod("encriptar", String.class);
        metodo.setAccessible(true);
        String resultado = (String) metodo.invoke(usuario, "test");
        System.out.println("Encriptado: " + resultado);
    }
}

class UsuarioPrivado {
    private String nombre;
    private String contrasenia;

    public UsuarioPrivado(String nombre, String contrasenia) {
        this.nombre = nombre;
        this.contrasenia = contrasenia;
    }

    private String encriptar(String texto) {
        return "***" + texto + "***";
    }
}
```

### Por que NO hacerlo

1. Rompe el encapsulamiento. El `private` deja de tener sentido.
2. El codigo puede romperse si la implementacion interna cambia.
3. Puede lanzar excepciones en entornos con SecurityManager.
4. Es mas lento que el acceso normal.
5. El codigo es dificil de leer y mantener.

### Cuando esta justificado

```java
// En un framework ORM como Hibernate:
// Necesita acceder a campos private para asignar valores desde la BD.

// En una libreria de testing:
// JUnit usa reflection para invocar metodos private de test.

// En serializacion personalizada:
// Jackson/Gson usan reflection para leer/escribir campos private.

// En tu codigo de aplicacion: NUNCA.
```

### Por que importa?

El `private` en Java es una barrera, pero no infranqueable. La cuestion no es tecnica (se puede saltar), sino de diseno: si necesitas acceder a algo `private` desde fuera, el diseno esta mal. Arregla el diseno, no saltes la barrera.

---

## 12. Private y modulos (Java 9+)

### El modulo como encapsulamiento fuerte

El sistema de modulos permite que clases `public` se comporten como `private` fuera del modulo:

```java
// Module: com.miempresa.core
module com.miempresa.core {
    exports com.miempresa.core.api;
    // com.miempresa.core.internal NO se exporta
}
```

```java
// com/miempresa/core/api/ServicioPublico.java
package com.miempresa.core.api;

public class ServicioPublico {
    public void hacerAlgo() {
        var interno = new AyudanteInterno();
        interno.ayudar();
    }
}
```

```java
// com/miempresa/core/internal/AyudanteInterno.java
package com.miempresa.core.internal;

// Esta clase es public, pero fuera del modulo... no existe
public class AyudanteInterno {
    public void ayudar() {
        System.out.println("Ayudando...");
    }
}
```

```java
// Otro modulo: com.miempresa.app
import com.miempresa.core.api.ServicioPublico;  // OK
// import com.miempresa.core.internal.AyudanteInterno;  // ERROR: no se exporta
```

### Por que importa?

Es como un `private` a nivel de modulo. Las clases `public` dentro de paquetes no exportados son "private del modulo". Nadie fuera puede verlas ni usarlas.

---

## 13. Private en testing (debate)

### El problema

Los metodos `private` no se pueden llamar desde un test directamente:

```java
public class Calculadora {
    public int sumar(int a, int b) {
        return a + b;
    }

    private int doblar(int n) {
        return n * 2;
    }
}
```

```java
// Test:
@Test
void testDoblar() {
    Calculadora calc = new Calculadora();
    // calc.doblar(5);  // ERROR: doblar es private
}
```

### Soluciones

**Solucion 1: Testea a traves de metodos publicos**

```java
public class Calculadora {
    public int calcular(int a, int b, String operacion) {
        return switch (operacion) {
            case "sumar" -> a + b;
            case "doblar" -> doblar(a);
            default -> 0;
        };
    }

    private int doblar(int n) {
        return n * 2;
    }
}

// Test:
@Test
void testDoblarATravesDeCalcular() {
    var calc = new Calculadora();
    assertEquals(10, calc.calcular(5, 0, "doblar"));
}
```

**Solucion 2: Extraer a otra clase con visibilidad package-private**

```java
// Esto ya no es private, es package-private
class CalculadoraHelper {
    int doblar(int n) {
        return n * 2;
    }
}

public class Calculadora {
    private CalculadoraHelper helper = new CalculadoraHelper();

    public int sumar(int a, int b) {
        return a + b;
    }

    public int doblarPublico(int n) {
        return helper.doblar(n);
    }
}

// El test esta en el mismo paquete, puede ver helper.doblar()
```

**Solucion 3: Reflection (si estas desesperado, pero no lo hagas)**

```java
Method metodo = Calculadora.class.getDeclaredMethod("doblar", int.class);
metodo.setAccessible(true);
int resultado = (int) metodo.invoke(new Calculadora(), 5);
```

### La regla

Si un metodo es tan complejo que necesita test propio, probablemente no deberia estar como `private` dentro de otra clase. Extraelo a su propia clase con visibilidad package-private.

### Por que importa?

Los `private` no se testean directamente porque son detalles de implementacion. Se testean indirectamente a traves de los metodos publicos que los usan. Si necesitas testear un `private` directamente, el diseno necesita revision.

---

## Errores Comunes

> Poner atributos `public` porque "es mas comodo". Cada atributo publico es un punto de fallo: cualquiera puede poner tu objeto en estado invalido sin que te enteres.

> Usar `private` en metodos y luego querer acceder desde tests. Disena pensando en testabilidad: los metodos complejos pueden ir a clases package-private testeables.

> Pensar que `private` protege contra todo. Tecnicamente se puede saltar con reflection, pero la seguridad no es el objetivo de `private`: el objetivo es encapsulamiento y claridad de diseno.

> No usar `private` en metodos de ayuda interna. Si un metodo solo lo usa la propia clase, hazlo `private`. No contamines la API publica.

> Confundir `private` con package-private. `private` es mas restrictivo. Package-private (sin modificador) permite acceso a todas las clases del mismo paquete.

> Crear getters y setters publicos para todo atributo private. Si un atributo solo se usa internamente, no necesita ni getter ni setter.

> Olvidar que las inner classes ven los private de la contenedora. No des por sentado que nadie ve tus private: las inner classes si los ven.

---

## Buenas Practicas

1. **Atributos siempre `private`**. Sin excepcion. Acceso mediante getters (y setters solo si es necesario).
2. **Metodos de ayuda interna `private`**. Si solo lo usas dentro de la clase, no lo expongas.
3. **Constructores `private` para patrones de creacion**. Singleton, Factory Method, Builder y Utility Class.
4. **No uses reflection para saltar `private`**. Si necesitas acceder, el diseno necesita cambiar.
5. **`private` en interfaces para compartir codigo** entre metodos default (Java 9+).
6. **Records**: aprovecha que los campos son automaticamente `private final`.
7. **Modulos**: usa la exportacion selectiva como "private a nivel de modulo".
8. **No todo necesita getter/setter**. Un atributo private interno no necesita exponerse.
9. **Copia defensiva** en getters de colecciones y arrays.
10. **Package-private para testing** en vez de hacer publico algo que deberia ser private.

---

## Conexiones

- [[05 - Modificadores de Acceso]] - Los 4 modificadores en conjunto
- [[06 - Atributos y Campos]] - Atributos private con getters
- [[07 - Constructores y this]] - Constructores private
- [[08 - Instanciacion y new]] - Factory methods con constructor private
- [[13 - Static vs Instancia]] - Singleton con constructor private
- [[19 - Separacion de Responsabilidades]] - Encapsulamiento y responsabilidades
- [[21 - Getters y Setters]] - Acceso controlado a atributos private
- [[22 - Separacion de Responsabilidades]] - Inyeccion de dependencias y encapsulamiento
- [[25 - Paquetes y Organizacion]] - Modulos y exportacion

---

## Tags
`#java #fundamentos #private #encapsulamiento #profundidad #modificadores #acceso`
