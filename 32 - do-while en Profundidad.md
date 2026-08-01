---
tags: [java, fundamentos, bucles, do-while, while, iteracion, profundidad]
---

# 32 - do-while en Profundidad

`do-while` es el unico bucle en Java que garantiza **al menos una ejecucion**. Primero ejecuta el bloque, luego evalua la condicion. Esto lo hace ideal para menus, validacion de entrada y cualquier situacion donde necesites hacer algo antes de decidir si repetir.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es do-while?

`do-while` ejecuta un bloque de codigo y LUEGO verifica una condicion. Si la condicion se cumple, repite. Si no, termina.

```
┌─────────────────────┐
│   EJECUTAR BLOQUE   │  ← Se ejecuta SIEMPRE al menos una vez
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  EVALUAR CONDICION   │
└──────────┬──────────┘
           │
     ┌─────┴─────┐
     ▼           ▼
   true        false
     │           │
     └──────┐    ▼
            │  SALIR
            ▼
      (vuelve a ejecutar)
```

### Sintaxis basica

```java
do {
    // Codigo que se ejecuta AL MENOS una vez
} while (condicion);  // <-- OJO: lleva punto y coma AL FINAL
```

```java
public class PrimerDoWhile {
    public static void main(String[] args) {
        int contador = 0;

        do {
            System.out.println("Vuelta numero " + contador);
            contador++;
        } while (contador < 3);

        // Salida:
        // Vuelta numero 0
        // Vuelta numero 1
        // Vuelta numero 2
    }
}
```

### La diferencia CRUCIAL con while

`while` primero pregunta, luego ejecuta. `do-while` primero ejecuta, luego pregunta.

```java
// MIENTRAS que la condicion sea false desde el inicio:
var condicion = false;

// while: NO ejecuta NUNCA
while (condicion) {
    System.out.println("while: esto NO se imprime");
}

// do-while: ejecuta al menos UNA VEZ
do {
    System.out.println("do-while: esto SI se imprime");
} while (condicion);
```

**Salida:**
```
do-while: esto SI se imprime
```

Cuando usar cada uno:
- `while` — "Mientras pase esto, haz esto" (puede que no se haga nunca)
- `do-while` — "Haz esto, y mientras pase esto, repetir" (se hace siempre al menos una vez)

### Ejemplo practico: Adivina el numero

```java
import java.util.Scanner;

public class AdivinaNumero {
    public static void main(String[] args) {
        var secreto = 7;
        var entrada = new Scanner(System.in);
        int intento;

        do {
            System.out.print("Adivina el numero (1-10): ");
            intento = entrada.nextInt();

            if (intento < secreto) {
                System.out.println("Mas alto");
            } else if (intento > secreto) {
                System.out.println("Mas bajo");
            }
        } while (intento != secreto);

        System.out.println("Acertaste!");
        entrada.close();
    }
}
```

El jugador SIEMPRE puede intentar al menos una vez. Con `while` tendrias que duplicar la lectura o inicializar `intento` con un valor que no sea valido para entrar al bucle.

### Como se lee en voz alta

```java
// "Haz esto... mientras se cumpla esta condicion"
do {
    System.out.println("Hola");
} while (true);
```

---

## NIVEL 2 - Mid

### Menu de opciones en consola

El caso de uso MAS COMUN de `do-while`. El menu se muestra siempre al menos una vez, y el usuario decide cuando salir.

```java
import java.util.Scanner;

public class MenuOpciones {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);
        int opcion;

        do {
            System.out.println("""
                === MENU PRINCIPAL ===
                1. Ver usuarios
                2. Agregar usuario
                3. Eliminar usuario
                4. Salir
                """);
            System.out.print("Selecciona una opcion: ");
            opcion = entrada.nextInt();
            entrada.nextLine();  // consumir salto

            switch (opcion) {
                case 1 -> System.out.println("Mostrando usuarios...");
                case 2 -> System.out.println("Agregando usuario...");
                case 3 -> System.out.println("Eliminando usuario...");
                case 4 -> System.out.println("Hasta luego!");
                default -> System.out.println("Opcion invalida");
            }
        } while (opcion != 4);

        entrada.close();
    }
}
```

