---
tags: [java, fundamentos, patrones, singleton, diseno, creacional]
---

# 27 - Singleton en Profundidad

El Singleton es un patron de diseno que garantiza que una clase tenga UNA SOLA instancia y proporciona un punto de acceso global a ella.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es Singleton?

Singleton significa que de una clase solo puede existir UN objeto. Por mas veces que intentes crear uno nuevo, siempre obtienes el mismo.

### El problema

Normalmente puedes crear cuantos objetos quieras:

```java
Coche c1 = new Coche();  // Objeto 1
Coche c2 = new Coche();  // Objeto 2 (distinto)
```

Pero a veces necesitas que solo haya UNO. Por ejemplo:
- La conexion a la base de datos (no necesitas 10 conexiones diferentes)
- La configuracion de la aplicacion (solo hay una configuracion)
- Un logger (quieres un solo punto de registro)

### La idea basica

```java
public class ConfiguracionGlobal {
    private static ConfiguracionGlobal instancia = new ConfiguracionGlobal();
    private String url = "http://localhost:8080";

    private ConfiguracionGlobal() {
        System.out.println("Creando configuracion...");
    }

    public static ConfiguracionGlobal obtener() {
        return instancia;
    }

    public String getUrl() {
        return url;
    }
}
```

```java
public class Principal {
    public static void main(String[] args) {
        ConfiguracionGlobal c1 = ConfiguracionGlobal.obtener();
        ConfiguracionGlobal c2 = ConfiguracionGlobal.obtener();

        System.out.println(c1 == c2);  // true: son el MISMO objeto
        System.out.println(c1.getUrl());
    }
}
```

Esto imprime:
```
Creando configuracion...
true
http://localhost:8080
```

### Lo importante ahora

1. El constructor es `private` (nadie puede hacer `new`)
2. Hay un `static` que guarda la unica instancia
3. Hay un metodo `static` que devuelve esa instancia
4. Siempre recibes el mismo objeto (no importa cuantas veces llames a `obtener()`)

---

## NIVEL 2 - Mid

### Singleton clasico (eager)

La instancia se crea cuando se carga la clase, antes de que nadie la pida:

```java
public class ConexionBaseDatos {
    private static final ConexionBaseDatos INSTANCIA = new ConexionBaseDatos();
    private boolean conectada;

    private ConexionBaseDatos() {
        System.out.println("Iniciando conexion a BD...");
        this.conectada = true;
    }

    public static ConexionBaseDatos obtener() {
        return INSTANCIA;
    }

    public void ejecutar(String sql) {
        if (!conectada) {
            throw new IllegalStateException("No conectado");
        }
        System.out.println("Ejecutando: " + sql);
    }

    public void cerrar() {
        conectada = false;
        System.out.println("Conexion cerrada");
    }
}
```

Ventaja: simple y thread-safe (la JVM garantiza que la clase se carga una sola vez).
Desventaja: la instancia se crea incluso si nunca se usa (si es pesada, puede ser un problema).

### Singleton perezoso (lazy)

La instancia solo se crea cuando alguien la pide por primera vez:

```java
public class GestorConfiguracion {
    private static GestorConfiguracion instancia;
    private Properties props;

    private GestorConfiguracion() {
        props = new Properties();
        cargarArchivo();
    }

    public static GestorConfiguracion obtener() {
        if (instancia == null) {
            instancia = new GestorConfiguracion();
        }
        return instancia;
    }

    private void cargarArchivo() {
        System.out.println("Cargando archivo de configuracion...");
    }

    public String get(String clave) {
        return props.getProperty(clave, "defecto");
    }
}
```

Problema: si dos hilos llaman a `obtener()` al mismo tiempo, pueden crear dos instancias.

### Singleton thread-safe con synchronized

```java
public class GestorConfiguracion {
    private static GestorConfiguracion instancia;

    private GestorConfiguracion() { }

    public static synchronized GestorConfiguracion obtener() {
        if (instancia == null) {
            instancia = new GestorConfiguracion();
        }
        return instancia;
    }
}
```

Con `synchronized`, solo un hilo puede entrar a `obtener()` a la vez. Pero es mas lento porque sincroniza siempre, incluso cuando la instancia ya existe.

### Double-checked locking (mejor rendimiento)

