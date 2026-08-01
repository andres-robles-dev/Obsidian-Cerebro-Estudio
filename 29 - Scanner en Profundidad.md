---
tags: [java, fundamentos, scanner, entrada, consola, archivos, parseo]
---

# 29 - Scanner en Profundidad

`Scanner` es una clase de Java que permite leer datos de entrada (teclado, archivos, Strings) y separarlos en partes (tokens) para procesarlos facilmente.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es Scanner?

`Scanner` sirve para leer datos que el usuario escribe en la consola. Piensa en el Scanner como un "lector" que espera a que escribas algo y lo convierte al tipo que necesitas.

### Importar Scanner

```java
import java.util.Scanner;
```

### Leer texto del teclado

```java
public class Saludo {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.print("Como te llamas? ");
        String nombre = entrada.nextLine();

        System.out.println("Hola " + nombre + "!");

        entrada.close();
    }
}
```

Cuando ejecutas el programa, se queda esperando a que escribas algo. Escribes "Ana" y presionas Enter. El programa imprime "Hola Ana!".

### Leer numeros

```java
public class Suma {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.print("Escribe el primer numero: ");
        int a = entrada.nextInt();

        System.out.print("Escribe el segundo numero: ");
        int b = entrada.nextInt();

        int suma = a + b;
        System.out.println("La suma es: " + suma);

        entrada.close();
    }
}
```

### Leer diferentes tipos

```java
public class DatosPersona {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.print("Nombre: ");
        String nombre = entrada.nextLine();

        System.out.print("Edad: ");
        int edad = entrada.nextInt();

        System.out.print("Altura (ej: 1.75): ");
        double altura = entrada.nextDouble();

        System.out.print("Es estudiante? (true/false): ");
        boolean estudiante = entrada.nextBoolean();

        System.out.println(nombre + " tiene " + edad + " anios, mide " + altura + "m");
        System.out.println("Estudiante: " + estudiante);

        entrada.close();
    }
}
```

### La regla basica

- `nextLine()` lee una linea entera de texto
- `nextInt()` lee un numero entero
- `nextDouble()` lee un numero decimal
- `nextBoolean()` lee true o false

---

## NIVEL 2 - Mid

### Todos los metodos next

```java
public class DemoTipos {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.print("Palabra (sin espacios): ");
        String palabra = entrada.next();          // Solo una palabra (hasta espacio)

        System.out.print("Linea completa: ");
        entrada.nextLine();  // Consumir el salto pendiente
        String linea = entrada.nextLine();        // Toda la linea

        System.out.print("Numero entero: ");
        int entero = entrada.nextInt();

        System.out.print("Numero decimal: ");
        double decimal = entrada.nextDouble();

        System.out.print("Numero largo: ");
        long largo = entrada.nextLong();

        System.out.print("Numero pequeno: ");
        byte pequeno = entrada.nextByte();

        System.out.print("Numero corto: ");
        short corto = entrada.nextShort();

        System.out.print("Numero con decimales simple: ");
        float flotante = entrada.nextFloat();

        System.out.print("Verdadero o falso: ");
        boolean bool = entrada.nextBoolean();

        entrada.close();
    }
}
```

### hasNext: validar antes de leer

Los metodos `hasNext` te permiten comprobar si hay algo que leer sin lanzar error:

```java
public class DemoHasNext {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.print("Escribe un numero entero: ");

        if (entrada.hasNextInt()) {
            int numero = entrada.nextInt();
            System.out.println("Escribiste el numero: " + numero);
        } else {
            System.out.println("Eso no es un numero entero!");
            String basura = entrada.nextLine();  // Limpiar la entrada erronea
        }

        entrada.close();
    }
}
```

### Los metodos hasNext disponibles

```java
entrada.hasNext();          // Hay algo (cualquier token)?
entrada.hasNextLine();      // Hay otra linea?
entrada.hasNextInt();       // Hay un entero para leer?
entrada.hasNextDouble();    // Hay un decimal para leer?
entrada.hasNextBoolean();   // Hay true/false?
entrada.hasNextLong();      // Hay un long?
entrada.hasNextFloat();     // Hay un float?
entrada.hasNextByte();      // Hay un byte?
entrada.hasNextShort();     // Hay un short?
entrada.hasNextBigInteger(); // Hay un BigInteger?
entrada.hasNextBigDecimal(); // Hay un BigDecimal?
```

### El problema del salto de linea

Este es el error mas comun con Scanner. Pasa cuando mezclas `nextInt()` (o `nextDouble()`, `next()`) con `nextLine()`:

```java
System.out.print("Edad: ");
int edad = entrada.nextInt();   // Lees 25, pero el Enter (\n) se queda

System.out.print("Nombre: ");
String nombre = entrada.nextLine();  // Lees el \n que sobro! No te deja escribir
```

