# 27 - Paquete e Imports

```java
// 1. PRIMERA linea del archivo: la declaracion del paquete
//    (la ruta invertida de la carpeta donde vive el archivo)
package com.empresa.pagos;

// 2. Despues: los imports (clases de otros paquetes)
import java.util.List;
import java.util.Scanner;

// 3. Luego la clase (archivo src/com/empresa/pagos/Main.java)
public class Main {
    public static void main(String[] args) {
        // Usar una clase del MISMO paquete no requiere import:
        Factura factura = new Factura();

        // Clase de otro paquete: import arriba...
        List<String> items = new java.util.ArrayList<>();

        // ...o nombre completo directo:
        Scanner sc = new java.util.Scanner(System.in);
    }
}
```

**Cuando se usa:** en todo proyecto organizado por carpetas (estructura estandar, proyectos con Maven/Gradle). Los paquetes agrupan clases relacionadas y evitan choques de nombres.

**Reglas:**
- El `package` es la PRIMERA sentencia del archivo (solo antes van comentarios)
- La ruta del paquete coincide con las carpetas: `com.empresa.pagos` = `com/empresa/pagos/`
- Los imports van despues del package y antes de la clase
- `java.lang` (String, Math, System) se importa solo: no hace falta import
- `import java.util.*` importa todo el paquete (menos explicito que importar cada clase)

**Ver tema:** [[25 - Paquetes y Organizacion]]
