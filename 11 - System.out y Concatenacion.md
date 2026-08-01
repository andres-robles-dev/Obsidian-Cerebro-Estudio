---
tags: [java, fundamentos, system-out, consola, concatenacion, printf, println]
---

# 11 - System.out y Concatenacion

---

## NIVEL JUNIOR

### System.out.println

Es la forma mas basica de mostrar informacion en la consola:

```java
System.out.println("Hola mundo!");
```

`println` imprime el texto y salta a la siguiente linea.

### System.out.print

Igual que `println` pero no salta de linea:

```java
System.out.print("Hola ");
System.out.print("mundo");
// Imprime: "Hola mundo" (sin salto)
```

### Concatenacion con +

Puedes unir texto y variables con el operador `+`:

```java
String nombre = "Ana";
int edad = 25;

System.out.println("Me llamo " + nombre + " y tengo " + edad + " anios");
// Imprime: Me llamo Ana y tengo 25 anios
```

### Ejemplo basico

```java
public class Saludo {
    public static void main(String[] args) {
        String ciudad = "Madrid";
        int temperatura = 28;

        System.out.print("La temperatura en ");
        System.out.print(ciudad);
        System.out.println(" es de " + temperatura + " grados");
    }
}
```

---

## NIVEL MID

### Concatenacion con diferentes tipos

Java convierte automaticamente los tipos a String cuando concatenas:

```java
public class DemoConcatenacion {
    public static void main(String[] args) {
        String texto = "El resultado es: ";
        int numero = 42;
        double decimal = 3.14;
        boolean activo = true;

        System.out.println(texto + numero);           // "El resultado es: 42"
        System.out.println("Valor: " + decimal);      // "Valor: 3.14"
        System.out.println("Activo: " + activo);      // "Activo: true"
    }
}
```

### System.out.printf

Para formatear la salida con control preciso:

```java
public class DemoPrintf {
    public static void main(String[] args) {
        String nombre = "Ana";
        int edad = 25;
        double altura = 1.75;

        // %s = String, %d = entero, %.2f = decimal con 2 cifras
        System.out.printf("Nombre: %s, Edad: %d, Altura: %.2f%n", nombre, edad, altura);

        // Alinear a la derecha
        System.out.printf("%10s %10s%n", "Producto", "Precio");
        System.out.printf("%10s %10.2f%n", "Laptop", 1200.50);
        System.out.printf("%10s %10.2f%n", "Mouse", 25.99);
    }
}
```

### System.err

Para mensajes de error:

```java
if (archivo == null) {
    System.err.println("Error: el archivo no existe");
}
```

---

## NIVEL SENIOR

### Text blocks (Java 15+)

Para textos largos sin concatenar:

```java
public class DemoTexBlocks {
    public static void main(String[] args) {
        String menu = """
            ========= MENU =========
            1. Ver perfil
            2. Editar datos
            3. Configuracion
            4. Salir
            ========================
            """;

        System.out.println(menu);
    }
}
```

### Text blocks con formato

```java
public class DemoFormato {
    public static void main(String[] args) {
        String nombre = "Ana";
        int edad = 25;
        String ciudad = "Madrid";

        String perfil = """
            DATOS DEL USUARIO
            -----------------
            Nombre:   %s
            Edad:     %d anios
            Ciudad:   %s
            """.formatted(nombre, edad, ciudad);

        System.out.println(perfil);
    }
}
```

### StringBuilder para muchas concatenaciones

Cuando concatenas muchas veces en un bucle, usa `StringBuilder`:

```java
public class DemoStringBuilder {
    public static void main(String[] args) {
        // Malo para rendimiento:
        String resultado = "";
        for (int i = 0; i < 100; i++) {
            resultado = resultado + i + ", ";  // Crea muchos objetos String
        }

        // Bueno:
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < 100; i++) {
            builder.append(i).append(", ");
        }
        String resultadoBueno = builder.toString();
    }
}
```

### Logging moderno en vez de System.out

```java
import java.util.logging.*;

public class DemoLogging {
    private static final Logger LOG = Logger.getLogger(DemoLogging.class.getName());

    public static void main(String[] args) {
        LOG.info("Aplicacion iniciada");
        LOG.warning("Recurso bajo memoria");
        LOG.fine("Detalle de depuracion");

        try {
            // codigo
        } catch (Exception e) {
            LOG.severe("Error critico: " + e.getMessage());
        }
    }
}
```

---

## Errores Comunes

> Usar `+` con muchos Strings en bucles. Cada `+` crea un nuevo objeto String, lo que es muy ineficiente. Usa `StringBuilder`.

> Olvidar que `+` suma numeros antes de concatenar. `System.out.println(2 + 2 + " resultado")` imprime "4 resultado", no "22 resultado".

> Confundir `println` con `print`. `println` salta de linea, `print` no.

> Usar `System.out` en produccion. En aplicaciones reales se usa un sistema de logging (SLF4J, java.util.logging).

---

## Buenas Practicas

1. Usa `printf` cuando necesites formato preciso (decimales, alineacion).
2. Usa text blocks (`"""..."""`) para textos multilinea.
3. Usa `StringBuilder` dentro de bucles con muchas concatenaciones.
4. No dejes `System.out.println` en codigo de produccion. Usa logging.
5. Prefiere `String.format()` o `"texto %s".formatted(variable)` para construir Strings.

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Conversion automatica a String
- [[04 - Variables y Literales]] - Literales String y concatenacion
- [[10 - Metodos de Instancia]] - Metodos que muestran informacion
- [[14 - Arrays Basicos y args]] - Mostrar arrays con Arrays.toString()
- [[20 - Javadoc y Documentacion]] - Documentar la salida

---

## Tags
`#java #fundamentos #system-out #consola #concatenacion #printf #println #text-blocks`
