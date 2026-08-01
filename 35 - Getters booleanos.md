---
tags: [java, fundamentos, boolean, getter, is-prefix, predicate, wrapper]
---

# 35 - Getters booleanos en Profundidad

En Java los campos booleanos se acceden con getters que usan el prefijo `is` en vez de `get`. Esta convencion, junto con la diferencia entre `boolean` primitivo y `Boolean` wrapper, es la base de la logica condicional y el manejo de estados en Java.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un getter booleano?

Un getter booleano es un metodo que devuelve `true` o `false` para indicar el estado de un campo. La convencion Java dice que si el campo es booleano, el getter debe empezar con `is` en vez de `get`.

```java
public class Usuario {
    private boolean activo;
    private boolean admin;

    // getter booleano: usa "is" no "get"
    public boolean isActivo() {
        return activo;
    }

    public boolean isAdmin() {
        return admin;
    }

    // setter normal (no booleano especial)
    public void setActivo(boolean activo) {
        this.activo = activo;
    }
}
```

### La convencion `isXxx()`

| Tipo de campo | Nombre del getter | Ejemplo |
|---------------|-------------------|---------|
| `boolean` | `isXxx()` | `isActivo()` |
| Otros tipos | `getXxx()` | `getNombre()` |

### Booleano simple vs String si/no

```java
public class Servicio {
    private boolean habilitado;

    // Con布尔:
    public boolean isHabilitado() {
        return habilitado;
    }

    // Convertir a si/no (util para UI):
    public String getHabilitadoTexto() {
        return habilitado ? "Si" : "No";
    }
}
```

### Operadores logicos basicos con booleanos

```java
boolean a = true;
boolean b = false;

// AND (&&): ambos deben ser true
System.out.println(a && b);   // false

// OR (||): al menos uno debe ser true
System.out.println(a || b);   // true

// NOT (!): invierte el valor
System.out.println(!a);       // false
System.out.println(!b);       // true
```

---

## NIVEL 2 - Mid

### Boolean (wrapper) vs boolean (primitivo)

```java
// Primitivo boolean: siempre true o false, nunca null
boolean primitivo = true;

// Boolean wrapper: puede ser null (util en bases de datos, APIs)
Boolean wrapper = null;

// Conversión primitivo -> Wrapper (autoboxing)
Boolean b1 = true;       // autoboxing
Boolean b2 = Boolean.valueOf(true);  // recomendado

// Conversion Wrapper -> primitivo (unboxing)
boolean p = b1;          // unboxing automatico

// El peligro: unboxing de null
Boolean posibleNull = null;
// boolean valor = posibleNull;  // NullPointerException!

// Forma segura de manejar null
boolean seguro = Boolean.TRUE.equals(posibleNull);  // false, no NPE
boolean valor = Boolean.FALSE.equals(posibleNull);  // false, no NPE
```

### Boolean methods utiles

```java
// Boolean.valueOf(String): convierte cadena a Boolean (objeto)
Boolean t = Boolean.valueOf("true");     // Boolean.TRUE
Boolean f = Boolean.valueOf("false");    // Boolean.FALSE
Boolean n = Boolean.valueOf("otro");     // Boolean.FALSE (no lanza exception)

// Boolean.parseBoolean(String): devuelve primitivo
boolean pt = Boolean.parseBoolean("true");    // true
boolean pf = Boolean.parseBoolean("false");   // false
boolean pn = Boolean.parseBoolean("otro");    // false (no lanza exception)

// La diferencia clave:
// valueOf devuelve Boolean (objeto, null-safe con equals)
// parseBoolean devuelve boolean (primitivo, no admite null)
```

### Getters booleanos en records (Java 14+)

```java
public record Registro(String nombre, boolean activo, boolean verificado) {
    // Java genera automaticamente los getters:
    // nombre() -> String
    // activo() -> boolean  (¡¡NO isActivo!!)
    // verificado() -> boolean

    // Pero la convencion IDE/JavaBeans sigue pidiendo isXxx() para boolean.
    // En records, el accessor es el nombre del campo directamente.

    // Para forzar isXxx(), puedes definir el metodo manualmente:
    public boolean isActivo() {
        return activo;
    }
}
```

