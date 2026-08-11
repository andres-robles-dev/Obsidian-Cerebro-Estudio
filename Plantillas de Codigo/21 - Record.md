# 21 - Record

```java
// Definicion minima: campos -> accessors, equals, hashCode, toString
public record Usuario(long id, String nombre, int edad) {

    // Compact constructor: valida antes de crear
    public Usuario {
        if (edad < 0) {
            throw new IllegalArgumentException("La edad no puede ser negativa");
        }
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre es obligatorio");
        }
    }
}

// Uso:
Usuario u = new Usuario(1, "Ana", 30);
long id = u.id();              // accessor SIN get
String nombre = u.nombre();
boolean iguales = u.equals(new Usuario(1, "Ana", 30));  // true por valor
```

**Cuando se usa:** para datos inmutables que solo transportan valores (DTOs, Value Objects, resultados). Cuando quieres igualdad por contenido sin escribir equals/hashCode a mano.

**Reglas:**
- Inmutable: campos `final`, sin setters
- Accessors sin prefijo: `u.nombre()`, no `u.getNombre()`
- `equals`/`hashCode`/`toString` se generan por VALOR automaticamente
- El compact constructor se escribe sin parametros y valida antes de asignar
- No apto para clases con logica mutable o herencia

**Ver tema:** [[37 - Record (y Value Objects)]]
