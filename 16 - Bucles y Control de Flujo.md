---
tags: [java, fundamentos, bucles, for, while, do-while, for-each, break, continue]
---

# 16 - Bucles y Control de Flujo

---

## NIVEL JUNIOR

### El bucle for

Repite un numero conocido de veces:

```java
for (int i = 0; i < 5; i++) {
    System.out.println("Vuelta numero " + i);
}
```

Partes:
- `int i = 0` - inicializacion (empieza aqui)
- `i < 5` - condicion (mientras se cumpla, sigue)
- `i++` - actualizacion (que pasa en cada vuelta)

### El bucle for-each

Para recorrer colecciones y arrays:

```java
int[] numeros = {10, 20, 30};
for (int numero : numeros) {
    System.out.println(numero);
}
```

### El bucle while

Repite mientras se cumpla una condicion:

```java
int contador = 0;
while (contador < 3) {
    System.out.println("Contador: " + contador);
    contador++;
}
```

### El bucle do-while

Igual que while, pero se ejecuta al menos una vez:

```java
int contador = 0;
do {
    System.out.println("Siempre al menos una vez");
    contador++;
} while (contador < 3);
```

---

## NIVEL MID

### break: salir del bucle

```java
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;  // Sale del bucle cuando i llega a 5
    }
    System.out.println(i);  // Imprime 0,1,2,3,4
}
```

### continue: saltar a la siguiente iteracion

```java
for (int i = 0; i < 5; i++) {
    if (i == 2) {
        continue;  // Salta la vuelta cuando i es 2
    }
    System.out.println(i);  // Imprime 0,1,3,4
}
```

### Bucle con listas

```java
import java.util.*;

List<String> nombres = List.of("Ana", "Luis", "Carlos");

// For-each (recomendado)
for (String nombre : nombres) {
    System.out.println(nombre);
}

// For tradicional (cuando necesitas el indice)
for (int i = 0; i < nombres.size(); i++) {
    System.out.println(i + ": " + nombres.get(i));
}
```

### While para leer datos hasta condicion

```java
import java.util.Scanner;

Scanner entrada = new Scanner(System.in);
String texto = "";
while (!texto.equals("salir")) {
    System.out.print("Escribe algo (salir para terminar): ");
    texto = entrada.nextLine();
    System.out.println("Escribiste: " + texto);
}
```

---

## NIVEL SENIOR

### Iteradores con remove

```java
import java.util.*;

public class DemoIterator {
    public static void main(String[] args) {
        List<String> palabras = new ArrayList<>(List.of("hola", "adios", "sol", "luna"));

        Iterator<String> iterador = palabras.iterator();
        while (iterador.hasNext()) {
            String p = iterador.next();
            if (p.length() < 4) {
                iterador.remove();  // Elimina seguro durante la iteracion
            }
        }

        System.out.println(palabras);  // [hola, adios, luna]
    }
}
```

### removeIf (mas moderno que Iterator)

```java
List<String> palabras = new ArrayList<>(List.of("hola", "adios", "sol", "luna"));
palabras.removeIf(p -> p.length() < 4);
System.out.println(palabras);  // [hola, adios, luna]
```

### For-each con Stream

```java
List<Integer> numeros = List.of(1, 2, 3, 4, 5);

numeros.stream()
    .filter(n -> n % 2 == 0)
    .forEach(n -> System.out.println("Par: " + n));
```

### For con indices y condiciones modernas

```java
public class DemoForModerno {
    public static void main(String[] args) {
        // Recorrer dos arrays a la vez
        String[] nombres = {"Ana", "Luis", "Carlos"};
        int[] edades = {25, 30, 28};

        for (int i = 0; i < nombres.length && i < edades.length; i++) {
            System.out.printf("%s tiene %d anios%n", nombres[i], edades[i]);
        }

        // For con continue y etiquetas
        etiqueta:
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (i == 1 && j == 1) {
                    continue etiqueta;  // Salta al siguiente i
                }
                System.out.println(i + "," + j);
            }
        }
    }
}
```

### Switch expression como control de flujo (Java 14+)

```java
public class DemoSwithExpresion {
    public static void main(String[] args) {
        int dia = 3;

        String tipo = switch (dia) {
            case 1, 2, 3, 4, 5 -> "Laborable";
            case 6, 7 -> "Finde";
            default -> "Dia invalido";
        };

        System.out.println(tipo);  // "Laborable"
    }
}
```

---

## Errores Comunes

> Bucle infinito por olvidar actualizar la variable. `for (int i = 0; i < 10; )` sin `i++` nunca termina.

> Modificar una lista mientras la recorres con for-each. Lanza `ConcurrentModificationException`. Usa `Iterator.remove()` o `removeIf`.

> Usar `=` en vez de `==` en la condicion del while o for. `while (x = 5)` asigna 5 a x y la condicion siempre es verdadera (si x no es 0).

> Confundir `break` con `continue`. `break` sale del bucle. `continue` salta a la siguiente iteracion.

> Acceder a `lista.get(i)` sin comprobar que `i < lista.size()`. Lanza `IndexOutOfBoundsException`.

---

## Buenas Practicas

1. Prefiere `for-each` sobre `for` tradicional cuando no necesites el indice.
2. Usa `removeIf` para eliminar elementos de colecciones en vez de iterar manualmente.
3. Usa `Stream.forEach` cuando combines filtros o mapcos.
4. Evita bucles infinitos. Asegurate de que la condicion de salida se cumpla.
5. Para recorrer dos colecciones a la vez, considera usar indices o `zip` con librerias.

---

## Conexiones

- [[14 - Arrays Basicos y args]] - Recorrer arrays
- [[15 - Listas y ArrayList]] - Recorrer listas
- [[24 - ArrayList - Metodos Avanzados]] - removeIf, stream
- [[12 - Operador Ternario]] - Condiciones simples

---

## Tags
`#java #fundamentos #bucles #for #while #do-while #for-each #break #continue`