El `do-while` es perfecto porque:
1. El menu se muestra siempre
2. No necesitas inicializar `opcion` con un valor artificial
3. La condicion de salida se evalua DESPUES de procesar la opcion

### Validacion de entrada con Scanner

Pedir un dato hasta que el usuario ingrese algo valido:

```java
import java.util.Scanner;

public class ValidarEntrada {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);
        int edad;

        do {
            System.out.print("Ingresa tu edad (1-120): ");

            while (!entrada.hasNextInt()) {
                System.out.print("Eso no es un numero. Ingresa tu edad: ");
                entrada.next();  // descartar el texto invalido
            }

            edad = entrada.nextInt();
            entrada.nextLine();  // consumir salto

            if (edad < 1 || edad > 120) {
                System.out.println("Edad fuera de rango");
            }
        } while (edad < 1 || edad > 120);

        System.out.println("Edad registrada: " + edad);
        entrada.close();
    }
}
```

### do-while con confirmacion

Pedir confirmacion para repetir una accion:

```java
import java.util.Scanner;

public class Confirmacion {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);
        String respuesta;

        do {
            System.out.print("Vas a borrar todos los datos. Continuar? (si/no): ");
            respuesta = entrada.nextLine().strip().toLowerCase();

            if (respuesta.equals("si")) {
                System.out.println("Datos borrados.");
            } else if (respuesta.equals("no")) {
                System.out.println("Operacion cancelada.");
            } else {
                System.out.println("Responde 'si' o 'no'.");
            }
        } while (!respuesta.equals("si") && !respuesta.equals("no"));

        entrada.close();
    }
}
```

### do-while anidados

Un menu dentro de otro menu:

```java
import java.util.Scanner;

public class MenuAnidado {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);
        int opcionPrincipal;

        do {
            System.out.println("""
                === PRINCIPAL ===
                1. Gestion usuarios
                2. Reportes
                3. Salir
                """);
            System.out.print("Opcion: ");
            opcionPrincipal = entrada.nextInt();
            entrada.nextLine();

            switch (opcionPrincipal) {
                case 1 -> {
                    int opcionUsuarios;
                    do {
                        System.out.println("""
                            === USUARIOS ===
                            1. Listar
                            2. Crear
                            3. Volver
                            """);
                        System.out.print("Opcion: ");
                        opcionUsuarios = entrada.nextInt();
                        entrada.nextLine();

                        switch (opcionUsuarios) {
                            case 1 -> System.out.println("Listando usuarios...");
                            case 2 -> System.out.println("Creando usuario...");
                            case 3 -> System.out.println("Volviendo...");
                            default -> System.out.println("Opcion invalida");
                        }
                    } while (opcionUsuarios != 3);
                }
                case 2 -> System.out.println("Generando reportes...");
                case 3 -> System.out.println("Saliendo...");
                default -> System.out.println("Opcion invalida");
            }
        } while (opcionPrincipal != 3);

        entrada.close();
    }
}
```

### do-while para procesar archivos

Leer un archivo linea por linea hasta el final:

```java
import java.nio.file.*;
import java.io.*;
import java.util.*;

public class LeerArchivo {
    public static void main(String[] args) throws IOException {
        var ruta = Path.of("datos.txt");

        if (!Files.exists(ruta)) {
            System.out.println("El archivo no existe");
            return;
        }

        try (var sc = new Scanner(ruta)) {
            String linea;
            do {
                linea = sc.hasNextLine() ? sc.nextLine() : null;
                if (linea != null) {
                    System.out.println(linea);
                }
            } while (linea != null);
        }
    }
}
```

---

## NIVEL 3 - Senior

### do-while como "bucle de busqueda" sin cuerpo

A veces se usa `do-while` con el cuerpo vacio para buscar un valor donde la actualizacion esta en la condicion misma. No es comun, pero se ve en algoritmos especificos:

```java
// Encontrar el primer multiplo de 7 mayor que 1000
int numero = 1000;
do {
} while (++numero % 7 != 0);

System.out.println(numero);  // 1001
```