```java
public class GestorConfiguracion {
    private static volatile GestorConfiguracion instancia;

    private GestorConfiguracion() { }

    public static GestorConfiguracion obtener() {
        if (instancia == null) {
            synchronized (GestorConfiguracion.class) {
                if (instancia == null) {
                    instancia = new GestorConfiguracion();
                }
            }
        }
        return instancia;
    }
}
```

Dos comprobaciones: la primera sin sincronizar (rapida), la segunda dentro de `synchronized` (solo la primera vez). Mas eficiente.

---

## NIVEL 3 - Senior

### Singleton con enum (la mejor forma segun Effective Java)

```java
public enum ConexionBD {
    INSTANCIA;

    private String url;
    private boolean conectada;

    ConexionBD() {
        this.url = System.getenv("DB_URL");
        this.conectada = false;
    }

    public void conectar() {
        System.out.println("Conectando a " + url);
        conectada = true;
    }

    public void ejecutar(String sql) {
        if (!conectada) {
            throw new IllegalStateException("No conectado");
        }
        System.out.println("SQL: " + sql);
    }
}
```

```java
// Uso:
ConexionBD.INSTANCIA.conectar();
ConexionBD.INSTANCIA.ejecutar("SELECT * FROM usuarios");
```

Ventajas del enum:
- Thread-safe garantizado por la JVM
- Resistente a serializacion (no necesita `readResolve()`)
- Resistente a reflection (no puedes crear otra instancia con reflection)
- Codigo minimo

### Bill Pugh Singleton Holder (el mas eficiente)

```java
public class GestorPool {
    private GestorPool() {
        System.out.println("Inicializando pool de conexiones...");
    }

    private static class Holder {
        private static final GestorPool INSTANCIA = new GestorPool();
    }

    public static GestorPool obtener() {
        return Holder.INSTANCIA;
    }

    public void ejecutar(String sql) {
        System.out.println("Ejecutando en pool: " + sql);
    }
}
```

Como funciona:
- La clase `Holder` solo se carga cuando se llama a `obtener()`
- Cuando se carga `Holder`, se crea la instancia (una sola vez)
- La JVM garantiza que la carga de clase es thread-safe
- No necesita `synchronized` ni `volatile`

Ventajas: perezoso, thread-safe, eficiente, simple.

### Singleton con inyeccion de dependencias (alternativa moderna)

En vez del patron Singleton, los frameworks como Spring manejan el ciclo de vida:

```java
// Con Spring:
@Component
public class ServicioUsuarios {
    // Spring crea una sola instancia por defecto (singleton scope)
    public void registrar(String email) {
        System.out.println("Registrando " + email);
    }
}

// En otra clase:
@Component
public class Controlador {
    private final ServicioUsuarios servicio;

    // Spring inyecta la misma instancia siempre
    public Controlador(ServicioUsuarios servicio) {
        this.servicio = servicio;
    }
}
```

### El debate: Singleton es antipatron?

**Problemas del Singleton clasico:**
- Dificil de testear (no puedes reemplazar la instancia por un mock)
- Estado global oculto (acoplamiento oculto)
- Viola el Principio de Responsabilidad Unica (gestiona su creacion y su logica)
- Dificil de paralelizar

**Cuando usar Singleton:**
- Cuando realmente solo debe haber una instancia
- Cuando es un recurso compartido fisicamente (conexion BD, logger)
- Cuando el estado global es necesario y no cambiara

**Cuando NO usarlo:**
- Solo para tener "acceso global" a algo
- Cuando podrias usar inyeccion de dependencias
- En proyectos que necesitan tests unitarios puros

### La regla practica

Usa Singleton para recursos de infraestructura (log, config, pool de conexiones). Usa inyeccion de dependencias para logica de negocio.

---

# METODO PROFUNDO (Curso completo de Singleton)

## 1. Que es Singleton?

Singleton es un patron de diseno creacional. Su nombre viene de "single" (unico) y "instance" (instancia). Garantiza que una clase tenga exactamente una instancia y proporciona un punto de acceso global a ella.

### El origen

El patron fue documentado en el libro "Design Patterns: Elements of Reusable Object-Oriented Software" (1994) por Gamma, Helm, Johnson y Vlissides (conocidos como la "Banda de los Cuatro" o GoF).

### El problema que resuelve

Hay recursos en un programa que solo deberian existir una vez:

```java
// Sin Singleton: cada clase crea su propia conexion
public class ServicioA {
    private ConexionBD conexion = new ConexionBD();  // Conexion 1
}

public class ServicioB {
    private ConexionBD conexion = new ConexionBD();  // Conexion 2 (distinta)
}

// Con Singleton: todos usan la misma
public class ServicioA {
    private ConexionBD conexion = ConexionBD.obtener();
}

public class ServicioB {
    private ConexionBD conexion = ConexionBD.obtener();  // La misma que en A
}
```

---

## 2. Las tres reglas del Singleton

Para ser Singleton, una clase debe cumplir tres reglas:

| Regla | Como se hace | Por que |
|-------|-------------|---------|
| Constructor privado | `private Clase() { }` | Nadie mas puede crear instancias |
| Una sola instancia | `private static final Clase INSTANCIA = ...` | Se guarda en un campo static |
| Acceso global | `public static Clase obtener() { return INSTANCIA; }` | Un metodo static para acceder |

Si falta alguna de las tres, no es un Singleton.

---

## 3. Eager Singleton (inicializacion temprana)

La instancia se crea cuando la clase se carga en memoria, antes de que nadie la pida.

```java
public class Logger {
    private static final Logger INSTANCIA = new Logger();
    private List<String> historial;

    private Logger() {
        this.historial = new ArrayList<>();
        System.out.println("Logger iniciado");
    }

    public static Logger obtener() {
        return INSTANCIA;
    }

    public void info(String mensaje) {
        String linea = "[INFO] " + mensaje;
        historial.add(linea);
        System.out.println(linea);
    }

    public List<String> obtenerHistorial() {
        return List.copyOf(historial);
    }
}
```

```java
Logger log = Logger.obtener();
log.info("Aplicacion iniciada");
log.info("Usuario registrado");
```

**Ventajas:**
- Simple de escribir y entender
- Thread-safe automatico (la JVM garantiza que la clase se carga una sola vez)
- Siempre disponible

**Desventajas:**
- La instancia se crea aunque no se use
- Si la creacion es lenta (lee archivos, conecta a BD), retrasa el arranque
- No puedes pasarle parametros

### Cuando usarlo

Cuando el Singleton es ligero (no hace operaciones costosas al crearse) y es casi seguro que se usara.

---

## 4. Lazy Singleton (inicializacion perezosa)

La instancia se crea solo cuando alguien la pide por primera vez.

```java
public class GestorConfiguracion {
    private static GestorConfiguracion instancia;
    private Properties propiedades;

    private GestorConfiguracion() { }

    public static GestorConfiguracion obtener() {
        if (instancia == null) {
            instancia = new GestorConfiguracion();
        }
        return instancia;
    }

    public String get(String clave) {
        if (propiedades == null) {
            cargarPropiedades();
        }
        return propiedades.getProperty(clave, "");
    }

    private void cargarPropiedades() {
        System.out.println("Cargando propiedades...");
        propiedades = new Properties();
        // ... leer archivo
    }
}
```

**Ventajas:**
- Solo se crea si se usa (ahorra recursos si nunca se necesita)
- Puede tener logica condicional en la creacion

**Desventajas:**
- NO es thread-safe (dos hilos pueden crear dos instancias si llaman a la vez)
- Mas complejo que eager
- La primera llamada es mas lenta

### El problema de hilos (thread-safety)

Sin sincronizacion, dos hilos pueden entrar al `if` a la vez:

```java
// Hilo 1: llama a obtener()
// Hilo 2: llama a obtener() al mismo tiempo

public static GestorConfiguracion obtener() {
    if (instancia == null) {           // Hilo 1: true. Hilo 2: true (aun no se asigno)
        instancia = new GestorConfiguracion();  // Se crean DOS instancias
    }
    return instancia;
}
```

---

## 5. Singleton thread-safe con synchronized

```java
public class GestorConfiguracion {
    private static GestorConfiguracion instancia;

    private GestorConfiguracion() { }

    // synchronized: solo un hilo a la vez puede ejecutar este metodo
    public static synchronized GestorConfiguracion obtener() {
        if (instancia == null) {
            instancia = new GestorConfiguracion();
        }
        return instancia;
    }
}
```

`Synchronized` significa: "solo un hilo puede estar aqui a la vez. Los demas esperan."