**Por que pasa?**
- `nextInt()` lee el numero pero NO consume el salto de linea (`\n`)
- `nextLine()` lee hasta el siguiente salto de linea y se encuentra con el `\n` que sobro

**Solucion 1: consumir el salto con nextLine()**

```java
System.out.print("Edad: ");
int edad = entrada.nextInt();
entrada.nextLine();  // Consumir el salto de linea pendiente

System.out.print("Nombre: ");
String nombre = entrada.nextLine();  // Ahora si funciona
```

**Solucion 2: leer todo como texto y convertir**

```java
System.out.print("Edad: ");
String edadTexto = entrada.nextLine();
int edad = Integer.parseInt(edadTexto);

System.out.print("Nombre: ");
String nombre = entrada.nextLine();
```

**Solucion 3: usar nextLine() siempre y convertir segun necesites**

```java
System.out.print("Edad: ");
int edad = Integer.parseInt(entrada.nextLine());

System.out.print("Altura: ");
double altura = Double.parseDouble(entrada.nextLine());

System.out.print("Nombre: ");
String nombre = entrada.nextLine();
```

Esta ultima es la mas recomendada porque evita el problema del salto de linea por completo.

### Leer desde un archivo

```java
import java.util.Scanner;
import java.nio.file.Path;

public class LeerArchivo {
    public static void main(String[] args) throws Exception {
        var entrada = new Scanner(Path.of("datos.txt"));

        while (entrada.hasNextLine()) {
            String linea = entrada.nextLine();
            System.out.println(linea);
        }

        entrada.close();
    }
}
```

### Leer desde un String

```java
public class ParsearTexto {
    public static void main(String[] args) {
        String datos = "Ana 25 1.75 true\nLuis 30 1.80 false";
        var entrada = new Scanner(datos);

        while (entrada.hasNextLine()) {
            String linea = entrada.nextLine();
            var lineaScan = new Scanner(linea);

            String nombre = lineaScan.next();
            int edad = lineaScan.nextInt();
            double altura = lineaScan.nextDouble();
            boolean activo = lineaScan.nextBoolean();

            System.out.println(nombre + " " + edad + " " + altura + " " + activo);
            lineaScan.close();
        }

        entrada.close();
    }
}
```

---

## NIVEL 3 - Senior

### Delimitadores personalizados

Por defecto, Scanner separa por espacios, saltos de linea y tabulaciones. Puedes cambiar el delimitador:

```java
// Datos separados por comas: "Ana,25,1.75"
var datos = new Scanner("Ana,25,1.75\nLuis,30,1.80");
datos.useDelimiter("[,\\n]");  // Delimitador: coma o salto de linea

while (datos.hasNext()) {
    System.out.println(datos.next());
}
// Imprime: Ana, 25, 1.75, Luis, 30, 1.80

// Otro ejemplo: archivo CSV
var csv = new Scanner(Path.of("productos.csv"));
csv.useDelimiter(";|\\n");

// Leer campos separados por punto y coma
while (csv.hasNext()) {
    String campo = csv.next();
    System.out.println("Campo: " + campo);
}
```

### Locale para decimales

En algunos paises los decimales se escriben con coma (`,`) en vez de punto (`.`):

```java
import java.util.Locale;

// Configurar Scanner para leer decimales con coma (formato europeo)
var entrada = new Scanner(System.in);
entrada.useLocale(Locale.of("es", "ES"));  // "es" para espanol

System.out.print("Escribe un decimal (ej: 3,14): ");
double valor = entrada.nextDouble();  // Lee "3,14" como 3.14
System.out.println("Valor: " + valor);
```

```java
// Si el archivo usa punto, configurar para EEUU
var archivo = new Scanner(Path.of("datos.txt"));
archivo.useLocale(Locale.US);  // Decimales con punto
```

### findInLine, findWithinHorizon, skip

Buscar patrones dentro de una linea:

```java
var entrada = new Scanner("Error: archivo no encontrado (codigo 404)");

// findInLine: busca un patron dentro de la linea actual
String codigo = entrada.findInLine("\\d+");  // Encuentra "404"
System.out.println("Codigo: " + codigo);

// findWithinHorizon: busca dentro de un limite de caracteres
var entrada2 = new Scanner("ID: A-123, Precio: 45.99 euros");
String id = entrada2.findWithinHorizon("A-\\d+", 20);  // Busca en los primeros 20 caracteres
System.out.println("ID encontrado: " + id);

// skip: saltarse una parte que no te interesa
var entrada3 = new Scanner("Nombre: Ana");
entrada3.skip("Nombre: ");  // Saltarse "Nombre: "
String nombre = entrada3.nextLine();
System.out.println(nombre);  // "Ana"
```

### Scanner con Pattern

