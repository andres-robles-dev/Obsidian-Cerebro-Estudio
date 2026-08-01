---
tags: [java, fundamentos, static, clase, memoria, metodos-estaticos, campos-estaticos]
---

# 28 - Static en Profundidad

`static` significa que algo pertenece a la CLASE, no a las instancias. Existe una sola vez y se comparte entre todos los objetos. Este archivo lo explora desde lo mas simple hasta lo mas profundo.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es static?

`static` quiere decir "de la clase". Un miembro `static` existe aunque no hayas creado ningun objeto. Hay una sola copia para todos.

### Static en campos

```java
public class Alumno {
    String nombre;           // Cada alumno tiene su nombre
    static int total = 0;    // Todos los alumnos comparten este contador

    public Alumno(String nombre) {
        this.nombre = nombre;
        total++;  // Incrementa el contador compartido
    }
}
```

```java
Alumno a1 = new Alumno("Ana");
Alumno a2 = new Alumno("Luis");
Alumno a3 = new Alumno("Carlos");

System.out.println(Alumno.total);  // 3 (compartido entre todos)
System.out.println(a1.nombre);     // "Ana" (propio de a1)
System.out.println(a2.nombre);     // "Luis" (propio de a2)
```

`total` es uno solo. Todos los alumnos lo comparten. `nombre` es de cada alumno. Cada uno tiene el suyo.

### Static en metodos

Un metodo `static` se llama sin crear un objeto:

```java
public class Calculos {
    public static int sumar(int a, int b) {
        return a + b;
    }

    public static double media(double a, double b) {
        return (a + b) / 2;
    }
}
```

```java
int resultado = Calculos.sumar(5, 3);  // No hace falta new
System.out.println(resultado);  // 8
```

Los metodos `static` no pueden acceder a `this` ni a campos de instancia porque no hay objeto.

### La regla simple

- Sin `static` = necesita un objeto (instancia)
- Con `static` = se usa directamente desde la clase

---

## NIVEL 2 - Mid

### Bloques static

Se ejecutan UNA VEZ cuando la clase se carga en memoria:

```java
public class Configuracion {
    public static final String NOMBRE_APP;
    public static final int PUERTO;
    public static final List<String> PERMISOS;

    static {
        System.out.println("Cargando configuracion...");
        NOMBRE_APP = "MiAplicacion";
        PUERTO = Integer.getInteger("app.puerto", 8080);
        PERMISOS = List.of("lectura", "escritura", "admin");
    }
}
```

El bloque `static` se ejecuta antes de que nadie pueda usar la clase.

### Constantes static final

Las constantes se escriben con `static final` y nombre en `UPPER_SNAKE_CASE`:

```java
public class Constantes {
    public static final double IVA = 0.21;
    public static final int MAX_INTENTOS = 3;
    public static final String VERSION = "2.0.0";
    public static final LocalDate FECHA_LANZAMIENTO = LocalDate.of(2026, 3, 15);
}
```

### Acceso correcto

```java
public class Demo {
    public static int contador = 0;

    public static void sumar() {
        contador++;
    }

    public void metodoInstancia() {
        // Los metodos de instancia SI pueden ver static
        contador++;
        sumar();
    }
}
```

```java
// Acceso correcto: por el nombre de la CLASE
Demo.contador++;
Demo.sumar();

// Acceso INCORRECTO (aunque funciona):
Demo d = new Demo();
d.contador++;  // Warning: The static field should be accessed in a static way
d.sumar();     // Warning: igual
```

Siempre accede a miembros `static` por el nombre de la clase, no por un objeto.

### Lo que NO puede hacer un metodo static

```java
public class Ejemplo {
    private String nombre;    // Campo de instancia
    private static int total; // Campo static

    public static void metodoEstatico() {
        // System.out.println(nombre);  // ERROR: no puede acceder a instancia
        // this.nombre = "test";       // ERROR: no existe this
        System.out.println(total);       // OK: puede acceder a static
    }

    public void metodoInstancia() {
        System.out.println(nombre);  // OK: puede acceder a todo
        System.out.println(total);   // OK: puede acceder a static
        metodoEstatico();            // OK: puede llamar a metodos static
    }
}
```

---

## NIVEL 3 - Senior

### Static en interfaces (Java 9+)

```java
public interface Procesador {
    void procesar(String datos);

    // Metodo static publico (utilidad asociada a la interfaz)
    static Procesador crearConsola() {
        return datos -> System.out.println("Procesando: " + datos);
    }

    // Metodo private static (comparte logica entre metodos)
    private static String sanitizar(String datos) {
        return datos.trim().toLowerCase();
    }

    default void procesarSeguro(String datos) {
        procesar(sanitizar(datos));
    }
}
```

