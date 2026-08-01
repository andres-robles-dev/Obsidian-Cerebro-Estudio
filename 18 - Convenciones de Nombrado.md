---
tags: [java, fundamentos, convenciones, nombrado, estilo]
---

# 18 - Convenciones de Nombrado

---

## NIVEL JUNIOR

### Las tres reglas de oro

| Que nombras | Como se escribe | Ejemplo |
|-------------|-----------------|---------|
| **Clases** | PascalCase (primera letra mayuscula) | `Persona`, `FacturaCliente` |
| **Metodos y variables** | camelCase (primera minuscula, siguientes mayusculas) | `calcularTotal()`, `edadPersona` |
| **Constantes** | UPPER_SNAKE_CASE (todo mayusculas con guion bajo) | `MAXIMO_INTENTOS`, `IVA` |

### Ejemplos basicos

```java
// PascalCase para clases
public class ClienteVip {
}

// camelCase para variables y metodos
String nombreCompleto;
int edadCliente;
public void calcularDescuento() {
}

// UPPER_SNAKE_CASE para constantes
public static final double PRECIO_MAXIMO = 999.99;
```

### Regla simple: nombres descriptivos

```java
// Malos nombres:
int x;
String d;
public void proc() {}

// Buenos nombres:
int edad;
String direccion;
public void procesarPago() {}
```

---

## NIVEL MID

### Convenciones detalladas

**Paquetes**: todo en minusculas, separado por puntos:
```java
package com.miempresa.app.modelo;
package com.miempresa.app.servicios;
```

**Metodos getters y setters**: sigue la convencion JavaBeans:
```java
public String getNombre() { return nombre; }
public void setNombre(String nombre) { this.nombre = nombre; }
public boolean isActivo() { return activo; }
```

**Metodos booleanos**: suelen empezar con `is`, `has`, `can`:
```java
public boolean isActivo() { }
public boolean hasPermiso() { }
public boolean canEjecutar() { }
```

**Variables temporales**: cortas pero claras:
```java
for (int i = 0; i < 10; i++) { }     // i para indices
for (String s : lista) { }            // s para elementos genericos
for (char c : texto.toCharArray()) { } // c para caracteres
```

### Lo que NO debes hacer

```java
public class cliente { }           // Mal: clases en mayuscula
public void CalcularTotal() { }    // Mal: metodos en camelCase
int Edad;                          // Mal: variables en camelCase
final double iva = 0.21;           // Mal: constantes en mayusculas
```

---

## NIVEL SENIOR

### Convenciones en records

```java
// Los records mantienen las mismas convenciones
public record DireccionCliente(
    String calle,
    String ciudad,
    String codigoPostal
) {
    // Metodo que sigue camelCase
    public String formatearCompleto() {
        return "%s, %s, %s".formatted(calle, ciudad, codigoPostal);
    }

    // Constante de clase
    public static final String PAIS_DEFECTO = "Espana";
}
```

### Convenciones en lambdas

```java
// Parametros cortos y claros
List<Producto> filtrados = productos.stream()
    .filter(p -> p.precio() > 100)
    .map(p -> p.nombre())
    .toList();

// Para operaciones complejas, nombres descriptivos
Consumer<String> logger = mensaje -> {
    System.out.println("[LOG] " + mensaje);
};
```

### Convenciones modernas (Java 2026)

```java
// Text blocks mantienen indentacion natural
String sql = """
    SELECT id, nombre, email
    FROM usuarios
    WHERE activo = true
    """;

// Pattern matching con nombres claros
if (objeto instanceof Usuario usuario) {
    System.out.println(usuario.nombre());
}

// Switch expression con etiquetas claras
String resultado = switch (codigo) {
    case 200 -> "OK";
    case 404 -> "NO ENCONTRADO";
    case 500 -> "ERROR SERVIDOR";
    default -> "DESCONOCIDO";
};
```

### Enumeraciones

```java
// Enum en PascalCase, valores en UPPER_SNAKE_CASE
public enum EstadoPedido {
    PENDIENTE,
    EN_PROCESO,
    ENVIADO,
    ENTREGADO,
    CANCELADO
}

// Metodos de enum en camelCase
public enum DiaSemana {
    LUNES(true),
    MARTES(true),
    SABADO(false),
    DOMINGO(false);

    private final boolean laborable;

    DiaSemana(boolean laborable) {
        this.laborable = laborable;
    }

    public boolean isLaborable() {
        return laborable;
    }
}
```

---

## Errores Comunes

> Poner mayuscula al principio de variables y metodos. `int Edad = 25;` o `void Procesar() {}`. Deben ser camelCase.

> Poner minuscula al principio de clases. `public class cliente {}`. Deben ser PascalCase.

> Nombres de una letra. `int x; String s;` solo aceptable en bucles (`i`, `j`) o lambdas (`p -> p.nombre()`).

> Nombres que no dicen nada. `datos`, `info`, `temp`, `cosa`. Deben describir el proposito.

> Nombres demasiado largos. `obtenerListaDeNombresDeUsuariosFiltradosPorActivacion`. Maximo 30-40 caracteres.

---

## Buenas Practicas

1. Clases en PascalCase: `FacturaElectronica`, `GestorArchivos`.
2. Metodos y variables en camelCase: `calcularTotal()`, `nombreCompleto`.
3. Constantes en UPPER_SNAKE_CASE: `MAX_INTENTOS`, `RUTA_ARCHIVO`.
4. Paquetes en minusculas: `com.miempresa.app.modelo`.
5. Nombres descriptivos pero no excesivamente largos.
6. Verbos para metodos (`calcular`, `obtener`, `guardar`), sustantivos para clases.
7. booleanos con prefijos `is`, `has`, `can`: `isActivo`, `hasPermiso`.
8. Getters: `getX` para objetos, `isX` para booleanos.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - PascalCase en nombres de clase
- [[05 - Modificadores de Acceso]] - Convencion de getters/setters
- [[10 - Metodos de Instancia]] - camelCase en metodos
- [[13 - Static vs Instancia]] - Constantes static final en UPPER_SNAKE
- [[20 - Javadoc y Documentacion]] - Documentar con nombres claros
- [[21 - Getters y Setters]] - Convencion JavaBeans
- [[25 - Paquetes y Organizacion]] - Nombres de paquetes

---

## Tags
`#java #fundamentos #convenciones #nombrado #estilo #pascalcase #camelcase`