```java
import java.util.Scanner;
import java.util.regex.Pattern;

public class DemoPattern {
    public static void main(String[] args) {
        // Extraer correos electronicos de un texto
        var entrada = new Scanner("Contactos: ana@mail.com, luis@mail.com");
        entrada.useDelimiter("[,\\s]+");

        var patronEmail = Pattern.compile("[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}");

        while (entrada.hasNext()) {
            String token = entrada.next();
            if (patronEmail.matcher(token).matches()) {
                System.out.println("Email encontrado: " + token);
            }
        }

        entrada.close();
    }
}
```

### useRadix: cambiar base numerica

```java
var entrada = new Scanner("FF 1010 777");
entrada.useRadix(16);  // Leer numeros en hexadecimal

while (entrada.hasNextInt()) {
    int numero = entrada.nextInt();
    System.out.println(numero);  // 255, 4112, 1911 (en decimal)
}
```

### reset: volver a configuracion por defecto

```java
var entrada = new Scanner("3,14  FF");
entrada.useLocale(Locale.of("es", "ES"));
entrada.useRadix(16);

// ... leer algunos valores ...

entrada.reset();  // Vuelve a Locale.US, radix 10, delimitador por defecto
```

### try-with-resources con Scanner

```java
public class DemoTryResources {
    public static void main(String[] args) {
        // Se cierra automaticamente al salir del try
        try (var entrada = new Scanner(Path.of("datos.txt"))) {
            while (entrada.hasNextLine()) {
                String linea = entrada.nextLine();
                System.out.println(linea);
            }
        } catch (IOException e) {
            System.out.println("Error leyendo archivo: " + e.getMessage());
        }
    }
}
```

---

# METODO PROFUNDO (Curso completo de Scanner)

## 1. Que es Scanner exactamente?

`Scanner` es una clase de Java que pertenece a `java.util.Scanner`. Se introdujo en Java 5 para facilitar la lectura y parseo de datos.

`Scanner` divide la entrada en **tokens** (partes) usando un **delimitador** (por defecto: espacios, saltos de linea, tabulaciones). Luego puedes leer cada token como String, int, double, etc.

### Como funciona internamente

```
Entrada: "Hola 42 3.14 true"
         |     |  |    |
Tokens:  Hola  42 3.14 true
         next  nextInt nextDouble nextBoolean
```

Scanner va leyendo token por token. Cada vez que llamas a `nextInt()`, avanza al siguiente token y lo convierte a entero.

### Donde puede leer

```java
new Scanner(System.in);          // Teclado
new Scanner(Path.of("archivo.txt"));  // Archivo
new Scanner(new File("datos.txt"));   // Archivo (forma antigua)
new Scanner("texto fijo");       // String
new Scanner(new StringReader("..."));  // Reader
new Scanner(InputStream)         // Cualquier InputStream
```

---

## 2. Scanner con System.in (teclado)

Este es el uso mas comun: leer lo que el usuario escribe en la consola.

```java
var entrada = new Scanner(System.in);
```

`System.in` es el flujo de entrada estandar. Por defecto, lo que escribes en la terminal.

### Ejemplo completo: registro de usuario

```java
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class RegistroUsuario {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.println("=== Registro de Usuario ===");

        System.out.print("Nombre completo: ");
        String nombre = entrada.nextLine();

        System.out.print("Email: ");
        String email = entrada.nextLine();

        System.out.print("Fecha de nacimiento (dd/MM/aaaa): ");
        String fechaTexto = entrada.nextLine();
        LocalDate fechaNacimiento = LocalDate.parse(fechaTexto,
            DateTimeFormatter.ofPattern("dd/MM/yyyy"));

        System.out.print("Altura en metros (ej: 1.75): ");
        double altura = Double.parseDouble(entrada.nextLine());

        System.out.print("Peso en kg (ej: 70.5): ");
        double peso = Double.parseDouble(entrada.nextLine());

        System.out.print("Acepta terminos? (si/no): ");
        boolean acepta = entrada.nextLine().equalsIgnoreCase("si");

        System.out.println("\nRegistro completado:");
        System.out.println(nombre + " - " + email);
        System.out.println("Nacido: " + fechaNacimiento);
        System.out.println("IMC: " + String.format("%.1f", peso / (altura * altura)));
        System.out.println("Terminos aceptados: " + (acepta ? "Si" : "No"));

        entrada.close();
    }
}
```

---

## 3. Todos los metodos next (lectura de tokens)

