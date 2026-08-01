---
tags: [java, fundamentos, enum, constantes, tipos-enumerados, profundidad]
---

# 33 - enum en Profundidad

Un `enum` (enumeracion) es un tipo especial de clase que define un conjunto fijo de constantes. Es mas que una lista de valores: puede tener campos, metodos, constructores y comportamientos distintos por constante.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un enum?

Un `enum` te permite agrupar constantes relacionadas bajo un mismo tipo. El compilador garantiza que solo puedes usar esos valores, no cualquier String o int.

```java
public enum Dia {
    LUNES, MARTES, MIERCOLES, JUEVES, VIERNES, SABADO, DOMINGO
}
```

```java
public class UsarEnum {
    public static void main(String[] args) {
        var hoy = Dia.MARTES;

        System.out.println(hoy);  // MARTES

        if (hoy == Dia.MARTES) {
            System.out.println("Hoy es martes");
        }
    }
}
```

### Por que enum y no String o int?

```java
// SIN enum — propenso a errores:
public static final String LUNES = "LUNES";
public static final String MARTES = "MARTES";
// Cualquiera puede pasar "lunes" (minusculas) o "miercoles" (sin tilde)

// CON enum — el compilador solo acepta los valores definidos:
public void agendar(Dia dia) { ... }
agendar(Dia.LUNES);           // OK
agendar("lunes");             // ERROR: no compila
```

**Ventajas:**
- Type-safe — no puedes pasar un valor invalido
- Todos los valores se conocen en tiempo de compilacion
- Autocompletado en el IDE
- `switch` exhaustivo (el compilador avisa si falta un caso)

### Los metodos automaticos de todo enum

| Metodo | Que hace | Ejemplo |
|--------|----------|---------|
| `.name()` | Devuelve el nombre exacto como String | `Dia.LUNES.name()` → `"LUNES"` |
| `.ordinal()` | Posicion en el enum (empieza en 0) | `Dia.LUNES.ordinal()` → `0` |
| `.toString()` | Igual que `.name()` (se puede sobreescribir) | `Dia.LUNES.toString()` → `"LUNES"` |
| `Enum.valueOf(tipo, nombre)` | Convierte String a enum (case-sensitive) | `Enum.valueOf(Dia.class, "LUNES")` → `Dia.LUNES` |
| `Dia.valueOf(nombre)` | Version corta | `Dia.valueOf("LUNES")` → `Dia.LUNES` |
| `Dia.values()` | Array con TODOS los valores | `Dia.values()` → `[LUNES, MARTES, ...]` |

```java
public class MetodosEnum {
    public static void main(String[] args) {
        var dia = Dia.SABADO;

        System.out.println(dia.name());     // SABADO
        System.out.println(dia.ordinal());  // 5
        System.out.println(dia);            // SABADO (toString por defecto)

        // .values() — recorrer todos
        for (var d : Dia.values()) {
            System.out.println(d.ordinal() + ": " + d);
        }

        // valueOf() — convertir String a enum
        var desdeTexto = Dia.valueOf("MIERCOLES");
        System.out.println(desdeTexto);  // MIERCOLES

        // CUIDADO: valueOf lanza IllegalArgumentException si no existe
        // var error = Dia.valueOf("MARTES_CON_TILDE");  // ERROR
    }
}
```

### enum con switch expression (Java moderno)

El `switch` con enum es exhaustivo: si cubres todos los valores, no necesitas `default`.

```java
public class DiasSemana {
    public static void main(String[] args) {
        var dia = Dia.VIERNES;

        var mensaje = switch (dia) {
            case LUNES, MARTES, MIERCOLES, JUEVES -> "Es dia laboral";
            case VIERNES -> "Por fin es viernes!";
            case SABADO, DOMINGO -> "Fin de semana!";
        };

        System.out.println(mensaje);  // Por fin es viernes!
    }
}
```

### Enum en constantes de negocio

```java
public enum EstadoPedido {
    PENDIENTE, PAGADO, ENVIADO, ENTREGADO, CANCELADO
}
```

