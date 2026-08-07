---
tags: [java, fundamentos, excepciones, try-catch, throwable, checked, unchecked, try-with-resources, errores]
---

# 40 - Manejo de Excepciones en Profundidad

Las excepciones son la forma en que Java maneja los errores en tiempo de ejecucion. Dominar la jerarquia Throwable, la diferencia entre checked y unchecked, y las herramientas modernas (try-with-resources, excepciones personalizadas) es esencial para escribir codigo robusto.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es una excepcion?

Una excepcion es un error que ocurre mientras el programa se ejecuta. En vez de que el programa se detenga de golpe, Java crea un objeto que describe el error y te da la oportunidad de manejarlo.

```java
public class EjemploError {
    public static void main(String[] args) {
        int[] numeros = {1, 2, 3};
        System.out.println(numeros[10]);  // ERROR: ArrayIndexOutOfBoundsException
        System.out.println("Fin");        // Esto NO se ejecuta
    }
}
```

### try-catch basico

```java
public class EjemploTryCatch {
    public static void main(String[] args) {
        try {
            // Codigo que puede fallar:
            int[] numeros = {1, 2, 3};
            System.out.println(numeros[10]);
        } catch (ArrayIndexOutOfBoundsException e) {
            // Codigo que se ejecuta SOLO si hay error:
            System.out.println("Indice fuera de rango!");
        }
        System.out.println("El programa sigue");  // Ahora si se ejecuta
    }
}
```

### Excepciones comunes que veras todos los dias

| Excepcion | Cuando ocurre | Ejemplo |
|-----------|---------------|---------|
| `ArithmeticException` | Dividir por cero | `10 / 0` |
| `ArrayIndexOutOfBoundsException` | Indice fuera del array | `arr[5]` con tamaño 3 |
| `NumberFormatException` | Convertir texto invalido a numero | `Integer.parseInt("hola")` |
| `NullPointerException` | Usar un objeto null | `String s = null; s.length();` |
| `InputMismatchException` | Scanner recibe tipo distinto | `nextInt()` y escribes "hola" |

### Como leer un stack trace

```java
// Cuando un programa falla, Java imprime algo como:
//
// Exception in thread "main" java.lang.NumberFormatException: For input string: "hola"
//     at Integer.parseInt(Integer.java:615)
//     at Ejemplo.main(Ejemplo.java:5)
//
// 1. El tipo de excepcion: NumberFormatException
// 2. El mensaje: For input string: "hola"
// 3. La pila de llamadas: donde ocurrio (la primera linea es donde fallo)
```

---

## NIVEL 2 - Mid

### La jerarquia de errores

```java
// Todo error en Java hereda de Throwable:
// 
// Throwable
//   ├── Error (problemas graves del sistema, NO los manejas)
//   │     ├── OutOfMemoryError
//   │     ├── StackOverflowError
//   │     └── ... 
//   └── Exception (problemas del programa, SI los manejas)
//         ├── RuntimeException (unchecked: no estan obligados a manejarse)
//         │     ├── NullPointerException
//         │     ├── IllegalArgumentException
//         │     └── ...
//         └── Otras (checked: el compilador OBLIGA a manejarlas)
//               ├── IOException
//               ├── SQLException
//               └── ...
```

### Checked vs unchecked (la diferencia mas importante)

```java
// UNCHECKED (RuntimeException): el compilador NO te obliga a manejarlas
// - Son errores de programacion o condiciones que el codigo deberia prevenir
int x = 10 / 0;  // compila sin try-catch (pero falla en runtime)

// CHECKED: el compilador OBLIGA a manejarlas o declararlas
import java.io.IOException;

public void leerArchivo() throws IOException {  // O declaras throws...
    throw new IOException("Archivo no encontrado");
}

public void llamar() {
    try {                                        // O manejas con try-catch
        leerArchivo();
    } catch (IOException e) {
        System.out.println("Error de archivo");
    }
}
```

