---
tags: [java, spring-boot, ioc, configuration, bean, autowired, inyeccion-dependencias, beans]
---

# 64 - Inyeccion de dependencias (Configuration, Bean, Autowired)

La inyección de dependencias es el mecanismo por el que Spring crea y conecta los objetos de tu aplicación: nadie construye sus propias dependencias, todos las **reciben**. Hay dos formas de declarar beans (escaneo con `@Component` y configuración explícita con `@Configuration` + `@Bean`) y una forma correcta de pedirlos (constructor con `@Autowired` o implícito). Este tema profundiza en las tres piezas y en sus reglas.

---

# METODO EXPLICATIVO

## 1. Las dos formas de registrar beans: escaneo vs configuracion explicita

El contenedor IoC necesita saber qué objetos gestionar (los "beans"). Hay dos caminos, y elegir bien entre ellos es la decisión más importante de la configuración:

**Escaneo** (`@Component` y estereotipos `@Service`, `@Repository`, `@RestController`): Spring descubre tus propias clases, las instancia y las registra automáticamente.

**Configuración explícita** (`@Configuration` + `@Bean`): tú escribes un método que **construye** el objeto y Spring lo registra tal como lo devuelves.

```java
@Configuration
public class ConfiguracionInfraestructura {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();  // clase de Spring, no puedes anotarla con @Service
    }

    @Bean
    public Reloj reloj() {
        return Reloj.sistema();     // control total: como se construye, con que parametros
    }
}
```

La regla de decisión es simple: si la clase es **tuya**, usa estereotipos de escaneo (menos código, descubrimiento automático). Si es de una **librería** (no puedes o no debes anotar su código fuente), o si su construcción necesita decisiones (parámetros, variantes, condiciones), usa `@Configuration` + `@Bean`. Ambos caminos producen exactamente el mismo resultado: un bean en el contenedor.

## 2. @Configuration en detalle: clase de fabrica con garantias

`@Configuration` no es una anotación decorativa: marca la clase como **fuente de definiciones de beans** y activa una garantía importante: los métodos `@Bean` se interceptan de modo que **llamar al método siempre devuelve el mismo objeto** (el bean del contenedor), no uno nuevo:

```java
@Configuration
public class Config {

    @Bean
    public Motor motor() {
        return new Motor("V8");
    }

    @Bean
    public Coche coche() {
        return new Coche(motor());   // NO crea un segundo motor: devuelve el mismo bean
    }
}
```

Aunque `motor()` parezca un método que se puede llamar, dentro de una `@Configuration` cada invocación resuelve al bean único. Si usas `@Configuration(proxyBeanMethods = false)` (optimización para arranque), esa garantía desaparece y los métodos `@Bean` no deben llamarse entre sí. La convención segura: en métodos `@Bean`, recibe las dependencias como **parámetros**, no llamando a otros métodos `@Bean`:

```java
@Bean
public Coche coche(Motor motor) {   // Spring te pasa el bean, cero ambiguedad
    return new Coche(motor);
}
```

## 3. @Autowired: por campo vs por constructor

`@Autowired` pide al contenedor que llene una dependencia. Puede ir en tres sitios, pero solo uno es correcto en producción:

```java
@Service
public class PedidoService {

    // MAL: inyeccion por campo. Impide probar sin Spring, oculta dependencias
    @Autowired
    private PedidoRepository repositorio;

    // ACEPTABLE: setter, para dependencias opcionales (raro que lo necesites)
    @Autowired
    public void setRepositorio(PedidoRepository r) { this.repositorio = r; }

    // CORRECTO: constructor. Funciona sin Spring, con new en tests
    private final Notificador notificador;

    @Autowired  // opcional desde Spring 4.3 si hay un solo constructor
    public PedidoService(Notificador notificador) {
        this.notificador = notificador;
    }
}
```

La inyección por constructor gana en tres frentes: los campos pueden ser `final` (inmutabilidad), la clase **funciona con `new` en un test JUnit sin levantar Spring**, y la lista de parámetros es documentación honesta de lo que la clase necesita. Por eso desde Spring 4.3, si la clase tiene **un único constructor**, la anotación se puede omitir: el constructor es el modo por defecto.

## 4. Colisiones: @Primary y @Qualifier

Cuando hay **varios beans del mismo tipo**, el contenedor no puede adivinar cuál inyectar y el arranque falla con `NoUniqueBeanDefinitionException`. Las herramientas para resolverlo:

```java
@Configuration
public class ConfigNotificadores {

    @Bean @Primary                      // el predeterminado si no se aclara nada
    public Notificador notificadorEmail() { return new NotificadorEmail(); }

    @Bean("notificadorSms")
    public Notificador notificadorSms() { return new NotificadorSms(); }
}

@Service
public class Alertas {

    private final Notificador notificador;

    public Alertas(@Qualifier("notificadorSms") Notificador notificador) {
        this.notificador = notificador;   // pido el especifico por nombre
    }
}
```