```java
public class DemoTodosLosNext {
    public static void main(String[] args) {
        var datos = new Scanner("Hola 42 3.14 true 1234567890 100 2000");

        String texto = datos.next();          // "Hola" (hasta espacio)
        int entero = datos.nextInt();          // 42
        double decimal = datos.nextDouble();   // 3.14
        boolean booleano = datos.nextBoolean();// true
        long largo = datos.nextLong();         // 1234567890
        byte b = datos.nextByte();             // 100
        short s = datos.nextShort();           // 2000

        // next() solo lee una palabra (hasta espacio o salto de linea)
        // nextLine() lee toda la linea hasta el Enter

        datos.close();
    }
}
```

### next() vs nextLine()

```java
var entrada = new Scanner("Hola mundo\nJava es genial");

String palabra = entrada.next();     // "Hola" (solo hasta espacio)
String otra = entrada.next();        // "mundo" (siguiente token)
String linea = entrada.nextLine();   // "" (linea vacia, el \n se consumio)

// Mejor: usa nextLine() para lineas completas
```

### nextBigInteger y nextBigDecimal

Para numeros muy grandes o muy precisos:

```java
import java.math.BigInteger;
import java.math.BigDecimal;

var entrada = new Scanner("12345678901234567890 999.9999999999");

BigInteger grande = entrada.nextBigInteger();
BigDecimal preciso = entrada.nextBigDecimal();

entrada.close();
```

---

## 4. Metodos hasNext (validacion)

Los metodos `hasNext` te permiten preguntar "hay algo que pueda leer como X?" antes de intentar leerlo. Esto evita errores.

### Ejemplo: leer hasta que no haya mas

```java
public class SumarNumeros {
    public static void main(String[] args) {
        System.out.println("Escribe numeros separados por espacio (Enter + Ctrl+D para terminar):");
        var entrada = new Scanner(System.in);
        int suma = 0;

        while (entrada.hasNextInt()) {
            suma += entrada.nextInt();
        }

        System.out.println("Suma total: " + suma);
        entrada.close();
    }
}
```

### Ejemplo: validar entrada del usuario

```java
public class PedirEdad {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);
        int edad;

        while (true) {
            System.out.print("Cuantos anios tienes? ");

            if (entrada.hasNextInt()) {
                edad = entrada.nextInt();
                if (edad >= 0 && edad <= 150) {
                    break;
                } else {
                    System.out.println("Edad no valida (0-150)");
                }
            } else {
                System.out.println("Eso no es un numero");
                entrada.next();  // Descartar el token invalido
            }
        }

        System.out.println("Tienes " + edad + " anios");
        entrada.close();
    }
}
```

### Tabla de validacion

| Metodo | Pregunta |
|--------|----------|
| `hasNext()` | Hay algun token disponible? |
| `hasNextLine()` | Hay otra linea? |
| `hasNextInt()` | El siguiente token se puede leer como int? |
| `hasNextDouble()` | El siguiente token se puede leer como double? |
| `hasNextBoolean()` | El siguiente token es true o false? |
| `hasNextLong()` | El siguiente token se puede leer como long? |
| `hasNextByte()` | El siguiente token se puede leer como byte? |
| `hasNextShort()` | El siguiente token se puede leer como short? |
| `hasNextFloat()` | El siguiente token se puede leer como float? |
| `hasNextBigInteger()` | El siguiente token se puede leer como BigInteger? |
| `hasNextBigDecimal()` | El siguiente token se puede leer como BigDecimal? |
| `hasNext(Pattern)` | El siguiente token coincide con un patron? |
| `hasNext(String)` | El siguiente token coincide con una expresion regular? |

---

## 5. El problema del salto de linea (explicado a fondo)

Este es el error mas clasico con Scanner. Vamos a entender por que ocurre y las formas de evitarlo.

### Por que ocurre?

Cuando escribes "25" y presionas Enter, en realidad envias dos cosas:

```
25\n
```

`nextInt()` lee el "25" pero NO consume el `\n`. Ese `\n` se queda esperando. Cuando luego llamas a `nextLine()`, esta lee inmediatamente el `\n` que quedo, sin darte oportunidad de escribir nada.

### Visualizacion

```java
var entrada = new Scanner(System.in);

System.out.print("Edad: ");
int edad = entrada.nextInt();
// Buffer despues de nextInt(): "\n"

System.out.print("Nombre: ");
String nombre = entrada.nextLine();
// nextLine() encuentra "\n" y devuelve "" inmediatamente
```

### Solucion recomendada: siempre nextLine()

La forma mas limpia es usar SIEMPRE `nextLine()` y convertir manualmente:

```java
public class FormaLimpia {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        System.out.print("Edad: ");
        int edad = Integer.parseInt(entrada.nextLine());

        System.out.print("Nombre: ");
        String nombre = entrada.nextLine();

        System.out.print("Altura: ");
        double altura = Double.parseDouble(entrada.nextLine());

        System.out.print("Activo: ");
        boolean activo = Boolean.parseBoolean(entrada.nextLine());

        System.out.println(nombre + " " + edad + " " + altura + " " + activo);
        entrada.close();
    }
}
```