```java
Procesador p = Procesador.crearConsola();
p.procesarSeguro("  HOLA  ");  // Imprime: "procesando: hola"
```

### Static en records

```java
public record Producto(String nombre, double precio) {
    // Los campos de un record siempre son de instancia (private final)

    // Constantes static
    public static final double IVA = 0.21;
    public static final List<String> CATEGORIAS_VALIDAS = List.of("ropa", "electronica", "libros");

    // Metodos static (utilidades relacionadas con el record)
    public static Producto crearProductoPorDefecto() {
        return new Producto("Sin nombre", 0.0);
    }

    public static boolean esPrecioValido(double precio) {
        return precio > 0;
    }

    // Metodos private static (ayuda interna)
    private static String normalizarNombre(String nombre) {
        return nombre.trim().substring(0, 1).toUpperCase() + nombre.trim().substring(1).toLowerCase();
    }

    public Producto {
        if (!esPrecioValido(precio)) {
            throw new IllegalArgumentException("Precio invalido: " + precio);
        }
    }
}
```

### Static en enums

```java
public enum DiaSemana {
    LUNES, MARTES, MIERCOLES, JUEVES, VIERNES, SABADO, DOMINGO;

    private static final List<DiaSemana> LABORABLES = List.of(LUNES, MARTES, MIERCOLES, JUEVES, VIERNES);
    private static final List<DiaSemana> FINDE = List.of(SABADO, DOMINGO);

    public static boolean esLaborable(DiaSemana dia) {
        return LABORABLES.contains(dia);
    }

    public static DiaSemana siguiente(DiaSemana dia) {
        var valores = values();
        return valores[(dia.ordinal() + 1) % valores.length];
    }
}
```

### Static import

```java
// Import estatico: puedes usar el metodo sin el nombre de la clase
import static java.util.Collections.*;
import static java.lang.Math.*;
import static java.time.LocalDate.*;

public class DemoStaticImport {
    public static void main(String[] args) {
        List<String> lista = new ArrayList<>();
        addAll(lista, "A", "B", "C");  // En vez de Collections.addAll(...)
        sort(lista);                    // En vez de Collections.sort(lista)

        double raiz = sqrt(16);         // En vez de Math.sqrt(16)
        double radio = random();        // En vez de Math.random()

        var hoy = now();                // En vez de LocalDate.now()
    }
}
```

Usalo con moderacion. Solo cuando mejore la legibilidad.

### Static y herencia: ocultacion (hiding)

Los metodos `static` NO se sobreescriben. Se ocultan:

```java
public class Padre {
    public static void saludar() {
        System.out.println("Hola desde Padre");
    }

    public void decir() {
        System.out.println("Decir desde Padre");
    }
}

public class Hijo extends Padre {
    // Esto NO es @Override. Es OCULTAR (hiding)
    public static void saludar() {
        System.out.println("Hola desde Hijo");
    }

    @Override
    public void decir() {
        System.out.println("Decir desde Hijo");
    }
}
```

```java
Padre p = new Hijo();
p.decir();       // "Decir desde Hijo" (polimorfismo: metodo de instancia)
p.saludar();     // "Hola desde Padre" (NO polimorfismo: se oculta, no se sobreescribe)

// Para llamar al static, usa la CLASE:
Padre.saludar();  // "Hola desde Padre"
Hijo.saludar();   // "Hola desde Hijo"
```

El metodo que se ejecuta depende del TIPO DE LA VARIABLE (compile-time), no del objeto real.

### Static y multi-threading

Un campo `static` mutable compartido entre hilos puede causar problemas:

```java
public class ContadorMal {
    public static int total = 0;  // Compartido entre hilos
}

// Hilo 1: ContadorMal.total++;  // Leer, sumar 1, escribir
// Hilo 2: ContadorMal.total++;  // Leer, sumar 1, escribir
// Resultado posible: total suma 1 en vez de 2 (race condition)
```

Soluciones:

```java
// Solucion 1: AtomicInteger
public class ContadorBien {
    public static final AtomicInteger total = new AtomicInteger(0);
}

// Hilo 1: ContadorBien.total.incrementAndGet();
// Hilo 2: ContadorBien.total.incrementAndGet();

// Solucion 2: synchronized
public class ContadorSync {
    private static int total = 0;

    public static synchronized void incrementar() {
        total++;
    }
}

// Solucion 3: ThreadLocal (cada hilo con su propio valor)
public class Contexto {
    private static final ThreadLocal<String> usuarioActual = new ThreadLocal<>();

    public static void setUsuario(String usuario) {
        usuarioActual.set(usuario);
    }

    public static String getUsuario() {
        return usuarioActual.get();
    }
}
```