```java
public class Pedido {
    private EstadoPedido estado;

    public Pedido() {
        this.estado = EstadoPedido.PENDIENTE;  // estado inicial
    }

    public void pagar() {
        if (estado == EstadoPedido.PENDIENTE) {
            estado = EstadoPedido.PAGADO;
        }
    }

    public void enviar() {
        if (estado == EstadoPedido.PAGADO) {
            estado = EstadoPedido.ENVIADO;
        }
    }

    public static void main(String[] args) {
        var p = new Pedido();
        System.out.println(p.estado);  // PENDIENTE
        p.pagar();
        System.out.println(p.estado);  // PAGADO
    }
}
```

---

## NIVEL 2 - Mid

### Enum con campos y constructor

Los enum pueden tener atributos, constructor y metodos como cualquier clase.

```java
public enum Dia {
    LUNES(false),
    MARTES(false),
    MIERCOLES(false),
    JUEVES(false),
    VIERNES(false),
    SABADO(true),
    DOMINGO(true);

    private final boolean esFinDeSemana;

    // El constructor solo puede ser private (o package-private)
    private Dia(boolean esFinDeSemana) {
        this.esFinDeSemana = esFinDeSemana;
    }

    public boolean esFinDeSemana() {
        return esFinDeSemana;
    }
}
```

```java
public class TestDia {
    public static void main(String[] args) {
        var hoy = Dia.SABADO;

        System.out.println(hoy + " es fin de semana? " + hoy.esFinDeSemana());
        // SABADO es fin de semana? true

        for (var d : Dia.values()) {
            System.out.println(d + " -> " + d.esFinDeSemana());
        }
        // LUNES -> false, MARTES -> false, ..., SABADO -> true, DOMINGO -> true
    }
}
```

### Enum con varios campos

```java
public enum Planeta {
    MERCURIO(3.303e23, 2.4397e6),
    VENUS(4.869e24, 6.0518e6),
    TIERRA(5.976e24, 6.37814e6),
    MARTE(6.421e23, 3.3972e6),
    JUPITER(1.9e27, 7.1492e7),
    SATURNO(5.688e26, 6.0268e7),
    URANO(8.686e25, 2.5559e7),
    NEPTUNO(1.024e26, 2.4746e7);

    private final double masa;
    private final double radio;

    Planeta(double masa, double radio) {
        this.masa = masa;
        this.radio = radio;
    }

    public double gravedadSuperficial() {
        return 6.67430e-11 * masa / (radio * radio);
    }
}
```

```java
public class TestPlaneta {
    public static void main(String[] args) {
        for (var p : Planeta.values()) {
            System.out.println("%s -> gravedad: %.2f m/s²".formatted(p, p.gravedadSuperficial()));
        }
    }
}
```

### Enum con metodos abstractos por constante

Cada constante puede tener su propio comportamiento:

```java
public enum Operacion {
    SUMA {
        @Override
        public double aplicar(double a, double b) {
            return a + b;
        }
    },
    RESTA {
        @Override
        public double aplicar(double a, double b) {
            return a - b;
        }
    },
    MULTIPLICACION {
        @Override
        public double aplicar(double a, double b) {
            return a * b;
        }
    },
    DIVISION {
        @Override
        public double aplicar(double a, double b) {
            if (b == 0) {
                throw new ArithmeticException("No se puede dividir por cero");
            }
            return a / b;
        }
    };

    public abstract double aplicar(double a, double b);
}
```

```java
public class Calculadora {
    public static void main(String[] args) {
        var a = 10.0;
        var b = 3.0;

        for (var op : Operacion.values()) {
            var resultado = op.aplicar(a, b);
            System.out.println("%s(%.1f, %.1f) = %.2f".formatted(op, a, b, resultado));
        }
        // SUMA(10.0, 3.0) = 13.00
        // RESTA(10.0, 3.0) = 7.00
        // MULTIPLICACION(10.0, 3.0) = 30.00
        // DIVISION(10.0, 3.0) = 3.33
    }
}
```

### Switch exhaustivo con enum

El compilador de Java moderno avisa si no cubres todos los valores de un enum en un `switch`:

```java
// COMPILA OK — todos los casos cubiertos
var mensaje = switch (dia) {
    case LUNES, MARTES, MIERCOLES, JUEVES -> "A trabajar";
    case VIERNES -> "Casi finde";
    case SABADO, DOMINGO -> "Descanso";
};

// ERROR de compilacion si agregas un nuevo valor al enum
// y no actualizas todos los switch
```

### EnumSet y EnumMap (rendimiento extremo)

`EnumSet` y `EnumMap` son implementaciones ultra-optimizadas para enum. Usan un `long` internamente (bitmask) para `EnumSet`.