Ventajas:
- No hay problemas de saltos de linea
- Un solo metodo (`nextLine()`) para todo
- Controlas la conversion con try-catch para errores

### Manejo de errores con nextLine

```java
public class PedirNumeroSeguro {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);
        int numero = 0;
        boolean valido = false;

        while (!valido) {
            System.out.print("Escribe un numero entero: ");
            String texto = entrada.nextLine();

            try {
                numero = Integer.parseInt(texto);
                valido = true;
            } catch (NumberFormatException e) {
                System.out.println("'" + texto + "' no es un numero valido");
            }
        }

        System.out.println("Escribiste: " + numero);
        entrada.close();
    }
}
```

---

## 6. Delimitadores personalizados

El delimitador determina como se separan los tokens.

### Delimitador por defecto

```
Espacios, saltos de linea, tabulaciones, retornos de carro
\p{javaWhitespace}+
```

### Cambiar delimitador

```java
var entrada = new Scanner("ana;25;1.75;true");
entrada.useDelimiter(";");
// Tokens: ["ana", "25", "1.75", "true"]

String nombre = entrada.next();      // "ana"
int edad = entrada.nextInt();        // 25
double altura = entrada.nextDouble();// 1.75
boolean activo = entrada.nextBoolean(); // true
```

### Leer archivo CSV con delimitador

```java
public class LeerCSV {
    public static void main(String[] args) throws Exception {
        var entrada = new Scanner(Path.of("empleados.csv"));
        entrada.useDelimiter("[;\\n]");  // Separado por ; o por saltos de linea

        int contador = 0;
        while (entrada.hasNext()) {
            String campo = entrada.next().trim();
            System.out.print(campo + "\t");
            contador++;
            if (contador % 3 == 0) {
                System.out.println();  // Cada 3 campos, nueva linea
            }
        }

        entrada.close();
    }
}
```

### Delimitador con Pattern

```java
import java.util.regex.Pattern;

var entrada = new Scanner("Ana:25, Luis:30, Carlos:28");
entrada.useDelimiter(Pattern.compile(",\\s*"));  // Coma seguida de espacios

while (entrada.hasNext()) {
    System.out.println(entrada.next());
}
// Imprime:
// Ana:25
// Luis:30
// Carlos:28
```

---

## 7. Scanner con archivos

### Leer archivo linea por linea

```java
import java.util.Scanner;
import java.nio.file.Path;

public class LeerArchivoLineas {
    public static void main(String[] args) throws Exception {
        var entrada = new Scanner(Path.of("datos.txt"));

        int numeroLinea = 1;
        while (entrada.hasNextLine()) {
            String linea = entrada.nextLine();
            System.out.println(numeroLinea + ": " + linea);
            numeroLinea++;
        }

        entrada.close();
    }
}
```

### Leer datos estructurados de un archivo

```java
// Archivo: alumnos.txt
// Ana,25,8.5
// Luis,22,7.0
// Carlos,24,9.2

public class LeerAlumnos {
    public static void main(String[] args) throws Exception {
        var entrada = new Scanner(Path.of("alumnos.txt"));

        while (entrada.hasNextLine()) {
            String linea = entrada.nextLine();
            var lineaScan = new Scanner(linea);
            lineaScan.useDelimiter(",");

            String nombre = lineaScan.next().trim();
            int edad = lineaScan.nextInt();
            double nota = lineaScan.nextDouble();

            System.out.println(nombre + " - " + edad + " anios - Nota: " + nota);
            lineaScan.close();
        }

        entrada.close();
    }
}
```

### Manejo de excepciones con archivos

```java
public class LeerArchivoSeguro {
    public static void main(String[] args) {
        var ruta = Path.of("datos.txt");

        if (!ruta.toFile().exists()) {
            System.out.println("El archivo no existe: " + ruta);
            return;
        }

        try (var entrada = new Scanner(ruta)) {
            while (entrada.hasNextLine()) {
                System.out.println(entrada.nextLine());
            }
        } catch (IOException e) {
            System.out.println("Error al leer el archivo: " + e.getMessage());
        }
    }
}
```

---

## 8. Scanner con Strings

Parsear datos desde un texto en vez de desde teclado o archivo:

```java
public class ParsearDatos {
    public static void main(String[] args) {
        // Datos de un formulario web
        String formulario = """
            nombre=Ana Martinez
            email=ana@email.com
            edad=25
            activo=true
            """;

        var entrada = new Scanner(formulario);

        while (entrada.hasNextLine()) {
            String linea = entrada.nextLine();
            if (linea.contains("=")) {
                var lineaScan = new Scanner(linea);
                lineaScan.useDelimiter("=");

                String clave = lineaScan.next().trim();
                String valor = lineaScan.next().trim();

                System.out.println(clave + " -> " + valor);
                lineaScan.close();
            }
        }

        entrada.close();
    }
}
```

