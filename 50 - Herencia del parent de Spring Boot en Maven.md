---
tags: [java, maven, spring-boot, parent, dependencias, build]
---

# 50 - Herencia del parent de Spring Boot en Maven

Todo proyecto Spring Boot con Maven declara un `parent`: `spring-boot-starter-parent`. Esa herencia le regala al proyecto versiones compatibles de todas las dependencias, configuración por defecto de plugins y propiedades útiles. Sin él tendrías que gestionar cada versión a mano.

---

# METODO EXPLICATIVO

## 1. Que es un parent en Maven

Maven permite que un `pom.xml` herede de otro mediante la sección `<parent>`. El hijo recibe todo lo del padre: dependencias gestionadas, configuración de plugins y propiedades. Es el mismo mecanismo de la herencia en Java llevado a la configuración de construcción.

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.4.1</version>
    <relativePath/>
</parent>
```

Con esas cinco líneas tu proyecto hereda cientos de decisiones ya tomadas por el equipo de Spring: qué versión de Tomcat funciona con qué versión de Spring, qué versión de Jackson, de Hibernate, de JUnit. La etiqueta `<relativePath/>` vacía le dice a Maven que no busque el padre en el disco local sino que lo descargue del repositorio.

## 2. Dependency management: el verdadero regalo

Lo más valioso que heredas no son dependencias, sino el **dependency management**: una tabla gigante de "si pides X sin versión, usa esta". Gracias a eso tus dependencias se declaran sin `<version>`:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <!-- Sin version: la decide el parent -->
    </dependency>
</dependencies>
```

Esto elimina una categoría entera de errores: versiones incompatibles entre librerías que deberían ir juntas. Spring prueba cada release del parent como un conjunto coherente, así que mientras no fuerces versiones a mano, todo encaja.

## 3. Que mas heredas sin darte cuenta

Además de las versiones, el parent configura:

- **Versión de Java por defecto** y codificación UTF-8 (sobrescribibles con propiedades).
- **Configuración de plugins** comunes: compilador, surefire (tests), jar, resources.
- **Perfiles y recursos**: filtrado de `application.properties` para inyectar valores del pom.

Por eso un pom de Spring Boot es tan corto comparado con uno tradicional: la mitad del archivo vive en el padre y tú solo declaras lo específico de tu proyecto.

## 4. Cuando NO quieres heredar el parent

Hay proyectos que ya tienen un parent propio (por ejemplo, el parent corporativo de tu empresa). Maven solo permite **un** padre, así que en ese caso no puedes heredar de Spring Boot. La alternativa es importar solo la tabla de versiones con scope `import`:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.4.1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Esto importa únicamente el dependency management (las versiones), sin la configuración de plugins ni las propiedades. Es el plan B oficial; si no tienes parent corporativo, usa la herencia normal.

## 5. Version del parent y version de Java

La versión del parent fija la generación de Spring Boot (2.x, 3.x) y cada generación exige una versión mínima de Java: Spring Boot 2.x funciona con Java 8+, Spring Boot 3.x exige Java 17+. Si tu JDK es anterior al mínimo, el proyecto ni compila. Elegir la versión del parent es, indirectamente, elegir el Java que necesitas instalado.

---

## Errores Comunes

> **Declarar versiones a mano dentro de dependencias gestionadas**. Si el parent ya gestiona `jackson-databind` y tu pones otra version distinta, rompes la coherencia probada por Spring. Solo fija versiones para librerias que el parent no gestiona.

> **Olvidar `<relativePath/>` y que Maven busque el padre en disco**. Sin esa etiqueta Maven intenta resolver el parent como un modulo local del mismo proyecto y falla con un error confuso si no lo encuentra.

> **Actualizar el parent sin revisar la version minima de Java**. Subir de Spring Boot 2.7 a 3.x con JDK 11 deja el proyecto sin compilar. Lee siempre la nota de release: la version de Java requerida cambia entre generaciones.

> **Heredar el parent y ademas importar spring-boot-dependencies**. Es redundante: la herencia ya incluye el dependency management. El import solo tiene sentido cuando NO puedes usar el parent.

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - Estructura Maven donde vive el pom
- [[49 - Spring Boot arranque de aplicacion]] - Lo que el parent trae al proyecto
- [[51 - Propiedades Maven y version de Java]] - Como sobrescribir la version de Java del parent
- [[52 - Starters de Spring Boot en Maven]] - Las dependencias sin version que el parent gestiona
- [[54 - Plugin de construccion de Spring Boot]] - El plugin que el parent preconfigura

---

## Tags
`#java #maven #spring-boot #parent #dependencias`