```java
import java.util.*;

public class DemoEnumSet {
    public static void main(String[] args) {
        // Crear un set con algunos valores
        var laborables = EnumSet.range(Dia.LUNES, Dia.VIERNES);
        var finde = EnumSet.of(Dia.SABADO, Dia.DOMINGO);
        var todos = EnumSet.allOf(Dia.class);
        var ninguno = EnumSet.noneOf(Dia.class);

        System.out.println(laborables);  // [LUNES, MARTES, MIERCOLES, JUEVES, VIERNES]
        System.out.println(finde);       // [SABADO, DOMINGO]

        // Operaciones de conjunto
        laborables.add(Dia.SABADO);
        System.out.println(laborables.contains(Dia.LUNES));  // true

        // Iterar solo los laborables
        for (var d : laborables) {
            System.out.println("Trabajar: " + d);
        }
    }
}
```

```java
public class DemoEnumMap {
    public static void main(String[] args) {
        // EnumMap es un Map especializado para claves enum
        var horarios = new EnumMap<Dia, String>(Dia.class);
        horarios.put(Dia.LUNES, "9:00 - 18:00");
        horarios.put(Dia.MARTES, "9:00 - 18:00");
        horarios.put(Dia.SABADO, "Cerrado");
        horarios.put(Dia.DOMINGO, "Cerrado");

        for (var entry : horarios.entrySet()) {
            System.out.println(entry.getKey() + " -> " + entry.getValue());
        }
    }
}
```

---

## NIVEL 3 - Senior

### Enum implementando interfaces

Un `enum` puede implementar interfaces, lo que permite usarlo en contextos genericos:

```java
public interface Describible {
    String describir();
}

public enum Color implements Describible {
    ROJO("#FF0000"),
    VERDE("#00FF00"),
    AZUL("#0000FF");

    private final String hex;

    Color(String hex) {
        this.hex = hex;
    }

    @Override
    public String describir() {
        return "Color %s (hex: %s)".formatted(name(), hex);
    }
}
```

```java
public class TestColor {
    public static void main(String[] args) {
        List<Describible> items = List.of(Color.ROJO, Color.AZUL);
        items.forEach(i -> System.out.println(i.describir()));
    }
}
```

### Enum como singleton (el patron mas SOLID)

Joshua Bloch (autor de *Effective Java*) dice que el enum es la MEJOR forma de implementar Singleton. La JVM garantiza una unica instancia por constante, incluso bajo serializacion y reflexion.

```java
public enum ConexionBD {
    INSTANCIA;

    private final String url = "jdbc:mysql://localhost:3306/mi_db";
    private final String usuario = "root";

    public void conectar() {
        System.out.println("Conectando a " + url);
    }

    public void ejecutar(String sql) {
        System.out.println("Ejecutando: " + sql);
    }
}
```

```java
// Uso:
ConexionBD.INSTANCIA.conectar();
ConexionBD.INSTANCIA.ejecutar("SELECT * FROM usuarios");

// Garantizado: solo existe UNA instancia
// Inmune a serializacion (la JVM maneja los valores enum especialmente)
// Inmune a reflexion (los enum no se pueden instanciar via reflection)
```

Comparado con el Singleton de clase ([[27 - Singleton en Profundidad]]):
- **Clase Singleton**: vulnerable a reflexion, requiere `readResolve()` para serializacion
- **Enum Singleton**: inmune a todo por diseño de la JVM

### Enum con estrategia (Strategy Pattern)

Cada constante puede encapsular una estrategia diferente:

```java
import java.util.function.*;

public enum FormatoTexto {
    MAYUSCULAS(String::toUpperCase),
    MINUSCULAS(String::toLowerCase),
    CAPITALIZAR(s -> {
        if (s == null || s.isBlank()) return s;
        return s.substring(0, 1).toUpperCase() + s.substring(1).toLowerCase();
    }),
    INVERTIR(s -> new StringBuilder(s).reverse().toString()),
    SIN_ESPACIOS(s -> s.replaceAll("\\s+", ""));

    private final UnaryOperator<String> transformacion;

    FormatoTexto(UnaryOperator<String> transformacion) {
        this.transformacion = transformacion;
    }

    public String aplicar(String texto) {
        return transformacion.apply(texto);
    }
}
```