---

## 9. Locale y formato de numeros

### Problema con decimales

En espanol se escribe `3,14` (con coma). En ingles se escribe `3.14` (con punto). Scanner usa el formato ingles por defecto.

```java
// Esto falla si el usuario escribe con coma:
var entrada = new Scanner(System.in);
double valor = entrada.nextDouble();  // Usuario escribe "3,14" -> ERROR!
```

### Solucion con useLocale

```java
import java.util.Locale;

public class DemoLocale {
    public static void main(String[] args) {
        var entrada = new Scanner(System.in);

        // Para espanol (decimal con coma)
        entrada.useLocale(Locale.of("es", "ES"));

        System.out.print("Escribe un decimal (ej: 3,14): ");
        double valor = entrada.nextDouble();
        System.out.println("Valor: " + valor);  // 3.14

        // Para archivos con punto (formato internacional)
        var archivo = new Scanner(Path.of("datos.txt"));
        archivo.useLocale(Locale.US);
    }
}
```

### Locales comunes

```java
entrada.useLocale(Locale.US);                  // Decimal: 3.14
entrada.useLocale(Locale.of("es", "ES"));      // Decimal: 3,14
entrada.useLocale(Locale.GERMANY);             // Decimal: 3,14
entrada.useLocale(Locale.FRANCE);              // Decimal: 3,14
entrada.useLocale(Locale.UK);                  // Decimal: 3.14
```

---

## 10. Scanner y Pattern (busqueda avanzada)

### findInLine

Busca un patron dentro de la linea actual (sin consumir toda la linea):

```java
var entrada = new Scanner("Error: archivo 'datos.txt' no encontrado");

// Buscar el nombre del archivo entre comillas
String archivo = entrada.findInLine("'[^']+'");
System.out.println("Archivo: " + archivo);  // 'datos.txt'

// Buscar palabra despues de "Error:"
entrada.reset();  // Vuelve al inicio (simulado)
```

### findWithinHorizon

Busca un patron dentro de un limite de caracteres:

```java
var entrada = new Scanner("ID: USR-001, Nombre: Ana, Edad: 25");

// Buscar solo en los primeros 15 caracteres
String id = entrada.findWithinHorizon("[A-Z]+-\\d+", 15);
System.out.println("ID: " + id);  // "USR-001"
```

### skip

Saltarse partes que no te interesan:

```java
var entrada = new Scanner("Usuario: ana@mail.com | Rol: admin");

entrada.skip("Usuario: ");     // Saltar
String email = entrada.next(); // "ana@mail.com"
entrada.skip("\\| Rol: ");    // Saltar
String rol = entrada.next();   // "admin"

System.out.println("Email: " + email + ", Rol: " + rol);
```

### match

Obtener informacion sobre el ultimo token leido:

```java
var entrada = new Scanner("123 ABC 456");

int numero = entrada.nextInt();
var resultado = entrada.match();  // Informacion del ultimo match
if (resultado != null) {
    System.out.println("Token: " + resultado.group());
    System.out.println("Inicio: " + resultado.start());
    System.out.println("Fin: " + resultado.end());
}
```

---

## 11. Cierre de Scanner (close)

### Por que cerrar Scanner?

`Scanner` que lee de un archivo o de un recurso debe cerrarse para liberar memoria y recursos del sistema.

```java
var entrada = new Scanner(Path.of("datos.txt"));
// ... leer datos ...
entrada.close();  // Libera el archivo
```

### try-with-resources (Java 7+)

La forma mas segura de cerrar recursos:

```java
try (var entrada = new Scanner(Path.of("datos.txt"))) {
    while (entrada.hasNextLine()) {
        System.out.println(entrada.nextLine());
    }
} catch (IOException e) {
    System.out.println("Error: " + e.getMessage());
}
// Se cierra automaticamente al salir del try
```

### NO cerrar System.in

Cuando lees de teclado (`System.in`), NO debes cerrar el Scanner si tu programa sigue funcionando:

```java
var entrada = new Scanner(System.in);
// ... leer datos ...
entrada.close();  // ESTO CIERRA System.in!
// A partir de aqui, no puedes leer mas de System.in en todo el programa
```

Si cierras el Scanner que usa `System.in`, cierras `System.in` para siempre. Si necesitas leer en varios metodos, crea un solo Scanner al inicio y usalo en todo el programa.

```java
public class Programa {
    private static final Scanner ENTRADA = new Scanner(System.in);

    public static void main(String[] args) {
        String nombre = pedirNombre();
        int edad = pedirEdad();
        System.out.println(nombre + " - " + edad);
        // No cierres ENTRADA!
    }

    static String pedirNombre() {
        System.out.print("Nombre: ");
        return ENTRADA.nextLine();
    }

    static int pedirEdad() {
        System.out.print("Edad: ");
        return Integer.parseInt(ENTRADA.nextLine());
    }
}
```

