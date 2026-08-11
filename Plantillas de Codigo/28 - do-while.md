# 28 - do-while (validacion y menus)

```java
import java.util.Scanner;

public class Menu {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int opcion;

        // Menu: se muestra al menos UNA vez y se repite hasta salir
        do {
            System.out.println("=== MENU ===");
            System.out.println("1. Ver saldo");
            System.out.println("2. Depositar");
            System.out.println("3. Salir");
            System.out.print("Opcion: ");
            opcion = sc.nextInt();

            switch (opcion) {
                case 1 -> System.out.println("Saldo: $100");
                case 2 -> System.out.println("Deposito exitoso");
                case 3 -> System.out.println("Adios");
                default -> System.out.println("Opcion invalida");
            }
        } while (opcion != 3);

        sc.close();
    }
}
```

**Cuando se usa:** para estructuras que deben ejecutarse al menos UNA vez: menus (repetir hasta salir) y validacion de entrada (pedir hasta que sea correcta).

**Reglas:**
- La condicion se evalua al FINAL: el bloque siempre corre al menos una vez
- Usalo cuando la primera ejecucion es segura; si puede no ejecutarse, usa `while`
- Cuidado con bucles infinitos: la condicion debe volverse falsa
- Para validar con `nextInt()` recuerda que texto invalido lanza `InputMismatchException`

**Ver tema:** [[32 - do-while en Profundidad]]