### finally: codigo que siempre se ejecuta

```java
Scanner scanner = null;
try {
    scanner = new Scanner(System.in);
    int edad = scanner.nextInt();
} catch (InputMismatchException e) {
    System.out.println("Debes escribir un numero");
} finally {
    // Se ejecuta SIEMPRE: haya error o no
    if (scanner != null) {
        scanner.close();
    }
}
```

### Multiples catch y multi-catch

```java
// Varios catch separados:
try {
    int numero = Integer.parseInt(texto);
    int resultado = 100 / numero;
} catch (NumberFormatException e) {
    System.out.println("No es un numero");
} catch (ArithmeticException e) {
    System.out.println("No puedo dividir por cero");
}

// Multi-catch: varios tipos en un solo catch (Java 7+)
try {
    // ...
} catch (NumberFormatException | ArithmeticException e) {
    System.out.println("Error de numeros: " + e.getMessage());
}
```

### throws vs throw

```java
// throws: DECLARA que el metodo puede lanzar una excepcion (en la firma)
public void validar(int edad) throws IllegalArgumentException {
    // throw: LANZA la excepcion (en el cuerpo)
    if (edad < 0) {
        throw new IllegalArgumentException("La edad no puede ser negativa");
    }
}
```

---

## NIVEL 3 - Senior

### try-with-resources (Java 7+)

Cierra los recursos automaticamente al salir del bloque:

```java
// ANTES (Java 6 y anteriores):
BufferedReader lector = null;
try {
    lector = new BufferedReader(new FileReader("datos.txt"));
    String linea = lector.readLine();
} finally {
    if (lector != null) lector.close();  // manual, facil de olvidar
}

// AHORA (Java 7+): el recurso se cierra solo
try (var lector = new BufferedReader(new FileReader("datos.txt"))) {
    String linea = lector.readLine();
    System.out.println(linea);
} catch (IOException e) {
    System.out.println("Error al leer: " + e.getMessage());
}
// Sin finally manual: el try-with-resources llama a close() automaticamente
```

Reglas del try-with-resources:
- El recurso debe implementar `AutoCloseable` (o `Closeable`)
- Se cierra al salir del bloque, incluso si hay excepcion
- Se pueden abrir varios recursos: `try (var a = ...; var b = ...)`

### Excepciones personalizadas

```java
// 1. Define tu propia excepcion:
public class PedidoInvalidoException extends RuntimeException {
    public PedidoInvalidoException(String mensaje) {
        super(mensaje);
    }

    public PedidoInvalidoException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

// 2. Uso:
public void procesarPedido(Pedido pedido) {
    if (pedido.productos().isEmpty()) {
        throw new PedidoInvalidoException("El pedido no tiene productos");
    }
    if (pedido.total() <= 0) {
        throw new PedidoInvalidoException(
            "El pedido debe tener un total positivo",
            new ArithmeticException("Total calculado incorrectamente")
        );
    }
}
```

Reglas de oro para excepciones personalizadas:
- Extiende `RuntimeException` para errores de logica (unchecked)
- Extiende `Exception` si el llamador debe manejarla obligatoriamente (checked)
- Nombre que termine en `Exception`
- Constructores con mensaje y causa (chain de excepciones)

### La cadena de causas (exception chaining)

```java
try {
    conexion.consultar("SELECT * FROM usuarios");
} catch (SQLException e) {
    // Envuelves la causa original en tu excepcion:
    throw new ServicioException("Fallo la consulta de usuarios", e);
    // e queda disponible como e.getCause()
}
```

---

# METODO PROFUNDO (Curso completo de Manejo de Excepciones)

## 1. Anatomia completa de Throwable

### La clase raiz de todo error