### Getter booleano con logica condicional

```java
public class CuentaBancaria {
    private boolean sobregiroPermitido;
    private double saldo;
    private double limiteSobregiro;

    // El getter calcula el estado, no solo lee un campo:
    public boolean isSobregiroPermitido() {
        return sobregiroPermitido && saldo >= -limiteSobregiro;
    }

    public boolean isSaldoNegativo() {
        return saldo < 0;
    }

    public boolean isSaldoSuficiente(double monto) {
        return saldo >= monto;
    }

    // Comportamiento basado en booleanos
    public boolean debitar(double monto) {
        if (isSaldoSuficiente(monto) || isSobregiroPermitido()) {
            saldo -= monto;
            return true;
        }
        return false;
    }
}
```

### Boolean en colecciones y generics

```java
// HashMap con Boolean como valor:
Map<String, Boolean> permisos = new HashMap<>();
permisos.put("lectura", true);
permisos.put("escritura", false);
permisos.put("admin", null);  // null valido en Boolean, NO en boolean

// Verificar permiso de forma segura:
boolean puedeLeer = Boolean.TRUE.equals(permisos.get("lectura"));     // true
boolean puedeEscribir = Boolean.TRUE.equals(permisos.get("escritura")); // false
boolean puedeAdmin = Boolean.TRUE.equals(permisos.get("admin"));        // false (null -> false)

// ArrayList de booleanos (requiere Boolean wrapper):
List<Boolean> flags = Arrays.asList(true, false, null, true);

// Filtrar solo los true (ignorando null):
List<Boolean> soloActivos = flags.stream()
    .filter(Boolean::booleanValue)  // cuidado: NPE si hay null
    .collect(Collectors.toList());

// Forma segura de filtrar:
List<Boolean> seguros = flags.stream()
    .filter(b -> Boolean.TRUE.equals(b))  // null se filtra automaticamente
    .collect(Collectors.toList());
```

### Comparaciones y verificacion de estados

```java
public class Configuracion {
    private boolean debug;
    private boolean modoPrueba;
    private boolean cacheHabilitada;

    // Combinaciones de estado para logica compleja:
    public boolean isModoDesarrollo() {
        return debug && !modoPrueba;
    }

    public boolean isModoTest() {
        return modoPrueba && !debug;
    }

    public boolean isModoDebugTest() {
        return debug && modoPrueba;
    }

    public boolean isCacheDisponible() {
        return cacheHabilitada && !isModoTest();
    }

    // Estado invalido (debug + test pero sin cache)
    public boolean isConfiguracion Invalida() {
        return debug && modoPrueba && !cacheHabilitada;
    }
}
```

---

## NIVEL 3 - Senior

### Predicados funcionales con booleanos (Java 8+)

Los getters booleanos son fundamentalmente predicados: funciones que devuelven `true` o `false`. Java 8 introduce la interfaz `Predicate<T>` para encapsular esta logica.

```java
import java.util.function.Predicate;

public class Validador {
    // Predicate: funcion que recibe T y devuelve boolean
    Predicate<String> esNoVacia = s -> s != null && !s.isBlank();
    Predicate<String> esLarga = s -> s != null && s.length() > 5;
    Predicate<Integer> esPositivo = n -> n != null && n > 0;

    // Combinacion de predicados (equivalente a && y ||):
    Predicate<String> esValida = esNoVacia.and(esLarga);

    // Uso:
    public boolean validar(String input) {
        return esValida.test(input);
    }
}
```

### Boolean Supplier: proveedor de boolean lazy