---

# METODO PROFUNDO (Curso completo de static)

## 1. Que es static exactamente?

`static` es un modificador que cambia el ciclo de vida y pertenencia de un miembro.

**Sin `static`:** el miembro nace cuando creas un objeto con `new`, vive mientras exista el objeto y muere cuando el objeto se recolecta.

**Con `static`:** el miembro nace cuando la clase se carga en la JVM, vive mientras la clase este cargada (normalmente hasta que la JVM termina) y hay una sola copia para todos.

### Donde vive en memoria

```
_____________________________
|         STACK             |   Variables locales, llamadas a metodos
|___________________________|

_____________________________
|         HEAP              |   Objetos creados con new
|    (objetos instancia)    |
|___________________________|

_____________________________
|       METASPACE           |   Datos de la clase, miembros static
|    (antes PermGen)        |
|___________________________|
```

Los miembros `static` viven en Metaspace (antes llamado PermGen). No en el Heap. Por eso existen aunque no haya objetos.

---

## 2. Campos static (variables de clase)

Un campo `static` es una variable que pertenece a la clase, no a las instancias.

```java
public class Empresa {
    private String nombreEmpleado;     // Cada empleado tiene su nombre
    private static String nombreEmpresa; // Todas los empleados comparten el nombre de la empresa
    private static int totalEmpleados;  // Todas los empleados comparten el contador

    public Empresa(String nombreEmpleado) {
        this.nombreEmpleado = nombreEmpleado;
        totalEmpleados++;
    }

    public static void setNombreEmpresa(String nombre) {
        nombreEmpresa = nombre;
    }

    public void mostrar() {
        System.out.println(nombreEmpleado + " trabaja en " + nombreEmpresa);
    }
}
```

```java
Empresa.setNombreEmpresa("TechCorp");

var e1 = new Empresa("Ana");
var e2 = new Empresa("Luis");

e1.mostrar();  // "Ana trabaja en TechCorp"
e2.mostrar();  // "Luis trabaja en TechCorp"

// Si cambias el nombre de la empresa:
Empresa.setNombreEmpresa("NewTech");
e1.mostrar();  // "Ana trabaja en NewTech"  (cambio para todos!)
```

### Valores por defecto

| Tipo | Valor por defecto |
|------|------------------|
| `int`, `long`, `short`, `byte` | `0` |
| `double`, `float` | `0.0` |
| `boolean` | `false` |
| Referencias (`String`, objetos) | `null` |

### Cuando usar campos static

| Situacion | Ejemplo |
|-----------|---------|
| Constantes | `public static final double IVA = 0.21;` |
| Contadores | `private static int totalInstancias;` |
| Instancia unica (Singleton) | `private static final Instancia INSTANCIA = new Instancia();` |
| Configuracion global | `public static String idioma;` (mejor con inmutables) |

---

## 3. Campos static final (constantes)

`static final` juntos crean una constante. La convencion es `UPPER_SNAKE_CASE`:

```java
public class Constantes {
    public static final int MAX_USUARIOS = 1000;
    public static final String URL_BASE = "https://api.miapp.com";
    public static final double IVA = 0.21;
    public static final Color COLOR_PRINCIPAL = Color.BLUE;
    public static final List<String> PERMISOS = List.of("lectura", "escritura");

    // Constante que necesita logica para inicializarse
    public static final LocalDate FECHA_INICIO = LocalDate.of(2026, 1, 1);
    public static final String RUTA_LOG = System.getProperty("user.home") + "/logs/";
}
```

### Inlining en compile-time

Para tipos primitivos y `String`, el compilador puede "inline" la constante:

```java
public class A {
    public static final int VALOR = 5;
}

public class B {
    public void metodo() {
        int x = A.VALOR;  // El compilador puede poner directamente: int x = 5;
    }
}
```

Si cambias `A.VALOR` y recompilas solo A, B seguira teniendo el valor viejo. Para evitarlo, recompila todo.

---

## 4. Metodos static

Un metodo `static` se asocia a la clase, no a una instancia.

### Para que sirven

```java
public class UtilidadesTexto {
    public static String capitalizar(String texto) {
        if (texto == null || texto.isBlank()) return "";
        return texto.substring(0, 1).toUpperCase() + texto.substring(1).toLowerCase();
    }

    public static String iniciales(String nombreCompleto) {
        return Arrays.stream(nombreCompleto.split(" "))
            .map(p -> String.valueOf(p.charAt(0)).toUpperCase())
            .collect(Collectors.joining("."));
    }

    public static boolean esEmailValido(String email) {
        return email != null && email.matches("^[A-Za-z0-9+_.-]+@(.+)$");
    }
}
```