```java
// Throwable es la clase raiz. Solo dos subclases directas:

public class Throwable {
    private String detailMessage;     // el mensaje
    private Throwable cause;          // la causa original
    private StackTraceElement[] stackTrace;  // la pila de llamadas

    public String getMessage() { ... }
    public Throwable getCause() { ... }
    public StackTraceElement[] getStackTrace() { ... }
    public void printStackTrace() { ... }
}

// Error: problemas que la JVM considera irrecuperables
// - OutOfMemoryError: no hay memoria
// - StackOverflowError: recursividad infinita
// - LinkageError: problemas de dependencias
// REGLA: no se capturan, no se lanzan, no se manejan

// Exception: problemas recuperables del programa
// - RuntimeException y subclases: unchecked
// - Resto de excepciones: checked
```

### Por que Error no se maneja

```java
try {
    // Algo que consume toda la memoria...
} catch (OutOfMemoryError e) {
    // MAL: aunque lo captures, el programa probablemente no puede continuar
    // La JVM no tiene memoria ni para ejecutar tu catch correctamente
}
// Los Error indican un problema de sistema, no de logica.
// Capturarlos no soluciona nada y puede empeorar las cosas.
```

---

## 2. Checked vs unchecked: el detalle completo

### La regla del compilador

| Tipo | Hereda de | El compilador obliga | Ejemplos |
|------|-----------|---------------------|----------|
| Checked | `Exception` (no RuntimeException) | SI: try-catch o throws | `IOException`, `SQLException`, `ClassNotFoundException` |
| Unchecked | `RuntimeException` | NO: puede propagar libremente | `NullPointerException`, `IllegalArgumentException`, `NumberFormatException` |

```java
// Checked: el compilador NO te deja ignorarlas
import java.io.IOException;

void metodoChecked() {
    throw new IOException();  // ERROR de compilacion: debe declarar o manejar
}

void metodoChecked() throws IOException {  // Solucion 1: declarar
    throw new IOException();
}

void metodoChecked() {                     // Solucion 2: manejar
    try {
        throw new IOException();
    } catch (IOException e) {
        // ...
    }
}

// Unchecked: compila sin ninguna obligacion
void metodoUnchecked() {
    throw new IllegalArgumentException();  // OK, sin declarar nada
}
```

### Por que existen las unchecked

Las RuntimeException representan errores de programacion que el codigo deberia prevenir ANTES:
- `NullPointerException`: deberias haber validado null
- `ArrayIndexOutOfBoundsException`: deberias haber comprobado length
- `NumberFormatException`: deberias haber validado el formato

Obligar a capturarlas en todas partes haria el codigo ilegible. Por eso son opcionales.

### La regla practica

```java
// Checked: el error esta FUERA de tu control (archivos, red, BD)
// - El compilador te recuerda que puede fallar
// - El llamador decide como manejar el fallo

// Unchecked: el error esta DENTRO de tu logica
// - Prevenir > capturar (valida antes de operar)
// - Lanzar IllegalArgumentException con mensaje claro cuando la entrada es invalida
```

---

## 3. try-catch-finally en profundidad

### Los tres bloques y sus reglas

```java
try {
    // 1. Se intenta ejecutar este bloque
} catch (TipoExcepcion e) {
    // 2. Solo si ocurre ese tipo de excepcion
} catch (OtroTipo e) {
    // 2b. Orden importa: los catch van de mas especifico a mas general
} finally {
    // 3. SIEMPRE se ejecuta (excepto si JVM termina)
}
```

### Reglas criticas

1. **El orden de los catch importa**: primero las excepciones mas especificas
```java
try { } 
catch (Exception e) { }          // MAL: si va primero, nunca llega a:
catch (NumberFormatException e) { }  // este (que es mas especifica)
// ERROR de compilacion: el catch de NumberFormatException es inalcanzable
```

2. **finally se ejecuta incluso con return**
```java
public int metodo() {
    try {
        return 1;              // se ejecuta el return...
    } finally {
        System.out.println("Siempre");  // ...pero finally corre antes de devolver
    }
}
```