```java
import java.util.function.BooleanSupplier;

public class VerificacionPerezosa {
    private boolean cacheLimpio;
    private boolean conexionActiva;
    private boolean datosActualizados;

    // BooleanSupplier: no ejecuta la logica hasta que se llama a getAsBoolean()
    BooleanSupplier puedoEjecutar = () -> cacheLimpio && conexionActiva && datosActualizados;

    public void procesarSiEsPosible() {
        if (puedoEjecutar.getAsBoolean()) {
            System.out.println("Ejecutando...");
        } else {
            System.out.println("Condiciones no cumplidas");
        }
    }
}
```

### Boolean en patrones de diseno

#### Strategy con booleanos

```java
public interface ValidacionStrategy {
    boolean isValid(String input);
}

public class ValidadorEmail implements ValidacionStrategy {
    @Override
    public boolean isValid(String input) {
        return input != null && input.contains("@") && input.contains(".");
    }
}

public class ValidadorCedula implements ValidacionStrategy {
    @Override
    public boolean isValid(String input) {
        return input != null && input.matches("\\d{10}");
    }
}

// Uso:
ValidacionStrategy email = new ValidadorEmail();
ValidacionStrategy cedula = new ValidadorCedula();

boolean emailOk = email.isValid("usuario@mail.com");
boolean cedulaOk = cedula.isValid("1234567890");
```

### Boolean en enums

Los enums pueden tener campos y metodos booleanos que definen comportamiento:

```java
public enum EstadoPedido {
    PENDIENTE(false),
    EN_PROCESO(false),
    ENVIADO(true),
    ENTREGADO(true),
    CANCELADO(false);

    private final boolean completado;

    EstadoPedido(boolean completado) {
        this.completado = completado;
    }

    public boolean isCompletado() {
        return completado;
    }

    public boolean isPendiente() {
        return this == PENDIENTE;
    }

    public boolean puedeCancelarse() {
        return this == PENDIENTE || this == EN_PROCESO;
    }
}

// Uso:
EstadoPedido estado = EstadoPedido.ENVIADO;
System.out.println(estado.isCompletado());    // true
System.out.println(estado.isPendiente());     // false
System.out.println(estado.puedeCancelarse()); // false
```

### Conversion implicita y trampas comunes

```java
// Java no convierte implicitamente Boolean a boolean en todos los contextos:
Boolean b = null;

// if() acepta boolean (primitivo), pero Boolean (wrapper) se hace auto-unboxing:
if (b) { }  // NullPointerException! (unboxing de null Boolean a boolean)

// La unica forma segura de usar Boolean en un if:
if (Boolean.TRUE.equals(b)) { }  // false si b es null, sin NPE
if (Boolean.FALSE.equals(b)) { }  // false si b es null, sin NPE
if (b != null && b) { }           // tambien funciona pero es mas verboso
if (Boolean.TRUE.equals(b)) { }  // forma idiomática y segura

// Boolean en ternario:
boolean resultado = (b != null) ? b : false;  // forma explicita
boolean resultado2 = Boolean.TRUE.equals(b);   // forma idiomatica (true si b es TRUE, false para null y FALSE)
```

### Bitwise ops vs logical ops en booleans

Para `boolean`, Java SOLO soporta operadores logicos (`&&`, `||`, `!`). No bitwise (`&`, `|`, `^`):

```java
boolean a = true;
boolean b = false;

// CORRECTO para boolean:
boolean andLogico = a && b;   // short-circuit: no evalua b si a es false
boolean orLogico = a || b;    // short-circuit: no evalua b si a es true

// Tambien se permite (no short-circuit, evalua ambos lados siempre):
boolean andBit = a & b;       // false (ambos se evaluan)
boolean orBit  = a | b;       // true (ambos se evaluan)
boolean xor    = a ^ b;       // true

// Diferencia clave:
// && y || cortan la evaluacion (short-circuit). & y | no.
// En contextos booleanos, & y | son validos pero no se recomiendan
// (pueden causar efectos secundarios inesperados).
```

### Boolean y JSON serializacion