**Problema:** Cada vez que llamas a `obtener()` (aunque la instancia ya exista), pagas el costo de la sincronizacion. Es como hacer cola en el supermercado cada vez, aunque ya tengas la leche.

---

## 6. Double-checked locking (la mejora)

```java
public class GestorConfiguracion {
    // volatile: garantiza que todos los hilos vean el valor actualizado
    private static volatile GestorConfiguracion instancia;

    private GestorConfiguracion() { }

    public static GestorConfiguracion obtener() {
        // Primer chequeo: SIN sincronizar (rapido)
        if (instancia == null) {
            // Segundo chequeo: CON sincronizar
            synchronized (GestorConfiguracion.class) {
                if (instancia == null) {
                    instancia = new GestorConfiguracion();
                }
            }
        }
        return instancia;
    }
}
```

Como funciona paso a paso:

1. Llamas a `obtener()`
2. Primera comprobacion: `if (instancia == null)` - si ya existe, devuelve la instancia (sin sincronizar)
3. Si es null, entramos al bloque `synchronized` (solo un hilo a la vez)
4. Segunda comprobacion dentro: `if (instancia == null)` - por si otro hilo ya la creo
5. Se crea la instancia
6. Se devuelve la instancia

`volatile` es necesario para que todos los hilos vean cuando `instancia` cambia de null a algo.

**Ventaja:** solo sincroniza la primera vez. Las siguientes llamadas son tan rapidas como un `if`.

---

## 7. Bill Pugh Singleton Holder (el mas recomendado)

```java
public class BaseDatos {
    private BaseDatos() {
        System.out.println("Inicializando base de datos...");
    }

    // Clase interna estatica: solo se carga cuando se usa
    private static class Holder {
        private static final BaseDatos INSTANCIA = new BaseDatos();
    }

    public static BaseDatos obtener() {
        return Holder.INSTANCIA;
    }

    public void conectar() {
        System.out.println("Base de datos conectada");
    }
}
```

### Por que funciona?

La magia esta en la clase interna `Holder`:

```java
private static class Holder {
    private static final BaseDatos INSTANCIA = new BaseDatos();
}
```

- Las clases internas estaticas NO se cargan hasta que se usan
- Cuando llamas a `BaseDatos.obtener()`, la JVM necesita `Holder.INSTANCIA`
- Eso obliga a cargar la clase `Holder`
- Al cargarse `Holder`, se ejecuta su inicializacion estatica: `new BaseDatos()`
- La JVM garantiza que la carga de una clase es thread-safe

### Ventajas

- **Lazy**: la instancia solo se crea cuando llaman a `obtener()` por primera vez
- **Thread-safe**: la JVM garantiza que la carga de clase es atomica
- **Sin sincronizacion**: no necesita `synchronized` ni `volatile`
- **Eficiente**: no tiene costo de sincronizacion
- **Simple**: poco codigo, facil de entender

Esta es la forma recomendada por la mayoria de expertos para Singleton en Java.

---

## 8. Singleton con enum (la mas segura)

```java
public enum GestorArchivos {
    INSTANCIA;

    private String directorioBase;
    private long totalArchivos;

    GestorArchivos() {
        this.directorioBase = System.getProperty("user.dir");
        this.totalArchivos = 0;
    }

    public void guardar(String nombre, byte[] contenido) {
        System.out.println("Guardando " + nombre + " en " + directorioBase);
        totalArchivos++;
    }

    public long totalArchivos() {
        return totalArchivos;
    }
}
```

```java
GestorArchivos.INSTANCIA.guardar("datos.txt", new byte[]{1,2,3});
GestorArchivos.INSTANCIA.guardar("config.xml", new byte[]{4,5,6});
System.out.println(GestorArchivos.INSTANCIA.totalArchivos());  // 2
```

### Por que es la mas segura?

**Proteccion contra reflection:**

Con un Singleton normal, alguien podria usar reflection para crear otra instancia:

```java
// Esto funciona en un Singleton normal:
Constructor<?> c = Clase.class.getDeclaredConstructor();
c.setAccessible(true);
Clase segundaInstancia = (Clase) c.newInstance();  // Segunda instancia!
```

Pero con un `enum`, la JVM garantiza que no puedes crear mas instancias ni con reflection. Java lo prohibe explicitamente.

