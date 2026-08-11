# 07 - Scanner

```java
try (Scanner sc = new Scanner(System.in)) {
    TipoDato nombreVariable = sc.nextMetodoDeLectura();
    TipoDato otraVariable = sc.nextMetodoDeLectura();

    System.out.println(nombreVariable);
}
```

**Cuando se usa:** para leer lo que el usuario escribe en la consola (tambien datos de un archivo con new Scanner(new File(...))).

**Reglas:**
- nextLine() lee la linea completa como String; nextInt(), nextDouble()... leen el valor tipado
- Despues de nextInt() queda un salto de linea pendiente: un nextLine() siguiente puede devolver vacio
- Si el usuario escribe texto donde esperas numero, lanza InputMismatchException
- Cierra con try-with-resources (o sc.close())
- Metodos de lectura disponibles: next, nextLine, nextInt, nextDouble, nextBoolean, hasNextInt...

---

## Ejemplo de uso

```java
import java.util.Scanner;

public class LeerDatos {
    public static void main(String[] args) {
        try (Scanner sc = new Scanner(System.in)) {
            System.out.print("Nombre: ");
            String nombre = sc.nextLine();

            System.out.print("Edad: ");
            int edad = sc.nextInt();

            System.out.println("Hola " + nombre + ", tienes " + edad);
        }
    }
}
```

**Ver tema:** [[29 - Scanner en Profundidad]]