3. **try sin catch es valido** (solo con finally o try-with-resources)
```java
try {
    operacion();
} finally {
    limpiar();  // asegura limpieza, deja que la excepcion propague
}
```

### Excepciones suprimidas (suppressed)

Cuando un try-with-resources lanza una excepcion y el close() lanza otra, la del close() se suprime:

```java
try (var recurso = new RecursoPeligroso()) {
    recurso.operar();  // lanza RuntimeException
    // y el close() tambien lanza... la segunda se SUPRIME
} catch (RuntimeException e) {
    for (Throwable suprimida : e.getSuppressed()) {
        System.out.println("Suprimida: " + suprimida);
    }
}
```

---

## 4. try-with-resources en profundidad

### Como funciona internamente

```java
// TU escribes:
try (var entrada = new BufferedReader(new FileReader("datos.txt"))) {
    return entrada.readLine();
}

// El compilador genera aproximadamente:
BufferedReader entrada = null;
Throwable principal = null;
try {
    entrada = new BufferedReader(new FileReader("datos.txt"));
    return entrada.readLine();
} catch (Throwable t) {
    principal = t;
    throw t;
} finally {
    if (entrada != null) {
        if (principal != null) {
            try {
                entrada.close();
            } catch (Throwable t) {
                principal.addSuppressed(t);  // las cercanas se suplementan
            }
        } else {
            entrada.close();
        }
    }
}
```

### Multiples recursos se cierran en orden inverso

```java
// Se cierran al reves: primero salida, luego entrada
try (
    var entrada = new BufferedReader(new FileReader("in.txt"));
    var salida = new BufferedWriter(new FileWriter("out.txt"))
) {
    // ...
}
```

### AutoCloseable vs Closeable

```java
// Closeable (java.io): solo throws IOException
// AutoCloseable (java.lang): throws Exception (mas general)

// Cualquier recurso tuyo puede ser AutoCloseable:
public class PoolConexiones implements AutoCloseable {
    @Override
    public void close() {
        System.out.println("Cerrando pool");
    }
}

try (var pool = new PoolConexiones()) {
    // uso el pool
}  // se llama close() automaticamente
```

---

## 5. throws vs throw vs lanzar correctamente

### throw: crear y lanzar

```java
throw new IllegalStateException("Configuracion incompleta");
// - Solo se lanza una excepcion por sentencia
// - El objeto debe ser Throwable (o subclase)
// - Despues de throw, el codigo siguiente del metodo no se ejecuta
```

### throws: declarar en la firma

```java
public void procesar() throws IOException, SQLException {
    // Declara todas las checked que puede lanzar
    // Los callers estan avisados
}
```

### Buenas practicas al lanzar

```java
// 1. Mensaje claro y accionable:
throw new IllegalArgumentException("El saldo no puede ser negativo: " + saldo);

// 2. Incluir el valor que causo el problema
throw new NumberFormatException("'abc' no es un numero valido");

// 3. No lanzar Exception generica (muy ambigua):
// throw new Exception("fallo");  // MAL
// Lanza el tipo mas especifico posible
```

### Lanzar la excepcion correcta segun el caso

| Situacion | Excepcion adecuada |
|-----------|-------------------|
| Argumento invalido | `IllegalArgumentException` |
| Estado invalido del objeto | `IllegalStateException` |
| Valor null inesperado | `NullPointerException` (o validar antes) |
| Indice fuera de rango | `IndexOutOfBoundsException` |
| Operacion no soportada | `UnsupportedOperationException` |
| Numero no valido | `NumberFormatException` |
| Recursos externos fallan | `IOException`, `SQLException`, etc. |
| Tu dominio falla | Tu excepcion personalizada |

---

## 6. Excepciones personalizadas en profundidad

### Estructura completa

```java
public class PagosException extends RuntimeException {

    public PagosException(String mensaje) {
        super(mensaje);
    }

    public PagosException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }

    // Puedes añadir campos de contexto:
    private String codigoOperacion;

    public PagosException(String mensaje, String codigoOperacion) {
        super(mensaje);
        this.codigoOperacion = codigoOperacion;
    }

    public String codigoOperacion() {
        return codigoOperacion;
    }
}
```