Esto funciona porque `++numero` se ejecuta en la condicion CADA VEZ que se evalua. El cuerpo vacio `{}` solo existe porque la sintaxis lo requiere.

```java
// Mas legible con while (preferible):
numero = 1000;
while (++numero % 7 != 0) {
    // cuerpo vacio
}
```

**Consejo**: Evita el cuerpo vacio. Usa `while` si la logica esta toda en la condicion, o mejor aun, escribe el codigo para que sea legible.

### Algoritmo de aproximacion (Newton-Raphson)

`do-while` es ideal para algoritmos que se aproximan hasta converger:

```java
public class RaizCuadrada {
    public static void main(String[] args) {
        var numero = 25.0;
        var tolerancia = 0.0001;
        var aproximacion = numero / 2;  // estimacion inicial
        double diferencia;

        do {
            var mejorAprox = (aproximacion + numero / aproximacion) / 2;
            diferencia = Math.abs(mejorAprox - aproximacion);
            aproximacion = mejorAprox;
        } while (diferencia > tolerancia);

        System.out.println("Raiz cuadrada de " + numero + " es ~" + aproximacion);
        // Raiz cuadrada de 25.0 es ~5.000000000053722
    }
}
```

El algoritmo SIEMPRE se ejecuta al menos una vez (la estimacion inicial puede ser buena o mala), y se repite hasta alcanzar la precision deseada.

### do-while con Iterator (caso pragmatico)

Cuando iteras manualmente con `Iterator`, necesitas al menos un elemento para empezar:

```java
import java.util.*;

public class DoWhileIterator {
    public static void main(String[] args) {
        var nombres = new ArrayList<>(List.of("Ana", "Carlos", "Beatriz"));
        var iterador = nombres.iterator();

        if (iterador.hasNext()) {  // Verificar que hay al menos uno
            do {
                var nombre = iterador.next();
                System.out.println(nombre);
            } while (iterador.hasNext());
        }
    }
}
```

Aunque el `if` externo es necesario para evitar que `do-while` falle en lista vacia. En la practica, `while (iterador.hasNext())` es mas claro.

### Performance: do-while vs while

A nivel de bytecode, `do-while` y `while` son casi identicos. La unica diferencia es cuantas veces se evalua la condicion:

- `while`: condicion evaluada N+1 veces (antes de cada iteracion + la ultima que falla)
- `do-while`: condicion evaluada N veces (despues de cada iteracion, no hay evaluacion extra)

En la practica, la diferencia es insignificante para el 99.9% de los casos. **Elige por legibilidad, no por rendimiento.**

```java
// Ejemplo extremo: evaluacion costosa en la condicion
// CON do-while (N evaluaciones):
int i = 0;
do {
    procesar(datos[i]);
} while (funcionCostosa(++i));

// CON while (N+1 evaluaciones — una extra al final):
i = 0;
while (funcionCostosa(i)) {
    procesar(datos[i]);
    i++;
}
```

### do-while con flujo de control avanzado

`break` y `continue` dentro de do-while:

```java
public class ControlFlujo {
    public static void main(String[] args) {
        int i = 0;

        do {
            i++;
            if (i == 3) continue;  // salta el print del 3
            if (i == 7) break;     // sale cuando llega a 7
            System.out.println("i = " + i);
        } while (i < 10);

        // Imprime: 1, 2, 4, 5, 6
    }
}
```

### do-while etiquetado para salir de bucles anidados

```java
public class BucleEtiquetado {
    public static void main(String[] args) {
        var matriz = new int[][]{
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        var objetivo = 5;
        var encontrado = false;

        externo: do {
            for (int fila = 0; fila < matriz.length; fila++) {
                for (int col = 0; col < matriz[fila].length; col++) {
                    if (matriz[fila][col] == objetivo) {
                        System.out.println("Encontrado en [" + fila + "][" + col + "]");
                        encontrado = true;
                        break externo;
                    }
                }
            }
        } while (false);  // solo una iteracion, pero permite el break etiquetado

        if (!encontrado) {
            System.out.println("No encontrado");
        }
    }
}
```

---

## Errores Comunes

### 1. Olvidar el punto y coma al final

