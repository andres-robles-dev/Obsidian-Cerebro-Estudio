---
tags: [java, fundamentos, wrapper, envolventes, tipos, autoboxing, inmutabilidad]
---

# 34 - Clases Wrapper (envolventes) en Profundidad

> **Requiere:** haber leido [[03 - Tipos Primitivos y Referencia]]. La guia de decision rapida (Â¿primitivo o wrapper?) esta en [[39 - Tipos y Wrappers - La Guia Definitiva]].

Las clases Wrapper son envoltorios que permiten usar tipos primitivos como objetos. Son fundamental para colecciones, generics y el ecosistema Java moderno.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que son las clases Wrapper?

Java tiene 8 tipos primitivos (`int`, `double`, `boolean`, etc.). Las clases Wrapper son versiones objeto de cada primitivo:

| Primitivo | Wrapper |
|-----------|---------|
| `byte` | `Byte` |
| `short` | `Short` |
| `int` | `Integer` |
| `long` | `Long` |
| `float` | `Float` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

### Por que existen?

Como ya intuiste en [[03 - Tipos Primitivos y Referencia]], los primitivos no entran en colecciones. Las colecciones de Java (`ArrayList`, `HashMap`, etc.) solo aceptan objetos, no primitivos:

```java
// Esto NO compila:
// ArrayList<int> numeros = new ArrayList<>();  // ERROR

// Esto si:
ArrayList<Integer> numeros = new ArrayList<>();
numeros.add(42);
```

### Crear Wrapper

```java
// Constructor (obsoleto pero funciona):
Integer i1 = new Integer(42);
Double d1 = new Double(3.14);

// Via static valueOf (recomendado):
Integer i2 = Integer.valueOf(100);
Double d2 = Double.valueOf(2.71);

// Via parse (devuelve primitivo, no Wrapper):
int primitivo = Integer.parseInt("123");  // devuelve int, no Integer

// Via autoboxing (Java convierte automaticamente):
Integer i3 = 42;  // Java hace: Integer.valueOf(42)
```

### Extraer el primitivo (unboxing)

```java
Integer wrapper = 42;
int primitivo = wrapper;  // Java hace: wrapper.intValue()
```

---

## NIVEL 2 - Mid

### Autoboxing y unboxing

Esto es lo que el tema [[03 - Tipos Primitivos y Referencia]] llamaba "valor vs referencia" â?" aqui el valor se envuelve. Java convierte automaticamente entre primitivos y Wrapper en ciertas situaciones:

```java
// Autoboxing: primitivo -> Wrapper
Integer a = 10;           // int -> Integer
Double b = 3.14;          // double -> Double
Character c = 'A';        // char -> Character

// Unboxing: Wrapper -> primitivo
int x = a;                // Integer -> int
double y = b;             // Double -> double

// En expresiones:
Integer i = 10;
int resultado = i + 5;    // i se hace unboxing, se suma, resultado es int
```

### El problema del null con autoboxing

```java
Integer posibleNull = null;
int valor = posibleNull;  // NullPointerException!
```

Si un `Integer` es `null` y lo intentas convertir a `int`, falla. Esto ocurre frecuentemente con datos de bases de datos u APIs que devuelven `Integer` y pueden ser `null`.

### Comparaciones con == vs .equals()

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);      // true (cache de Integer)

Integer c = 128;
Integer d = 128;
System.out.println(c == d);      // false (fuera de la cache)

// La forma correcta siempre:
System.out.println(c.equals(d));  // true
```

### Cache de Integer (-128 a 127)

Java cachea los valores de `Integer` entre -128 y 127 por defecto:

```java
// Dentro de la cache:
Integer a = 100;
Integer b = 100;
System.out.println(a == b);   // true: misma instancia

// Fuera de la cache:
Integer c = 200;
Integer d = 200;
System.out.println(c == d);   // false: instancias distintas
```

### ConversiÃ³n de tipos entre Wrapper

```java
// String a Integer:
int n1 = Integer.parseInt("42");       // devuelve int (primitivo)
Integer n2 = Integer.valueOf("42");    // devuelve Integer (objeto)

// Integer a String:
String s1 = Integer.toString(42);
String s2 = String.valueOf(42);
String s3 = 42 + "";                    // concatenacion con vacio

// String a Double:
double pi = Double.parseDouble("3.14159");

// Integer a Double (conversion con perdida de precision cuidado):
int entero = 10;
double decimal = entero;                // widening automatico
Integer wrapper = entero;
Double d = wrapper.doubleValue();       // conversion explicita
```

---

## NIVEL 3 - Senior

### Inmutabilidad de los Wrapper

Todos los Wrapper son inmutables. Una vez creado, su valor no cambia:

```java
Integer a = 100;
Integer b = a;           // b apunta al mismo objeto (o cache)
a = 200;                 // a ahora apunta a un INTEGER nuevo (200)
System.out.println(b);   // sigue siendo 100

