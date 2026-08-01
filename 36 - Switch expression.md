---
tags: [java, fundamentos, switch, expression, pattern-matching, sealed, java14, java17, java21]
---

# 36 - Switch expression en Profundidad

El switch expression es la evolucion del switch tradicional en Java. Permite que un switch devuelva un valor, elimina la necesidad de break, soporta flechas (->) y, a partir de Java 21, el pattern matching con tipos sellados.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un switch expression?

Un switch expression es un switch que **devuelve un valor**. En lugar de ejecutar bloques de codigo y romper con `break`, cada case usa una flecha `->` y produce un resultado que se puede asignar a una variable.

### Switch tradicional vs switch expression

```java
// SWITCH TRADICIONAL (antes de Java 14):
String diaTipo;
switch (dia) {
    case "LUN", "MAR", "MIÉ", "JUE", "VI":
        diaTipo = "laborable";
        break;
    case "SÁB", "DOM":
        diaTipo = "fin de semana";
        break;
    default:
        diaTipo = "desconocido";
        break;
}

// SWITCH EXPRESSION (Java 14+):
String diaTipo = switch (dia) {
    case "LUN", "MAR", "MIÉ", "JUE", "VI" -> "laborable";
    case "SÁB", "DOM" -> "fin de semana";
    default -> "desconocido";
};
```

### Diferencias clave

| Switch tradicional | Switch expression |
|---|---|
| No devuelve valor | Devuelve un valor asignable a variable |
| Usa `break` para salir | Usa `->` (flecha) — no necesita `break` |
| Puede tener caida entre cases (fall-through) | Cada case es terminal, no hay caida |
| Cada case puede tener multiples sentencias | Cada case es una expresion |
| `default` opcional | `default` opcional pero recomendado |

### Sintaxis basica con flecha

```java
int numero = 3;
String resultado = switch (numero) {
    case 1 -> "uno";
    case 2 -> "dos";
    case 3 -> "tres";
    default -> "otro";
};
System.out.println(resultado);  // "tres"
```

### Manejo de enum con switch expression

```java
enum Estado { PENDIENTE, EN_CURSO, COMPLETADO, CANCELADO }

Estado estado = Estado.EN_CURSO;

String mensaje = switch (estado) {
    case PENDIENTE -> "Esperando inicio";
    case EN_CURSO -> "En progreso";
    case COMPLETADO -> "Terminado";
    case CANCELADO -> "Cancelado";
};
```

---

## NIVEL 2 - Mid

### Yield: multiple sentencias por case

Cuando un case necesita mas de una sentencia antes de devolver un valor, usa `yield`:

```java
String descripcion = switch (dia) {
    case "LUN", "MAR", "MIÉ", "JUE", "VI" -> {
        int hora = java.time.LocalTime.now().getHour();
        if (hora < 12) {
            yield "Mañana laborable";
        } else if (hora < 18) {
            yield "Tarde laborable";
        } else {
            yield "Noche laborable";
        }
    }
    case "SÁB", "DOM" -> "Fin de semana";
    default -> "Desconocido";
};
```

`yield` funciona como `return` dentro de un case de un switch expression. Termina la ejecucion de ese case y devuelve el valor.

### Switch expression con tipos primitivos

```java
int dias = switch (mes) {
    case "ENE", "MAR", "ABR", "JUN", "AGO", "OCT", "DIC" -> 31;
    case "ABR", "JUN", "SEP", "NOV" -> 30;
    case "FEB" -> {
        boolean esBisiesto = (anio % 4 == 0 && anio % 100 != 0) || (anio % 400 == 0);
        yield esBisiesto ? 29 : 28;
    }
    default -> throw new IllegalArgumentException("Mes invalido: " + mes);
};
```

### Arrow label con bloque de codigo (sin yield)

Cuando el case no necesita devolver un valor pero tiene logica compleja:

```java
switch (comando) {
    case "INICIAR" -> {
        System.out.println("Iniciando sistema...");
        inicializarConfiguracion();
        conectarBaseDeDatos();
    }
    case "DETENER" -> {
        System.out.println("Deteniendo sistema...");
        desconectarBaseDeDatos();
        limpiarRecursos();
    }
    default -> System.out.println("Comando no reconocido");
}
```

Nota: cuando usas `->` con un bloque `{}` y no necesitas devolver un valor, no usas `yield`.

