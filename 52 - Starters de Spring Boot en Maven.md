---
tags: [java, maven, spring-boot, starters, dependencias]
---

# 52 - Starters de Spring Boot en Maven

Un starter es una dependencia "paquete" que agrupa todo lo necesario para una capacidad: `spring-boot-starter-web` trae el servidor, MVC y JSON de una vez. En vez de cazar librerías sueltas y sus versiones, declaras la capacidad que quieres y el starter resuelve el resto.

---

# METODO EXPLICATIVO

## 1. La idea: dependencias por capacidad, no por libreria

En un proyecto tradicional declarabas cada librería por separado: el servidor, el framework web, el conversor JSON, la validación... y cada una con su versión, que además tenían que ser compatibles entre sí. Un starter invierte el razonamiento: dices **qué quieres hacer** ("web", "datos JPA", "seguridad", "tests") y el starter trae el conjunto probado de librerías que lo hace posible.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Sin versión (la decide el parent) y sin más detalle. Esa sola declaración arrastra transitivamente a Tomcat embebido, Spring MVC y Jackson. Tu pom dice la intención; Maven resuelve los medios.

## 2. Los starters que veras en casi todo proyecto

Cada starter cubre una capacidad con nombre predecible (`spring-boot-starter-*`):

- **`spring-boot-starter-web`**: aplicaciones web y APIs REST. Tomcat + Spring MVC + Jackson. Si tu app expone endpoints HTTP, lo necesitas.
- **`spring-boot-starter-data-jpa`**: persistencia con JPA/Hibernate. Si guardas entidades en base de datos relacional, este es el tuyo.
- **`spring-boot-starter-validation`**: validación declarativa con anotaciones (`@NotNull`, `@Size`). Capa de entrada de datos.
- **`spring-boot-starter-security`**: autenticación y autorización. Activa la cadena de filtros de seguridad.
- **`spring-boot-starter-test`**: JUnit, Mockito, AssertJ y utilidades de test. Solo para el alcance `test`.
- **`spring-boot-starter-thymeleaf`**: plantillas HTML del lado servidor. Solo si renderizas vistas.

La regla práctica: si necesitas una capacidad, busca primero si existe un starter oficial antes de añadir librerías sueltas. El starter garantiza el conjunto compatible; las piezas sueltas te obligan a ser tú el garante.

## 3. Dependencias transitivas: lo que llega sin pedirlo

Cuando declaras un starter, Maven descarga también todo lo que ese starter necesita: son las **dependencias transitivas**. `spring-boot-starter-web` no contiene código propio casi; es una lista que arrastra a Tomcat, a Spring MVC, a Jackson y a su vez a lo que cada uno de ellos necesita.

Esto es cómodo pero tiene una consecuencia: tu classpath final es mucho mayor que tu pom. Dos comandos te permiten inspeccionarlo cuando algo falla (versiones en conflicto, clases duplicadas):

```xml
<!-- mvn dependency:tree : muestra el arbol completo de dependencias -->
<!-- mvn dependency:analyze : detecta dependencias usadas no declaradas y declaradas no usadas -->
```

El segundo es especialmente valioso: te dice si estás usando una librería que solo te llega de rebote (frágil: si el starter la quita, tu código deja de compilar) para que la declares explícitamente.

## 4. Starters de terceros y el sufijo invertido

La convención de nombres distingue el origen: los starters oficiales de Spring empiezan por `spring-boot-starter-*`. Los de la comunidad usan el orden inverso (`*-spring-boot-starter`), por ejemplo `postgresql-spring-boot-starter` o `testcontainers-spring-boot-starter`. Si ves un starter con el nombre "al revés", es de un tercero: funciona igual, pero la garantía de compatibilidad la da su autor, no Spring.

## 5. Cuando un starter trae demasiado

A veces un starter arrastra algo que no quieres: el caso clásico es `spring-boot-starter-web` trayendo Tomcat cuando prefieres Jetty, o un starter de logging trayendo Logback cuando usas Log4j2. Maven permite **excluir** transitivas concretas:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jetty</artifactId>
</dependency>
```

Excluyes lo que sobra y declaras el sustituto. Úsalo con moderación: cada exclusión es una decisión de compatibilidad que asumes tú en vez de Spring.

---

## Errores Comunes

> **Anadir librerias sueltas existiendo un starter**. Declarar `jackson-databind` y `tomcat-embed-core` por separado en vez de `spring-boot-starter-web` te convierte en responsable de unas versiones que Spring ya probo juntas. Busca el starter primero.

> **Usar clases que llegan por transitividad sin declararlas**. Compila hoy porque el starter las arrastra, pero si el starter cambia en la proxima version tu codigo se rompe. Lo que importas en tu codigo, lo declaras en tu pom.

> **Mezclar starters de generaciones distintas**. Un starter de Spring Boot 2.x con parent 3.x arrastra versiones viejas que chocan con el resto. Todos los starters deben ser de la misma generacion que el parent.

> **Confundir starters oficiales con los de terceros**. Los oficiales (`spring-boot-starter-*`) siguen el ciclo de releases de Spring; los de terceros (`*-spring-boot-starter`) dependen de su mantenedor. Revisa que el de terceros soporte tu version de Boot antes de anadirlo.

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - El pom donde se declaran los starters
- [[49 - Spring Boot arranque de aplicacion]] - Cada starter activa una parte de la autoconfiguracion
- [[50 - Herencia del parent de Spring Boot en Maven]] - El parent que fija las versiones de los starters
- [[53 - Dependencias de prueba y alcance test]] - El starter de test y su alcance especial
- [[56 - Validacion con Spring Boot]] - El starter de validacion en accion
- [[66 - Jackson y JSON en Java]] - El Jackson que el starter-web trae consigo

---

## Tags
`#java #maven #spring-boot #starters #dependencias`