### Cuando crear una excepcion personalizada

- Cuando el manejo del error depende de tu dominio (no de Java)
- Cuando necesitas campos extra (codigo, operacion, usuario)
- Cuando los callers deben capturarla de forma especifica
- Cuando quieres una API limpia: `catch (PagosException e)`

### Cuando NO crearla

- Si una excepcion estandar ya describe el problema (`IllegalArgumentException`)
- Si nunca la capturas de forma especifica (un catch generico basta)

---

## 7. Propagacion y stack trace

### Como se propaga una excepcion

```java
public void a() { b(); }     // a llama a b
public void b() { c(); }     // b llama a c
public void c() {
    throw new RuntimeException("Fallo en c");
}

// El stack trace muestra el camino:
// RuntimeException: Fallo en c
//     at Clase.c(Clase.java:10)
//     at Clase.b(Clase.java:6)
//     at Clase.a(Clase.java:2)
//     at Clase.main(Clase.java:14)
```

### La regla de la propagacion

- Si un metodo no captura una excepcion, esta se propaga al llamador
- Si nadie la captura, llega al `main` y el programa termina
- Captura la excepcion **en el nivel donde puedes hacer algo util**

```java
// MAL: capturar donde no puedes hacer nada util
public void interfaz() {
    try {
        logicaNegocio();  // aqui solo puedes mostrar el error
    } catch (SQLException e) {
        // no puedes reintentar ni recuperarte aqui
    }
}

// BIEN: dejar propagar y capturar en el nivel apropiado
public void interfaz() {
    try {
        logicaNegocio();
    } catch (SQLException e) {
        mostrarMensajeAlUsuario("No se pudo guardar, intenta de nuevo");
        registrarError(e);
    }
}
```

### El anti-patron: tragar excepciones

```java
try {
    operacionRiesgosa();
} catch (Exception e) {
    // MAL: la excepcion desaparece sin rastro
    // El programa parece que funciona, pero fallo silenciosamente
}

// Si de verdad no puedes hacer nada, al menos registra:
} catch (Exception e) {
    Logger.log(e);  // minimo: deja rastro
}
```

---

## 8. Excepciones y Java moderno (Optional, streams)

### Optional como alternativa para "ausencia" (no para errores)

```java
// Cuando algo puede no existir (NO es un error): usa Optional
public Optional<Usuario> buscarPorId(long id) {
    return repositorio.findById(id);  // vacio si no existe
}

// Cuando algo FALLA (es un error): usa excepciones
public Usuario buscarPorIdOExigido(long id) {
    return repositorio.findById(id)
        .orElseThrow(() -> new UsuarioNoEncontradoException(id));
    // lanza excepcion cuando el fallo es un error real
}
```

### orElseThrow: el puente moderno

```java
Optional<String> config = leerConfiguracion("db.url");

String url = config.orElseThrow(
    () -> new ConfiguracionInvalidaException("Falta la propiedad db.url")
);

// Y si quieres la excepcion exacta por defecto:
String valor = config.orElseThrow();  // lanza NoSuchElementException
```

### Excepciones dentro de streams

```java
// Los streams no pueden lanzar checked exceptions directamente:
List<String> textos = List.of("1", "2", "x");

// MAL: parseInt no compila dentro del map sin manejo
// textos.stream().map(Integer::parseInt)  // OK si es unchecked

// Para checked exceptions dentro de streams, necesitas envolverlas:
public int convertir(String s) {
    try {
        return Integer.parseInt(s);
    } catch (NumberFormatException e) {
        throw new ConversionException("No se puede convertir: " + s, e);
    }
}

textos.stream().map(this::convertir).toList();
```

---

## 9. Anti-patrones y malas practicas