---

## 12. Scanner vs BufferedReader

`BufferedReader` es otra forma de leer datos en Java. Es mas basica pero mas rapida.

### Comparativa

| Caracteristica | Scanner | BufferedReader |
|---------------|---------|---------------|
| Parseo automatico | Si (`nextInt()`, `nextDouble()`) | No (solo texto, tu conviertes) |
| Delimitadores | Si, personalizables | No |
| Metodos hasNext | Si | No |
| Rendimiento | Mas lento | Mas rapido |
| Linea completa | `nextLine()` | `readLine()` |
| Archivos grandes | Suficiente | Mejor para archivos grandes |

### Ejemplo con BufferedReader

```java
import java.io.BufferedReader;
import java.io.FileReader;

public class DemoBufferedReader {
    public static void main(String[] args) throws Exception {
        try (var lector = new BufferedReader(new FileReader("datos.txt"))) {
            String linea;
            while ((linea = lector.readLine()) != null) {
                System.out.println(linea);
            }
        }
    }
}
```

### Cuando usar cada uno

Usa **Scanner** cuando:
- Necesitas parsear datos (leer ints, doubles directamente)
- Los datos son variados o poco estructurados
- Quieres usar delimitadores personalizados
- La cantidad de datos es pequena o mediana

Usa **BufferedReader** cuando:
- Necesitas la maxima velocidad (archivos muy grandes)
- Solo lees texto linea por linea sin parsear
- El rendimiento es critico

---

## 13. Scanner vs Console

Java tiene una tercera forma de leer de consola: `System.console()`.

### Ejemplo con Console

```java
public class DemoConsole {
    public static void main(String[] args) {
        var consola = System.console();

        if (consola == null) {
            System.out.println("No hay consola disponible");
            return;
        }

        String nombre = consola.readLine("Nombre: ");
        char[] contrasenia = consola.readPassword("Contrasenia: ");

        System.out.println("Hola " + nombre);
    }
}
```

### Diferencia clave

| Caracteristica | Scanner + System.in | Console |
|---------------|-------------------|---------|
| Funciona en IDE | Si | No (devuelve null) |
| Solo en terminal real | No | Si |
| `readPassword()` (ocultar contrasenia) | No | Si |
| `printf` directo | No | `consola.printf()` |
| Simpleza | Muy simple | Requiere mas pasos |

`System.console()` solo funciona cuando ejecutas el programa desde una terminal real, no desde IDEs como Eclipse o IntelliJ.

### Para contrasenias: Console

```java
public class Login {
    public static void main(String[] args) {
        var consola = System.console();
        if (consola == null) {
            System.out.println("La consola no esta disponible");
            System.out.println("Usa el programa desde una terminal real");
            return;
        }

        String usuario = consola.readLine("Usuario: ");
        char[] contrasenia = consola.readPassword("Contrasenia: ");

        System.out.println("Bienvenido " + usuario + "!");
        Arrays.fill(contrasenia, ' ');  // Limpiar contrasenia de memoria
    }
}
```

---

## 14. Scanner vs Stream (alternativa moderna)

Java moderno ofrece alternativas a Scanner que pueden ser mas adecuadas en algunos casos.

### Files.lines() (Java 8+)

Lee archivos usando Stream API:

```java
import java.nio.file.*;

public class DemoFilesLines {
    public static void main(String[] args) throws Exception {
        // Leer archivo linea por linea con Stream
        try (var lineas = Files.lines(Path.of("datos.txt"))) {
            lineas
                .filter(l -> !l.isBlank())
                .map(String::toUpperCase)
                .forEach(System.out::println);
        }
    }
}
```

### String.lines() (Java 11+)

Dividir un String en lineas:

```java
String texto = """
    Ana,25,8.5
    Luis,22,7.0
    Carlos,24,9.2
    """;

texto.lines()
    .filter(l -> !l.isBlank())
    .map(l -> l.split(","))
    .forEach(partes -> System.out.println(partes[0] + " - " + partes[1]));
```

### Files.newBufferedReader (Java 8+)

```java
try (var lector = Files.newBufferedReader(Path.of("datos.txt"))) {
    String linea;
    while ((linea = lector.readLine()) != null) {
        System.out.println(linea);
    }
}
```

### Cuando usar cada alternativa