// "Modificar" un Integer crea NUEVO objeto siempre
Integer i = 1;
i = i + 1;               // unboxing (1), suma (2), autoboxing (Integer(2))
```

Esto significa que los Wrapper son thread-safe por naturaleza (no hay estado mutable compartido).

### Comparacion segura entre Wrapper

```java
// MAL: == comparo referencias (puede fallar fuera de la cache)
if (a == b) { }         // puede dar results incorrectos

// BIEN: siempre usar .equals()
if (a.equals(b)) { }    // compara el valor real

// ALTERNATIVA: usar intValue() para comparar primitivos
if (a.intValue() == b.intValue()) { }
```

### Uso con colecciones y generics

```java
// HashMap con Wrapper como clave:
Map<Integer, String> mapa = new HashMap<>();
mapa.put(1, "Uno");
mapa.put(null, "Nulos tambiÃ©n permitidos");  // null es vÃ¡lido en Wrapper, NO en primitivos

// Problema con null como clave:
Integer clave = obtenerDeBD();  // puede ser null
String valor = mapa.get(clave);  // funciona si clave es null (devuelve valor asociado a null)

// Si intentas usar un primitivo como clave con null:
int nulo = null;  // ERROR: no puedes asignar null a un primitivo
```

### Optional con Wrapper (moderno)

```java
// Cuando la BD puede devolver null para un campo Integer:
Integer resultado = obtenerDeBaseDeDatos();

// Sin Optional (null peligroso):
// int val = resultado;  // NullPointerException si resultado es null

// Con Optional:
int seguro = Optional.ofNullable(resultado)
    .orElse(0);              // usa 0 si es null

int calculado = Optional.ofNullable(resultado)
    .orElseGet(() -> calcularDefault());  // lambda lazy

int lanzando = Optional.ofNullable(resultado)
    .orElseThrow(() -> new RuntimeException("Valor requerido"));
```

### ConversiÃ³n entre primitivos y Wrapper en arrays

```java
// Primitivo a Wrapper (boxing):
int[] primitivos = {1, 2, 3};
Integer[] objetos = Arrays.stream(primitivos)
    .boxed()
    .toArray(Integer[]::new);

// Wrapper a primitivo (unboxing):
Integer[] wrappers = {4, 5, 6};
int[] prims = Arrays.stream(wrappers)
    .mapToInt(Integer::intValue)
    .toArray();
```

### Problema del autoboxing en bucles (performance)

```java
// MAL: crea millones de objetos Integer innecesarios
Integer suma = 0;
for (int i = 0; i < 1_000_000; i++) {
    suma += i;  // cada iteracion: unboxing + suma + autoboxing
}

// BIEN: usar primitivos
long suma = 0;
for (int i = 0; i < 1_000_000; i++) {
    suma += i;
}
```

El autoboxing en bucles calientes puede ser 10-100x mas lento que usar primitivos.

### Valores especiales

```java
// MAX/MIN de cada wrapper:
System.out.println(Integer.MAX_VALUE);   // 2147483647  (2^31 - 1)
System.out.println(Integer.MIN_VALUE);   // -2147483648  (-2^31)
System.out.println(Long.MAX_VALUE);      // 9223372036854775807
System.out.println(Double.MAX_VALUE);    // 1.7976931348623157E308
System.out.println(Double.MIN_VALUE);    // 4.9E-324 (el positivo mas pequeno > 0)

// Constantes util:
System.out.println(Integer.SIZE);      // 32 (bits)
System.out.println(Integer.BYTES);     // 4 (bytes)
System.out.println(Integer.toBinaryString(42)); // "101010"
System.out.println(Integer.toHexString(255));   // "ff"
System.out.println(Integer.bitCount(15));       // 4 (cuantos bits en 1)
```

### Metodos utiles de Integer/Long

```java
// Comparacion:
Integer.compare(10, 20);              // -1, 0, o 1
Integer.compareUnsigned(0, -1);       // 1 (int unsigned)

// Parse con radix (base):
Integer.parseInt("1010", 2);          // 10 (binario a decimal)
Integer.parseInt("FF", 16);           // 255 (hex a decimal)
Integer.toHexString(255);             // "ff"
Integer.toBinaryString(10);           // "1010"

// Overflow detection (Java 8+):
Math.addExact(2000000000, 2000000000);  // throws ArithmeticException en overflow
Math.multiplyExact(100000, 100000);     // throws ArithmeticException

