---
tags: [java, fundamentos, glosario, referencia, terminologia]
---

# 99 - Glosario Rapido

Referencia alfabetica de terminos clave. Cada entrada tiene definicion y enlace al tema completo.

---

## A

**Abstraccion** -- Ocultar complejidad mostrando solo lo esencial. En Java: `abstract class`, `interface`. -> [[01 - Clases y Estructura Basica]]

**Acceso (Modificadores)** -- `public`, `protected`, `default`, `private`. Controlan visibilidad. -> [[05 - Modificadores de Acceso]]

**Alias / Aliasing** -- Dos o mas referencias apuntando al mismo objeto en Heap. `a = b; a.x = 1` --> `b.x == 1`. -> [[09 - Multiples Objetos e Identidad]]

**API** -- Contrato publico (clases, metodos, constantes) que una libreria expone.

**Argumento** -- Valor real pasado al llamar metodo: `metodo(42)` -> `42` es argumento. Ver Parametro.

**Array** -- Objeto contenedor tamano fijo, indice `0..length-1`. `int[]`, `String[]`. -> [[14 - Arrays Basicos y args]]

**ArrayList** -- Implementacion `List` respaldada por array dinamico. -> [[15 - Listas y ArrayList]]

**Atributo / Campo / Field** -- Variable declarada en clase (fuera de metodos). Estado del objeto. -> [[06 - Atributos y Campos]]

**Autoboxing / Unboxing** -- Conversion automatica `int` <-> `Integer`, `double` <-> `Double`, etc. -> [[03 - Tipos Primitivos y Referencia]]

**Autenticacion** -- Verificar identidad (quien eres): login, token, certificado. Distinto de autorizacion. -> [[57 - Spring Security autenticacion y cadena de filtros]]

**Authorization Server** -- Servidor que autentica y emite tokens (Keycloak, Auth0). Tu API es resource server, no esto. -> [[62 - Spring Security OAuth2 Resource Server y JWT]]

**Autorizacion** -- Verificar permisos (que puedes hacer): roles, reglas por ruta. -> [[57 - Spring Security autenticacion y cadena de filtros]]

**Autoconfiguracion (Spring Boot)** -- Spring configura automaticamente lo que detecta en el classpath. Convencion sobre configuracion. -> [[49 - Spring Boot arranque de aplicacion]]

**Autowired** -- Anotacion que pide una dependencia al contenedor. Preferida en el constructor (o implicita si es unico). -> [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]]

---

## B

**Bloque de Inicializacion Estatico** -- `static { ... }` Ejecuta una vez al cargar clase. -> [[17 - Flujo de Ejecucion JVM]]

**Bloque de Inicializacion de Instancia** -- `{ ... }` Ejecuta cada `new`, antes del constructor. -> [[07 - Constructores y this]]

**Builder Pattern** -- Patron para construir objetos complejos paso a paso. -> [[07 - Constructores y this]]

**Bytecode** -- Codigo intermedio `.class` ejecutado por JVM.

---

## C

**CamelCase / lowerCamelCase** -- `miVariable`, `calcularTotal()`. Estandar variables/metodos. -> [[18 - Convenciones de Nombrado]]

**Casteo / Cast** -- Conversion explicita de tipo: `(int) 3.14`, `(String) obj`. Riesgo `ClassCastException`.

**Clase** -- Plantilla (`class`) que define atributos y metodos. Molde para objetos. -> [[01 - Clases y Estructura Basica]]

**Clase Wrapper** -- `Integer`, `Double`, `Boolean`, etc. Envoltura objeto de primitivo. -> [[03 - Tipos Primitivos y Referencia]]

**Copia Defensiva** -- Clonar objeto/coleccion mutable al entrar/salir para evitar aliasing. -> [[21 - Getters y Setters]]

**Cohesion** -- Grado en que elementos de un modulo/pertenecen juntos. Alta = buena. -> [[19 - Separacion de Responsabilidades]]

