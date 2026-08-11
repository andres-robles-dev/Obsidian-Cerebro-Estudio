# 18 - Try-Catch-Finally

```java
try {
    codigoQuePuedeFallar;
} catch (NombreExcepcion e) {
    codigoDeManejoDelError;
} catch (NombreExcepcionGeneral e) {
    codigoDeManejoDelError;
} finally {
    codigoQueSiempreSeEjecuta;
}
```

**Cuando se usa:** para manejar errores en tiempo de ejecucion sin que el programa se detenga.

**Reglas:**
- Los catch van del mas especifico al mas general (Exception al final)
- finally se ejecuta SIEMPRE (haya error o no), incluso con return
- Un catch vacio traga el error: minimo registralo
- Para recursos (Scanner, archivos) prefiere try-with-resources
- Los errores unchecked (NullPointer, NumberFormat...) se previenen validando antes

---

## Ejemplo de uso

```java
import java.util.Scanner;

public class Ejemplo {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        try {
            System.out.print("Ingresa un numero: ");
            int numero = sc.nextInt();
            int resultado = 100 / numero;
            System.out.println("Resultado: " + resultado);

        } catch (ArithmeticException e) {
            System.out.println("No se puede dividir por cero");

        } catch (InputMismatchException e) {
            System.out.println("Debes escribir un numero");

        } catch (Exception e) {
            System.out.println("Error inesperado: " + e.getMessage());

        } finally {
            sc.close();
        }
    }
}
```

**Ver tema:** [[40 - Manejo de Excepciones]]