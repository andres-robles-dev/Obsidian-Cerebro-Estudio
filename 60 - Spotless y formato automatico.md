---
tags: [java, formato, spotless, calidad, maven, estilo]
---

# 60 - Spotless y formato automatico

Spotless es un plugin de Maven que formatea tu código automáticamente según unas reglas fijas: cada vez que construyes, el código queda con el mismo estilo sin que nadie tenga que discutirlo ni revisarlo. Elimina las discusiones sobre formato de las code reviews para siempre.

---

# METODO EXPLICATIVO

## 1. El problema que nadie admite tener

En todo equipo hay una guerra silenciosa sobre el formato: espacios contra tabuladores, dónde van las llaves, cuántos saltos entre métodos, el orden de los imports. Cada pull request mezcla cambios reales con "arreglos" de estilo, las revisiones pierden tiempo en lo cosmético y el historial de git se ensucia con commits de formato.

La solución no es un documento de estilo que nadie lee: es una **herramienta que lo impone sola**. Si el formato lo decide una máquina y se aplica en cada build, el debate desaparece porque ya no hay nada que decidir. Ese es el trabajo de Spotless.

## 2. Declarar el plugin y elegir un formateador

```xml
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>2.44.0</version>
    <configuration>
        <java>
            <palantirJavaFormat>
                <version>2.50.0</version>
            </palantirJavaFormat>
            <importOrder>
                <order>java,javax,org,com,\#</order>
            </importOrder>
            <removeUnusedImports/>
        </configuration>
    </java>
</plugin>
```

Tres decisiones en la configuración: el **formateador** (`palantirJavaFormat` es el más usado; alternativas: `googleJavaFormat`, `eclipse`), el **orden de imports** (grupos separados, sin comodines mezclados) y la **limpieza de imports no usados**. Una vez declarado, dos comandos:

- **`mvn spotless:check`**: verifica el formato y falla si algo no cumple. Es el que se usa en integración continua para rechazar código mal formateado.
- **`mvn spotless:apply`**: reformatea todo el código automáticamente. Es el que usas en local antes de hacer commit.

## 3. El flujo de trabajo con Spotless

El ciclo es simple: programas con normalidad, ejecutas `mvn spotless:apply` antes de commitear (o dejas que el IDE lo haga al guardar si instalas el conector), y el código queda normalizado. En el servidor de integración, `spotless:check` actúa como guardián: si alguien subió código sin formatear, el build falla con la lista exacta de archivos.

Para que el guardián sea efectivo, el check debe correr en la fase `verify` del build atado al plugin (con `<execution>`), no como un comando manual que la gente olvida. La regla de automatización: **todo lo que depende de la disciplina humana acabará sin hacerse**; lo que está en el build se cumple siempre.

## 4. Que normaliza y que no

Spotless normaliza lo mecánico: indentación, espacios alrededor de operadores, posición de llaves, saltos de línea, orden y limpieza de imports, finales de línea. No toca nada semántico: no renombra variables, no reordena métodos, no simplifica lógica. Es seguro ejecutarlo sobre todo el proyecto de golpe: el código resultante compila igual y se comporta igual, solo se ve distinto.

Esa garantía tiene una consecuencia práctica para equipos con historial largo: puedes aplicar Spotless a todo el repositorio en un solo commit ("normalizar formato") y a partir de ahí el `git blame` sigue siendo útil, porque todos los cambios posteriores de formato ya no existen.

## 5. Spotless y el IDE: evitar la pelea

Si tu IDE tiene su propio formateador con reglas distintas, acabas en un bucle: el IDE formatea de una manera, Spotless de otra, y cada guardado genera diffs. La solución es alinear ambos: configura el IDE para que use el mismo formateador (hay conectores para IntelliJ y Eclipse con Palantir y Google Java Format) o desactiva el formateo al guardar y deja que Spotless sea la única autoridad. Dos formateadores con reglas distintas es peor que ninguno.

---

## Errores Comunes

> **Anadir el plugin pero no atarlo al build**. Si `spotless:check` solo se ejecuta a mano, nadie lo ejecuta. Añade la `<execution>` en fase `verify` para que el formateo sea obligatorio, no opcional.

> **Elegir formateador sin consensuarlo y cambiarlo a mitad del proyecto**. Cambiar de Google a Palantir reformatea todos los archivos y contamina el historial. Decide una vez al inicio y no lo toques mas.

> **Formateadores distintos en IDE y Spotless**. Cada guardado produce diffs que Spotless revierte o viceversa. Una sola autoridad de formato: alinea el IDE con Spotless o desactiva el del IDE.

> **Commitear codigo sin pasar spotless:apply y dejar que el CI falle**. Funciona como red de seguridad, pero ensucia el historial con commits de "arreglar formato". Pasa el apply en local antes del push.

> **Incluir el formato en el mismo commit que la funcionalidad**. Mezclar cambios reales con reformateo dificulta la revision. Si tocas archivos antiguos sin formatear, normalizalos en commit separado.

---

## Conexiones

- [[18 - Convenciones de Nombrado]] - Estilo de nombres (lo que Spotless no cubre)
- [[20 - Javadoc y Documentacion]] - El formato del codigo acompana a su documentacion
- [[55 - Cierre del descriptor Maven]] - El pom donde se declara el plugin
- [[61 - Imports en profundidad]] - Lo que ordena la regla importOrder de Spotless

---

## Tags
`#java #formato #spotless #calidad #maven`