### Switch expression con String y pattern matching basico

```java
String tipo = switch (objeto.getClass().getSimpleName()) {
    case "String" -> "texto";
    case "Integer" -> "numero entero";
    case "Double" -> "numero decimal";
    case "Boolean" -> "booleano";
    default -> "otro tipo";
};
```

### Switch como expresion en asignacion ternaria

```java
// Switch expression puede usarse dentro de cualquier expresion:
String resultado = (edad >= 18)
    ? switch (rol) {
        case "ADMIN" -> "Acceso total";
        case "USER" -> "Acceso limitado";
        default -> "Sin acceso";
      }
    : "Menor de edad, sin acceso";
```

### Excepciones en switch expression

```java
// Puedes lanzar excepciones directamente desde un case:
int valor = switch (tipo) {
    case "ENTERO" -> Integer.parseInt(texto);
    case "DECIMAL" -> Double.parseDouble(texto);
    case "BOOLEANO" -> Boolean.parseBoolean(texto) ? 1 : 0;
    default -> throw new IllegalArgumentException("Tipo no soportado: " + tipo);
};
```

### Null handling en switch expression

```java
String tipo = switch (texto) {
    case null -> "nulo";
    case "" -> "vacio";
    default -> "tiene contenido";
};
```

El `case null` es valido en switch expressions y maneja el caso null sin necesidad de verificacion previa.

---

## NIVEL 3 - Senior

### Pattern matching con switch (Java 21 final)

Java 21 finalizo el pattern matching para switch. Permite hacer type testing y casting en una sola sentencia:

```java
Object objeto = obtenerObjeto();

String tipo = switch (objeto) {
    case String s -> "Es un string: " + s;
    case Integer i -> "Es un entero: " + i;
    case Double d -> "Es un doble: " + d;
    case Boolean b -> "Es un booleano: " + b;
    case null -> "Es nulo";
    default -> "Es otro tipo: " + objeto.getClass().getSimpleName();
};
```

La variable declarada en cada case (`s`, `i`, `d`, `b`) esta tipada automaticamente y solo es accesible dentro de ese case.

### Pattern matching con sealed classes

Cuando una jerarquia de clases es `sealed`, el compilador puede verificar que todos los cases estan cubiertos:

```java
sealed interface Forma permits Circulo, Rectangulo, Triangulo {}
record Circulo(double radio) implements Forma {}
record Rectangulo(double ancho, double alto) implements Forma {}
record Triangulo(double base, double altura) implements Forma {}

double area = switch (forma) {
    case Circulo c -> Math.PI * c.radio() * c.radio();
    case Rectangulo r -> r.ancho() * r.alto();
    case Triangulo t -> 0.5 * t.base() * t.altura();
};
// El compilador garantiza que todos los tipos de Forma estan cubiertos
// Si añades un nuevo tipo sellado, el compilador obliga a actualizar el switch
```

### Guardas en pattern matching

Puedes añadir condiciones adicionales a cada case con `when`:

```java
String clasificacion = switch (numero) {
    case Integer i when i > 0 && i <= 10 -> "pequeno positivo";
    case Integer i when i > 10 -> "grande positivo";
    case Integer i when i < 0 && i >= -10 -> "pequeno negativo";
    case Integer i when i < -10 -> "grande negativo";
    case Integer i when i == 0 -> "cero";
    case null -> "nulo";
    default -> "no es un entero";
};
```

### Nested switch expressions

Los switch expressions pueden anidarse:

```java
String resultado = switch (categoria) {
    case "FRUTA" -> switch (nombre) {
        case "MANZANA" -> "Roja o verde";
        case "BANANA" -> "Amarilla";
        case "NARANJA" -> "Naranja";
        default -> "Otra fruta";
    };
    case "VERDURA" -> switch (nombre) {
        case "ZANAHORIA" -> "Naranja";
        case "ESPINACA" -> "Verde";
        default -> "Otra verdura";
    };
    default -> "Categoria desconocida";
};
```

### Switch expression con records y destructuring

Java 21+ permite destructuring de records dentro de pattern matching:

```java
record Punto(int x, int y) {}
record Circulo(Punto centro, double radio) {}
record Rectangulo(Punto esquinaSupIzq, double ancho, double alto) {}

String descripcion = switch (figura) {
    case Circulo(Punto p, double r) ->
        "Circulo en (" + p.x() + "," + p.y() + ") con radio " + r;
    case Rectangulo(Punto p, double w, double h) ->
        "Rectangulo en (" + p.x() + "," + p.y() + ") con " + w + "x" + h;
};
```

### Switch expressions con yield y flujo de control

```java
// yield puede usarse en bloques de codigo para devolver desde cualquier punto:
int resultado = switch (opcion) {
    case 1 -> {
        if (condicionA) {
            yield 10;
        } else if (condicionB) {
            yield 20;
        } else {
            yield 30;
        }
    }
    case 2 -> {
        for (int i = 0; i < 10; i++) {
            if (i == 5) yield 50;  // yield desde dentro de un bucle
        }
        yield 0;
    }
    default -> 0;
};
```

### Switch como statement (sin asignar a variable)

Un switch expression tambien puede usarse como sentencia sin asignar el resultado:

```java
// La expresion se evalua pero el resultado se descarta:
switch (dia) {
    case "LUN", "MAR", "MIÉ", "JUE", "VI" ->
        System.out.println("Dia laborable");
    case "SÁB", "DOM" ->
        System.out.println("Fin de semana");
    default ->
        System.out.println("Desconocido");
};
```

### Switch expression con ThreadLocal y scopes

```java
// El switch expression crea un scope propio para cada case:
String resultado = switch (tipo) {
    case "A" -> {
        String temp = "procesando A";
        yield temp.toUpperCase();
    }
    case "B" -> {
        String temp = "procesando B";
        yield temp.toUpperCase();
    }
    default -> "desconocido";
};
// temp no es accesible fuera de su case (scope limitado al bloque)
```

### Compatibilidad hacia atras: JEP 441 y JEP 474

- **JEP 441** (Java 14): Switch expressions disponibles como preview
- **JEP 474** (Java 21): Pattern matching para switch (second preview, luego final)
- **JEP 441** elimino la restriccion de que un case solo puede tener una constante (ahora se permiten múltiples constantes con coma)

### Compilacion y verificacion exhaustiva

Con tipos sellados, el compilador verifica que el switch es exhaustivo:

```java
sealed interface Servicio permits EmailService, SMSService, PushService {}

String notificar = switch (servicio) {
    case EmailService e -> "Enviando email a " + e.email();
    case SMSService s -> "Enviando SMS a " + s.telefono();
    case PushService p -> "Enviando push a " + p.dispositivo();
};
// Si añades un nuevo tipo a 'permits', el compilador da error aqui
// obligandote a actualizar el switch
```

Sin tipos sellados, el compilador exige un `default` o da error si no cubre todos los posibles valores.

### Performance del switch expression

```java
// Internamente, Java compila el switch expression de forma eficiente:
// - Para enumeraciones pequenas: tabla de saltos (tableswitch)
// - Para enumeraciones grandes o dispersas: tabla hash (lookupswitch)
// - Para strings: hash basado en string (desde Java 7)
// - Para pattern matching: cadena de instanceof checks optimizada

// El switch expression no tiene overhead adicional respecto al switch tradicional.
// La compilacion JIT de HotSpot optimiza ambos de la misma manera.
```

### Switch expression en lambdas y metodos funcionales

```java
import java.util.function.Function;

// Switch expression como lambda:
Function<String, Integer> diasEnMes = mes -> switch (mes) {
    case "ENE", "MAR", "ABR", "JUN", "AGO", "OCT", "DIC" -> 31;
    case "ABR", "JUN", "SEP", "NOV" -> 30;
    case "FEB" -> 28;
    default -> throw new IllegalArgumentException("Mes invalido");
};

// Switch expression en metodo de referencia:
Function<String, String> tipoDia = dia -> switch (dia) {
    case "LUN", "MAR", "MIÉ", "JUE", "VI" -> "laborable";
    case "SÁB", "DOM" -> "fin de semana";
    default -> "desconocido";
};
```

### Switch expression con records y sealed en profundidad