// Divison exacta (solo si divide exactamente):
Math.floorDiv(-7, 3);   // -3 (floor division, no truncation)
Math.floorMod(-7, 3);   // 2 (modulo siempre positivo)
```

### Character Wrapper especial

```java
Character c = 'A';

// Verificar tipo de caracter:
Character.isLetter('A');       // true
Character.isDigit('5');        // true
Character.isWhitespace(' ');   // true
Character.isUpperCase('A');    // true
Character.isLowerCase('a');    // true

// Convertir:
Character.toUpperCase('a');    // 'A'
Character.toLowerCase('A');    // 'a'
Character.getNumericValue('5'); // 5 (como int)

// Iterar rangos:
for (char ch = 'A'; ch <= 'Z'; ch++) {
    System.out.print(ch + " ");  // A B C ... Z
}
```

### Boolean Wrapper detalles

```java
// Boolean tiene solo dos instancias posibles (patrÃ³n singleton):
Boolean verdadero = Boolean.TRUE;
Boolean falso = Boolean.FALSE;

// La Ãºnica forma de obtener instancia diferente es con new (obsoleto):
Boolean otro = new Boolean(true);  // evita usar esto

// valueOf retorna TRUE o FALSE (las instancias cacheadas):
Boolean b = Boolean.valueOf("true");
System.out.println(b == Boolean.TRUE);  // true

// parseBoolean devuelve primitivo:
boolean primitivo = Boolean.parseBoolean("true");  // true
boolean noExiste = Boolean.parseBoolean("truÃ©");    // false (no lanza excepcion)
```

---

# METODO PROFUNDO (Curso completo de Wrapper Classes)

## 1. Origen y necesidad de los Wrapper

### El problema de los tipos primitivos

Java tiene 8 tipos primitivos que no son objetos. Pero las colecciones de Java (`List`, `Set`, `Map`) solo aceptan objetos:

```java
// Java Collections Framework:
List<E>           // E debe ser un tipo referencia (objeto)
Set<E>            // lo mismo
Map<K, V>         // K y V deben ser tipos referencia
```

```java
// Esto NO compila:
List<int> numeros = new ArrayList<int>();
Map<int, String> mapa = new HashMap<int, String>();
```

Las clases Wrapper resuelven esto permitiendo que tipos primitivos se usen donde se necesita un objeto:

```java
// Con Wrapper:
List<Integer> numeros = new ArrayList<>();
numeros.add(1);
numeros.add(2);
numeros.add(null);  // null es valido en Wrapper, NO en int

Map<Integer, String> mapa = new HashMap<>();
mapa.put(1, "primero");
mapa.put(null, "valor nulo");  // permitido
```

### La caja de tipos (type system)

Java es tanto primitivo como orientado a objetos:

| Aspecto | Primitivo | Wrapper |
|---------|-----------|---------|
| Tipo en la VM | Valor directo en stack | Objeto en heap |
| Null | No puede ser null | Puede ser null |
| Colecciones | No | Si |
| Generics | No | Si |
| TamaÃ±o | Fijo (segÃºn tipo) | Objeto con overhead |
| Default value | 0, false, '\u0000' | null |

---

## 2. Las 8 clases Wrapper detalladas

### Byte: `-128` a `127`

```java
// 8 bits con signo, rango: -128 a 127
Byte b = 127;      // Maximo valor de byte
Byte bMin = -128;   // Minimo valor de byte

// ConversiÃ³n:
byte primitivo = b;                  // unboxing
int ampliado = b;                    // widening automatico (byte -> int)
short tambiÃ©n = b;                   // byte -> short funciona

// byte a otros tipos (con conversiÃ³n explicita si pierde informacion):
long desdeLong = b;                 // byte a long (widening, seguro)
float desdeFloat = b;               // byte a float (widening, pero puede perder precision)
double desdeDouble = b;             // byte a double (widening)
```

### Short: `-32768` a `32767`

```java
Short s = 30000;
short primitivo = s;

// ConversiÃ³n a int (widening, seguro):
int i = s;  // No pierde informaciÃ³n

// ConversiÃ³n a byte (narrowing, puede perder datos):
byte b = s.byteValue();  // Si s > 127 o s < -128, se trunca
```

### Integer: `-2^31` a `2^31 - 1`

El Wrapper mÃ¡s usado. Incluye parsing, conversiones de base y deteccion de overflow.

```java
Integer i = 42;

// ConversiÃ³n de base:
String binario = Integer.toBinaryString(42);    // "101010"
String octal = Integer.toOctalString(42);       // "52"
String hex = Integer.toHexString(42);           // "2a"