```java
public class ProcesadorTexto {
    public static void main(String[] args) {
        var texto = "hola mundo java";

        for (var formato : FormatoTexto.values()) {
            var resultado = formato.aplicar(texto);
            System.out.println(formato + ": '" + resultado + "'");
        }
        // MAYUSCULAS: 'HOLA MUNDO JAVA'
        // MINUSCULAS: 'hola mundo java'
        // CAPITALIZAR: 'Hola mundo java'
        // INVERTIR: 'avaj odnum aloh'
        // SIN_ESPACIOS: 'holamundojava'
    }
}
```

### Enum con Predicate (filtros)

```java
import java.util.function.Predicate;

public enum FiltroNumero implements Predicate<Integer> {
    POSITIVOS(n -> n > 0),
    NEGATIVOS(n -> n < 0),
    CERO(n -> n == 0),
    PARES(n -> n % 2 == 0),
    IMPARES(n -> n % 2 != 0);

    private final Predicate<Integer> predicado;

    FiltroNumero(Predicate<Integer> predicado) {
        this.predicado = predicado;
    }

    @Override
    public boolean test(Integer n) {
        return predicado.test(n);
    }
}
```

```java
public class TestFiltro {
    public static void main(String[] args) {
        var numeros = List.of(-3, -2, -1, 0, 1, 2, 3, 4, 5);

        var filtrados = numeros.stream()
            .filter(FiltroNumero.PARES)
            .toList();
        System.out.println(filtrados);  // [-2, 2, 4]

        var positivos = numeros.stream()
            .filter(FiltroNumero.POSITIVOS)
            .toList();
        System.out.println(positivos);  // [1, 2, 3, 4, 5]
    }
}
```

### Enum en lugar de constantes agrupadas (reemplaza `static final`)

```java
// MAL: constantes sueltas, sin tipo
public static final String ROLE_ADMIN = "ADMIN";
public static final String ROLE_USER = "USER";
public static final String ROLE_GUEST = "GUEST";

// BIEN: enum con tipo y comportamiento
public enum Rol {
    ADMIN("Acceso total"),
    USER("Acceso limitado"),
    GUEST("Solo lectura");

    private final String descripcion;

    Rol(String descripcion) {
        this.descripcion = descripcion;
    }

    public String descripcion() {
        return descripcion;
    }

    public boolean puedeEliminar() {
        return this == ADMIN;
    }

    public boolean puedeEditar() {
        return this == ADMIN || this == USER;
    }
}
```

### Pattern Matching con enum (Java 21+)

Con `switch` pattern matching, los enum se integran perfectamente:

```java
public class PatternMatchingEnum {
    public static void main(String[] args) {
        Object valor = Dia.SABADO;

        // Pattern matching con switch (Java 21+)
        var resultado = switch (valor) {
            case Dia d when d.esFinDeSemana() -> "Es finde!";
            case Dia d -> "Es dia laboral";
            case String s -> "Es texto: " + s;
            case null -> "Es null";
            default -> "No se que es";
        };

        System.out.println(resultado);
    }
}
```

### Serializacion de enum (garantia JVM)

Los enum son inherentemente serializables. La JVM garantiza que al deserializar un enum, obtienes la misma instancia singleton, no una copia.

```java
import java.io.*;

public class SerializarEnum {
    public static void main(String[] args) throws Exception {
        var ruta = Path.of("dia.ser");

        // Escribir
        try (var out = new ObjectOutputStream(new FileOutputStream(ruta.toFile()))) {
            out.writeObject(Dia.SABADO);
        }

        // Leer
        try (var in = new ObjectInputStream(new FileInputStream(ruta.toFile()))) {
            var leido = (Dia) in.readObject();
            System.out.println(leido);                     // SABADO
            System.out.println(leido == Dia.SABADO);       // true (misma instancia!)
        }

        Files.deleteIfExists(ruta);
    }
}
```

Con una clase Singleton normal, deserializar crearia una segunda instancia a menos que implementes `readResolve()`. Con enum, la JVM lo maneja automaticamente.

---

## Errores Comunes

### 1. Usar ordinal() para logica de negocio

```java
// MAL: si reordenas los enum, la logica se rompe
if (dia.ordinal() >= 5) {
    System.out.println("Es finde");
}  // Funciona hoy, pero si agregas un dia entre VIERNES y SABADO...

// BIEN: usar campos explicitos
if (dia.esFinDeSemana()) {
    System.out.println("Es finde");
}
```