```java
String cap = UtilidadesTexto.capitalizar("hola mundo");  // "Hola mundo"
String ini = UtilidadesTexto.iniciales("ana maria lopez");  // "A.M.L"
boolean val = UtilidadesTexto.esEmailValido("ana@email.com");  // true
```

### Factory methods static

Son metodos `static` que crean y devuelven objetos:

```java
public class Usuario {
    private String nombre;
    private String email;
    private boolean admin;

    private Usuario(String nombre, String email, boolean admin) {
        this.nombre = nombre;
        this.email = email;
        this.admin = admin;
    }

    // Factory methods
    public static Usuario crearUsuario(String nombre, String email) {
        return new Usuario(nombre, email, false);
    }

    public static Usuario crearAdmin(String nombre, String email) {
        return new Usuario(nombre, email, true);
    }

    public static Usuario crearInvitado() {
        return new Usuario("Invitado", "invitado@temp.com", false);
    }
}
```

```java
Usuario normal = Usuario.crearUsuario("Ana", "ana@email.com");
Usuario admin = Usuario.crearAdmin("Luis", "luis@empresa.com");
Usuario invitado = Usuario.crearInvitado();
```

Ventajas sobre `new`:
- Tienen nombre descriptivo (sabes que crean)
- Pueden devolver subtipos o instancias ya existentes
- Pueden tener logica antes de crear

### main: el metodo static mas importante

```java
public class Aplicacion {
    public static void main(String[] args) {
        // main es static porque la JVM lo llama sin crear objetos
        var app = new Aplicacion();
        app.iniciar();
    }

    private void iniciar() {
        System.out.println("Aplicacion iniciada");
    }
}
```

Desde Java 21 puedes usar un main mas simple:

```java
public class Hola {
    void main() {
        System.out.println("Hola Java 2026!");
    }
}
```

---

## 5. Bloques static (inicializacion estatica)

Un bloque `static` se ejecuta UNA VEZ cuando la clase se carga en la JVM.

```java
public class ConfiguracionApp {
    public static final String NOMBRE;
    public static final String VERSION;
    public static final Map<String, String> PROPIEDADES = new HashMap<>();
    public static final List<String> MODULOS;

    static {
        System.out.println("Inicializando configuracion de la aplicacion...");
        NOMBRE = "SistemaGestion";
        VERSION = "2.0.0";

        // Lectura de propiedades
        PROPIEDADES.put("idioma", "es");
        PROPIEDADES.put("zonaHoraria", "Europe/Madrid");
        PROPIEDADES.put("moneda", "EUR");

        // Logica condicional
        MODULOS = switch (System.getenv("ENTORNO")) {
            case "produccion" -> List.of("core", "pagos");
            case "desarrollo" -> List.of("core", "pagos", "debug", "test");
            default -> List.of("core");
        };
    }

    public static String get(String clave) {
        return PROPIEDADES.getOrDefault(clave, "no definido");
    }
}
```

### Orden de inicializacion de una clase

Cuando la JVM carga una clase, el orden es:

1. **Campos static** se inicializan a sus valores por defecto (0, null, false)
2. **Bloques static** se ejecutan en orden de aparicion
3. **Campos de instancia** se inicializan (solo cuando haces `new`)
4. **Bloques de instancia** se ejecutan (solo cuando haces `new`)
5. **Constructor** se ejecuta (solo cuando haces `new`)

```java
public class DemoOrden {
    private static int primero = 1;                          // Paso 1: 1
    private static int segundo;                              // Paso 1: 0

    static {
        System.out.println("Bloque static 1: " + primero);   // Paso 2: imprime 1
        segundo = 2;                                         // Paso 2: segundo = 2
    }

    private static int tercero = 3;                          // Paso 1: 3

    static {
        System.out.println("Bloque static 2: " + segundo);   // Paso 2: imprime 2
    }

    private String nombre = "instancia";                      // Paso 3
    {                                                         // Paso 4
        System.out.println("Bloque instancia");
    }

    public DemoOrden() {                                      // Paso 5
        System.out.println("Constructor");
    }
}
```

---

## 6. Static en interfaces (Java 9+)

### Metodos public static en interfaces

Desde Java 8, las interfaces pueden tener metodos `static` publicos:

```java
public interface Formateador {
    String formatear(String texto);

    // Metodo static: utilidad asociada a la interfaz
    static Formateador mayusculas() {
        return String::toUpperCase;
    }

    static Formateador minusculas() {
        return String::toLowerCase;
    }

    static Formateador capitalizado() {
        return texto -> {
            if (texto == null || texto.isBlank()) return "";
            return texto.substring(0, 1).toUpperCase() + texto.substring(1).toLowerCase();
        };
    }
}
```

```java
var mayus = Formateador.mayusculas();
System.out.println(mayus.formatear("hola"));  // "HOLA"

var cap = Formateador.capitalizado();
System.out.println(cap.formatear("hOLA"));    // "Hola"
```

### Metodos private static en interfaces (Java 9+)

Comparten codigo entre metodos `static` y `default` sin exponerlo:

```java
public interface Validador {
    boolean esValido(String valor);

    default boolean esValidoYNoNulo(String valor) {
        return valor != null && !valor.isBlank() && esValido(valor);
    }

    // Metodos compartidos solo dentro de la interfaz
    private static String sanitizar(String valor) {
        return valor.trim().toLowerCase();
    }

    static boolean esEmailValido(String email) {
        return email != null && sanitizar(email).matches("^[a-z0-9+_.-]+@(.+)$");
    }

    static boolean esTelefonoValido(String telefono) {
        return telefono != null && sanitizar(telefono).matches("\\+?\\d{9,15}");
    }
}
```

---

## 7. Static en records

Los records pueden tener miembros `static`, pero sus campos son siempre de instancia:

```java
public record Pedido(int id, String cliente, List<String> productos) {
    // Constantes static
    public static final int MAX_PRODUCTOS = 20;
    public static final String ESTADO_PENDIENTE = "PENDIENTE";
    public static final String ESTADO_ENVIADO = "ENVIADO";

    // Contador de instancias (static)
    private static final AtomicInteger CONTADOR = new AtomicInteger(0);

    // Factory method static
    public static Pedido crearVacio(int id, String cliente) {
        return new Pedido(id, cliente, List.of());
    }

    public static Pedido crearConProducto(int id, String cliente, String producto) {
        return new Pedido(id, cliente, List.of(producto));
    }

    // Metodo public static
    public static int totalPedidosCreados() {
        return CONTADOR.get();
    }

    // Constructor compacto
    public Pedido {
        if (productos.size() > MAX_PRODUCTOS) {
            throw new IllegalArgumentException("Demasiados productos");
        }
        CONTADOR.incrementAndGet();
    }
}
```

```java
var p1 = Pedido.crearVacio(1, "Ana");
var p2 = Pedido.crearConProducto(2, "Luis", "Laptop");
System.out.println(Pedido.totalPedidosCreados());  // 2
```

---

## 8. Static en enums

Los enums pueden tener campos y metodos `static`:

```java
public enum TipoUsuario {
    ADMIN("Acceso total"),
    EDITOR("Puede editar contenido"),
    LECTOR("Solo lectura"),
    INVITADO("Acceso limitado");

    private final String descripcion;

    private static final Map<String, TipoUsuario> POR_NOMBRE = Arrays.stream(values())
        .collect(Collectors.toMap(
            t -> t.name().toLowerCase(),
            t -> t
        ));

    TipoUsuario(String descripcion) {
        this.descripcion = descripcion;
    }

    public String getDescripcion() {
        return descripcion;
    }

    // Metodo static: buscar por nombre
    public static TipoUsuario desdeNombre(String nombre) {
        return POR_NOMBRE.getOrDefault(nombre.toLowerCase(), INVITADO);
    }

    // Metodo static: valores por defecto
    public static List<TipoUsuario> valoresPredeterminados() {
        return List.of(LECTOR, INVITADO);
    }
}
```

```java
TipoUsuario t = TipoUsuario.desdeNombre("ADMIN");
System.out.println(t.getDescripcion());  // "Acceso total"
```

---

## 9. Static import

`import static` importa miembros estaticos de una clase para usarlos sin el prefijo:

```java
// Sin import static:
public class SinImport {
    public double calcular(double radio) {
        return Math.PI * Math.pow(radio, 2);
    }
}

// Con import static:
import static java.lang.Math.PI;
import static java.lang.Math.pow;

public class ConImport {
    public double calcular(double radio) {
        return PI * pow(radio, 2);
    }
}
```

### Cuando usarlo