**Proteccion contra serializacion:**

Cuando serializas y deserializas un Singleton normal, puedes obtener dos instancias distintas (a menos que implementes `readResolve()`). Con `enum`, la deserializacion siempre devuelve la misma instancia.

**Proteccion contra clonacion:**

No puedes clonar un `enum`.

### La unica desventaja

No puedes usar lazy loading con enum. La instancia se crea cuando la clase `enum` se carga. Pero en la practica, para la mayoria de casos, esto no es un problema.

---

## 9. Singleton con record (Java moderno)

Los `record` son inmutables, lo cual encaja bien con Singleton:

```java
public record ConfiguracionApp(
    String nombreApp,
    String version,
    String entorno
) {
    public static final ConfiguracionApp INSTANCIA = new ConfiguracionApp(
        getEnvOrDefault("APP_NOMBRE", "MiApp"),
        getEnvOrDefault("APP_VERSION", "1.0.0"),
        getEnvOrDefault("APP_ENTORNO", "desarrollo")
    );

    private static String getEnvOrDefault(String clave, String defecto) {
        return System.getenv(clave) != null ? System.getenv(clave) : defecto;
    }

    public void mostrar() {
        System.out.printf("%s v%s (%s)%n", nombreApp, version, entorno);
    }
}
```

```java
ConfiguracionApp.INSTANCIA.mostrar();
String nombre = ConfiguracionApp.INSTANCIA.nombreApp();
```

Al ser un `record`, los campos son `final`, el objeto es inmutable y los getters son automaticos. Perfecto para una configuracion global.

---

## 10. Singleton vs clase static

A veces la gente confunde Singleton con una clase de solo metodos estaticos:

```java
// Clase de utilidades (NO es Singleton):
public class Utilidades {
    private Utilidades() { }  // Solo para que no instancien

    public static int sumar(int a, int b) { return a + b; }
    public static String formatear(String s) { return s.trim(); }
}
```

```java
// Singleton (SI tiene estado):
public class Configuracion {
    private static Configuracion instancia;
    private Properties props;  // Tiene estado

    private Configuracion() { }

    public static Configuracion obtener() {
        if (instancia == null) instancia = new Configuracion();
        return instancia;
    }

    public String get(String clave) { return props.getProperty(clave); }
}
```

### Diferencia clave

| Clase static | Singleton |
|-------------|-----------|
| Solo metodos, sin estado | Tiene estado (atributos) |
| No se puede pasar como parametro | Se puede pasar como objeto |
| No implementa interfaces | Puede implementar interfaces |
| No se puede sustituir por un mock | Se puede (con mas esfuerzo) |
| Sin polimorfismo | Puede tener polimorfismo |

Usa clase static para utilidades sin estado (como `Math`). Usa Singleton para recursos con estado (logger, config, pool).

---

## 11. Problemas del Singleton (y como mitigarlos)

### Problema 1: Dificil de testear

```java
// Codigo acoplado al Singleton:
public class Procesador {
    public void procesar() {
        Logger log = Logger.obtener();  // Acoplado al Singleton real
        log.info("Procesando...");
    }
}

// En el test:
@Test
void testProcesar() {
    Procesador p = new Procesador();
    p.procesar();
    // No puedo reemplazar Logger por un mock
    // Si Logger escribe en un archivo, el test escribe en archivos
}
```

**Solucion 1: Interfaz + inyeccion**

```java
public interface Logger {
    void info(String mensaje);
}

public class LoggerConsola implements Logger {
    public static final Logger INSTANCIA = new LoggerConsola();
    private LoggerConsola() { }
    public void info(String mensaje) { System.out.println(mensaje); }
}

public class Procesador {
    private final Logger logger;

    public Procesador(Logger logger) {  // Inyeccion: recibe el logger
        this.logger = logger;
    }

    public void procesar() {
        logger.info("Procesando...");
    }
}

// En el test:
@Test
void testProcesar() {
    Logger mock = mensaje -> {};  // Logger que no hace nada
    Procesador p = new Procesador(mock);
    p.procesar();  // No escribe nada, solo prueba la logica
}
```

**Solucion 2: Metodo que permite reemplazar (para tests)**

