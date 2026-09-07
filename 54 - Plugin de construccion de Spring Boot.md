---
tags: [java, maven, spring-boot, plugin, build, jar-ejecutable]
---

# 54 - Plugin de construccion de Spring Boot

El `spring-boot-maven-plugin` es el plugin que convierte tu proyecto en un `.jar` ejecutable autocontenido: reempaqueta las clases, mete las dependencias dentro y añade el cargador que arranca el `main`. Sin él, `mvn package` produciría un jar normal que no sabe arrancar solo.

---

# METODO EXPLICATIVO

## 1. Que hace un plugin en Maven

Los plugins son los que ejecutan el trabajo real del build: compilar, probar, empaquetar. Las dependencias son librerías que tu código usa; los plugins son herramientas que Maven usa para construir tu proyecto. Viven en la sección `<build><plugins>`, separada de `<dependencies>`.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

Sin versión: el parent ya configura cuál corresponde a tu generación de Boot. Este bloque mínimo activa el objetivo `repackage`, que es donde ocurre la magia.

## 2. El repackage: de jar normal a jar ejecutable

Un `mvn package` estándar produce un jar con solo tus clases: si lo ejecutas con `java -jar`, falla porque ni contiene las dependencias ni sabe cuál es la clase principal. El objetivo `repackage` del plugin toma ese jar y lo transforma:

1. Mueve tus clases a `BOOT-INF/classes/`.
2. Copia todas las dependencias de alcance `compile` y `runtime` a `BOOT-INF/lib/`.
3. Añade el cargador de Spring Boot (`org.springframework.boot.loader`) en la raíz.
4. Escribe el `MANIFEST.MF` apuntando al cargador y declarando tu clase principal en `Start-Class`.

El resultado es un único archivo que arranca con `java -jar miapp.jar` en cualquier máquina con el JDK adecuado, sin Maven, sin Tomcat instalado, sin nada más.

## 3. Arrancar y construir imagenes desde Maven

El plugin ofrece dos objetivos útiles más allá del empaquetado:

- **`spring-boot:run`**: levanta la aplicación directamente con `mvn spring-boot:run`, sin empaquetar antes. Es el ciclo rápido de desarrollo: cambias código, relanzas, pruebas. Más lento que el IDE en caliente pero sin configuración adicional.
- **`spring-boot:build-image`**: construye una imagen Docker de tu aplicación usando Cloud Native Buildpacks, sin escribir `Dockerfile`. Convierte el jar en imagen con un solo comando, con capas optimizadas (las dependencias cambian poco y se cachean aparte de tus clases).

## 4. Excluir dependencias del jar y otras opciones

A veces quieres un jar ligero porque el entorno ya aporta algo (un agente de monitorización, un driver provisto por la plataforma). El plugin permite excluir artefactos concretos del reempaquetado:

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <excludes>
            <exclusion>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
            </exclusion>
        </excludes>
    </configuration>
</plugin>
```

Lombok es el ejemplo canónico: solo se necesita al compilar (genera código), así que no tiene sentido empaquetarlo. Cada exclusión debe ser consciente: si excluyes algo que sí se necesita en ejecución, el fallo aparece al arrancar en producción, no al compilar.

## 5. El plugin y el ciclo de vida de Maven

Conviene situar el plugin en el ciclo de vida completo: `validate` -> `compile` -> `test` -> `package` -> `verify` -> `install` -> `deploy`. El `repackage` se engancha a la fase `package`, después de que el jar normal ya existe. Y la fase `test` corre antes: si un test falla, el build se detiene y no hay jar. El orden es una garantía: **ningún artefacto se empaqueta con tests en rojo**.

---

## Errores Comunes

> **Olvidar el plugin y obtener un jar que no arranca**. El `java -jar` responde con "no main manifest attribute" porque falta el repackage. Si tu jar no arranca solo, este plugin es lo primero que hay que comprobar.

> **Declarar el plugin con una version distinta a la del parent**. El repackage y el cargador deben coincidir con la generacion de Boot del proyecto. Sin version heredada, Maven puede resolver una incompatible.

> **Confundir dependencias con plugins**. Poner `spring-boot-maven-plugin` dentro de `<dependencies>` no hace nada util y anade el jar del plugin al classpath. Cada cosa en su seccion: codigo en dependencies, herramientas en build/plugins.

> **Saltarse los tests con -DskipTests para "ir mas rapido"**. El atajo empaqueta codigo sin verificar. Usalo solo en casos justificados (iteracion local rapida); el build de integracion siempre debe correr los tests.

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - El ciclo de vida Maven donde se engancha el plugin
- [[48 - JUnit y pruebas de unidad]] - Los tests que corren antes del empaquetado
- [[50 - Herencia del parent de Spring Boot en Maven]] - El parent que versiona el plugin
- [[53 - Dependencias de prueba y alcance test]] - Lo que el repackage excluye del jar
- [[59 - Docker Compose]] - El jar ejecutable como pieza que se conteneriza

---

## Tags
`#java #maven #spring-boot #plugin #build`