```java
import static java.util.Collections.*;
import static java.util.Comparator.*;
import static java.util.stream.Collectors.*;
import static java.time.LocalDate.*;

public class DemoStaticImport {
    public static void main(String[] args) {
        var lista = new ArrayList<String>();
        addAll(lista, "z", "a", "b", "c", "x");
        sort(lista);
        System.out.println(lista);  // [a, b, c, x, z]

        var ordenInverso = reverseOrder();
        lista.sort(ordenInverso);
        System.out.println(lista);  // [z, x, c, b, a]

        // Agrupacion con Collectors
        var numeros = List.of(1, 2, 3, 4, 5, 6);
        var porParidad = numeros.stream()
            .collect(groupingBy(n -> n % 2 == 0));
        System.out.println(porParidad);

        // Fechas
        var hoy = now();
        var inicioMes = hoy.withDayOfMonth(1);
    }
}
```

### Reglas de uso

```java
// import static CLASE.MIEMBRO;     // Importa un miembro especifico
import static java.lang.Math.PI;

// import static CLASE.*;            // Importa todos los miembros static
import static java.lang.Math.*;

// Ventaja: codigo mas limpio y corto
// Desventaja: puede confundir de donde viene cada cosa

// Recomendacion: usalo con moderacion y solo cuando sea obvio
// Math.*, Collections.*, LocalDate.* suelen ser claros
```

---

## 10. Static y herencia: ocultacion (hiding)

Los metodos `static` no son polimorficos. No se sobreescriben, se **ocultan**.

```java
public class Empleado {
    public static String tipo() {
        return "Empleado";
    }

    public void trabajar() {
        System.out.println("Empleado trabajando");
    }
}

public class Gerente extends Empleado {
    // Esto OCULTA el metodo static de Empleado
    public static String tipo() {
        return "Gerente";
    }

    @Override
    public void trabajar() {
        System.out.println("Gerente gestionando equipo");
    }
}
```

```java
Empleado e = new Gerente();
System.out.println(e.tipo());     // "Empleado" (NO polimorfismo: se oculta)
e.trabajar();                      // "Gerente gestionando equipo" (polimorfismo)

// Para Static, la referencia importa:
Empleado.tipo();    // "Empleado"
Gerente.tipo();     // "Gerente"

// Para instancia, el objeto importa:
Empleado e2 = new Gerente();
e2.trabajar();      // "Gerente gestionando equipo" (polimorfismo: va al objeto real)
```

### Por que pasa esto?

Los metodos `static` se resuelven en **compile-time** segun el tipo de la variable. Los metodos de instancia se resuelven en **runtime** segun el tipo del objeto real.

### @Override no funciona con static

```java
public class A {
    public static void metodo() { }
}

public class B extends A {
    // @Override  // ERROR: metodo static no se puede sobreescribir
    public static void metodo() { }
}
```

---

## 11. Static y this

En un contexto `static`, NO existe `this`. Porque `this` es el objeto actual, y en un metodo static no hay objeto.

```java
public class Ejemplo {
    private String nombre;
    private static int total;

    public void metodoInstancia() {
        System.out.println(this.nombre);  // OK: this existe
        System.out.println(total);         // OK: accede a static
    }

    public static void metodoEstatico() {
        // System.out.println(this.nombre);  // ERROR: no hay this
        // this.metodoInstancia();            // ERROR: no hay this
        System.out.println(total);            // OK: static accede a static
    }
}
```

### Y si necesitas acceder a instancia desde static?

Tienes que recibirla como parametro:

```java
public class Utilidades {
    // Recibe la instancia como parametro
    public static void mostrarNombre(Persona p) {
        System.out.println(p.getNombre());
    }

    // Factory: crea y devuelve instancias
    public static Persona crearDefecto() {
        return new Persona("Sin nombre");
    }
}
```

---

## 12. Static y multi-threading (problemas)

Cuando varios hilos acceden a un campo `static` mutable sin sincronizacion, ocurren **race conditions**:

```java
public class ContadorInc {
    public static int contador = 0;

    public static void incrementar() {
        contador++;  // No es atomico!
    }
}

// 10 hilos llaman a incrementar() 1000 veces cada uno
// Resultado esperado: 10000
// Resultado real: puede ser menor (ej: 9876)
```

`contador++` no es una sola operacion. Son tres:
1. Leer `contador`
2. Sumar 1
3. Escribir `contador`

Si dos hilos leen el mismo valor antes de que ninguno escriba, ambos escriben el mismo resultado y pierdes una cuenta.

### Soluciones

```java
// Solucion 1: AtomicInteger (la mas recomendada)
public class ContadorSeguro {
    private static final AtomicInteger contador = new AtomicInteger(0);

    public static void incrementar() {
        contador.incrementAndGet();
    }
}

// Solucion 2: synchronized
public class ContadorSync {
    private static int contador = 0;

    public static synchronized void incrementar() {
        contador++;
    }
}

// Solucion 3: LongAdder (muchos hilos, alta concurrencia)
public class ContadorAdder {
    private static final LongAdder contador = new LongAdder();

    public static void incrementar() {
        contador.increment();
    }
}
```

