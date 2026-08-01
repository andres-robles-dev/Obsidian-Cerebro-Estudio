---
tags: [java, fundamentos, operador-ternario, condicional, expresion]
---

# 12 - Operador Ternario

---

## NIVEL JUNIOR

### Que es el operador ternario?

Es una forma corta de escribir un `if-else` cuando quieres elegir entre dos valores.

### Sintaxis

```java
variable = (condicion) ? valorSiVerdadero : valorSiFalso;
```

### Ejemplo basico

```java
int edad = 18;
String mensaje = (edad >= 18) ? "Eres mayor de edad" : "Eres menor de edad";
System.out.println(mensaje);
```

Esto es equivalente a:

```java
int edad = 18;
String mensaje;
if (edad >= 18) {
    mensaje = "Eres mayor de edad";
} else {
    mensaje = "Eres menor de edad";
}
System.out.println(mensaje);
```

### Otro ejemplo

```java
int nota = 7;
String resultado = (nota >= 5) ? "Aprobado" : "Suspenso";
System.out.println(resultado);
```

### Caracteristica importante

El ternario **siempre devuelve un valor**. No es como un `if` que solo ejecuta codigo.

---

## NIVEL MID

### Ternario con diferentes tipos

```java
public class DemoTernario {
    public static void main(String[] args) {
        int numero = 10;

        // Con String
        String tipo = (numero % 2 == 0) ? "Par" : "Impar";

        // Con int
        int maximo = (numero > 5) ? numero : 5;

        // Con boolean
        boolean esPositivo = (numero >= 0) ? true : false;

        System.out.println(tipo);      // "Par"
        System.out.println(maximo);    // 10
        System.out.println(esPositivo); // true
    }
}
```

### Ternario en metodos

```java
public class Calculo {
    public double precioConDescuento(double precio, boolean esVip) {
        return esVip ? precio * 0.8 : precio;
    }

    public String clasificar(int nota) {
        return (nota >= 9) ? "Sobresaliente"
             : (nota >= 7) ? "Notable"
             : (nota >= 5) ? "Aprobado"
             : "Suspenso";
    }
}
```

### Ternario dentro de concatenacion

```java
public class Demo {
    public static void main(String[] args) {
        int temperatura = 30;
        System.out.println(
            "Hace " + temperatura + " grados. " +
            (temperatura > 25 ? "Hace calor" : "Temperatura agradable")
        );
    }
}
```

---

## NIVEL SENIOR

### Ternario con Optional

```java
import java.util.Optional;

public class DemoTernarioModerno {
    public Optional<String> obtenerNombre(boolean existe) {
        return existe ? Optional.of("Ana") : Optional.empty();
    }

    public String formatoSeguro(String valor) {
        return valor != null ? valor.trim().toUpperCase() : "SIN VALOR";
    }
}
```

### Ternario con switch expression (Java 14+)

Para mas de dos opciones, `switch` como expresion es mas legible que ternarios anidados:

```java
public class DemoSwithExpresion {
    public static void main(String[] args) {
        int dia = 3;

        // Con ternarios anidados (dificil de leer):
        String tipo = (dia == 6 || dia == 7) ? "Finde" : (dia == 1) ? "Lunes" : "Laborable";

        // Con switch expresion (mas claro):
        String tipoClaro = switch (dia) {
            case 1 -> "Lunes";
            case 6, 7 -> "Finde";
            default -> "Laborable";
        };

        System.out.println(tipoClaro);
    }
}
```

### Ternario con record

```java
public record Resultado(boolean exito, String mensaje) {
    public static Resultado de(boolean condicion) {
        return condicion
            ? new Resultado(true, "Operacion completada")
            : new Resultado(false, "Operacion fallida");
    }
}
```

### Pattern matching con ternario (Java 21+)

```java
public class DemoPatternTernario {
    public String clasificar(Object obj) {
        return switch (obj) {
            case String s -> s.length() > 5 ? "Texto largo" : "Texto corto";
            case Integer i -> i > 0 ? "Positivo" : i < 0 ? "Negativo" : "Cero";
            case null -> "Es nulo";
            default -> "Tipo desconocido";
        };
    }
}
```

---

## Errores Comunes

> Usar ternario para acciones en vez de valores. El ternario debe devolver un valor, no ejecutar acciones. Para ejecutar acciones usa `if-else`.

> Anidar ternarios sin control. `a ? b ? c : d : e` es dificil de leer. Para mas de 2 opciones usa `switch` expresion.

> Poner operaciones complejas en cada rama. Si la logica es larga, usa `if-else` tradicional.

> Olvidar los parentesis en expresiones compuestas. `(a > 0 && b > 0) ? "ok" : "ko"` esta bien, pero sin parentesis puede dar resultados inesperados.

---

## Buenas Practicas

1. Usa ternario solo para elegir entre DOS valores simples.
2. Para mas de 2 opciones, usa `switch` expresion (Java 14+).
3. Si la logica es larga o tiene efectos secundarios, usa `if-else`.
4. Manten el ternario en una sola linea si es posible.
5. No anides ternarios. Si necesitas anidar, mejor usa `switch` o `if-else`.

---

## Conexiones

- [[04 - Variables y Literales]] - Asignacion de literales con ternario
- [[10 - Metodos de Instancia]] - Return con ternario
- [[16 - Bucles y Control de Flujo]] - if-else vs ternario
- [[23 - Metodos - Parametros, Retorno y Return]] - Ternario como expresion de retorno

---

## Tags
`#java #fundamentos #operador-ternario #condicional #expresion #if-else`
