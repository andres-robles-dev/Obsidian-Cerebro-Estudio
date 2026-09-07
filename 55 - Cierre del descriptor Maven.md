---
tags: [java, maven, pom, descriptor, build, estructura]
---

# 55 - Cierre del descriptor Maven

El `pom.xml` es el descriptor completo del proyecto: coordenadas, parent, propiedades, dependencias y construcción. Este tema recorre el documento entero de principio a fin para que sepas leer cualquier pom real y escribir el tuyo sin copiar a ciegas.

---

# METODO EXPLICATIVO

## 1. Que es el descriptor y como se lee

El `pom.xml` (Project Object Model) es un documento XML que describe **todo** lo que Maven necesita saber: quién es el proyecto, de qué hereda, qué necesita y cómo se construye. Se lee de arriba abajo en cinco bloques, siempre en el mismo orden por convención:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 1. Coordenadas: quien soy -->
    <!-- 2. Parent: de quien heredo -->
    <!-- 3. Properties: mis variables -->
    <!-- 4. Dependencies: que necesito -->
    <!-- 5. Build: como me construyo -->

</project>
```

La cabecera (`modelVersion 4.0.0`) es ritual: siempre igual, nunca se toca. Lo interesante empieza debajo.

## 2. Bloque 1: coordenadas del proyecto

```xml
<groupId>com.miempresa</groupId>
<artifactId>mi-aplicacion</artifactId>
<version>1.0.0</version>
<name>Mi Aplicacion</name>
<description>Aplicacion de ejemplo con Spring Boot</description>
<packaging>jar</packaging>
```

Las tres coordenadas (`groupId`, `artifactId`, `version`) son la **identidad** del proyecto: así lo encontrarán otros proyectos si lo publicas. El `groupId` sigue la convención de dominio invertido (`com.miempresa`), igual que los paquetes Java. El `packaging` dice qué produce el build: `jar` para aplicaciones Boot (el plugin lo hace ejecutable), `war` si despliegas en servidor externo, `pom` si es un proyecto padre agregador.

## 3. Bloque 2 a 4: parent, propiedades y dependencias

Estos tres bloques ya tienen tema propio; aquí solo hay que ver cómo encajan juntos:

```xml
<!-- 2. Herencia: versiones y configuracion base -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.4.1</version>
    <relativePath/>
</parent>

<!-- 3. Variables: version de Java y valores propios -->
<properties>
    <java.version>21</java.version>
</properties>

<!-- 4. Necesidades: capacidades (starters) + driver + test -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

El orden no es casual: el parent debe ir antes porque propiedades y dependencias pueden usar lo que el parent define. Maven resuelve el pom en orden, así que declarar una dependencia que usa una propiedad definida más abajo sigue funcionando (Maven hace varias pasadas), pero el orden canónico ayuda a los humanos a leerlo.

## 4. Bloque 5: construccion y cierre

```xml
<!-- 5. Construccion: herramientas del build -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>

</project> <!-- cierre del descriptor -->
```

La etiqueta `</project>` final cierra el documento: todo pom bien formado termina ahí. Un error frecuente al editar a mano es romper el balance de etiquetas (abrir `<dependency>` y olvidar cerrarla); el síntoma es un error de parseo XML antes siquiera de que Maven empiece a trabajar. Los IDEs modernos validan esto al vuelo: si el pom aparece subrayado en rojo, no ejecutes nada hasta arreglarlo.

## 5. El pom completo minimo y como verificarlo

Juntando los cinco bloques, el pom mínimo viable de una API Spring Boot cabe en unas cuarenta líneas. Para verificar que el descriptor es correcto y completo, estos son los comandos de diagnóstico:

```xml
<!-- mvn validate : comprueba que el pom es correcto (sintaxis y modelo) -->
<!-- mvn help:effective-pom : muestra el pom final tras aplicar la herencia -->
<!-- mvn dependency:tree : muestra todas las dependencias resueltas -->
```

La secuencia de verificación ante un pom que falla es siempre la misma: primero sintaxis (`validate`), luego herencia (¿resuelve el parent?), luego dependencias (¿existen las versiones?). Atacar en ese orden evita perseguir errores de dependencias cuando el problema real es una etiqueta mal cerrada.

---

## Errores Comunes

> **Etiquetas XML desbalanceadas al editar a mano**. Un `<dependency>` sin su `</dependency>` produce un error de parseo críptico. Deja que el IDE formatee y valide el XML antes de ejecutar Maven.

> **Duplicar secciones**. Poner dos bloques `<dependencies>` o dos `<properties>` hace que Maven solo tenga en cuenta uno (o falle). Cada seccion aparece una sola vez; todo lo nuevo se anade dentro de la existente.

> **Declarar dependencias fuera de `<dependencies>` o plugins fuera de `<build>`**. El pom compila como XML valido pero Maven ignora lo mal ubicado en silencio. Si anades algo y "no hace efecto", revisa primero en que seccion lo pusiste.

> **Olvidar el cierre `</project>` al pegar fragmentos**. Al copiar bloques de internet es facil dejar el documento sin cerrar. Todo pom termina con `</project>`: si tu archivo no termina asi, esta incompleto.

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - Donde vive el pom en el proyecto
- [[50 - Herencia del parent de Spring Boot en Maven]] - El bloque parent
- [[51 - Propiedades Maven y version de Java]] - El bloque properties
- [[52 - Starters de Spring Boot en Maven]] - El bloque dependencies
- [[53 - Dependencias de prueba y alcance test]] - Scopes dentro de dependencies
- [[54 - Plugin de construccion de Spring Boot]] - El bloque build

---

## Tags
`#java #maven #pom #descriptor #build`
