# 27 - Paquete e Imports

```java
package nombre.del.paquete;

import nombre.del.paquete.NombreClase;

public class NombreClase {
    public TipoDato nombreAtributo;
}
```

**Cuando se usa:** en todo proyecto organizado por carpetas (estructura estandar, proyectos con Maven/Gradle). Los paquetes agrupan clases relacionadas y evitan choques de nombres.

**Reglas:**
- El package es la PRIMERA sentencia del archivo (solo antes van comentarios)
- La ruta del paquete coincide con las carpetas: com.empresa.pagos = com/empresa/pagos/
- Los imports van despues del package y antes de la clase
- java.lang (String, Math, System) se importa solo
- import java.util.* importa todo el paquete (menos explicito que importar cada clase)

---

## Ejemplo de uso

```java
package com.empresa.pagos;

import java.util.List;
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        List<String> items = new java.util.ArrayList<>();

        Scanner sc = new java.util.Scanner(System.in);
    }
}
```

**Ver tema:** [[25 - Paquetes y Organizacion]]