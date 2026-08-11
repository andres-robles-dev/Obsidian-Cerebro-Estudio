# 28 - do-while

```java
do {
    codigoARepetir;
} while (condicion);
```

**Cuando se usa:** para estructuras que deben ejecutarse al menos UNA vez: menus (repetir hasta salir) y validacion de entrada (pedir hasta que sea correcta).

**Reglas:**
- La condicion se evalua al FINAL: el bloque siempre corre al menos una vez
- Si la primera ejecucion podria no pasar, usa while
- La condicion debe volverse falsa o habra un bucle infinito
- Ideal para menus con opcion de salida

---

## Ejemplo de uso

```java
import java.util.Scanner;

public class Menu {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int opcion;

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

**Ver tema:** [[32 - do-while en Profundidad]]