```java
// Scanner: para entrada interactiva de usuario
var entrada = new Scanner(System.in);

// Files.lines(): para procesar archivos con Stream API
try (var lineas = Files.lines(Path.of("grande.txt"))) {
    lineas.filter(l -> l.contains("error")).forEach(System.out::println);
}

// BufferedReader: maxima velocidad para archivos grandes
try (var lector = Files.newBufferedReader(Path.of("grande.txt"))) {
    String linea;
    while ((linea = lector.readLine()) != null) { proceso(linea); }
}

// String.lines(): parsear Strings multilinea
texto.lines().forEach(System.out::println);
```

---

## 15. Errores comunes con Scanner

### InputMismatchException

Cuando intentas leer un tipo y el token no coincide:

```java
var entrada = new Scanner("Hola");
int numero = entrada.nextInt();  // InputMismatchException: "Hola" no es int
```

**Solucion:** Usa `hasNextInt()` antes de `nextInt()`.

### NoSuchElementException

Cuando intentas leer y no hay mas tokens:

```java
var entrada = new Scanner("Hola");
String a = entrada.next();  // "Hola"
String b = entrada.next();  // NoSuchElementException: no hay mas tokens
```

**Solucion:** Usa `hasNext()` o `hasNextLine()` antes de leer.

### IllegalStateException

Cuando el Scanner esta cerrado:

```java
var entrada = new Scanner(System.in);
entrada.close();
String s = entrada.next();  // IllegalStateException: Scanner cerrado
```

**Solucion:** No uses un Scanner cerrado.

### Olvidar cerrar Scanner de archivo

```java
var entrada = new Scanner(Path.of("datos.txt"));
// ... leer ...
// Olvidaste entrada.close() -> recurso sin liberar
```

**Solucion:** Usa `try-with-resources`.

### Cerrar Scanner de System.in

```java
var entrada = new Scanner(System.in);
entrada.close();  // Cierra System.in para siempre
```

**Solucion:** No cierres Scanner que usa `System.in` si el programa sigue.

### Confundir next() con nextLine()

```java
var entrada = new Scanner("Hola mundo");
String p = entrada.next();      // "Hola" (solo una palabra)
String l = entrada.nextLine();  // " mundo" (el resto de la linea)
```

**Solucion:** `next()` lee hasta espacio. `nextLine()` lee toda la linea.

---

## Errores Comunes (resumen)

> Mezclar `nextInt()`/`nextDouble()` con `nextLine()` sin consumir el salto de linea. Usa siempre `nextLine()` y convierte con `Integer.parseInt()`.

> Usar `hasNextInt()` una sola vez y asumir que sigue siendo true despues. Si hay multiples tokens, comprueba cada vez.

> Cerrar Scanner que usa `System.in`. Cierra solo Scanners de archivos o recursos externos.

> Olvidar importar `java.util.Scanner`. Siempre importalo al principio.

> No manejar `InputMismatchException`. Si el usuario escribe texto donde esperas un numero, el programa se rompe. Valida con `hasNext*` o usa try-catch.

> Usar `next()` para leer nombres completos. `next()` solo lee hasta el primer espacio. Usa `nextLine()` para nombres con apellidos.

> Asumir que el delimitador son solo espacios. Por defecto incluye tabulaciones y saltos de linea.

---

## Buenas Practicas

1. **Usa siempre `nextLine()` y convierte manualmente**. Evita el problema del salto de linea y tienes mas control.
2. **Valida con `hasNext*`** antes de leer para evitar `InputMismatchException`.
3. **try-with-resources** para Scanners de archivos. Se cierran solos.
4. **No cierres Scanner de `System.in`** si el programa sigue ejecutandose.
5. **Un solo Scanner** para toda la aplicacion si lees de teclado. Pasalo como parametro.
6. **Usa `useDelimiter()`** para datos estructurados (CSV, logs).
7. **Usa `useLocale()`** si esperas decimales con coma (formato europeo).
8. **Para contrasenias**, usa `System.console().readPassword()` si la consola esta disponible.
9. **Para archivos grandes**, considera `BufferedReader` o `Files.lines()` por rendimiento.
10. **Captura excepciones** al convertir (`Integer.parseInt`, `Double.parseDouble`) para dar mensajes utiles al usuario.

---

## Conexiones

- [[11 - System.out y Concatenacion]] - Salida por consola (companero de entrada)
- [[14 - Arrays Basicos y args]] - `String[] args` como otra forma de entrada
- [[16 - Bucles y Control de Flujo]] - Bucles con `hasNext()` para leer multiples datos
- [[17 - Flujo de Ejecucion JVM]] - Flujos de entrada y salida
- [[23 - Metodos - Parametros, Retorno y Return]] - Metodos que procesan datos leidos
- [[03 - Tipos Primitivos y Referencia]] - Conversion de String a tipos primitivos
- [[04 - Variables y Literales]] - Almacenar datos leidos en variables

---

## Tags
`#java #fundamentos #scanner #entrada #consola #archivos #parseo #io`