`@Primary` dice "por defecto, este"; `@Qualifier` en el punto de consumo dice "este concreto, sin importar el default". La política sana: **un `@Primary` para el caso general y `@Qualifier` donde la elección importa**. Si estás poniendo `@Qualifier` en la mayoría de los puntos de consumo, son probablemente conceptos distintos y deberían ser tipos distintos.

## 5. Perfiles y condiciones: beans que existen solo a veces

No todos los beans deben existir siempre. Los **perfiles** activan configuraciones por entorno, y las condiciones activan por reglas:

```java
@Bean
@Profile("dev")                          // solo si el perfil dev esta activo
public DataSource dataSourceDev() { return new DataSourceH2(); }

@Bean
@Profile("prod")
public DataSource dataSourceProd() { return new DataSourcePostgres(); }

@Bean
@ConditionalOnProperty(name = "cache.enabled", havingValue = "true")
public Cache cache() { return new Cache(); }
```

Los perfiles resuelven el problema clásico: la misma aplicación con distintas bases de datos por entorno. `@ConditionalOnProperty` (y familia) es más común en librerías: el bean solo existe si una propiedad de configuración lo pide. Recuerda que la no existencia también es información: si un bean condicional no se crea, el código que lo necesita falla con un error claro de bean ausente.

## 6. Ciclo de vida: @PostConstruct, @PreDestroy y scope

Un bean tiene vida: se crea, se usa y se destruye con el contexto. Los hooks estándar:

```java
@Component
public class ConectorBD {

    @PostConstruct       // tras la inyeccion de dependencias, antes de servir peticiones
    public void conectar() { /* inicializacion que necesita deps resueltas */ }

    @PreDestroy          // al apagar el contexto
    public void desconectar() { /* liberar recursos */ }
}
```

`@PostConstruct` existe porque un constructor no puede: en el momento del constructor, las dependencias aún no están inyectadas. Toda inicialización que dependa de otras dependencias va ahí, no en el constructor. Por defecto los beans son **singleton**: una única instancia compartida por toda la aplicación, lo que obliga a que sean sin estado (o con estado protegido). Existen otros scopes (`prototype`, `request`...) pero el 99% del código debe ser singleton sin estado: si tu bean guarda estado mutable por petición, es un bug en espera de aparecer.

## 7. Probar componentes inyectados sin Spring

El gran pago de la inyección por constructor: en tests, la clase se prueba con `new` y mocks, sin arrancar el contenedor:

```java
@Test
void alertaEnviaSms() {
    Notificador mock = Mockito.mock(Notificador.class);
    Alertas alertas = new Alertas(mock);       // Spring no participa

    alertas.enviar("mensaje");

    Mockito.verify(mock).enviar("mensaje");
}
```

Si necesitas levantar Spring (config de beans, perfiles, wire completo), eso es un test de integración con `@SpringBootTest`. La frontera sana: unit tests con `new` para lógica, tests de contexto cuando lo que pruebas es la configuración en sí.

---

## Errores Comunes

> **@Autowired en campo por comodidad**. Impide `final`, obliga a reflexion para testear y oculta las dependencias. Constructor siempre: una dependencia por parametro y sin `@Autowired` si es el unico constructor.

> **Crear dependencias con new dentro de un @Service**. El contenedor no controla ese objeto: sin inyeccion, sin sustituibilidad, sin mocks en test. Todo lo que necesita la clase se declara como parametro del constructor.

> **Dos beans del mismo tipo sin @Primary ni @Qualifier**. El arranque aborta con NoUniqueBeanDefinitionException. Decidir el default con @Primary y los casos concretos con @Qualifier.

> **Llamar a metodos @Bean entre si en una @Configuration**. Funciona con proxy (devuelve el mismo bean) pero rompe con proxyBeanMethods = false. Recibe las dependencias como parametros del metodo @Bean.

> **Inicializar en el constructor lo que necesita dependencias inyectadas**. En el constructor los campos aun son null. Cualquier inicializacion con dependencias va en @PostConstruct.

> **Guardar estado mutable en un bean singleton**. Dos peticiones simultaneas comparten el mismo bean: estado por peticion se pisa. Los beans deben ser sin estado; el estado de la peticion va en los objetos del flujo.

> **Inyeccion circular (A necesita B y B necesita A)**. El contenedor lo detecta y aborta (o con warning si usa proxies). Indica que la responsabilidad esta mal repartida: extraer la parte comun a un tercer componente.

---

## Conexiones

- [[49 - Spring Boot arranque de aplicacion]] - El escaneo de componentes y el arranque del contexto
- [[22 - Separacion de Responsabilidades]] - La inyeccion por constructor como disciplina de arquitectura
- [[27 - Singleton en Profundidad]] - El scope singleton comparado con el patron manual
- [[63 - Interfaces y Override (implementar contratos)]] - Los beans se inyectan por contrato
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Levantar el contexto en pruebas de integracion
- [[48 - JUnit y pruebas de unidad]] - Probar sin Spring con new y mocks

---

## Tags
`#java #spring-boot #ioc #inyeccion-dependencias #beans`