```java
public class Logger {
    private static Logger instancia = new Logger();

    private Logger() { }

    public static Logger obtener() {
        return instancia;
    }

    // Para tests: reemplazar la instancia
    static void setInstancia(Logger mock) {
        instancia = mock;
    }

    public void info(String mensaje) {
        System.out.println(mensaje);
    }
}
```

### Problema 2: Estado global oculto

El Singleton introduce estado global. Cualquier parte del programa puede modificarlo sin que te enteres.

```java
// En cualquier parte del codigo:
ConfiguracionGlobal.obtener().setModoDebug(false);
// Otra parte del codigo espera que debug este activo... pero alguien lo cambio
```

**Solucion:** Hacer el Singleton inmutable (todos los campos final, sin setters).

### Problema 3: Acoplamiento

Cuando una clase usa un Singleton directamente, esta acoplada a el. No puedes usar esa clase en otro contexto sin el Singleton.

**Solucion:** Inyeccion de dependencias (recibir por constructor en vez de llamar a `obtener()`).

---

## 12. Singleton con parametros

A veces necesitas pasarle datos al Singleton antes de que se cree:

```java
public class GestorBaseDatos {
    private static volatile GestorBaseDatos instancia;
    private final String url;
    private final String usuario;
    private final String contrasenia;

    private GestorBaseDatos(String url, String usuario, String contrasenia) {
        this.url = url;
        this.usuario = usuario;
        this.contrasenia = contrasenia;
        conectar();
    }

    private void conectar() {
        System.out.println("Conectando a " + url + " como " + usuario);
    }

    public static GestorBaseDatos inicializar(String url, String usuario, String contrasenia) {
        if (instancia != null) {
            throw new IllegalStateException("Ya inicializado");
        }
        synchronized (GestorBaseDatos.class) {
            if (instancia == null) {
                instancia = new GestorBaseDatos(url, usuario, contrasenia);
            }
        }
        return instancia;
    }

    public static GestorBaseDatos obtener() {
        if (instancia == null) {
            throw new IllegalStateException("No inicializado. Llama a inicializar() primero");
        }
        return instancia;
    }
}
```

```java
// Al arrancar la aplicacion:
GestorBaseDatos.inicializar("jdbc:mysql://localhost:3306/mibd", "admin", "pass123");

// En el resto del codigo:
GestorBaseDatos bd = GestorBaseDatos.obtener();
```

Pero esto es fragil. Si alguien llama a `obtener()` antes de `inicializar()`, falla.

**Alternativa mas limpia:** Usar un `record` de configuracion:

```java
public record ConfigBD(String url, String usuario, String contrasenia) {
    public static ConfigBD deVariablesDeEntorno() {
        return new ConfigBD(
            System.getenv("DB_URL"),
            System.getenv("DB_USUARIO"),
            System.getenv("DB_PASS")
        );
    }
}

public enum ConexionBD {
    INSTANCIA;

    private ConfigBD config;

    public void configurar(ConfigBD config) {
        this.config = config;
    }

    public void ejecutar(String sql) {
        if (config == null) {
            throw new IllegalStateException("Configurar primero");
        }
        System.out.println("Ejecutando en " + config.url() + ": " + sql);
    }
}

// Uso:
ConexionBD.INSTANCIA.configurar(ConfigBD.deVariablesDeEntorno());
ConexionBD.INSTANCIA.ejecutar("SELECT 1");
```

---

## 13. Singleton y serializacion

Cuando serializas un Singleton y lo deserializas, normalmente obtienes una nueva instancia:

```java
// Sin proteccion: la deserializacion crea una nueva instancia
ObjectOutputStream salida = new ObjectOutputStream(new FileOutputStream("singleton.ser"));
salida.writeObject(MiSingleton.obtener());

ObjectInputStream entrada = new ObjectInputStream(new FileInputStream("singleton.ser"));
MiSingleton copia = (MiSingleton) entrada.readObject();

System.out.println(MiSingleton.obtener() == copia);  // false! Son dos objetos distintos
```

Para evitarlo, implementa `readResolve()`:

```java
public class MiSingleton implements Serializable {
    private static final MiSingleton INSTANCIA = new MiSingleton();

    private MiSingleton() { }

    public static MiSingleton obtener() {
        return INSTANCIA;
    }

    // Garantiza que al deserializar se devuelva la misma instancia
    private Object readResolve() {
        return INSTANCIA;
    }
}
```