| Anti-patron | Problema | Solucion |
|-------------|----------|----------|
| Capturar `Exception` generico | Oculta errores inesperados | Captura tipos especificos |
| Capturar y tragar | Fallos silenciosos | Al menos registrar |
| Capturar y no hacer nada | Igual que arriba | Re-lanzar o manejar |
| Usar excepciones para control de flujo | Lento y confuso | if/else primero |
| Lanzar `Exception` generico | El llamador no sabe que fallo | Tipos especificos |
| `printStackTrace()` en produccion | Sin log estructurado | Usar un logger |
| Multi-catch de tipos no relacionados | Confunde el manejo | Catch separados |

```java
// MAL: excepciones como control de flujo
try {
    int n = Integer.parseInt(texto);
} catch (NumberFormatException e) {
    return 0;  // era una validacion, no un error
}

// BIEN: validar antes
int n = texto.matches("\\d+") ? Integer.parseInt(texto) : 0;
```

---

## 10. Buenas practicas definitivas

1. **Prevenir antes que capturar**: valida null, rangos y formatos ANTES de operar.
2. **Captura lo especifico**: nunca `catch (Exception)` como primera opcion.
3. **No tragues excepciones**: si no puedes manejarla, dejala propagar o registrala.
4. **Usa try-with-resources** para cualquier recurso (archivos, conexiones, scanners).
5. **Crea excepciones de dominio** para errores de negocio con contexto.
6. **Mensajes accionables**: di que fallo y que valor lo causo.
7. **Conserva la causa** con `new MiExcepcion(msj, causa)` — nunca pierdas el stack trace original.
8. **No uses excepciones para control de flujo**: if/else es mas rapido y claro.
9. **Checked solo cuando el llamador DEBE manejar** el error (IO, red, BD).
10. **Unchecked para errores de programacion**: IllegalArgumentException, IllegalStateException, etc.

---

## Errores Comunes

> Capturar `Exception` (o peor, `Throwable`) en vez de la excepcion especifica. Oculta errores inesperados y hace el codigo dificil de mantener.

> Tragar excepciones: `catch (Exception e) { }` vacio. El programa falla silenciosamente y nadie sabe por que.

> Usar try-catch donde un if hubiera bastado (ej: validar formato con regex antes de parseInt).

> Olvidar cerrar recursos (Scanner, archivos, conexiones). Usa try-with-resources siempre.

> Lanzar `Exception` generico: `throw new Exception("fallo")`. Los llamadores no pueden manejarla de forma especifica.

> Poner el catch generico antes del especifico (orden incorrecto = error de compilacion).

> Usar printStackTrace() en produccion. Usa un sistema de logs.

> Capturar una excepcion y no propagar la causa: pierdes el stack trace original y el diagnostico.

---

## Conexiones

- [[02 - Punto de Entrada main]] - Capturar excepciones en main para dar mensajes amigables
- [[03 - Tipos Primitivos y Referencia]] - NumberFormatException al convertir String a numero
- [[08 - Instanciacion y new]] - IllegalArgumentException al validar en constructores
- [[17 - Flujo de Ejecucion JVM]] - StackOverflowError y el flujo de errores en la JVM
- [[20 - Javadoc y Documentacion]] - Documentar @throws en los metodos
- [[21 - Getters y Setters]] - Lanzar excepciones en setters para estado invalido
- [[26 - Private en Profundidad]] - Validar en constructores con excepciones
- [[27 - Singleton en Profundidad]] - IllegalStateException cuando no esta inicializado
- [[29 - Scanner en Profundidad]] - InputMismatchException y manejo de errores con archivos
- [[30 - Principio fundamental de Java (y POO)]] - El papel de los errores en el diseno
- [[39 - Tipos y Wrappers - La Guia Definitiva]] - Wrappers y null (NullPointerException)

---

## Tags
`#java #fundamentos #excepciones #try-catch #throwable #checked #unchecked #try-with-resources #errores #robustez`