// Parse desde cualquier base:
int desdeBin = Integer.parseInt("1010", 2);   // 10
int desdeHex = Integer.parseInt("FF", 16);    // 255

// Overflow safe arithmetic (Java 8+):
int suma = Math.addExact(1000000000, 1000000000);  // ArithmeticException (overflow)
```

### Long: `-2^63` a `2^63 - 1`

```java
Long l = 10000000000L;  // Nota: la L es obligatoria para literales long

// Suffix L obligatorio:
long primitivo = l;           // unboxing
long mas = 9999999999999L;   // sin L, Java intenta int y falla (excede el rango)
```

### Float: 32-bit IEEE 754

```java
Float f = 3.14f;  // La f es obligatoria para literales float

// PrecisiÃ³n limitada (no es exacto para muchos decimales):
System.out.println(0.1f + 0.2f);          // 0.30000001 (no es 0.3 exacto)
System.out.println(0.1 + 0.2);            // Con double: 0.30000000000000004

// Constantes especiales:
Float.POSITIVE_INFINITY;   // infinito positivo
Float.NEGATIVE_INFINITY;   // infinito negativo
Float.NaN;                 // Not a Number
Float.MAX_VALUE;           // 3.4028235E38
Float.MIN_VALUE;           // 1.4E-45 (el positivo mas pequeno > 0)

// Comparar Float con NaN (usa .equals, no ==):
Float nan = Float.NaN;
System.out.println(nan == Float.NaN);        // false
System.out.println(nan.equals(Float.NaN));   // true (porque.equals compara bits)
System.out.println(Float.isNaN(nan));        // true (forma correcta de verificar NaN)
```

### Double: 64-bit IEEE 754

```java
Double d = 3.14159;  // Sin sufijo: un double literal se convierte automÃ¡ticamente a Double

// El Wrapper mas preciso para decimales:
System.out.println(0.1 + 0.2);             // 0.30000000000000004 (impreciso)

// No usar Double para dinero (usa BigDecimal en su lugar):
Double precio = 19.99;
Double impuesto = 1.99;
Double total = precio + impuesto;  // 21.980000000000004 (NO use asi!)

// Para dinero:
import java.math.BigDecimal;
BigDecimal dinero = new BigDecimal("19.99");
BigDecimal impuestoBd = new BigDecimal("1.99");
BigDecimal totalBd = dinero.add(impuestoBd); // 21.99 exacto
```

### Character: Unicode 16-bit

```java
Character ch = 'A';

// Rango Unicode: U+0000 a U+FFFF (Basic Multilingual Plane).
// Java puede representar caracteres suplementarios (emoji, etc.) con surrogates:
Character high = '\uD83D';  // surrogate
Character low = '\uDE00';   // surrogate
// j juntos forman un emoji ðY~?

// Metodos utilÃ­simos:
Character.isLetter('A');           // true
Character.isDigit('7');            // true
Character.isWhitespace(' ');       // true
Character.isUpperCase('a');        // false
Character.isLowerCase('A');        // false
Character.toUpperCase('a');        // 'A'
Character.toLowerCase('A');        // 'a'
Character.getNumericValue('9');    // 9
Character.isLetterOrDigit('_');    // false (_ no es letra ni digito)
Character.isJavaIdentifierStart('a'); // true (puede iniciar identificador Java)
Character.isJavaIdentifierPart('$');  // true (puede ser parte de identificador)
```

### Boolean: true / false

```java
Boolean bool = true;

// Dos unicas instancias posibles (como enum):
Boolean t = Boolean.TRUE;
Boolean f = Boolean.FALSE;

// Parse vs ValueOf: boolean parseBoolean no lanza excepcion, devuelve false para cualquier string que no sea "true" (case-insensitive):
System.out.println(Boolean.parseBoolean("true"));    // true
System.out.println(Boolean.parseBoolean("TRUE"));    // true
System.out.println(Boolean.parseBoolean("True"));    // true
System.out.println(Boolean.parseBoolean("false"));   // false
System.out.println(Boolean.parseBoolean("1"));       // false (NO es "true"!)
System.out.println(Boolean.parseBoolean("si"));      // false

// valueOf: devuelve Boolean (objeto), no primitivo:
Boolean b = Boolean.valueOf("true");   // Boolean.TRUE (cacheado)
```

---

## 3. Caching y comparaciones (problema frecuente)

### Integer cache

```java
// Java cachea Integer desde -128 hasta 127 por defecto (JVM estÃ¡ndar).
// Fuera de ese rango, cada nuevo autoboxing crea un NUEVO objeto.

