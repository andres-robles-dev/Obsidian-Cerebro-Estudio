# 07 - Scanner

```java
import java.util.Scanner;

public class LeerDatos {
    public static void main(String[] args) {
        // try-with-resources: se cierra solo
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

**Cuando se usa:** para leer lo que el usuario escribe en la consola (o datos de un archivo con `new Scanner(new File(...))`).

**Reglas:**
- `nextLine()` lee la linea completa como String
- `nextInt()`, `nextDouble()`... leen el valor tipado
- Despues de `nextInt()` queda un `\n` pendiente: si luego llamas `nextLine()` puede devolver vacio
- Si el usuario escribe texto donde esperas numero, lanza `InputMismatchException`
- Cierralo con try-with-resources (o `sc.close()`)

**Ver tema:** [[29 - Scanner en Profundidad]]
