---
tags: [java, spring-boot, arranque, ioc, autoconfiguracion, main]
---

# 49 - Spring Boot arranque de aplicacion

Spring Boot es un framework que elimina la configuración manual repetitiva de una aplicación Java de servidor: levanta un contenedor web embebido, autoconfigura todo lo que detecta en el classpath y arranca tu aplicación con una sola línea. Es el estándar actual para backend en Java.

---

# METODO EXPLICATIVO

## 1. Que problema resuelve Spring Boot

Antes de Spring Boot, arrancar una aplicación web en Java requería: instalar un servidor (Tomcat) por separado, escribir archivos XML de configuración de decenas de páginas, desplegar un `.war` manualmente y cruzar los dedos. Era tan pesado que "configurar el proyecto" podía llevar días.

Spring Boot invierte el modelo: en vez de tú configurar el servidor, el servidor viene **embebido dentro de tu aplicación**. Tu programa es un `main()` normal de Java que, al ejecutarse, levanta Tomcat dentro de su propio proceso. El resultado es un único `.jar` ejecutable que arranca con `java -jar miapp.jar`.

## 2. El main minimo que arranca todo

```java
@SpringBootApplication
public class MiAplicacion {

    public static void main(String[] args) {
        SpringApplication.run(MiAplicacion.class, args);
    }
}
```

Este es todo el código necesario para arrancar una aplicación Spring Boot vacía (escuchando en el puerto 8080). Dos elementos:

- **`@SpringBootApplication`**: una anotación compuesta que agrupa tres comportamientos: marcar la clase como fuente de configuración (`@Configuration`), activar la autoconfiguración (`@EnableAutoConfiguration`) y activar el escaneo de componentes (`@ComponentScan`).
- **`SpringApplication.run()`**: el método que crea el contexto de la aplicación, ejecuta la autoconfiguración, escanea los componentes, levanta el servidor embebido y deja la aplicación corriendo.

Conecta con el punto de entrada clásico: es el mismo `public static void main` de siempre, solo que en vez de tu lógica va el arranque del framework. El hilo main ya no termina: queda vivo atendiendo peticiones.

## 3. El escaneo de componentes y el contenedor IoC

`@ComponentScan` busca en el paquete de la clase anotada (y subpaquetes) todas las clases marcadas con estereotipos: `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`. Cada una que encuentra la **instancia una vez** y la guarda en el **contenedor IoC** (Inversión de Control), también llamado ApplicationContext.

El contenedor IoC es el corazón de Spring: un mapa de objetos ya creados y conectados entre sí. Cuando una clase necesita otra, no hace `new`, la **pide** con `@Autowired` o (mejor) por constructor, y el contenedor se la entrega. Esto es la inyección de dependencias: nadie crea sus propias dependencias, todas vienen de fuera.

```java
@Service
public class PedidoService {
    private final PedidoRepository repositorio;

    // Spring inyecta automaticamente el repositorio al construir el servicio
    public PedidoService(PedidoRepository repositorio) {
        this.repositorio = repositorio;
    }
}

@RestController
public class PedidoController {
    private final PedidoService servicio;

    public PedidoController(PedidoService servicio) {
        this.servicio = servicio;
    }
}
```

Desde el constructor hacia abajo todo son objetos normales de Java: puedes probarlos con `new` en un test JUnit sin Spring. Esa es la clave de la testabilidad: el framework conecta, tu código no depende del framework.

## 4. La autoconfiguracion: convencion sobre configuracion

`@EnableAutoConfiguration` hace que Spring Boot mire qué librerías hay en el classpath y configure automáticamente lo necesario. Si detecta `spring-boot-starter-web`, levanta Tomcat y el despachador MVC. Si detecta un driver de base de datos (H2, PostgreSQL), configura un DataSource. Si detecta Spring Security, activa la cadena de filtros de seguridad.

Todo esto sin una línea de configuración tuya. Y cuando quieres cambiar un valor por defecto, lo haces con **propiedades** en `application.properties` o `application.yml`:

```properties
server.port=8081
spring.datasource.url=jdbc:postgresql://localhost:5432/midb
spring.datasource.username=app
```

El principio se llama **convención sobre configuración**: el framework elige valores razonables por defecto, y tú solo declaras las excepciones. No configurar nada ya funciona; configurar es sobrescribir.

## 5. El ciclo de arranque paso a paso

Cuando ejecutas `SpringApplication.run()` ocurre esta secuencia:

1. Se crea el `ApplicationContext` (el contenedor, inicialmente vacío).
2. Se ejecuta la autoconfiguración: cada starter aporta sus beans por defecto.
3. Se escanean tus componentes (`@Service`, `@Repository`, `@Controller`...) y se registran como beans.
4. Se resuelven las inyecciones: cada constructor recibe sus dependencias ya creadas.
5. Se levanta el servidor embebido (Tomcat por defecto, puerto 8080).
6. Se ejecutan los `CommandLineRunner` / `ApplicationRunner` si los hay (código que corre justo después del arranque, útil para cargar datos iniciales).
7. La aplicación queda corriendo hasta que recibe señal de apagado.

Si algo falla (un bean que no se puede construir, el puerto ocupado), el arranque aborta con un mensaje en el banner de Spring. Aprende a leer esos mensajes: el 90% de los errores de arranque son dependencias que Spring no pudo resolver, y el log te dice exactamente cuál.

## 6. El ApplicationContext como ciudadano accesible

Rara vez necesitas tocar el contexto directamente, pero conviene saber que es un objeto real con API: puedes pedirle beans, consultar perfiles activos o publicar eventos. En la práctica lo ves en dos sitios: en tests (`@SpringBootTest` levanta el contexto completo para probar contra él) y en código de arranque (`CommandLineRunner` recibe el contexto implícitamente al ejecutarse dentro de él).

---

## Errores Comunes

> **Poner la clase Application fuera del paquete raiz**. `@ComponentScan` solo escanea hacia abajo desde el paquete donde esta `@SpringBootApplication`. Si tus controllers estan en `com.otro`, Spring no los ve y los endpoints devuelven 404 sin ningun error en el log.

> **Dos metodos main o dos clases con @SpringBootApplication**. El empaquetado no sabe cual es la principal y falla, o arranca la equivocada. Una sola clase de arranque por proyecto.

> **Hacer new de un @Service en vez de inyectarlo**. Si creas el objeto a mano, Spring no le inyecta sus dependencias y obtienes NullPointerException dentro del servicio. Todo lo que Spring gestiona se pide, no se crea.

> **Confundir @Component, @Service y @Repository**. Tecnicamente los tres registran un bean igual; la diferencia es semantica (y `@Repository` anade traduccion de excepciones de persistencia). Usa cada uno en su capa: el nombre documenta la intencion.

---

## Conexiones

- [[02 - Punto de Entrada main]] - El main de Spring Boot es el mismo main de Java
- [[22 - Separacion de Responsabilidades]] - Capas Service/Repository/Controller e inyeccion por constructor
- [[25 - Paquetes y Organizacion]] - La estructura de paquetes decide que ve el escaneo
- [[50 - Herencia del parent de Spring Boot en Maven]] - Como llega Spring Boot a tu proyecto
- [[52 - Starters de Spring Boot en Maven]] - Los starters que activan la autoconfiguracion
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Probar contra el contexto levantado
- [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]] - Como se registran y conectan los beans

---

## Tags
`#java #spring-boot #arranque #ioc #autoconfiguracion`
