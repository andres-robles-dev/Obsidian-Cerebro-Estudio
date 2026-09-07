---
tags: [java, imports, paquetes, organizacion, compilador, estilo]
---

# 61 - Imports en profundidad

Los `import` son las declaraciones que traen clases de otros paquetes a tu archivo: sin ellos tendrías que escribir el nombre completo de cada clase. Parecen trámite, pero esconden decisiones sobre colisiones, comodines, imports estáticos y el coste real de compilación.

---

# METODO EXPLICATIVO

## 1. Que hace realmente un import

Java organiza las clases en paquetes y exige identificar cada clase de forma única. Sin imports, cada uso de una clase externa requeriría su nombre completamente cualificado:

```java
// Sin imports: funciona, pero es ilegible
java.util.List<String> nombres = new java.util.ArrayList<>();
java.time.LocalDate hoy = java.time.LocalDate.now();
```

El `import` es un **alias de escritura**: le dice al compilador "cuando veas `List` en este archivo, me refiero a `java.util.List`". No carga nada en memoria, no enlaza nada en ejecución, no tiene coste en runtime. Es pura azúcar sintáctica para el programador, resuelta entirely en tiempo de compilación.

Hay un paquete que nunca necesita import: `java.lang` (`String`, `Integer`, `Math`, `Exception`...). El compilador lo importa implícitamente en cada archivo. Todo lo demás debe importarse o cualificarse.

## 2. Import simple, colision y nombre cualificado

El caso normal es un import por clase. El problema aparece cuando dos paquetes tienen una clase con el mismo nombre:

```java
import java.util.Date;
import java.sql.Date;  // ERROR: colision, el compilador no sabe cual es Date
```

Java no permite importar dos clases homónimas: hay que elegir una con import y usar la otra con su **nombre completamente cualificado**:

```java
import java.util.Date;

public class Ejemplo {
    Date fechaUtil = new Date();            // java.util.Date por el import
    java.sql.Date fechaSql = new java.sql.Date(...);  // cualificada a mano
}
```

La regla general: importa lo que usas mucho, cualifica lo que colisiona. Y si un archivo necesita ambas versiones con frecuencia, es señal de que hace demasiadas cosas: la colisión de nombres suele ser síntoma de mezcla de responsabilidades (lógica de negocio + acceso a datos en la misma clase).

## 3. Imports con comodin: el asterisco

```java
import java.util.*;  // importa todo lo USADO de java.util, no todo el paquete
```

El comodín `.*` importa las clases del paquete que realmente uses, pero tiene tres costes. Primero, **legibilidad**: nadie sabe qué clases vienen de dónde sin buscarlas. Segundo, **fragilidad ante colisiones futuras**: si una versión nueva de la librería añade una clase con el mismo nombre que otra que usas de otro paquete con comodín, el código deja de compilar sin que hayas tocado nada. Tercero, **ruido en diffs**: herramientas como Spotless expanden o colapsan estos imports y generan cambios cosméticos.

Por eso el estándar moderno es **un import por clase, sin comodines**. Los IDEs los gestionan solos (auto-import al escribir, `Ctrl+Shift+O` / optimizar imports) y Spotless puede prohibirlos. El comodín solo se justifica en código desechable o en imports estáticos de constantes muy usados.

## 4. Imports estaticos: traer metodos y constantes

El import estático trae miembros estáticos (métodos o constantes) para usarlos sin la clase:

```java
import static java.lang.Math.PI;
import static java.lang.Math.sqrt;
import static org.junit.jupiter.api.Assertions.assertEquals;

double area = PI * sqrt(2.0);  // sin Math. delante

@Test
void suma() {
    assertEquals(5, calc.sumar(2, 3));  // sin Assertions. delante
}
```

Dos usos legítimos dominan la práctica: **aserciones en tests** (`assertEquals`, `assertThrows`) y **constantes** muy repetidas. Fuera de esos casos, el import estático oscurece el origen del método: cuando lees `sqrt(x)` no sabes si es `Math.sqrt` o un método propio. La norma: estáticos para tests y constantes, cualificados para todo lo demás.

## 5. Orden, limpieza y automatizacion

El orden canónico de imports (el que Spotless impone con `importOrder`) agrupa por origen: primero `java.*`, luego `javax.*`, luego terceros (`org.*`, `com.*`), luego estáticos, cada grupo ordenado alfabéticamente y separado por línea en blanco:

```java
import java.time.LocalDate;
import java.util.List;

import org.springframework.stereotype.Service;

import static org.junit.jupiter.api.Assertions.assertEquals;
```

Y la higiene continua: **ningún import sin usar**. Los imports muertos confunden (sugieren dependencias que no existen) y algunos checks de calidad los marcan como error. El IDE los atenúa en gris; `removeUnusedImports` de Spotless los elimina en cada build. Entre ambos, mantener los imports limpios cuesta cero esfuerzo consciente.

---

## Errores Comunes

> **Importar dos clases con el mismo nombre**. El compilador lo rechaza directamente. Importa una y cualifica la otra con su paquete completo.

> **Abusar del comodin `.*`**. Compila igual pero esconde dependencias, es fragil ante nuevas versiones de librerias y genera ruido con los formateadores. Un import por clase.

> **Dejar imports sin usar**. Sugieren dependencias falsas y ensucian el archivo. Activa la limpieza automatica (IDE + Spotless `removeUnusedImports`) y no pienses mas en ello.

> **Import estatico de metodos con nombres genericos**. Un `import static ...Utilidades.procesar` hace que `procesar(x)` sea indescifrable sin ir a buscarlo. Estaticos solo para aserciones de test y constantes evidentes.

> **Importar clases del mismo paquete**. Las clases del propio paquete se ven sin import. Anadirlo es ruido (y algunos IDEs lo marcan como innecesario).

---

## Conexiones

- [[25 - Paquetes y Organizacion]] - Los paquetes que los imports conectan
- [[48 - JUnit y pruebas de unidad]] - Los imports estaticos de Assertions
- [[60 - Spotless y formato automatico]] - importOrder y removeUnusedImports
- [[01 - Clases y Estructura Basica]] - Donde van los imports en el archivo

---

## Tags
`#java #imports #paquetes #organizacion #estilo`