```java
// Jerarquia sellada completa:
sealed interface Expresion permits Suma, Resta, Multiplicacion, Division {}
record Suma(Expresion izquierda, Expresion derecha) implements Expresion {}
record Resta(Expresion izquierda, Expresion derecha) implements Expresion {}
record Multiplicacion(Expresion izquierda, Expresion derecha) implements Expresion {}
record Division(Expresion izquierda, Expresion derecha) implements Expresion {}
record Numero(double valor) implements Expresion {}

// Evaluador con switch expression y pattern matching:
double evaluar(Expresion expr) {
    return switch (expr) {
        case Numero n -> n.valor();
        case Suma s -> evaluar(s.izquierda()) + evaluar(s.derecha());
        case Resta r -> evaluar(r.izquierda()) - evaluar(r.derecha());
        case Multiplicacion m -> evaluar(m.izquierda()) * evaluar(m.derecha());
        case Division d -> {
            double divisor = evaluar(d.derecha());
            if (divisor == 0) throw new ArithmeticException("Division por cero");
            yield evaluar(d.izquierda()) / divisor;
        }
    };
}
```

---

## Errores Comunes

> Olvidar que `->` elimina la caida entre cases (fall-through). En el switch tradicional, sin `break` el flujo cae al siguiente case. Con `->`, cada case es terminal y no hay caida.

> Usar `break` en un switch expression. `break` no existe en switch expressions; usa `->` para terminar un case y `yield` para devolver un valor desde un bloque.

> Usar `return` en vez de `yield` dentro de un bloque de case. `return` sale del metodo completo; `yield` devuelve el valor del case y continua el switch expression.

> No cubrir todos los casos sin tipos sellados. Sin `sealed`, el compilador exige un `default` o dara error de compilacion si no todos los valores posibles estan cubiertos.

> Declarar la variable del case fuera del bloque de case. Las variables declaradas en un case solo son accesibles dentro de ese case.

> Confundir `switch expression` (devuelve valor) con `switch statement` (no devuelve valor). Ambos usan `->` pero solo el expression se asigna a una variable.

> Intentar hacer side effects en un switch expression sin `yield`. Si el case tiene un bloque `{}` y necesitas devolver algo, debes usar `yield`.

> Usar `==` para comparar Strings en los case labels. Los case labels de String usan `.equals()` internamente, no `==`.

> Asumir que el orden de los cases importa para el rendimiento. En switch expressions, el orden no afecta la ejecucion (se optimiza internamente por el compilador).

---

## Buenas Practicas

1. **Usa switch expression siempre que sea posible** — es mas limpio, seguro y legible que el switch tradicional.
2. **Prefiere `->` sobre `case:` + `break`** — elimina errores de olvidar `break` y no tener caida no deseada.
3. **Usa `yield` para logica compleja** dentro de un case que necesita devolver un valor.
4. **Usa tipos sellados (`sealed`) con pattern matching** — el compilador verifica exhaustividad y evita olvidar cases.
5. **Usa `default` como minima seguridad** — incluso con sealed, `default` captura valores inesperados en runtime.
6. **No mezcles switch expression y switch statement** en el mismo bloque — genera confusion.
7. **Usa `case null`** para manejar valores null explícitamente en vez de verificar antes.
8. **Usa guardas `when`** para condiciones adicionales en pattern matching.
9. **No uses `break` en switch expressions** — es un error de compilacion.
10. **Usa records con destructuring** en pattern matching para acceder a campos directamente sin casting.

---

## Conexiones

- [[02 - Punto de Entrada main]] — switch en el metodo main para menus y seleccion
- [[07 - Constructores y this]] — switch para seleccionar logica de construccion segun tipo
- [[12 - Operador Ternario]] — switch expression como alternativa al ternario cuando hay muchas ramas
- [[13 - Static vs Instancia]] — switch sobre tipos de objeto para comportamiento polimorfico
- [[16 - Bucles y Control de Flujo]] — switch como estructura de control junto a if/else/for/while
- [[23 - Metodos - Parametros, Retorno y Return]] — switch expression como metodo que retorna un valor
- [[26 - Private en Profundidad]] — switch expression para encapsulamiento de logica de seleccion
- [[30 - Principio fundamental de Java (y POO)]] — switch con sealed classes y polimorfismo
- [[31 - Curso Completo de .method()]] — switch expression como metodo funcional
- [[33 - enum en Profundidad]] — switch expression sobre enum es el caso de uso mas limpio
- [[34 - Clases Wrapper (envolventes)]] — switch sobre tipos de Wrapper con pattern matching

---

## Tags
`#java #fundamentos #switch #expression #pattern-matching #sealed #java14 #java17 #java21`