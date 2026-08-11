# 12 - Arrays

```java
// Declarar e inicializar con tamano fijo
int[] numeros = new int[5];
numeros[0] = 10;
numeros[1] = 20;

// Inicializacion directa
String[] nombres = {"Ana", "Luis", "Paz"};

// Recorrer por indice
for (int i = 0; i < nombres.length; i++) {
    System.out.println(nombres[i]);
}

// Recorrer con for-each (no necesitas el indice)
for (String nombre : nombres) {
    System.out.println(nombre);
}

// Los argumentos de main SON un array
public static void main(String[] args) {
    // args.length, args[0], args[1]...
}
```

**Cuando se usa:** cuando el tamano es FIJO y conocido (pocos elementos, argumentos, tablas fijas). Para listas que crecen usa ArrayList.

**Reglas:**
- Tamano fijo: no se puede agregar ni quitar elementos
- Indices de 0 a `length - 1` (fuera de eso: `ArrayIndexOutOfBoundsException`)
- `length` es una propiedad (sin parentesis), distinto de `String.length()`
- Se inicializan por defecto: numeros a 0, booleanos a false, objetos a null

**Ver tema:** [[14 - Arrays Basicos y args]]
