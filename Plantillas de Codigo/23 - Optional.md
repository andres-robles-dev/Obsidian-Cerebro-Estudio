# 23 - Optional

```java
import java.util.Optional;

// Un metodo devuelve Optional cuando el valor puede NO existir:
public Optional<String> buscarNombre(long id) {
    if (id < 1) {
        return Optional.empty();       // sin valor
    }
    return Optional.of("Ana");         // con valor (prohibido null)
}

// Uso en el llamador:
Optional<String> nombre = buscarNombre(1);

// 1. Si existe, hacer algo:
nombre.ifPresent(n -> System.out.println("Hola " + n));

// 2. Valor por defecto:
String valor = nombre.orElse("Sin nombre");

// 3. Exigir el valor (si no existe, lanza):
String resultado = nombre.orElseThrow(
    () -> new IllegalStateException("El usuario no existe")
);

// 4. Encadenar transformaciones:
String mayuscula = nombre.map(String::toUpperCase).orElse("---");
```

**Cuando se usa:** como retorno de metodos que pueden "no tener" un resultado (busquedas, configuraciones opcionales). Evita el `null` silencioso.

**Reglas:**
- Representa AUSENCIA, no errores: si es un error de verdad, lanza excepcion
- NUNCA uses `Optional.get()` a ciegas (lanza `NoSuchElementException` si esta vacio)
- No lo uses como parametro ni campo: solo como retorno
- `Optional.of(null)` lanza: usa `ofNullable` si puede llegar null
- Prefiere `orElseThrow` cuando la ausencia es inaceptable

**Ver tema:** [[23 - Metodos - Parametros, Retorno y Return]]