```java
do {
    System.out.println("Hola");
} while (true)   // ERROR: falta punto y coma
```

El `do-while` es el UNICO bucle que lleva punto y coma al final. `while` y `for` NO llevan.

### 2. Crear un bucle infinito sin querer

```java
int x = 0;
do {
    System.out.println(x);
    // Olvidaste incrementar x
} while (x < 10);  // Nunca cambia, siempre 0 < 10 = true
```

### 3. Usar do-while cuando while es mas claro

```java
// Si la logica no requiere ejecutarse al menos una vez, usa while.
// MAL:
int i = 0;
do {
    System.out.println("Hola");
} while (i < 0);  // Se ejecuta 1 vez aunque i < 0 sea false

// BIEN (no necesitas al menos una ejecucion):
while (i < 0) {
    System.out.println("Hola");
}  // No se ejecuta nunca
```

### 4. No inicializar variables usadas en la condicion

```java
String entrada;
do {
    System.out.print("Escribe algo: ");
    entrada = sc.nextLine();
} while (entrada.length() > 0);  // OK: entrada se asigna antes de evaluar
```

Esto funciona porque `do-while` asigna PRIMERO y evalua DESPUES. Con `while` no compilaria.

### 5. Confundir do-while con while + cuerpo antes

```java
// Esto NO es equivalente a do-while si la variable no esta inicializada:
int x;
// x++;  // ERROR: no compila, x no esta inicializada

int y = 0;
y++;
while (y < 3) {
    y++;
}  // y empieza en 1, no en 0
```

---

## Buenas Practicas

1. **Usa do-while para menus y validacion de entrada** — Es su proposito natural. La accion (mostrar menu, pedir dato) se ejecuta siempre, la decision de repetir viene despues.

2. **No uses do-while cuando while funciona igual de claro**
   ```java
   // Prefiere while si no necesitas la ejecucion garantizada:
   while (condicion) { ... }
   
   // Usa do-while cuando NECESITAS que pase al menos una vez:
   do { ... } while (condicion);
   ```

3. **Inicializa bien las variables** — La variable en la condicion debe tener un valor valido DESPUES de la primera ejecucion.

4. **Manten el cuerpo pequeno** — Si el bloque dentro de `do` crece mucho, extraelo a un metodo.

5. **Evita el cuerpo vacio** — `do { } while (++i < n);` es confuso. Usa `while` o escribe el codigo claramente.

6. **Combina con switch expression moderno** para menus mas limpios
   ```java
   do {
       mostrarMenu();
       opcion = entrada.nextInt();
       System.out.println(switch (opcion) {
           case 1 -> "Ver usuarios";
           case 2 -> "Agregar usuario";
           case 3 -> "Salir";
           default -> "Opcion invalida";
       });
   } while (opcion != 3);
   ```

7. **try-with-resources para Scanner dentro de do-while** — Cerrar Scanner dentro del bucle es trampa. Declaralo fuera.

8. **Considera recursion para arboles, do-while para lineales** — Si el problema es inherentemente recursivo (arboles, JSON), no lo fuerces con do-while. Si es lineal (menus, aproximaciones), do-while es perfecto.

---

## Conexiones

- [[16 - Bucles y Control de Flujo]] — Base de todos los bucles en Java
- [[07 - Constructores y this]] — Bucles se usan en constructores para inicializar colecciones
- [[08 - Instanciacion y new]] — Crear objetos dentro de bucles (cuidado con el rendimiento)
- [[10 - Metodos de Instancia]] — Los metodos pueden contener bucles do-while
- [[11 - System.out y Concatenacion]] — Mostrar menus con text blocks y System.out.print
- [[14 - Arrays Basicos y args]] — Recorrer arrays con do-while
- [[15 - Listas y ArrayList]] — Iterar listas con do-while (aunque for-each es mejor)
- [[23 - Metodos - Parametros, Retorno y Return]] — Metodos que encierran bucles do-while
- [[29 - Scanner en Profundidad]] — El companero inseparable del do-while en menus
- [[31 - Curso Completo de .method()]] — Los metodos que se usan dentro de bucles
- [[99 - Glosario Rapido]] — Definiciones rapidas de terminos de bucles