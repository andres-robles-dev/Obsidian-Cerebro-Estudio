# 12 - Arrays

```java
TipoDato[] nombreArray = new TipoDato[capacidad];

TipoDato[] nombreArray = {valor1, valor2, valor3};

nombreArray[indice] = valor;
TipoDato valorObtenido = nombreArray[indice];

int longitud = nombreArray.length;

for (TipoDato contador = valorInicial; contador < nombreArray.length; contador++) {
    System.out.println(nombreArray[contador]);
}

for (TipoDato elemento : nombreArray) {
    System.out.println(elemento);
}
```

**Cuando se usa:** cuando el tamano es FIJO y conocido (pocos elementos, argumentos de main, tablas fijas). Para listas que crecen usa ArrayList.

**Reglas:**
- Tamano fijo: no se puede agregar ni quitar elementos
- Indices de 0 a length-1 (fuera de eso: ArrayIndexOutOfBoundsException)
- length es una propiedad sin parentesis (distinto de String.length())
- Inicializacion por defecto: numeros a 0, booleanos a false, objetos a null

---

## Ejemplo de uso

```java
int[] numeros = new int[5];
numeros[0] = 10;

String[] nombres = {"Ana", "Luis", "Paz"};

for (int i = 0; i < nombres.length; i++) {
    System.out.println(nombres[i]);
}

for (String nombre : nombres) {
    System.out.println(nombre);
}
```

**Ver tema:** [[14 - Arrays Basicos y args]]