### 2. Pasar el nombre mal a valueOf

```java
// MAL:
Dia.valueOf("Sabado");     // IllegalArgumentException (case-sensitive)
Dia.valueOf("SABADO ");    // IllegalArgumentException (espacio extra)

// BIEN:
Dia.valueOf("SABADO");     // OK

// Para entradas de usuario, normaliza:
var entrada = "  Sabado  ";
var normalizado = entrada.strip().toUpperCase();  // "SABADO"
var dia = Dia.valueOf(normalizado);
```

### 3. Tener un enum con demasiadas constantes o logica

```java
// MAL: enum que hace de todo
public enum TodoPoderoso {
    // 50 constantes con logica compleja que deberia estar en clases separadas
}

// BIEN: enum es para CONJUNTOS FIJOS y PEQUENOS
// Si el conjunto cambia frecuentemente o es enorme, NO es enum
```

### 4. Olvidar que enum tiene constructor PRIVATE siempre

```java
public enum Color {
    ROJO("#FF0000");

    // Color(String hex) { }  // Solo puede ser private o package-private
    // public Color(String hex) { }  // ERROR: no compila
}
```

### 5. Usar == vs equals con enum

Con enum, **== funciona** porque cada constante es singleton. Pero por consistencia, usa `==` (es mas eficiente y seguro):

```java
var dia = Dia.LUNES;

if (dia == Dia.LUNES) { }      // OK y preferido
if (dia.equals(Dia.LUNES)) { } // Tambien funciona, pero redundante
```

### 6. No manejar valueOf de entrada insegura

```java
// Si el String viene del usuario, podria lanzar exception:
try {
    var rol = Rol.valueOf(entrada.strip().toUpperCase());
} catch (IllegalArgumentException e) {
    System.out.println("Rol invalido");
    // O preguntar de nuevo
}
```

---

## Buenas Practicas

1. **Usa enum en vez de constantes String o int** siempre que tengas un conjunto fijo de valores.

2. **No uses ordinal() para logica** — Si importa el orden, anade un campo `int orden` explicito.

3. **Aprovecha switch exhaustivo** — Si agregas una constante al enum, el compilador te avisara donde falta implementacion.

4. **Usa EnumSet para conjuntos y EnumMap para mapas** — Son drasticamente mas rapidos y compactos que HashSet/HashMap.

5. **Implementa interfaces para dar comportamiento generico** — Los enum pueden ser `Comparator`, `Predicate`, `Function`, etc.

6. **Para Singleton, prefiere enum sobre clase clasica** — Es mas seguro, mas simple, y la JVM lo garantiza.

7. **No pongas logica compleja en el enum** — Si un metodo tiene mas de 5-10 lineas, extraelo a una clase helper. El enum debe ser declarativo, no ejecutivo.

8. **Usa nombres en MAYUSCULAS para las constantes** (convencion Java), y camelCase para los campos/metodos.

9. **Documenta cada constante** si no es obvia:
   ```java
   public enum Estado {
       /** El pedido fue creado pero no pagado */
       PENDIENTE,
       /** El pago fue confirmado */
       PAGADO,
       /** El producto esta en camino */
       ENVIADO
   }
   ```

10. **Enum con atributos para valores compuestos** — Si necesitas asociar datos (codigos, descripciones), anade campos en vez de usar `switch`.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] — Un enum es un tipo especial de clase
- [[05 - Modificadores de Acceso]] — El constructor de enum siempre es private
- [[10 - Metodos de Instancia]] — Enum puede tener metodos de instancia
- [[12 - Operador Ternario]] — Alternativa a switch simple con enum
- [[13 - Static vs Instancia]] — Los enum son implicitamente static (no se instancian con new)
- [[16 - Bucles y Control de Flujo]] — Recorrer enum con for-each y switch
- [[26 - Private en Profundidad]] — Constructor private en enum (que es el unico permitido)
- [[27 - Singleton en Profundidad]] — Comparacion: enum singleton vs clase singleton
- [[28 - Static en Profundidad]] — Los metodos values() y valueOf() son estaticos
- [[32 - do-while en Profundidad]] — Menu tipico que usa enum + do-while
- [[99 - Glosario Rapido]] — Definiciones rapidas de terminos relacionados