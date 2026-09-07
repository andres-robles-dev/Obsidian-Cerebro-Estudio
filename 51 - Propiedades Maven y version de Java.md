---
tags: [java, maven, propiedades, version-java, compilador, pom]
---

# 51 - Propiedades Maven y version de Java

Las propiedades de Maven son variables con nombre (`<java.version>`, `<project.build.sourceEncoding>`) que centralizan valores repetidos en el `pom.xml`. La más importante en un proyecto Spring Boot es la que fija la versión de Java con la que se compila: una sola línea decide el nivel de lenguaje de todo el proyecto.

---

# METODO EXPLICATIVO

## 1. Que es una propiedad en Maven

Una propiedad es un par nombre-valor declarado en `<properties>` y usable en cualquier parte del pom con sintaxis `${nombre}`. Su función es la misma que una constante en código: **un solo punto de cambio** para un valor que se usa en varios sitios.

```xml
<properties>
    <java.version>21</java.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

Maven sustituye cada `${java.version}` que encuentre (en plugins, recursos filtrados, perfiles) por su valor antes de construir. Si mañana subes de Java 21 a 25, cambias un carácter en un sitio y todo el build lo respeta.

## 2. java.version: la propiedad que manda en Spring Boot

El parent de Spring Boot define la propiedad `<java.version>` y la usa para configurar el plugin del compilador (`maven-compiler-plugin`): qué sintaxis acepta (`source`/`release`) y contra qué API compila. Redefinirla en tu pom es la forma oficial de elegir el Java del proyecto:

```xml
<properties>
    <java.version>21</java.version>
</properties>
```

Esto equivale a compilar con `--release 21`: puedes usar records, pattern matching y text blocks, pero si intentas usar algo de una versión posterior, el compilador lo rechaza. La propiedad es un contrato: "este proyecto es Java 21".

Importante: la propiedad decide con qué nivel compila Maven, pero **el JDK que ejecuta la compilación debe ser igual o superior**. Si `java.version` es 21 y tu `JAVA_HOME` apunta a un JDK 17, el build falla. Propiedad e instalación tienen que estar de acuerdo.

## 3. sourceEncoding: la propiedad silenciosa

```xml
<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
```

Fija la codificación con la que Maven lee tus `.java` y tus recursos. El parent de Spring Boot ya la trae como UTF-8, así que normalmente no la tocas. Pero conviene saber que existe porque su ausencia es un bug clásico: en Windows, sin esta propiedad Maven usa la codificación del sistema y los caracteres no ASCII (tildes, eñes) se corrompen en la compilación. Si ves `?` donde debería haber una `ñ`, este es el primer sitio donde mirar.

## 4. Propiedades personalizadas y filtrado de recursos

Puedes definir tus propias propiedades y usarlas tanto en el pom como, con filtrado activado, dentro de `application.properties`:

```xml
<properties>
    <java.version>21</java.version>
    <app.version>1.4.0</app.version>
</properties>
```

```properties
# application.properties con filtrado: @...@ se sustituye en el build
info.app.version=@app.version@
```

Así la versión visible de la aplicación en ejecución siempre coincide con la del pom, sin duplicar el dato. El mecanismo se llama **filtrado de recursos**: Maven copia los recursos sustituyendo los marcadores por los valores de las propiedades.

## 5. Ver las propiedades efectivas

Cuando heredas de un parent, tu pom real es la suma del tuyo más el del padre más los valores por defecto de Maven. Para ver el resultado final existe el plugin de ayuda:

```xml
<!-- En terminal, dentro del proyecto -->
<!-- mvn help:effective-pom : muestra el pom completo tras la herencia -->
<!-- mvn help:evaluate -Dexpression=java.version : muestra el valor de una propiedad -->
```

Ante cualquier duda ("¿qué versión de Java estoy usando realmente?"), no adivines: pregunta a Maven por el pom efectivo. La respuesta siempre está ahí.

---

## Errores Comunes

> **Confundir java.version con el JDK instalado**. La propiedad dice a Maven con que nivel compilar; el JDK instalado es el que compila. Si no coinciden (propiedad 21, JDK 17), el build falla aunque el pom este perfecto.

> **Poner la version de Java en el plugin del compilador a mano**. Funciona, pero duplica la configuracion y lucha contra el parent. La forma idiomatica en Spring Boot es solo `<java.version>` en properties.

> **Olvidar sourceEncoding en proyectos heredados sin parent**. Sin el parent de Spring Boot no hay UTF-8 por defecto y en Windows los acentos se rompen. Declara siempre la codificacion de forma explicita.

> **Usar ${...} en application.properties esperando sustitucion sin activar el filtrado**. Maven solo sustituye en recursos si el filtrado esta activo para esa carpeta. Sin el, el literal `${app.version}` llega tal cual a la aplicacion en ejecucion.

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - Donde vive el pom dentro de la estructura Maven
- [[50 - Herencia del parent de Spring Boot en Maven]] - El parent que define estas propiedades
- [[52 - Starters de Spring Boot en Maven]] - Las dependencias que compilan con este nivel de Java
- [[55 - Cierre del descriptor Maven]] - El pom completo donde van las properties

---

## Tags
`#java #maven #propiedades #version-java #pom`
