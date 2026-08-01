---
tags: [java, fundamentos, paquetes, organizacion, package, import, module]
---

# 25 - Paquetes y Organizacion

---

## NIVEL JUNIOR

### Que es un paquete?

Un paquete es una carpeta que agrupa clases relacionadas. Es como una carpeta en tu ordenador donde guardas archivos del mismo tipo.

### Declarar un paquete

Al principio del archivo:

```java
package musica.modelo;

public class Cancion {
    // ...
}
```

### Importar clases de otros paquetes

```java
package musica.aplicacion;

import musica.modelo.Cancion;

public class Reproductor {
    public static void main(String[] args) {
        Cancion c = new Cancion();
    }
}
```

### Estructura de carpetas

```
src/
└── musica/
    ├── modelo/
    │   └── Cancion.java        (package musica.modelo)
    ├── servicio/
    │   └── Reproductor.java     (package musica.servicio)
    └── aplicacion/
        └── Principal.java      (package musica.aplicacion)
```

---

## NIVEL MID

### Por que usar paquetes?

1. **Organizacion**: clases relacionadas juntas
2. **Evitar conflictos**: dos clases con mismo nombre en distintos paquetes
3. **Control de acceso**: `protected` y `default` dependen del paquete

### Convencion de nombres

```java
package com.miempresa.miapp.modelo;
package com.miempresa.miapp.servicio;
package com.miempresa.miapp.repositorio;
```

### Import estatico

```java
import static java.util.Collections.*;

List<String> lista = new ArrayList<>();
sort(lista);        // En vez de Collections.sort(lista)
shuffle(lista);     // En vez de Collections.shuffle(lista)
```

### Visibilidad package-private

Cuando no pones modificador de acceso, es visible solo dentro del mismo paquete:

```java
package musica.modelo;

class AyudanteInterno {
    // Solo accesible desde clases en musica.modelo
    void metodoAyuda() { }
}
```

---

## NIVEL SENIOR

### Modularidad con module-info.java (Java 9+)

```java
// module-info.java
module com.miempresa.musica {
    // Que paquetes exporta este modulo (visibles fuera)
    exports com.miempresa.musica.modelo;
    exports com.miempresa.musica.servicio;

    // Que modulos necesita
    requires java.base;  // Siempre implicito
    requires java.sql;
}
```

### Estructura Maven/Gradle

```
proyecto/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/miempresa/
│   │   │       ├── modelo/
│   │   │       ├── repositorio/
│   │   │       ├── servicio/
│   │   │       └── aplicacion/
│   │   └── resources/
│   │       ├── config.properties
│   │       └── logback.xml
│   └── test/
│       └── java/
│           └── com/miempresa/
│               ├── modelo/
│               ├── servicio/
│               └── ...
├── pom.xml          (Maven)
├── build.gradle     (Gradle)
└── README.md
```

### Vertical slicing vs Horizontal

**Horizontal** (tradicional): capas tecnicas
```
com.miempresa.modelo.Usuario
com.miempresa.repositorio.UsuarioRepositorio
com.miempresa.servicio.UsuarioServicio
```

**Vertical** (por funcionalidad):
```
com.miempresa.usuario.
    ├── Usuario.java
    ├── UsuarioRepositorio.java
    └── UsuarioServicio.java

com.miempresa.pedido.
    ├── Pedido.java
    ├── PedidoRepositorio.java
    └── PedidoServicio.java
```

### Package privado para implementaciones

```java
// Publica: la interfaz que ven los demas modulos
package com.miempresa.servicio;

public interface UsuarioServicio {
    Usuario registrar(String nombre, String email);
}

// Package-private: nadie fuera del paquete ve la implementacion
package com.miempresa.servicio;

class UsuarioServicioImpl implements UsuarioServicio {
    @Override
    public Usuario registrar(String nombre, String email) {
        // ...
    }
}
```

### Organizacion con records sellados

```java
// com.miempresa.modelo.pagos/
public sealed interface MedioPago
    permits Tarjeta, Paypal, Cripto, Transferencia {}

public record Tarjeta(String numero, String titular) implements MedioPago {}
public record Paypal(String email) implements MedioPago {}
public record Cripto(String direccionWallet) implements MedioPago {}
public record Transferencia(String iban) implements MedioPago {}
```

---

## Errores Comunes

> No declarar el `package` al principio del archivo. Si no declaras paquete, la clase va al paquete por defecto (no recomendado para proyectos reales).

> Confundir la estructura de directorios con la declaracion `package`. La clase `package com.miempresa.modelo` debe estar en `com/miempresa/modelo/`.

> Importar clases del mismo paquete. No hace falta importar clases que estan en el mismo paquete.

> Usar `import *` en vez de imports especificos. `import java.util.*;` importa todo, lo que puede causar conflictos. Mejor importar solo lo que necesitas.

> Poner varias clases publicas en el mismo archivo. Solo una clase `public` por archivo.

---

## Buenas Practicas

1. Nombres de paquete en minusculas: `com.miempresa.app.modelo`.
2. Un proposito por paquete: `modelo`, `repositorio`, `servicio`, `aplicacion`.
3. Programa contra interfaces, expone solo las interfaces en paquetes publicos.
4. Manten las implementaciones package-private cuando sea posible.
5. Usa el sistema de modulos (module-info.java) para proyectos grandes.
6. Sigue la estructura Maven/Gradle estandar.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - La clase tiene un paquete
- [[05 - Modificadores de Acceso]] - Visibilidad entre paquetes
- [[18 - Convenciones de Nombrado]] - Nombres de paquetes
- [[19 - Separacion de Responsabilidades]] - Organizacion por responsabilidades
- [[20 - Javadoc y Documentacion]] - Documentar paquetes con package-info.java
- [[22 - Separacion de Responsabilidades]] - Arquitectura en capas

---

## Tags
`#java #fundamentos #paquetes #organizacion #package #import #module #modulos`
