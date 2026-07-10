---
tags: [java, fundamentos, glosario, referencia, terminologia]
---

# 99 - Glosario Rápido

> **Referencia alfabética** de términos clave. Cada entrada: definición 1 línea + enlace a nota profunda.

---

## A

**Abstracción** — Ocultar complejidad mostrando solo lo esencial. En Java: `abstract class`, `interface`. → `[[01 - Clases y Estructura Basica]]`

**Acceso (Modificadores)** — `public`, `protected`, `default`, `private`. Controlan visibilidad. → `[[05 - Modificadores de Acceso]]`

**Alias / Aliasing** — Dos o más referencias apuntando al **mismo objeto** en Heap. `a = b; a.x = 1 → b.x == 1`. → `[[09 - Multiples Objetos e Identidad]]`

**API (Application Programming Interface)** — Contrato público (clases, métodos, constantes) que una librería expone.

**Argumento** — Valor **real** pasado al llamar método: `metodo(42)` → `42` es argumento. Ver *Parámetro*.

**Array** — Objeto contenedor tamaño fijo, índice `0..length-1`. `int[]`, `String[]`. → `[[14 - Arrays Basicos y args]]`

**Atributo / Campo / Field** — Variable declarada en clase (fuera de métodos). Estado del objeto. → `[[06 - Atributos y Campos]]`

**Autoboxing / Unboxing** — Conversión automática `int ↔ Integer`, `double ↔ Double`, etc. → `[[03 - Tipos Primitivos y Referencia]]`

---

## B

**Bloque de Inicialización Estático** — `static { ... }` Ejecuta **una vez** al cargar clase. → `[[15 - Flujo de Ejecucion JVM]]`

**Bloque de Inicialización de Instancia** — `{ ... }` Ejecuta **cada `new`**, antes del constructor. → `[[07 - Constructores y this]]`

**Boxing** — Ver *Autoboxing*.

**Builder Pattern** — Patrón para construir objetos complejos paso a paso: `Usuario.builder().nombre("A").email("a@b").build()`.

**Bytecode** — Código intermedio `.class` ejecutado por JVM. Independiente de plataforma.

---

## C

**CamelCase / lowerCamelCase** — `miVariable`, `calcularTotal()`. Estándar variables/métodos. → `[[16 - Convenciones de Nombrado]]`

**Casteo / Cast** — Conversión explícita de tipo: `(int) 3.14`, `(String) obj`. Riesgo `ClassCastException`.

**Clase** — Plantilla (`class`) que define atributos y métodos. Molde para objetos. → `[[01 - Clases y Estructura Basica]]`

**Clase Abstracta** — `abstract class`: no instanciable, puede tener métodos abstractos y concretos. → `[[01 - Clases y Estructura Basica]]`

**Clase Anónima** — `new Interface() { ... }` Implementación *ad-hoc* de interfaz/clase abstracta.

**Clase Interna / Nested** — Clase dentro de otra. `static` = *nested*; sin `static` = *inner* (tiene `this` externo).

**Clase Wrapper** — `Integer`, `Double`, `Boolean`, `Character`, `Long`, `Byte`, `Short`, `Float`. Envoltura objeto de primitivo.

**Cohesión** — Grado en que elementos de un módulo/clase pertenecen juntos. **Alta** = buena. → `[[17 - Separacion de Responsabilidades]]`

**Colección** — `List`, `Set`, `Map`, `Queue`... Estructuras de datos dinámicas (`java.util.*`).

**Composición** — "Tiene un" fuerte (`class Motor { Pieza p; }` ciclo de vida ligado). Vs *Agregación* (vida independiente).

**Concurrencia** — Múltiples hilos ejecutando código compartiendo memoria. `Thread`, `ExecutorService`, `java.util.concurrent`.

**Constante** — `static final` + inmutable. Convención `UPPER_SNAKE_CASE`. → `[[04 - Variables y Literales]]`, `[[16 - Convenciones de Nombrado]]`

**Constructor** — Método especial `NombreClase(...)` sin retorno. Inicializa objeto. → `[[07 - Constructores y this]]`

**Contexto Estático** — Dentro de miembros `static`. No hay `this`. Solo accede a `static`. → `[[13 - Static vs Instancia]]`

**Coupling (Acoplamiento)** — Dependencia entre módulos. **Bajo** = bueno. → `[[17 - Separacion de Responsabilidades]]`

---

## D

**DIP (Dependency Inversion Principle)** — Depender de abstracciones (interfaces), no concreciones. → `[[17 - Separacion de Responsabilidades]]`