```java
// Al serializar Boolean (wrapper) a JSON, null se representa como null:
// {"activo": true}  o  {"activo": null}

// Al serializar boolean (primitivo), nunca null:
// {"activo": true}  o  {"activo": false}

// Jackson (libreria de JSON) comportamiento:
public class Config {
    private Boolean nullableActivo;   // serializa como: "nullableActivo": true/false/null
    private boolean primitivoActivo;  // serializa como: "primitivoActivo": true/false
}
```

### Boolean y Optional (fluido y seguro)

```java
// Cuando un campo Boolean puede ser null y quieres usar Optional:
Boolean activoDB = obtenerDeBaseDeDatos();  // puede ser null

// Forma 1: Optional con map
boolean resultado = Optional.ofNullable(activoDB)
    .map(Boolean::booleanValue)    // solo si no es null
    .orElse(false);                 // default si es null

// Forma 2: BooleanUtils de Apache Commons Lang (truco de utileria)
// boolean resultado = BooleanUtils.toBooleanDefaultIfNull(activoDB, false);

// Forma 3: ternario directo (sin Optional)
boolean resultadoTernario = activoDB != null ? activoDB : false;
```

### Reflexion y el campo "alternate" boolean

```java
import java.lang.reflect.Field;

public class DemoReflection {
    public static void main(String[] args) throws Exception {
        var obj = new DemoReflection();

        Field campoBoolean = DemoReflection.class.getDeclaredField("flag");
        campoBoolean.setAccessible(true);

        // Obtener valor boolean via reflection (manejado automaticamente)
        boolean valor = campoBoolean.getBoolean(obj);  // boolean primitivo

        // Setear valor boolean via reflection
        campoBoolean.setBoolean(obj, true);  // funciona con boolean primitivo
    }

    private boolean flag = false;
}
```

### Boolean como condicion para metodos y control de flujo

```java
public class ControlFlujo {
    private boolean procesando;
    private boolean pausado;
    private boolean error;

    // Metodo que devuelve boolean como control de flujo
    public boolean intentarProcesar() {
        if (pausado || error) {
            return false;  // condicion de parada
        }
        if (procesando) {
            return false;  // ya se esta procesando
        }
        procesando = true;
        try {
            // logica de procesamiento...
            return true;
        } catch (Exception e) {
            error = true;
            return false;
        } finally {
            procesando = false;
        }
    }

    // Metodo booleando otro metodo booleano (encadenamiento):
    public boolean puedeIniciar() {
        return !procesando && !pausado && !error;
    }

    public boolean puedeReanudar() {
        return !procesando && pausado && !error;
    }
}
```

### Boolean y streams: filtrado booleano

```java
import java.util.*;
import java.util.stream.*;

public class FiltroBooleano {
    public static void main(String[] args) {
        record Archivo(String nombre, boolean existe, boolean esDirectorio, long tamaño) {}

        List<Archivo> archivos = Arrays.asList(
            new Archivo("main.java", true, false, 2048),
            new Archivo("utils.java", true, false, 1024),
            new Archivo("build/", true, true, 4096),
            new Archivo("config.yml", false, false, 0)
        );

        // Filtrar solo archivos que existen y son archivos (NO directorios)
        List<Archivo> soloArchivos = archivos.stream()
            .filter(Archivo::existe)              // Boolean getter boolean
            .filter(a -> !a.esDirectorio())        // boolean negado
            .collect(Collectors.toList());

        // Filtrar archivos existentes mayores a 1500 bytes
        long totalBytes = archivos.stream()
            .filter(Archivo::existe)
            .filter(a -> a.tamaño() > 1500)
            .mapToLong(Archivo::tamaño)
            .sum();

        // Contar cuantos son directorios vs archivos
        long numDirectorios = archivos.stream()
            .filter(Archivo::esDirectorio)
            .count();

        long numArchivos = archivos.stream()
            .filter(a -> !a.esDirectorio())
            .count();
    }
}
```

---

## Errores Comunes

> Usar `getActivo()` en vez de `isActivo()`. La convencion JavaBeans exige `isXxx()` para `boolean` y `isXxx()` para `Boolean`. Los IDEs y frameworks (Jackson, Spring, etc.) siguen esta convencion.

