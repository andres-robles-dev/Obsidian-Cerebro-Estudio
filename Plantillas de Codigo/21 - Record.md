# 21 - Record

```java
public record NombreRecord(TipoDato nombreAtributo, TipoDato otroAtributo) {

    public NombreRecord {
        if (condicionInvalida) {
            throw new IllegalArgumentException("mensaje de error");
        }
    }
}

NombreRecord nombreVariable = new NombreRecord(argumento1, argumento2);

TipoDato valor = nombreVariable.nombreAtributo();
```

**Cuando se usa:** para datos inmutables que solo transportan valores (DTOs, Value Objects, resultados). Igualdad por contenido sin escribir equals/hashCode a mano.

**Reglas:**
- Inmutable: campos final, sin setters
- Accessors SIN get: variable.nombreAtributo(), no getNombreAtributo()
- equals, hashCode y toString se generan por VALOR automaticamente
- El compact constructor se escribe sin parametros y valida antes de asignar
- No apto para clases con logica mutable o herencia

---

## Ejemplo de uso

```java
public record Usuario(long id, String nombre, int edad) {

    public Usuario {
        if (edad < 0) {
            throw new IllegalArgumentException("La edad no puede ser negativa");
        }
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre es obligatorio");
        }
    }
}

Usuario u = new Usuario(1, "Ana", 30);
long id = u.id();
String nombre = u.nombre();
boolean iguales = u.equals(new Usuario(1, "Ana", 30));   // true por valor
```

**Ver tema:** [[37 - Record (y Value Objects)]]