**DTO (Data Transfer Object)** — Objeto simple (solo datos, `record`/`class` con getters) para mover datos entre capas. → `[[17 - Separacion de Responsabilidades]]`

**Dynamic Binding / Dispatch** — Llamada a método resuelta en **runtime** según tipo real del objeto (polimorfismo).

---

## E

**Encapsulamiento** — Ocultar estado (`private`) y exponer comportamiento controlado (`public` métodos). → `[[05 - Modificadores de Acceso]]`, `[[06 - Atributos y Campos]]`

**Enum** — `enum Color { ROA `enum` define conjunto fijo de constantes (`ROJO, VERDE`). Tipo seguro. → `[[03 - Tipos Primitivos y Referencia]]`

**Equals / HashCode** — Contrato: `a.equals(b) → a.hashCode()==b.hashCode()`. Sobrescribir **ambos** juntos. → `[[09 - Multiples Objetos e Identidad]]`

**Excepción** — Evento disruptivo (`throw`). `checked` (obliga `try/catch`/`throws`) vs `unchecked` (`RuntimeException`).

**Expresión** — Código que **produce valor**: `a + b`, `cond ? x : y`, `metodo()`. Vs *Sentencia* (acción, `;`).

---

## F

**Field** — Ver *Atributo*.

**Final** — `final` variable = inmutable (referencia), `final` método = no overridable, `final` clase = no heredable.

**Frame (Stack Frame)** — Bloque en Stack por llamada a método: locales, operand stack, return address. → `[[15 - Flujo de Ejecucion JVM]]`

**Funcional Interface** — Interface con **un solo método abstracto** (SAM). Base de *lambdas*. `@FunctionalInterface`.

---

## G

**Garbage Collector (GC)** — Recolector de basura automático: libera objetos inalcanzables en Heap. Generacional (Young/Old). → `[[15 - Flujo de Ejecucion JVM]]`

**Generics / Genéricos** — Tipos parametrizados `List<T>`, `Map<K,V>`. Type safety en compile-time, *erasure* en runtime.

**Getters / Setters** — Métodos `getX()`, `setX()` para acceder/modificar campos `private`. Convención JavaBeans.

---

## H

**Heap (Montículo)** — Memoria compartida donde viven **objetos** (`new`) y arrays. Gestionado por GC. → `[[08 - Instanciacion y new]]`, `[[15 - Flujo de Ejecucion JVM]]`

**Herencia** — `extends`. Subclase reutiliza/extiende superclase. `Object` raíz universal. → `[[01 - Clases y Estructura Basica]]`

**Hidden Class / Clase Oculta** — Clases generadas en runtime (lambdas, proxies, reflection).

---

## I

**Identidad** — Dirección memoria única de objeto. `==` compara identidad. → `[[09 - Multiples Objetos e Identidad]]`

**Igualdad** — Equivalencia lógica de contenido. `.equals()` bien implementado. → `[[09 - Multiples Objetos e Identidad]]`

**Inmutabilidad** — Objeto cuyo estado **no cambia** tras construcción (`final` campos, sin setters, copias defensivas). `String`, `LocalDate`, `record`.

**Inicializador** — Ver *Bloque de Inicialización*.

**Instancia** — Objeto concreto creado con `new Clase()`. Tiene identidad, estado, comportamiento. → `[[08 - Instanciacion y new]]`

**Instanciar** — Ejecutar `new Clase()` → reserva Heap + constructor.

**Interface** — `interface` Contrato: métodos abstractos (implícitos `public abstract`), `default`, `static`, `private`. Múltiple implementación. → `[[01 - Clases y Estructura Basica]]`

**Inyección de Dependencias (DI)** — Contenedor provee dependencias (constructor/setter) en lugar de `new` manual. Spring `@Autowired`, CDI `@Inject`.

**Instancia Variable** — Ver *Atributo*.

**Invariante** — Condición que **siempre** cumple un objeto válido (ej: `saldo >= 0`). Constructor/métodos la garantizan.

---

## J

**JAR** — Java ARchive: `.class` + recursos + `MANIFEST.MF`. Ejecutable: `java -jar app.jar`.

**JDK** — Java Development Kit: `javac`, `java`, `jshell`, `jcmd`, librerías.

**JIT (Just-In-Time)** — Compilador JVM que traduce bytecode caliente a código nativo en runtime.

**JVM (Java Virtual Machine)** — Máquina virtual: carga clases, ejecuta bytecode, gestiona memoria (Stack/Heap/Metaspace), GC. → `[[15 - Flujo de Ejecucion JVM]]`

---

## L

**Lambda** — Función anónima: `(a,b) -> a+b`. Implementa *Functional Interface*. Closure sobre variables `final`/`effectively final`.

**Ley de Demeter** — "Habla solo con amigos inmediatos". `a.getB().getC().hacer()` ❌ → `a.hacerConC()` ✅.

**Literal** — Valor escrito en código: `42`, `3.14f`, `'A'`, `"Hola"`, `true`, `null`. → `[[04 - Variables y Literales]]`

**Local Variable** — Variable declarada **dentro de método/bloque**. Sin valor por defecto. Scope = bloque `{}`.

---

## M

**Main** — `public static void main(String[] args)` Punto de entrada JVM. → `[[02 - Punto de Entrada main]]`

**Método** — Bloque nombrado con parámetros, retorno, cuerpo. `void` = sin retorno. → `[[10 - Metodos de Instancia]]`

**Método Estático** — `static`. Pertenece a clase. Sin `this`. Invocado `Clase.metodo()`. → `[[13 - Static vs Instancia]]`

**Método de Instancia** — Sin `static`. Requiere objeto. `obj.metodo()`. Accede a `this`. → `[[10 - Metodos de Instancia]]`

**Método Sobrescrito (Override)** — Subclase redefine método instancia de padre. `@Override` annotation. Polimorfismo.

**Método Sobrecargado (Overload)** — Mismo nombre, **firma distinta** (parámetros). Resuelto en compile-time.

**Módulo (Java 9+)** — `module-info.java` Define exports/requires. Encapsulamiento fuerte.

**Mutabilidad** — Capacidad de cambiar estado tras creación. Opuesto a *Inmutabilidad*.

---

## N

**Null** — Referencia "apunta a nada". `NullPointerException` al dereferenciar. Evitar con `Optional`, null-checks, `@NotNull`.

**NPE (NullPointerException)** — Excepción #1 en Java. Acceso a miembro en `null`.

---

## O

**Objeto** — Instancia de clase. Estado (campos) + Comportamiento (métodos) + Identidad.

**Objeto Valor (Value Object)** — Inmutable, igualdad por contenido, sin identidad propia. `Dinero`, `LocalDate`, `record`.

**Overloading** — Ver *Método Sobrecargado*.

**Overriding** — Ver *Método Sobrescrito*.

---

## P

**Paquete** — `package com.ejemplo;` Agrupa clases relacionadas. Controla visibilidad `default`. → `[[16 - Convenciones de Nombrado]]`

**Parámetro** — Variable en **firma** del método: `void m(int x)` → `x` es parámetro. Ver *Argumento*.

**PascalCase / UpperCamelCase** — `MiClase`, `CalcularTotal()`. Estándar clases/interfaces/enums/records. → `[[16 - Convenciones de Nombrado]]`

**Polimorfismo** — Mismo mensaje (`obj.metodo()`), comportamiento distinto según tipo real. *Dynamic dispatch*.

**Pool (String Pool / Integer Cache)** — Zona especial Heap donde literales / valores pequeños se reusan. `"a" == "a"` true; `new String("a") == "a"` false.

**Primitivo** — `byte, short, int, long, float, double, boolean, char`. Valor directo, no objeto. → `[[03 - Tipos Primitivos y Referencia]]`

**Programación Orientada a Objetos (POO/OOP)** — Paradigma: encapsulamiento, herencia, polimorfismo, abstracción.

**Proyecto** — Estructura: `src/main/java`, `src/test/java`, `pom.xml`/`build.gradle`.

---

## R

**Record (Java 14+)** — `record Punto(int x, int y) {}` Clase inmutable concisa: campos `final`, constructor canónico, `equals/hashCode/toString`, getters `x()`, `y()`.

**Referencia** — Variable que guarda **dirección** de objeto en Heap. `String s = "hola";` → `s` es referencia.

**Reflection** — Inspección/manipulación runtime de clases (`Class<?>`, `Method`, `Field`). Costoso, rompe encapsulamiento.

**Return** — `return valor;` Sale de método devolviendo valor. `void` → `return;` opcional al final.

---

## S

**Scope (Ámbito)** — Región donde variable es visible. Clase, método, bloque `{}`, `for(;;)`.

**Sentencia (Statement)** — Instrucción que **realiza acción** (no produce valor): `if`, `for`, `x=1;`, `return;`.

**Serialización** — Convertir objeto → bytes (guardar/red). `Serializable`, `Externalizable`, JSON (Jackson/Gson).

**Setter** — Ver *Getters/Setters*.

**Shadowing (Sombreo)** — Variable local/parámetro con mismo nombre que campo: `int x` oculta `this.x`. Usa `this.x`.

**Single Responsibility Principle (SRP)** — Una clase, una razón para cambiar. → `[[17 - Separacion de Responsabilidades]]`

**Stack (Pila)** — Memoria por hilo: frames de métodos, locales, operand stack. Rápida, LIFO. → `[[15 - Flujo de Ejecucion JVM]]`

**StackOverflowError** — Stack agotado (recursión infinita, frames profundos). → `[[15 - Flujo de Ejecucion JVM]]`

**Static** — Miembro de **clase**, no instancia. Uno solo compartido. `static campo`, `static metodo`, `static block`. → `[[13 - Static vs Instancia]]`

**String** — Clase inmutable, secuencia caracteres UTF-16. Literal `"..."` → String Pool. `+` concatena. → `[[03 - Tipos Primitivos y Referencia]]`, `[[11 - System.out y Concatenacion]]`

**Superclase / Clase Padre** — Clase extendida (`extends Padre`). `Object` si none.

**This** — Referencia al **objeto actual** (`this.campo`, `this()`, `this.metodo()`). No existe en `static`. → `[[07 - Constructores y this]]`

---

## T

**Tell, Don't Ask** — Manda al objeto (`obj.hazlo()`), no le preguntas datos para decidir fuera (`if (obj.getX()) obj.setY()`). → `[[17 - Separacion de Responsabilidades]]`

**Tipo de Dato** — Clasificación de valores: primitivo o referencia. Define operaciones válidas y memoria. → `[[03 - Tipos Primitivos y Referencia]]`

**Type Erasure (Borrado de Tipos)** — Genéricos existen solo en compile-time. `List<String>` → `List` en runtime.

**Type Parameter** — `T`, `E`, `K`, `V` en `class Box<T>`. Placeholder de tipo. → `[[16 - Convenciones de Nombrado]]`

---

## U

**UML** — Unified Modeling Language. Diagramas clases, secuencia, casos de uso.

**Unboxing** — Ver *Autoboxing*.

**UPPER_SNAKE_CASE** — `MAX_SIZE`, `URL_BASE`. Constantes `static final`. → `[[16 - Convenciones de Nombrado]]`

---

## V

**Value Object** — Ver *Objeto Valor*.

**Var (Java 10+)** — `var x = "hola";` Inferencia tipo local. No en campos, parámetros, retorno.

**Variable** — Nombre + Tipo + Valor (en memoria). Local, campo, parámetro. → `[[04 - Variables y Literales]]`

**Varargs** — `void m(String... args)` Recibe 0..N argumentos como array. Solo último parámetro.

**Visibilidad** — Ver *Modificadores de Acceso*.

**VO (Value Object)** — Ver *Objeto Valor*.

---

## W

**Wrapper** — Ver *Clase Wrapper*.

---

## Referencias Cruzadas Rápidas

| Concepto | Nota Principal |
|----------|----------------|
| Clases, Objetos, `new` | `[[01 - Clases y Estructura Basica]]`, `[[08 - Instanciacion y new]]` |
| `main`, args, arranque | `[[02 - Punto de Entrada main]]` |
| `int`, `String`, `boolean`, literales | `[[03 - Tipos Primitivos y Referencia]]`, `[[04 - Variables y Literales]]` |
| `private`, `public`, encapsulamiento | `[[05 - Modificadores de Acceso]]`, `[[06 - Atributos y Campos]]` |
| Constructores, `this`, sobrecarga | `[[07 - Constructores y this]]` |
| Identidad vs Igualdad, `equals`, `hashCode` | `[[09 - Multiples Objetos e Identidad]]` |
| Métodos, `void`, `return`, sobrecarga | `[[10 - Metodos de Instancia]]` |
| `println`, `printf`, `+`, `StringBuilder` | `[[11 - System.out y Concatenacion]]` |
| `? :` ternario | `[[12 - Operador Ternario]]` |
| `static` vs instancia, memoria | `[[13 - Static vs Instancia]]`, `[[15 - Flujo de Ejecucion JVM]]` |
| Arrays, `args`, `for-each` | `[[14 - Arrays Basicos y args]]` |
| Nombrado: Pascal, camel, UPPER_SNAKE | `[[16 - Convenciones de Nombrado]]` |
| Capas, DIP, SRP, Domain/Infra | `[[17 - Separacion de Responsabilidades]]` |

---

> **Uso**: `Ctrl+Click` en enlaces `[[...]]` para saltar a nota completa. Busca con `Ctrl+F` término aquí.