> Hacer unboxing de `Boolean` null sin verificar. `if (booleanoObj)` lanza `NullPointerException` si el Boolean es null. Usa siempre `Boolean.TRUE.equals(booleanoObj)` para verificar null-safe.

> Confundir `Boolean.parseBoolean()` con `Boolean.valueOf()`. Ambos ignoran strings que no son "true" (case-insensitive) y devuelven false/null en vez de lanzar excepcion.

> Usar `==` para comparar Boolean objects. `Boolean` objects fuera de la cache [-128, 127] comparan por referencia. Usa `Boolean.TRUE.equals()` o `Boolean.FALSE.equals()` en vez de `==`.

> Crear booleanos con `new Boolean(true/false)`. El constructor esta obsoleto. Usa `Boolean.valueOf()` o autoboxing.

> Asumir que `boolean` puede ser null. En bases de datos e APIs, muchas veces el campo es `Boolean` (wrapper) y puede ser null. Siempre valida antes de hacer unboxing.

> Usar `&` y `|` en vez de `&&` y `||` para booleanos. Los operadores bitwise no hacen cortocircuito (evaluan ambos operandos siempre), lo cual puede causar errores (ej: `if (obj != null & obj.esValido())` lanza NPE si obj es null).

> Omitir el prefijo `is` en getters booleanos. Aunque `getActivo()` compila correctamente, rompe la convencion y frameworks como Jackson, Spring Data, Hibernate, y los IDE pueden no reconocerlo como getter.

---

## Buenas Practicas

1. **Nombra getters booleanos con `is`**: `isActivo`, `isValid`, `isEmpty`. Nunca `getActivo`, `getValid`.
2. **Usa `boolean` (primitivo)** cuando el valor nunca sea null. Mas rapido y sin riesgo de NPE.
3. **Usa `Boolean` (wrapper)** cuando el valor puede ser null (datos de BD, campos opcionales).
4. **Verifica null de Boolean** con `Boolean.TRUE.equals()` o `Boolean.FALSE.equals()` antes de usarlo en `if`.
5. **Devuelve boolean desde metodos de verificacion**: `isValida()`, `puedeIniciar()`, `tienePermiso()`. La firma dice "esto se pregunta, no se obtiene".
6. **Encadena booleanos con metodos descriptivos**: `puedeIniciar()` es mas legible que `!estaProcesando && !estaPausado && !tieneError`.
7. **Usa `BooleanSupplier`** para condiciones lazy/perezosas que no quieres evaluar hasta que sean necesarias.
8. **Predicate<T>** para encapsular logica booleana compleja y reutilizable.
9. **En records accede directo**: `activo()` en vez de `isActivo()`, ya que records generan accessors con el nombre del campo.
10. **En JSON serializacion**: `Boolean` permite null, `boolean` no. Elige segun tu caso de uso (API con campos opcionales necesita Boolean).

---

## Conexiones

- [[04 - Variables y Literales]] - boolean literal true/false y tipos primitivos
- [[05 - Modificadores de Acceso]] - getters publicos y encapsulamiento del campo boolean private
- [[06 - Atributos y Campos]] - atributos boolean como estado del objeto
- [[07 - Constructores y this]] - inicializar campos boolean en el constructor
- [[10 - Metodos de Instancia]] - getters como metodos de instancia
- [[12 - Operador Ternario]] - ternario con booleanos para conversiones si/no
- [[13 - Static vs Instancia]] - static Boolean TRUE/FALSE como constantes singleton
- [[21 - Getters y Setters]] - convencion de getters y setters (incluyendo isXxx)
- [[23 - Metodos - Parametros, Retorno y Return]] - retornar boolean desde metodos
- [[33 - enum en Profundidad]] - booleanos como campos de enum para estados
- [[34 - Clases Wrapper (envolventes)]] - Boolean (wrapper) vs boolean (primitivo)

---

## Tags
`#java #fundamentos #boolean #getter #is-prefix #wrapper #predicate #encapsulamiento`