Integer a = 127;
Integer b = 127;
System.out.println(a == b);        // true (misma instancia cacheada)
System.out.println(a.equals(b));   // true

Integer c = 128;
Integer d = 128;
System.out.println(c == d);        // false (instancias diferentes)
System.out.println(c.equals(d));   // true

// Puedes ampliar la cache con JVM argument:
// -XX:AutoBoxCacheMax=200  (cachea -128 a 200)
```

### Long cache

Misma regla que Integer, cachea valores tÃ­picos:

```java
Long la = 100L;
Long lb = 100L;
System.out.println(la == lb);       // true (cache)

Long lc = 1000L;
Long ld = 1000L;
System.out.println(lc == ld);       // false (fuera de la cache tÃ­pica)
```

### Short y Byte tambiÃ©n tienen cache (todas las operaciones de autoboxing usan cache):

```java
Byte ba = 10;
Byte bb = 10;
System.out.println(ba == bb);       // true
```

### La regla de oro para comparar Wrapper

```java
// SIEMPRE usar .equals() para comparar Wrapper:
Integer a = 1000, b = 1000;

// MAL (puede dar false cuando los valores son iguales):
boolean iguales = (a == b);  // false (fuera de la cache)

// BIEN (siempre correcto):
iguales = a.equals(b);       // true

// TAMBIEN BIEN (convertir a primitivos y comparar):
iguales = (a.intValue() == b.intValue());  // true
```

---

## 4. Null, Unboxing y NullPointerException

### El peligro del null

```java
Integer posibleNull = obtenerDeBaseDeDatos();  // puede ser null

// Cualquier uso de unboxing sobre null falla:
int valor = posibleNull;              // NullPointerException!
int suma = posibleNull + 1;           // NullPointerException!
boolean esCero = (posibleNull == 0);  // NullPointerException! (unboxing en ==)
```

### Como detectar y manejar null

```java
// Forma 1: verificar null antes
if (posibleNull != null) {
    int seguro = posibleNull;
}

// Forma 2: usar Optional (recomendado en Java moderno)
int seguro = Optional.ofNullable(posibleNull).orElse(0);

// Forma 3: usar comparaciÃ³n segura con Objects.equals()
// (solo funciona si comparas con otro Wrapper, no con primitivo):
boolean esCero = Integer.valueOf(0).equals(posibleNull);  // falso si es null

// Forma 4: usar primitivo por defecto si es null
int seguro = posibleNull != null ? posibleNull : 0;  // operador ternario
```

### Autoboxing silencioso con null

```java
// Muy peligroso: un autoboxing con null dentro de una expresion:
Integer x = null;
int y = 5;
// int z = x + y;  // NullPointerException (x se hace unboxing y da null)

// Pero el autoboxing al asignar no falla si es directo:
Integer z = null;  // OK, null es un valor valido de Integer
```

### HashMap con null en claves Wrapper

```java
Map<Integer, String> mapa = new HashMap<>();
mapa.put(null, "valor para null");  // OK, Integer permite null
mapa.put(1, "valor para 1");        // OK

// El problema: si la clave es null y haces get(null), funciona correctamente:
String resultado = mapa.get(null);  // devuelve "valor para null"

// Pero si usas un primer con valor null en operaciones:
Integer clave = null;
// int h = clave.hashCode();  // NullPointerException

// HashMap maneja null internamente, pero TU codigo debe verificar null antes de hacer operations
// sobre el objeto directamente (como hashCode() o compareTo() etc).
```

---

## 5. ConversiÃ³n entre tipos y widening/narrowing

### Widening automÃ¡tico (promocion implicita)

Cuando asignas un primitivo mas pequeÃ±o a un Wrapper de mayor rango:

```java
byte b = 10;
short s = b;            // byte -> short (widening, no pierde datos)
int i = s;              // short -> int (widening)
long l = i;             // int -> long (widening)
float f = l;            // long -> float (posible perdida de precision silenciosa)
double d = f;           // float -> double (posible perdida de precision silenciosa)

// Con Wrapper:
Integer wi = b;         // byte autoboxed + widening, funciona sin conversion explicita
```

### Narrowing explicito (reduccion, puede perder datos)

```java
double d = 10.99;
int i = (int) d;                 // narrowing explicito, pierde la parte decimal (10)
long l = (long) d;               // pierde la parte decimal (10)
byte b = (byte) i;               // narrowing, puede truncar si i > 127

// El orden importa:
double d2 = 1000.99;
int i2 = (int) d2;      // 1000 (pierde decimales)
byte b2 = (byte) i2;    // -24 (overflow: 1000 > 127, se trunca)
```

### Wrapper a primitivo: methods de conversion

```java
Integer w = 42;

