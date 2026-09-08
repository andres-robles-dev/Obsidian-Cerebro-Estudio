---
tags: [java, maven, spring-boot, testing, scope-test, dependencias]
---

# 53 - Dependencias de prueba y alcance test

Las dependencias de prueba (JUnit, Mockito, AssertJ) solo se necesitan para compilar y ejecutar los tests, nunca en la aplicación en producción. Maven lo expresa con el alcance `test`: esas librerías están disponibles en `src/test` pero no viajan al `.jar` final ni contaminan el classpath de ejecución.

---

# METODO EXPLICATIVO

## 1. Que significa el alcance (scope) de una dependencia

Cada dependencia Maven tiene un alcance que dice **cuándo está disponible**: al compilar, al probar, al ejecutar, o siempre. Los dos que importan en el día a día son el alcance por defecto (`compile`: disponible en todas partes) y `test` (solo visible para el código de pruebas).

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

Con `<scope>test</scope>`, JUnit y Mockito existen para `src/test/java` pero son invisibles para `src/main/java`. Esto no es un capricho organizativo: es una **garantía de arquitectura**. Si tu código de producción intentara importar `org.junit`, la compilación fallaría. El alcance impide físicamente que la lógica de negocio dependa de las herramientas de prueba.

## 2. El starter de test: todo el arsenal de pruebas en una linea

`spring-boot-starter-test` agrupa el stack completo de testing de Spring Boot: JUnit 5 como motor, Mockito para dobles de prueba, AssertJ para aserciones fluidas, más utilidades de Spring Test (`MockMvc`, `TestRestTemplate`) y soporte JSON para tests. Una línea y tienes todo el laboratorio montado, con versiones coherentes gracias al parent.

La consecuencia práctica: en un proyecto Spring Boot estándar no declaras JUnit suelto. Si lo ves declarado por separado con versión manual, es herencia de un proyecto anterior a Boot o alguien que no conocía el starter.

## 3. Que viaja al jar final y que no

Cuando el plugin de Spring Boot construye el `.jar` ejecutable, empaqueta las dependencias de alcance `compile` (y `runtime`) dentro del `BOOT-INF/lib`, pero **excluye** las de alcance `test`. Tu artefacto de producción no contiene JUnit, ni Mockito, ni AssertJ: es más pequeño y no expone herramientas de test en un entorno productivo.

Esta separación también protege contra un fallo sutil: código de test que se cuela en producción. Como el compilador nunca ve las librerías de test desde `src/main`, ese error es imposible por construcción, no por disciplina.

## 4. Los demas alcances que conviene conocer

Además de `compile` (por defecto) y `test`, hay tres que aparecen en proyectos reales:

- **`provided`**: la necesita para compilar pero el entorno la aporta al ejecutar. Caso típico: la API de servlets cuando despliegas en un Tomcat externo en vez del embebido (ya no es lo habitual con Boot, pero existe).
- **`runtime`**: no la necesita para compilar (no la importas en tu código) pero sí al ejecutar. Caso típico: el driver JDBC de PostgreSQL. Tu código solo usa la API `java.sql`; la implementación concreta solo se carga en ejecución.
- **`import`** (solo en `dependencyManagement`): importa la tabla de versiones de otro pom, como el plan B del parent.

El caso del driver JDBC merece atención porque es el patrón más común: declaras el driver como `runtime` y tu código queda desacoplado de la base de datos concreta. Cambiar de PostgreSQL a H2 para los tests es cambiar una dependencia, no una línea de Java.

## 5. Donde viven los tests y por que importa

Maven impone la separación física: `src/main/java` para producción, `src/test/java` para pruebas, con la misma estructura de paquetes en espejo. El compilador aplica alcances distintos a cada árbol: `main` ve `compile` + `provided`; `test` ve todo lo anterior más `test` y `runtime`.

Esta asimetría es deliberada: los tests pueden ver la producción (para probarla), pero la producción nunca puede ver los tests. La dirección de la dependencia está grabada en la estructura de carpetas.

---

## Errores Comunes

> **Olvidar `<scope>test</scope>` en el starter de test**. Sin el, JUnit viaja al jar de produccion y tu codigo principal puede importar clases de test. El starter oficial ya trae el scope; si lo declaras a mano, no lo omitas.

> **Importar clases de test desde codigo de produccion**. Aunque el alcance lo impide al compilar, algunos IDEs lo permiten temporalmente y luego el build de Maven falla. Si necesitas una clase en ambos lados, vive en `src/main`, no en `src/test`.

> **Declarar el driver JDBC en compile en vez de runtime**. Funciona, pero acopla tu codigo a una base de datos concreta y sugiere que la importas directamente. En `runtime` documenta la intencion: solo se necesita al ejecutar.

> **Poner logica de negocio en src/test para reutilizarla**. Los tests no se empaquetan: si otro modulo necesita esa clase, no la encontrara. El codigo reutilizable siempre va en `src/main`.

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - La separacion src/main y src/test
- [[48 - JUnit y pruebas de unidad]] - Lo que el starter de test pone a tu disposicion
- [[50 - Herencia del parent de Spring Boot en Maven]] - El parent que versiona el starter de test
- [[52 - Starters de Spring Boot en Maven]] - El starter de test como caso especial
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Que se prueba con estas dependencias
- [[62 - Spring Security OAuth2 Resource Server y JWT]] - Tokens de test firmados en el classpath

---

## Tags
`#java #maven #spring-boot #testing #dependencias`