**CQS (Command-Query Separation)** -- Principio: cada metodo es O command O query, nunca ambos. -> [[46 - Metodos de Consulta y Tell Don't Ask]]

**Contenedor IoC** -- ApplicationContext de Spring: crea, guarda y conecta los beans. Inyeccion de dependencias. -> [[49 - Spring Boot arranque de aplicacion]]

**Coleccion** -- `List`, `Set`, `Map`, `Queue`. Estructuras de datos dinamicas.

**Bean** -- Objeto gestionado por el contenedor IoC de Spring. Scope por defecto: singleton. -> [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]]

**Bearer Token** -- Esquema de cabecera Authorization: `Bearer <token>`. El JWT viaja asi en cada peticion. -> [[62 - Spring Security OAuth2 Resource Server y JWT]]

**Constante** -- `static final` + inmutable. Convencion `UPPER_SNAKE_CASE`. -> [[04 - Variables y Literales]], [[18 - Convenciones de Nombrado]]

**Constructor** -- Metodo especial sin retorno. Inicializa objeto. -> [[07 - Constructores y this]]

**Contexto Estatico** -- Dentro de miembros `static`. No hay `this`. Solo accede a `static`. -> [[13 - Static vs Instancia]]

**Acoplamiento (Coupling)** -- Dependencia entre modulos. Bajo = bueno. -> [[19 - Separacion de Responsabilidades]]

---

## D

**DIP (Dependency Inversion Principle)** -- Depender de abstracciones (interfaces), no concreciones. -> [[22 - Separacion de Responsabilidades]]

**DTO (Data Transfer Object)** -- Objeto simple (solo datos) para mover entre capas. -> [[22 - Separacion de Responsabilidades]]

**Client (Keycloak)** -- Aplicacion registrada dentro de un realm: la ficha de identidad de tu app ante Keycloak. -> [[65 - Keycloak (identidad y seguridad)]]

**Client Credentials** -- Flujo OAuth2 servicio-a-servicio: se autentica con client-id/secret y recibe token sin humano. -> [[65 - Keycloak (identidad y seguridad)]]

**Docker Compose** -- Archivo YAML que levanta el entorno completo (base de datos, cache) con un comando. -> [[59 - Docker Compose]]

---

## E

**Encapsulamiento** -- Ocultar estado (`private`) y exponer comportamiento controlado (`public` metodos). -> [[05 - Modificadores de Acceso]]

**Enum** -- `enum` define conjunto fijo de constantes. -> [[03 - Tipos Primitivos y Referencia]]

**Equals / HashCode** -- Contrato: `a.equals(b)` implica `a.hashCode() == b.hashCode()`. -> [[09 - Multiples Objetos e Identidad]]

**Excepcion** -- Evento disruptivo (`throw`). Checked (obliga manejo) vs unchecked (`RuntimeException`).

**Expresion** -- Codigo que produce valor: `a + b`, `cond ? x : y`. -> [[12 - Operador Ternario]]

---

## F

**Field** -- Ver Atributo.

**Final** -- `final` variable = no reasignable, `final` metodo = no overridable, `final` clase = no heredable. -> [[06 - Atributos y Campos]]

**Frame (Stack Frame)** -- Bloque en Stack por llamada a metodo. -> [[17 - Flujo de Ejecucion JVM]]

---

## G

**Garbage Collector (GC)** -- Recolector de basura: libera objetos inalcanzables en Heap. -> [[17 - Flujo de Ejecucion JVM]]

**Generics** -- Tipos parametrizados `List<T>`, `Map<K,V>`. Type safety en compile-time. -> [[15 - Listas y ArrayList]]

**Getters / Setters** -- Metodos `getX()`/`setX()` (o `isX()` para `boolean`). -> [[21 - Getters y Setters]]

**Ley de Demeter** -- No hables con extraños: un metodo solo invoca metodos en sus amigos directos (this, parametros, objetos que crea, campos directos). -> [[46 - Metodos de Consulta y Tell Don't Ask]]

---

## H

**Heap** -- Memoria compartida donde viven objetos (`new`) y arrays. -> [[08 - Instanciacion y new]], [[17 - Flujo de Ejecucion JVM]]

**Herencia** -- `extends`. Subclase reutiliza/extiende superclase. -> [[01 - Clases y Estructura Basica]]

---

## I

**Identidad** -- Direccion de memoria unica de objeto. `==` compara identidad. -> [[09 - Multiples Objetos e Identidad]]

**Igualdad** -- Equivalencia logica de contenido. `.equals()`. -> [[09 - Multiples Objetos e Identidad]]

**Import** -- Declaracion que trae una clase de otro paquete al archivo. Sin coste en runtime. -> [[61 - Imports en profundidad]]

**JSON** -- Formato de texto para intercambio de datos: Jackson lo convierte a/de objetos Java. -> [[66 - Jackson y JSON en Java]]

**Inmutabilidad** -- Objeto cuyo estado no cambia tras construccion. `record`, `String`, `LocalDate`. -> [[21 - Getters y Setters]], [[47 - List.copyOf() (inmutabilidad)]]

**Instancia** -- Objeto concreto creado con `new`. Tiene identidad, estado, comportamiento. -> [[08 - Instanciacion y new]]

**Interface** -- `interface` Contrato con metodos abstractos, `default`, `static`, `private`. -> [[01 - Clases y Estructura Basica]]

**Interface** -- `interface` Contrato con metodos abstractos, `default`, `static`, `private`. -> [[01 - Clases y Estructura Basica]]

**Interfaces funcionales** -- Interfaz con un solo metodo abstracto. Base de las lambdas. -> [[63 - Interfaces y Override (implementar contratos)]], [[42 - Lambda en Profundidad]]

**Inyeccion de Dependencias (DI)** -- Recibir dependencias por constructor en vez de crear dentro. -> [[22 - Separacion de Responsabilidades]]

---

## J

**Javadoc** -- Comentarios `/** ... */` con etiquetas `@param`, `@return`, `@throws`. -> [[20 - Javadoc y Documentacion]]

**JVM (Java Virtual Machine)** -- Maquina virtual: carga clases, ejecuta bytecode, gestiona memoria. -> [[17 - Flujo de Ejecucion JVM]]

**JUnit** -- Libreria estandar de pruebas unitarias en Java: `@Test`, assertions, `assertThrows`. -> [[48 - JUnit y pruebas de unidad]]

**JWT (JSON Web Token)** -- Token firmado en tres partes: header.payload.signature. Payload legible (base64), firma garante de integridad. -> [[62 - Spring Security OAuth2 Resource Server y JWT]]

---

## L

**Lambda** -- Funcion anonima: `(a, b) -> a + b`. Implementa Functional Interface. -> [[16 - Bucles y Control de Flujo]]

**Literal** -- Valor escrito en codigo: `42`, `3.14f`, `'A'`, `"Hola"`, `true`. -> [[04 - Variables y Literales]]

**List.copyOf()** -- Crea copia inmutable de una coleccion. Sin null, copia real, UnsupportedOperationException en modificacion. -> [[47 - List.copyOf() (inmutabilidad)]]

---

## M

**Main** -- `public static void main(String[] args)`. Punto de entrada JVM. -> [[02 - Punto de Entrada main]]

**Maven (Parent)** -- `spring-boot-starter-parent`: herencia de versiones compatibles y configuracion base. -> [[50 - Herencia del parent de Spring Boot en Maven]]

**Maven (POM)** -- `pom.xml`: descriptor del proyecto (coordenadas, dependencias, build). -> [[55 - Cierre del descriptor Maven]]

**Maven (Scope test)** -- Dependencias solo para pruebas: no viajan al jar final. -> [[53 - Dependencias de prueba y alcance test]]

**MockMvc** -- Simula peticiones HTTP contra controllers sin servidor real. Con `@WebMvcTest`. -> [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]]

**Metodo** -- Bloque nombrado con parametros, retorno, cuerpo. -> [[10 - Metodos de Instancia]], [[23 - Metodos - Parametros, Retorno y Return]]

**Mapper (Keycloak)** -- Configuracion que decide que claims entran en el token (roles, nombre, scopes). -> [[65 - Keycloak (identidad y seguridad)]]

**Metodo Estatico** -- `static`. Pertenece a clase. Sin `this`. -> [[13 - Static vs Instancia]]

**Metodos de Consulta (Query Methods)** -- Metodos que devuelven informacion sobre el estado sin modificarlo ni efectos secundarios. Opuesto a command methods. -> [[46 - Metodos de Consulta y Tell Don't Ask]]

**Modelo Anemico** -- Clase con solo getters/setters sin logica de negocio. Anti-patron. Opuesto a modelo rico. -> [[46 - Metodos de Consulta y Tell Don't Ask]]

**Metodo de Instancia** -- Sin `static`. Requiere objeto. Accede a `this`. -> [[10 - Metodos de Instancia]]

**Modulo (Java 9+)** -- `module-info.java` Define exports/requires. -> [[25 - Paquetes y Organizacion]]

**OAuth2** -- Framework de autorizacion: servidor de autorizacion emite tokens, resource server los valida. -> [[62 - Spring Security OAuth2 Resource Server y JWT]]

**@Override** -- Anotacion de verificacion: el compilador comprueba que el metodo implementa o sobrescribe. -> [[63 - Interfaces y Override (implementar contratos)]]

---

## N

**Null** -- Referencia que apunta a nada. `NullPointerException` al usarla. -> [[09 - Multiples Objetos e Identidad]]

**ObjectMapper** -- Clase nucleo de Jackson: convierte objetos a JSON (writeValueAsString) y JSON a objetos (readValue). -> [[66 - Jackson y JSON en Java]]

---

## O

**Objeto** -- Instancia de clase. Estado + Comportamiento + Identidad. -> [[08 - Instanciacion y new]]

**Operador Ternario** -- `condicion ? valorSi : valorNo`. Expresion condicional. -> [[12 - Operador Ternario]]

**Optional** -- Contenedor que puede o no tener valor. Evita null. -> [[23 - Metodos - Parametros, Retorno y Return]]

---

## P

**Paquete** -- `package com.empresa.modulo`. Namespace jerarquico. -> [[25 - Paquetes y Organizacion]]

**Parametro** -- Variable en la firma del metodo. Java = paso por valor. -> [[23 - Metodos - Parametros, Retorno y Return]]

**PascalCase / UpperCamelCase** -- `MiClase`, `FacturaCliente`. Estandar clases. -> [[18 - Convenciones de Nombrado]]

**Polimorfismo** -- Mismo mensaje (`obj.metodo()`), comportamiento distinto segun tipo real.

**Realm (Keycloak)** -- Espacio de identidad aislado: usuarios, roles y configuracion propios. -> [[65 - Keycloak (identidad y seguridad)]]

**Primary / Qualifier** -- Resuelven colisiones de beans del mismo tipo: default y eleccion concreta. -> [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]]

**Primitivo** -- `byte, short, int, long, float, double, boolean, char`. Valor directo. -> [[03 - Tipos Primitivos y Referencia]]

**Prueba de Unidad** -- Codigo que comprueba automaticamente un metodo: estructura Arrange-Act-Assert. -> [[48 - JUnit y pruebas de unidad]]

---

## R

**Record (Java 14+)** -- `record Punto(int x, int y) {}` Clase inmutable concisa. -> [[01 - Clases y Estructura Basica]]

**Referencia** -- Variable que guarda direccion de objeto en Heap. -> [[03 - Tipos Primitivos y Referencia]]

**Return** -- `return valor;` Sale de metodo devolviendo valor. `void` -> solo `return;`. -> [[23 - Metodos - Parametros, Retorno y Return]]

---

## S

**Scope (Ambito)** -- Region donde una variable es visible. -> [[04 - Variables y Literales]]

**SecurityFilterChain** -- Bean que configura la cadena de filtros de seguridad: reglas por ruta, sesion, CSRF. -> [[57 - Spring Security autenticacion y cadena de filtros]]

**Spotless** -- Plugin que formatea el codigo automaticamente: `spotless:check` verifica, `spotless:apply` aplica. -> [[60 - Spotless y formato automatico]]

**Starter (Spring Boot)** -- Dependencia paquete por capacidad (`starter-web`, `starter-test`): trae el conjunto compatible. -> [[52 - Starters de Spring Boot en Maven]]

**Separacion de Responsabilidades (SRP)** -- Una clase, una responsabilidad. -> [[19 - Separacion de Responsabilidades]], [[22 - Separacion de Responsabilidades]]

**Stack** -- Memoria por hilo: frames de metodos, locales. -> [[17 - Flujo de Ejecucion JVM]]

**Static** -- Miembro de clase, no instancia. Uno solo compartido. -> [[13 - Static vs Instancia]]

**Stream API** -- `list.stream().filter().map().toList()`. Procesamiento funcional de colecciones. -> [[24 - ArrayList - Metodos Avanzados]]

**String** -- Clase inmutable para texto. Literal `"..."` -> String Pool. -> [[03 - Tipos Primitivos y Referencia]]

**Switch Expression (Java 14+)** -- `switch (x) { case 1 -> "uno"; default -> "otro"; }`. -> [[16 - Bucles y Control de Flujo]]

---

## T

**Tell, Don't Ask** -- Dile al objeto que haga algo, no le preguntes datos para decidir fuera. -> [[22 - Separacion de Responsabilidades]], [[46 - Metodos de Consulta y Tell Don't Ask]]

**Text Blocks (Java 15+)** -- `"""..."""` para Strings multilinea. -> [[11 - System.out y Concatenacion]]

**Tipo de Dato** -- Primitivo o referencia. Define operaciones y memoria. -> [[03 - Tipos Primitivos y Referencia]]

---

## U

**UPPER_SNAKE_CASE** -- `MAX_SIZE`, `IVA`. Constantes `static final`. -> [[18 - Convenciones de Nombrado]]

---

## V

**Validacion (Bean Validation)** -- Reglas declarativas en el DTO (`@NotBlank`, `@Email`) evaluadas con `@Valid`. -> [[56 - Validacion con Spring Boot]]

**Var (Java 10+)** -- `var x = "hola";` Inferencia de tipo en variables locales. -> [[04 - Variables y Literales]]

**Variable** -- Nombre + Tipo + Valor. Local, campo o parametro. -> [[04 - Variables y Literales]]

**Varargs** -- `void m(String... args)` recibe 0..N argumentos como array. -> [[23 - Metodos - Parametros, Retorno y Return]]

**Resource Server** -- API que protege recursos y valida tokens JWT en vez de hacer login. -> [[62 - Spring Security OAuth2 Resource Server y JWT]]

**Serializacion** -- Convertir un objeto a formato transferible (JSON con Jackson). Inversa: deserializacion. -> [[66 - Jackson y JSON en Java]]

**Void** -- Tipo de retorno "sin valor". Metodo solo produce efecto lateral. -> [[10 - Metodos de Instancia]]

---

## W

**Wrapper** -- Ver Clase Wrapper.

---

## Referencias Cruzadas

| Concepto | Nota Principal |
|----------|---------------|
| Clases, objetos, `new` | [[01 - Clases y Estructura Basica]], [[08 - Instanciacion y new]] |
| `main`, args, arranque | [[02 - Punto de Entrada main]] |
| Tipos, primitivos, wrappers | [[03 - Tipos Primitivos y Referencia]] |
| Variables, literales, constantes | [[04 - Variables y Literales]] |
| Modificadores de acceso | [[05 - Modificadores de Acceso]] |
| Atributos, campos, estado | [[06 - Atributos y Campos]] |
| Constructores, `this`, sobrecarga | [[07 - Constructores y this]] |
| Identidad, igualdad, `equals` | [[09 - Multiples Objetos e Identidad]] |
| Metodos, `void`, `return` | [[10 - Metodos de Instancia]], [[23 - Metodos - Parametros, Retorno y Return]] |
| `System.out`, concatenacion | [[11 - System.out y Concatenacion]] |
| Operador ternario | [[12 - Operador Ternario]] |
| `static` vs instancia | [[13 - Static vs Instancia]] |
| Arrays, `args` | [[14 - Arrays Basicos y args]] |
| Listas, ArrayList | [[15 - Listas y ArrayList]], [[24 - ArrayList - Metodos Avanzados]] |
| Bucles, for, while | [[16 - Bucles y Control de Flujo]] |
| JVM, memoria, Stack, Heap | [[17 - Flujo de Ejecucion JVM]] |
| Convenciones de nombrado | [[18 - Convenciones de Nombrado]] |
| Separacion de responsabilidades | [[19 - Separacion de Responsabilidades]], [[22 - Separacion de Responsabilidades]] |
| Javadoc, documentacion | [[20 - Javadoc y Documentacion]] |
| Getters y Setters | [[21 - Getters y Setters]] |
| Paquetes, organizacion | [[25 - Paquetes y Organizacion]] |
| JUnit, pruebas de unidad | [[48 - JUnit y pruebas de unidad]] |
| Spring Boot, arranque, IoC | [[49 - Spring Boot arranque de aplicacion]] |
| Maven parent, propiedades | [[50 - Herencia del parent de Spring Boot en Maven]], [[51 - Propiedades Maven y version de Java]] |
| Starters, dependencias, scopes | [[52 - Starters de Spring Boot en Maven]], [[53 - Dependencias de prueba y alcance test]] |
| Plugin Boot, descriptor pom | [[54 - Plugin de construccion de Spring Boot]], [[55 - Cierre del descriptor Maven]] |
| Validacion, seguridad | [[56 - Validacion con Spring Boot]], [[57 - Spring Security autenticacion y cadena de filtros]] |
| MockMvc, SpringBootTest | [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] |
| Docker Compose, Spotless, imports | [[59 - Docker Compose]], [[60 - Spotless y formato automatico]], [[61 - Imports en profundidad]] |
| OAuth2, JWT, resource server | [[62 - Spring Security OAuth2 Resource Server y JWT]] |
| Interfaces, contratos, @Override | [[63 - Interfaces y Override (implementar contratos)]] |
| @Configuration, @Bean, @Autowired | [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]] |
| Keycloak, realm, client, mappers | [[65 - Keycloak (identidad y seguridad)]] |
| Jackson, JSON, ObjectMapper | [[66 - Jackson y JSON en Java]] |

---

## Tags
`#java #fundamentos #glosario #referencia #terminologia`
