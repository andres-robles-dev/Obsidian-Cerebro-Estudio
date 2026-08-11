# 22 - List.of / Set.of / Map.of

```java
import java.util.List;
import java.util.Map;
import java.util.Set;

// Colecciones INMUTABLES (Java 9+)
List<String> nombres = List.of("Ana", "Luis", "Paz");
Set<Integer> numeros = Set.of(1, 2, 3);
Map<String, Integer> edades = Map.of("Ana", 30, "Luis", 25);

// Copia inmutable de una coleccion mutable:
List<String> copia = List.copyOf(otraLista);

// Para Map con mas de 10 pares:
Map<String, Integer> grande = Map.ofEntries(
    Map.entry("Ana", 30),
    Map.entry("Luis", 25)
);
```

**Cuando se usa:** para crear listas/sets/mapas de datos fijos (configuracion, constantes, valores conocidos) de forma concisa y segura.

**Reglas:**
- INMUTABLES: cualquier modificacion lanza `UnsupportedOperationException`
- `null` esta PROHIBIDO (lanza `NullPointerException` al crearla)
- `Set.of` no acepta duplicados (lanza `IllegalArgumentException`)
- `Map.of` acepta hasta 10 pares; para mas usa `Map.ofEntries`
- Usalas para programacion defensiva: datos que no deben cambiar nunca

**Ver tema:** [[38 - List.of y Colecciones Inmutables]]