---

## 13. ThreadLocal: static pero uno por hilo

`ThreadLocal` permite tener una variable que parece `static` (un solo nombre) pero cada hilo tiene su propio valor:

```java
public class ContextoSeguridad {
    // Cada hilo tiene su propio usuario sin compartirlo
    private static final ThreadLocal<String> usuarioActual = new ThreadLocal<>();
    private static final ThreadLocal<String> tokenActual = new ThreadLocal<>();

    public static void setUsuario(String usuario) {
        usuarioActual.set(usuario);
    }

    public static String getUsuario() {
        return usuarioActual.get();
    }

    public static void setToken(String token) {
        tokenActual.set(token);
    }

    public static void limpiar() {
        usuarioActual.remove();
        tokenActual.remove();
    }
}
```

```java
// Hilo principal:
ContextoSeguridad.setUsuario("ana_admin");
procesar();

void procesar() {
    System.out.println(ContextoSeguridad.getUsuario());  // "ana_admin"
    // Si otro hilo se ejecuta en paralelo, tiene su propio valor
}

// En Spring: RequestContextHolder, SecurityContextHolder usan ThreadLocal
```

Es como si cada hilo tuviera su propia copia de la variable. El nombre es global, el valor es por hilo.

---

## 14. Carga de clases y static

### Cuando se carga una clase

La JVM carga una clase la PRIMERA VEZ que se usa:

```java
public class DemoCarga {
    public static void main(String[] args) {
        System.out.println("Antes de usar ClasePesada");
        // En este punto, ClasePesada NO esta cargada

        ClasePesada.metodo();  // Aqui se carga: se ejecuta el bloque static

        System.out.println("Despues de usar ClasePesada");
    }
}

class ClasePesada {
    static {
        System.out.println("Cargando ClasePesada... (bloque static)");
        // Aqui se inicializan los recursos estaticos
    }

    public static void metodo() {
        System.out.println("Metodo de ClasePesada ejecutandose");
    }
}
```

Salida:
```
Antes de usar ClasePesada
Cargando ClasePesada... (bloque static)
Metodo de ClasePesada ejecutandose
Despues de usar ClasePesada
```

### Que dispara la carga de una clase

```java
class Ejemplo {
    static final int CONSTANTE = 5;           // Referencia a constante NO carga la clase (si es primitivo/String)
    static final LocalDate FECHA = LocalDate.now();  // Referencia a constante SI carga (no es primitivo/String)

    static int valor = 10;                    // Referencia a campo SI carga
    static { }                                // Bloques static al cargar
}
```

```java
System.out.println(Ejemplo.CONSTANTE);  // NO carga la clase (inlining en compile-time)
System.out.println(Ejemplo.valor);       // SI carga la clase
```

### Orden de carga con herencia

```java
class A {
    static { System.out.println("A static"); }
}

class B extends A {
    static { System.out.println("B static"); }
}

// B b = new B();
// Salida:
// "A static"
// "B static"

// Los bloques static del padre se ejecutan antes que los del hijo
```

---

## 15. Static vs Instancia: guia de uso

### Cuando usar static

```java
// 1. Constantes
public static final int MAX = 100;

// 2. Utilidades sin estado (solo parametros -> resultado)
public static String capitalizar(String s) { ... }

// 3. Factory methods (alternativa a new)
public static Usuario crearPorDefecto() { ... }

// 4. Singleton (unica instancia)
private static final Instancia UNICA = new Instancia();

// 5. Metodo main (punto de entrada)
public static void main(String[] args) { ... }
```

### Cuando usar instancia

```java
// 1. Estado del objeto
private String nombre;
private int edad;

// 2. Comportamiento que depende del estado
public void mostrar() { System.out.println(this.nombre); }

// 3. Polimorfismo (necesitas @Override)
public void ejecutar() { ... }

// 4. Cuando implementas una interfaz
public class Servicio implements Runnable {
    @Override public void run() { ... }
}
```

### Regla practica

```
Pregunta: El metodo necesita acceder a this o campos de instancia?
  Si  → metodo de instancia
  No  → puede ser static

Pregunta: El campo debe ser unico para cada objeto?
  Si  → campo de instancia
  No  → puede ser static
```

---

## 16. Static y testing

### Problema: metodos static dificiles de testear

```java
public class Servicio {
    public void procesar() {
        String ruta = Configuracion.get("ruta_archivos");
        // Acoplado al metodo static Configuracion.get()
    }
}

// En el test no puedes reemplazar Configuracion.get()
```

