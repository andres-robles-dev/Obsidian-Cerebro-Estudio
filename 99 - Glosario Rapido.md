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

**Coleccion** -- `List`, `Set`, `Map`, `Queue`. Estructuras de datos dinamicas.

**Constante** -- `static final` + inmutable. Convencion `UPPER_SNAKE_CASE`. -> [[04 - Variables y Literales]], [[18 - Convenciones de Nombrado]]

**Constructor** -- Metodo especial sin retorno. Inicializa objeto. -> [[07 - Constructores y this]]

**Contexto Estatico** -- Dentro de miembros `static`. No hay `this`. Solo accede a `static`. -> [[13 - Static vs Instancia]]

**Acoplamiento (Coupling)** -- Dependencia entre modulos. Bajo = bueno. -> [[19 - Separacion de Responsabilidades]]

---

## D

**DIP (Dependency Inversion Principle)** -- Depender de abstracciones (interfaces), no concreciones. -> [[22 - Separacion de Responsabilidades]]

**DTO (Data Transfer Object)** -- Objeto simple (solo datos) para mover entre capas. -> [[22 - Separacion de Responsabilidades]]

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

---

## H

**Heap** -- Memoria compartida donde viven objetos (`new`) y arrays. -> [[08 - Instanciacion y new]], [[17 - Flujo de Ejecucion JVM]]

**Herencia** -- `extends`. Subclase reutiliza/extiende superclase. -> [[01 - Clases y Estructura Basica]]

---

## I

**Identidad** -- Direccion de memoria unica de objeto. `==` compara identidad. -> [[09 - Multiples Objetos e Identidad]]

**Igualdad** -- Equivalencia logica de contenido. `.equals()`. -> [[09 - Multiples Objetos e Identidad]]

**Inmutabilidad** -- Objeto cuyo estado no cambia tras construccion. `record`, `String`, `LocalDate`. -> [[21 - Getters y Setters]]

**Instancia** -- Objeto concreto creado con `new`. Tiene identidad, estado, comportamiento. -> [[08 - Instanciacion y new]]

**Interface** -- `interface` Contrato con metodos abstractos, `default`, `static`, `private`. -> [[01 - Clases y Estructura Basica]]

**Inyeccion de Dependencias (DI)** -- Recibir dependencias por constructor en vez de crear dentro. -> [[22 - Separacion de Responsabilidades]]

---

## J

**Javadoc** -- Comentarios `/** ... */` con etiquetas `@param`, `@return`, `@throws`. -> [[20 - Javadoc y Documentacion]]

**JVM (Java Virtual Machine)** -- Maquina virtual: carga clases, ejecuta bytecode, gestiona memoria. -> [[17 - Flujo de Ejecucion JVM]]

---

## L

**Lambda** -- Funcion anonima: `(a, b) -> a + b`. Implementa Functional Interface. -> [[16 - Bucles y Control de Flujo]]

**Literal** -- Valor escrito en codigo: `42`, `3.14f`, `'A'`, `"Hola"`, `true`. -> [[04 - Variables y Literales]]

---

## M

**Main** -- `public static void main(String[] args)`. Punto de entrada JVM. -> [[02 - Punto de Entrada main]]

**Metodo** -- Bloque nombrado con parametros, retorno, cuerpo. -> [[10 - Metodos de Instancia]], [[23 - Metodos - Parametros, Retorno y Return]]

**Metodo Estatico** -- `static`. Pertenece a clase. Sin `this`. -> [[13 - Static vs Instancia]]

**Metodo de Instancia** -- Sin `static`. Requiere objeto. Accede a `this`. -> [[10 - Metodos de Instancia]]

**Modulo (Java 9+)** -- `module-info.java` Define exports/requires. -> [[25 - Paquetes y Organizacion]]

---

## N

**Null** -- Referencia que apunta a nada. `NullPointerException` al usarla. -> [[09 - Multiples Objetos e Identidad]]

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

**Primitivo** -- `byte, short, int, long, float, double, boolean, char`. Valor directo. -> [[03 - Tipos Primitivos y Referencia]]

---

## R

**Record (Java 14+)** -- `record Punto(int x, int y) {}` Clase inmutable concisa. -> [[01 - Clases y Estructura Basica]]

**Referencia** -- Variable que guarda direccion de objeto en Heap. -> [[03 - Tipos Primitivos y Referencia]]

**Return** -- `return valor;` Sale de metodo devolviendo valor. `void` -> solo `return;`. -> [[23 - Metodos - Parametros, Retorno y Return]]

---

## S

**Scope (Ambito)** -- Region donde una variable es visible. -> [[04 - Variables y Literales]]

**Separacion de Responsabilidades (SRP)** -- Una clase, una responsabilidad. -> [[19 - Separacion de Responsabilidades]], [[22 - Separacion de Responsabilidades]]

**Stack** -- Memoria por hilo: frames de metodos, locales. -> [[17 - Flujo de Ejecucion JVM]]

**Static** -- Miembro de clase, no instancia. Uno solo compartido. -> [[13 - Static vs Instancia]]

**Stream API** -- `list.stream().filter().map().toList()`. Procesamiento funcional de colecciones. -> [[24 - ArrayList - Metodos Avanzados]]

**String** -- Clase inmutable para texto. Literal `"..."` -> String Pool. -> [[03 - Tipos Primitivos y Referencia]]

**Switch Expression (Java 14+)** -- `switch (x) { case 1 -> "uno"; default -> "otro"; }`. -> [[16 - Bucles y Control de Flujo]]

---

## T

**Tell, Don't Ask** -- Dile al objeto que haga algo, no le preguntes datos para decidir fuera. -> [[22 - Separacion de Responsabilidades]]

**Text Blocks (Java 15+)** -- `"""..."""` para Strings multilinea. -> [[11 - System.out y Concatenacion]]

**Tipo de Dato** -- Primitivo o referencia. Define operaciones y memoria. -> [[03 - Tipos Primitivos y Referencia]]

---

## U

**UPPER_SNAKE_CASE** -- `MAX_SIZE`, `IVA`. Constantes `static final`. -> [[18 - Convenciones de Nombrado]]

---

## V

**Var (Java 10+)** -- `var x = "hola";` Inferencia de tipo en variables locales. -> [[04 - Variables y Literales]]

**Variable** -- Nombre + Tipo + Valor. Local, campo o parametro. -> [[04 - Variables y Literales]]

**Varargs** -- `void m(String... args)` recibe 0..N argumentos como array. -> [[23 - Metodos - Parametros, Retorno y Return]]

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

---

## Tags
`#java #fundamentos #glosario #referencia #terminologia`
