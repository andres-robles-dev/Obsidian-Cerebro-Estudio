# 22 - List.of / Set.of / Map.of

```java
List<TipoDato> nombreLista = List.of(valor1, valor2, valor3);

Set<TipoDato> nombreSet = Set.of(valor1, valor2, valor3);

Map<TipoClave, TipoValor> nombreMapa = Map.of(clave1, valor1, clave2, valor2);

List<TipoDato> copiaInmutable = List.copyOf(nombreColeccion);
```

**Cuando se usa:** para crear listas/sets/mapas de datos fijos (configuracion, constantes, valores conocidos) de forma concisa y segura.

**Reglas:**
- INMUTABLES: cualquier modificacion lanza UnsupportedOperationException
- null esta PROHIBIDO (lanza NullPointerException al crearla)
- Set.of no acepta duplicados (lanza IllegalArgumentException)
- Map.of acepta hasta 10 pares; para mas usa Map.ofEntries
- Usalas para programacion defensiva: datos que no deben cambiar

---

## Ejemplo de uso

```java
List<String> nombres = List.of("Ana", "Luis", "Paz");
Set<Integer> numeros = Set.of(1, 2, 3);
Map<String, Integer> edades = Map.of("Ana", 30, "Luis", 25);

List<String> copia = List.copyOf(otraLista);

Map<String, Integer> grande = Map.ofEntries(
    Map.entry("Ana", 30),
    Map.entry("Luis", 25)
);
```

**Ver tema:** [[38 - List.of y Colecciones Inmutables]]