// Convertir a cualquier primitivo numerico:
byte bt = w.byteValue();
short st = w.shortValue();
int nt = w.intValue();              // el mas directo
long lg = w.longValue();
float fl = w.floatValue();          // posible perdida de precision
double db = w.doubleValue();

// Todos los metodos del Wrapper:
int comparacion = w.compareTo(50);             // -1, 0, o 1
int hash = w.hashCode();                       // 42 (igual que intValue())
String string = w.toString();                  // "42"
int bits = w.bitCount(42);                     // 3 (cuantos bits en 1 tiene 42 = 101010)
int hwb = w.highestOneBit(42);                 // 32 (el bit mas alto: 100000 = 32)
int lwb = w.lowestOneBit(42);                  // 2 (el bit mas bajo: 10 = 2)
int leading = w.numberOfLeadingZeros(42);      // 26 (ceros a la izquierda en 32 bits)
int trailing = w.numberOfTrailingZeros(42);    // 1 (ceros a la derecha)
```

---

## 6. Wrapper y expresiones aritmÃ©ticas

### Autoboxing + primitivo = primitivo

```java
Integer wrapper = 10;
int primitivo = 5;

// Cuando operas Wrapper + primitivo, el Wrapper se hace unboxing:
int resultado = wrapper + primitivo;  // wrapper.intValue() + 5  (resultado es int)

// Cuando operas Wrapper + Wrapper, ambos se hacen unboxing y resultado es primitivo:
Integer w1 = 10;
Integer w2 = 20;
int suma = w1 + w2;  // w1.intValue() + w2.intValue() = 30 (primitivo)

// AsignaciÃ³n a Wrapper: el resultado se hace autoboxing automÃ¡ticamente:
Integer resultadoWrapper = w1 + w2;  // equivalente a Integer.valueOf(30)

// Cadena de operaciones mixtas:
Integer a = 1;
int b = 2;
Integer c = 3;
long d = 4;
float resultadoMixto = a + b + c + d;  // TODO: a+b+c+d = 10 (int), luego 10.0f (float) 
// Pero: a+b+c = 6 (int, porque int+int=int), luego 6+4=10, luego 10.0f
```

### Wrapper con String concatenaciÃ³n

```java
Integer n = 42;
String s = "El numero es " + n;  // n se hace toString() implicitamente: "El numero es 42"

// Pero cuidado con operaciones mixtas:
Integer x = null;
String resultado = "valor: " + x;  // "valor: null" (el null se convierte a la cadena "null")
// No da NullPointerException! Java maneja null en concatenaciÃ³n con String de forma especial

// Pero si concatenas un primitivo despuÃ©s del null:
String r2 = resultado + 5;  // "valor: null5" (funciona sin error)
```

---

## 7. Wrapper y genÃ©ricos (tipos parametrizados)

### Por quÃ© los genÃ©ricos solo aceptan Wrapper:

```java
// Generics solo aceptan tipos referencia (clases):
List<int> numeros = new ArrayList<>();                // ERROR
List<Integer> numeros = new ArrayList<>();            // OK

Map<int, String> mapa = new HashMap<>();              // ERROR
Map<Integer, String> mapa = new HashMap<>();          // OK

// Los genÃ©ricos tambiÃ©n no permiten tipos primitivos como type variable:
public class Caja<T> {
    private T valor;
}

Caja<int> caja = new Caja<>();    // ERROR: int es primitivo
Caja<Integer> caja = new Caja<>(); // OK
```

### El concepto de `? extends Number` (PECS)

```java
// Puedes usar el supertipo Number para aceptar cualquier Wrapper numerico:
public double sumar(List<? extends Number> numeros) {
    double total = 0;
    for (Number n : numeros) {
        total += n.doubleValue();  // Number tiene doubleValue()
    }
    return total;
}

// Uso:
List<Integer> enteros = List.of(1, 2, 3);
List<Double> decimales = List.of(1.5, 2.5);
List<Long> largos = List.of(100L, 200L);

sumar(enteros);    // OK
sumar(decimales);  // OK
sumar(largos);     // OK
sumar(List.of(1, 2.5, 3L));  // ERROR: tipos mixtos en la lista no tipados
```

### Wrapper primitivo especial: `void` -> `Void`

Existe un Wrapper para `void` aunque se rara vez se usa directamente:

```java
// Usado en Reflection y generics cuando necesitas representar "no retorna nada":
Callable<Void> tarea = () -> {
    System.out.println("Haciendo algo");
    return null;  // Void no tiene valor, siempre null
};
```

---

## 8. Wrapper y Streams (Java 8+)

### Boxing y unboxing en Streams

```java
// Stream<Integer> (objetos Boxing cada elemento en el pipeline):
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);  // autoboxing implicito