### Solucion 1: Envolver en una interfaz

```java
public interface Configuracion {
    String get(String clave);
}

public class ConfiguracionReal implements Configuracion {
    @Override
    public String get(String clave) {
        return ConfiguracionGlobal.get(clave);  // Llama al static real
    }
}

public class Servicio {
    private final Configuracion config;

    public Servicio(Configuracion config) {  // Inyeccion
        this.config = config;
    }

    public void procesar() {
        String ruta = config.get("ruta_archivos");
    }
}

// En el test:
@Test
void testProcesar() {
    var mockConfig = mock(Configuracion.class);
    when(mockConfig.get("ruta_archivos")).thenReturn("/test/ruta");
    var servicio = new Servicio(mockConfig);
    servicio.procesar();
}
```

### Solucion 2: Metodo que permita reemplazar (para tests)

```java
public class Reloj {
    private static Reloj instancia = new Reloj();

    private Reloj() { }

    public static LocalDateTime ahora() {
        return instancia.obtenerAhora();
    }

    protected LocalDateTime obtenerAhora() {
        return LocalDateTime.now();
    }

    // Para tests: reemplazar la implementacion
    static void setInstancia(Reloj mock) {
        instancia = mock;
    }
}

// En el test:
@Test
void testConFechaFija() {
    var mockReloj = mock(Reloj.class);
    when(mockReloj.obtenerAhora()).thenReturn(LocalDateTime.of(2026, 1, 1, 0, 0));
    Reloj.setInstancia(mockReloj);
    // Ahora Reloj.ahora() devuelve siempre la fecha fija
}
```

---

## Errores Comunes

> Usar `static` para todo porque "es mas comodo". El estado global `static` crea acoplamiento oculto, problemas de concurrencia y codigo dificil de testear.

> Acceder a miembros `static` a traves de un objeto en vez de la clase. `objeto.metodoStatic()` funciona pero confunde. Usa siempre `Clase.metodoStatic()`.

> Pensar que los metodos `static` se sobreescriben con herencia. No se sobreescriben, se ocultan. El metodo que se ejecuta depende del tipo de la variable, no del objeto.

> Usar `this` en un metodo `static`. No existe `this` en contexto `static`. No hay objeto.

> Mutar campos `static` desde multiples hilos sin sincronizacion. Causa race conditions. Usa `AtomicInteger`, `synchronized` o `ThreadLocal`.

> Poner logica de negocio en metodos `static`. Los `static` son para utilidades y constantes, no para logica que deberia estar en objetos.

> Confundir `static` (pertenece a la clase) con "final" (no se puede cambiar). `static` y `final` son conceptos distintos. Se pueden usar juntos o por separado.

> Olvidar que los bloques `static` se ejecutan en orden. Si tienes varios bloques `static`, el orden importa.

---

## Buenas Practicas

1. **`static final` para constantes**. `public static final int MAX = 100;` con `UPPER_SNAKE_CASE`.
2. **Metodos `static` para utilidades puras**. Sin estado, solo parametros -> resultado.
3. **Accede a `static` por la clase**. `Clase.metodo()` no `objeto.metodo()`.
4. **Evita `static` mutable**. El estado global es dificil de testear y mantener.
5. **Usa `AtomicInteger` o `synchronized`** para contadores static compartidos entre hilos.
6. **`ThreadLocal`** para valores que parecen globales pero son por hilo.
7. **Factory methods `static`** como alternativa a constructores.
8. **Static import con moderacion**. Solo cuando mejore la legibilidad.
9. **No uses `static` para evitar crear objetos**. La POO existe por una razon.
10. **Bloques `static` para inicializacion pesada** (lectura de archivos, configuracion).

---

## Conexiones

- [[13 - Static vs Instancia]] - La base que ya tienes sobre static
- [[01 - Clases y Estructura Basica]] - Miembros de clase
- [[02 - Punto de Entrada main]] - main es static
- [[06 - Atributos y Campos]] - Campos static
- [[07 - Constructores y this]] - Bloques static, no hay this en static
- [[08 - Instanciacion y new]] - Static existe sin new
- [[10 - Metodos de Instancia]] - Static vs metodos de instancia
- [[17 - Flujo de Ejecucion JVM]] - Carga de clases, Metaspace
- [[18 - Convenciones de Nombrado]] - UPPER_SNAKE_CASE
- [[26 - Private en Profundidad]] - Metodos private static
- [[27 - Singleton en Profundidad]] - Campo static para la instancia unica

---

## Tags
`#java #fundamentos #static #clase #memoria #metodos-estaticos #campos-estaticos`