Con `readResolve()`, la deserializacion devuelve la instancia existente en vez de crear una nueva.

**Con enum no necesitas esto.** Los `enum` ya manejan la serializacion correctamente por defecto. Es otra razon para preferir enum.

---

## 14. Singleton en Spring Boot

En Spring, el "singleton" es el alcance (scope) por defecto de los beans:

```java
@Component  // Spring crea una sola instancia (singleton scope)
public class ServicioUsuarios {
    public void registrar(String email) {
        System.out.println("Registrando " + email);
    }
}

@Component
public class ControladorUsuarios {
    private final ServicioUsuarios servicio;

    // Spring inyecta SIEMPRE la misma instancia de ServicioUsuarios
    public ControladorUsuarios(ServicioUsuarios servicio) {
        this.servicio = servicio;
    }
}
```

No escribes el patron Singleton manualmente. Spring lo maneja por ti.

**Diferencia con el patron Singleton:**

| Patron Singleton | Spring Singleton |
|-----------------|-----------------|
| Tu escribes el codigo | Spring lo maneja |
| Constructor private | Constructor public normal |
| `Clase.obtener()` | `@Autowired` o inyeccion por constructor |
| Solo uno por JVM | Uno por contexto de Spring (puede haber varios contextos) |
| Dificil de testear | Facil de testear (Spring reemplaza el bean) |

### La recomendacion

Si usas Spring, no escribas Singletons manualmente. Deja que Spring gestione las instancias. Si no usas framework, usa Bill Pugh Holder o enum.

---

## Errores Comunes

> Olvidar que Singleton NO es thread-safe por defecto. Si usas lazy loading sin `synchronized`, dos hilos pueden crear dos instancias.

> Usar Singleton para todo. No todo necesita ser Singleton. Muchas veces un objeto puede crearse varias veces sin problema.

> Singleton con estado mutable (campos publicos o setters). Cualquier parte del programa puede cambiarlo, causando bugs dificiles de encontrar.

> No proteger contra serializacion. Si tu Singleton implementa `Serializable` sin `readResolve()`, la deserializacion crea otra instancia.

> Singleton y herencia. Un Singleton normalmente deberia ser `final` o tener el constructor `private` (lo que impide la herencia).

> Confundir Singleton con clase de utilidades static. Si no tiene estado, no necesitas Singleton. Usa metodos static y un constructor private para evitar instanciacion.

> Crear Singleton con Spring y ademas con el patron manual. Si usas Spring, deja que Spring gestione el ciclo de vida.

---

## Buenas Practicas

1. **Prefiere `enum` para Singleton** si no usas framework. Es la forma mas segura contra reflection, serializacion y clonacion.
2. **Bill Pugh Holder** si necesitas lazy loading sin complicaciones.
3. **Eager** si el Singleton es ligero y siempre se usa.
4. **No hagas Singleton con estado mutable**. Si tiene setters, el estado global puede cambiar sin control.
5. **Inyeccion de dependencias** como alternativa: recibe el Singleton por constructor en vez de llamar a `obtener()` directamente.
6. **Interfaces**: programa contra interfaces, no contra la clase Singleton concreta. Facilita tests y cambios.
7. **Serializacion**: si implementas `Serializable`, anade `readResolve()`.
8. **Con Spring**: no escribas el patron manual. Usa `@Component` o `@Service` con scope singleton (el default).
9. **Testea**: si tu codigo usa `Clase.obtener()` directamente, encapsulalo en un campo que puedas reemplazar en tests.

---

## Conexiones

- [[05 - Modificadores de Acceso]] - Constructor private
- [[07 - Constructores y this]] - Constructor privado
- [[08 - Instanciacion y new]] - Control de como se crean objetos
- [[13 - Static vs Instancia]] - Campo static para la instancia unica
- [[19 - Separacion de Responsabilidades]] - SRP y cuando el Singleton lo viola
- [[21 - Getters y Setters]] - Getter static para obtener la instancia
- [[22 - Separacion de Responsabilidades]] - Inyeccion de dependencias como alternativa
- [[25 - Paquetes y Organizacion]] - Donde colocar el Singleton en la estructura
- [[26 - Private en Profundidad]] - Private en constructores (base del Singleton)

---

## Tags
`#java #fundamentos #patrones #singleton #diseno #creacional #poo`