// Para evitar Boxing en pipelines numericos, usa los metodos specializados:
IntStream ints = IntStream.of(1, 2, 3, 4, 5);       // primitivo, sin Boxing
LongStream longs = LongStream.of(1L, 2L, 3L);       // primitivo
DoubleStream doubles = DoubleStream.of(1.0, 2.0, 3.0); // primitivo

// Los streams de primitivos son mas rapidos y no generan objetos Wrapper temporales:
int suma = IntStream.rangeClosed(1, 100).sum();  // 5050, sin crear Integer objects

// Convertir Stream<Integer> a IntStream:
List<Integer> lista = Arrays.asList(1, 2, 3, 4, 5);
int sum = lista.stream().mapToInt(Integer::intValue).sum();  // mapToInt() quita Boxing

// Convertir IntStream de vuelta a Stream<Integer>:
IntStream primitivo = IntStream.of(1, 2, 3);
Stream<Integer> objetos = primitivo.boxed();  // unboxing + vuelta a autoboxing
```

### Ejemplo practico: promedio de una lista de Integer

```java
List<Integer> puntuaciones = Arrays.asList(null, 85, 90, null, 78, 92);

// Filtrar nulls y calcular promedio:
OptionalDouble promedio = puntuaciones.stream()
    .filter(Objects::nonNull)              // quitar nulls
    .mapToInt(Integer::intValue)          // convertir a IntStream (quita boxing)
    .average();                            // devuelve OptionalDouble

System.out.println(promedio.orElse(0.0));  // 86.25
```

---

## 9. Valores especiales y casos lÃ­mite

### NaN (Not a Number) y -0.0

```java
Double nan = Double.NaN;
Double otroNan = Double.NaN;

// NaN es especial: NO es igual a si mismo:
System.out.println(nan == nan);          // false (regla IEEE 754)
System.out.println(nan.equals(nan));     // true (.equals compara bits, ambos son NaN)

// Como verificar NaN:
Double.isNaN(nan);  // VERDADERO (forma correcta y segura)

// Negativo cero:
Double cero = 0.0;
Double negCero = -0.0;
System.out.println(cero == negCero);     // true (== los considera iguales)
System.out.println(cero.equals(negCero)); // false (.equals los distingue)
System.out.println(Double.compare(0.0, -0.0)); // 1 (0.0 > -0.0)
```

### Infinito

```java
Double infinitoPos = Double.POSITIVE_INFINITY;
Double infinitoNeg = Double.NEGATIVE_INFINITY;

System.out.println(infinitoPos > 1e308);  // true
System.out.println(infinitoNeg < -1e308);  // true
System.out.println(infinitoPos == infinitoNeg);  // false
System.out.println(infinitoPos / infinitoPos);   // NaN (infinito/infinito = NaN)
```

### Conversiones implicas peligrosas

```java
// Integer a Long (widening seguro):
Integer i = 100;
Long l = i.longValue();  // correcto. Pero AUTOMATICAMENTE:

// Esto FUNCIONA (autoboxing + widening):
// Long ll = i;  // ERROR: no hay conversiÃ³n implicita de Integer a Long directamente
// Long ll = (long) i;  // NECESITA cast explicito

// Lo que SÃ? funciona automÃ¡tico:
int prim = i;          // unboxing: Integer -> int
long largo = prim;    // widening: int -> long
// Pero Integer -> Long directo no existe como conversion implicita de tipo
```

---

## 10. Inmutabilidad y su impacto

### Wrapper inmutable = Thread-safe

```java
// Como todos los Wrapper son inmutibles, son inherentemente thread-safe:
Integer compartido = 42;

// Hilo 1:
int a = compartido;  // unboxing (lea el valor 42)

// Hilo 2:
int b = compartido;  // unboxing (lea el mismo valor 42)

// No hay riesgo de condicion de carrera porque integer 42 nunca cambia.
// (Pero si la variable reference compartida se reasigna, hay de otra cosa)
```

### Wrapper reassignable pero inmutable:

```java
Integer x = 10;   // x apunta a Integer(10)
x = 20;           // x ahora apunta a Integer(20), el objeto Integer(10) no cambia

// x es un String de referencia que puede apuntar a otro objeto.
// El objeto Integer en si nunca se modifica.
```

### Para datos mutables, usa otras estructuras:

```java
// Si necesitas "mutabilidad" sobre un primitivo, usa AtomicInteger, AtomicLong, etc:
import java.util.concurrent.atomic.AtomicInteger;

AtomicInteger contador = new AtomicInteger(0);
contador.incrementAndGet();  // atomico: 1
contador.incrementAndGet();  // atomico: 2
contador.get();              // 2

// AtomicInteger usa un int interno (primitivo), pero permite operaciones atÃ³micas
// comparables a un "Wrapper mutable". En concurrencia, es la alternativa correcta.
```

---

## Errores Comunes

> Usar `==` para comparar Wrapper fuera del rango de cache (-128 a 127 para `Integer`, valores tÃ­picos para otros Wrapper). Usa `.equals()` siempre.

> Hacer unboxing de `null`: `Integer i = null; int x = i;` lanza `NullPointerException`.

> Usar `parseInt` cuando necesitas un `Integer` (objeto): `parseInt` devuelve `int`. Usa `valueOf` para obtener `Integer`.

> Hacer operaciones aritmÃ©ticas en bucles con autoboxing (crea millones de objetos temporales y presiona al GC).

> Usar `Double`/`Float` para cÃ¡lculos monetarios. Usa `BigDecimal` en su lugar.

> Asumir que `new Integer(42) == new Integer(42)` es `true`. Crea dos objetos distintos, son diferentes incluso en valor y fuera de cache.

> Confundir `Boolean.parseBoolean()` con `Boolean.valueOf()`. `parseBoolean` devuelve primitivo y no lanza excepcion para string invalidos (devuelve false).

> Hacer casting narrowing sin verificar rango: `(byte) 300` da `-56` (overflow silencioso).

---

## Buenas Practicas

1. **Prefiere primitivos en variables locales y campos** cuando no necesitas null ni collections: `int` > `Integer`.
2. **Usa `valueOf()` sobre el constructor** (los constructores estÃ¡n obsoletos): `Integer.valueOf()` reutiliza la cache interna de JVM.
3. **Compara Wrapper con `.equals()`**, nunca con `==`.
4. **Usa `Optional<Integer>`** cuando el valor puede ser `null` y quieres manejarlo explicitamente.
5. **Usa `IntStream`, `LongStream`, `DoubleStream`** en vez de `Stream<Integer>` etc. para evitar boxing en pipelines numericos.
6. **Valida null antes de unboxing**: verifica `!= null` antes de operar con Integer/Long/etc.
7. **No uses Wrapper para datos monetarios**: usa `BigDecimal` o centavos con `long`.
8. **Usa `Objects.equals()` para comparar Wrapper que pueden ser null** sin NPE: `Objects.equals(a, b)`.
9. **En colecciones, prefiere los tipos especializados** de Eclipse Collections o Koloboke si el performance importa (evitan boxing).
10. **Aprovecha la inmutabilidad**: como los Wrapper son inmutables y thread-safe, son seguros para compartir entre hilos sin sincronizaciÃ³n.

---


### Puente al tema 39: Cuando elegir cada uno

Esta guia sintetiza la decision en 5 reglas:

| Situacion | Eleccion | Por que |
|-----------|----------|---------|
| Calculo / bucle / contador | int / long | Sin GC, max rendimiento |
| Constante interna / config | int / double | Nunca null, compile-time |
| Lista / Set / Map / generico | Integer / Long | Requiere tipo referencia |
| DTO / API / BD (nullable) | Integer / Long | Permite null / Optional |
| Clave compuesta Map | Record / Wrapper | quals/hashCode generado |

Guia completa con flowchart, tabla maestra, anti-patrones y checklist: [[39 - Tipos y Wrappers - La Guia Definitiva]].

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Base: tipos primitivos vs referencia, a los que Wrapper envuelven
- [[04 - Variables y Literales]] - Autoboxing y unboxing automaticos en asignaciones
- [[05 - Modificadores de Acceso]] - Los constructores de Wrapper son public, no private (a diferencia de Singleton)
- [[13 - Static vs Instancia]] - La cache de Integer usa instancias static compartidas (Singleton implÃ­cito)
- [[23 - Metodos - Parametros, Retorno y Return]] - Boxing/unboxing en retornos y parametros
- [[30 - Principio fundamental de Java (y POO)]] - Objeto vs primitivo; Wrapper es la puente necesaria para colecciones
- [[31 - Curso Completo de .method()]] - Metodos como parseInt, valueOf, compareTo, etc. en Wrapper
- [[33 - enum en Profundidad]] - Boolean tiene solo dos instancias (patrón similar al Singleton/enum)
- [[39 - Tipos y Wrappers - La Guia Definitiva]] - Guia de decision y sintesis

---

## Tags
`#java #fundamentos #wrapper #envolventes #tipos #autoboxing #inmutabilidad #colecciones #guia`
