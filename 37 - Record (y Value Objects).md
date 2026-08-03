---
tags: [java, fundamentos, record, value-object, inmutabilidad, ddd, java16, java21, poo]
---

# 37 - Record (y Value Objects) en Profundidad

Un record es una clase inmutable que genera automaticamente sus componentes, accessors, equals, hashCode y toString. Es la herramienta perfecta para modelar Value Objects: objetos que se definen por su valor, no por su identidad.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un record?

Un record es una forma corta de declarar una clase que solo guarda datos. Reemplaza a las clases con muchos getters, equals, hashCode y toString escritos a mano.

```java
// Forma tradicional (mucho codigo):
public class Punto {
    private final int x;
    private final int y;

    public Punto(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }

    @Override
    public boolean equals(Object o) { /* ... */ }

    @Override
    public int hashCode() { /* ... */ }

    @Override
    public String toString() { /* ... */ }
}

// Con record (una linea):
public record Punto(int x, int y) { }
```

### Sintaxis basica

```java
public record Usuario(String nombre, int edad) { }

// Uso:
Usuario u = new Usuario("Ana", 30);
System.out.println(u.nombre());   // "Ana"  (accessor, no getNombre())
System.out.println(u.edad());     // 30
System.out.println(u);            // Usuario[nombre=Ana, edad=30]
```

### Que genera un record automaticamente?

| Elemento | Que hace |
|----------|----------|
| Constructor | Inicializa todos los componentes en orden |
| Accessors | `nombre()`, `edad()` — sin prefijo `get` |
| `equals()` | Compara por VALOR (todos los componentes) |
| `hashCode()` | Calculado con todos los componentes |
| `toString()` | `Usuario[nombre=Ana, edad=30]` |
| Campos | `private final` — inmutables |

### Record vs clase normal

```java
// Clase normal:
// - Puede tener estado mutable
// - Puede heredar de otra clase
// - Tienes que escribir equals/hashCode/toString tu mismo

// Record:
// - Siempre inmutable
// - No puede heredar de otra clase (pero puede implementar interfaces)
// - Todo lo basico ya esta generado
```

---

## NIVEL 2 - Mid

### Compact constructor (validacion)

```java
public record Rango(int minimo, int maximo) {
    // Constructor compacto: se usa para validar ANTES de asignar
    public Rango {
        if (minimo > maximo) {
            throw new IllegalArgumentException(
                "Minimo (" + minimo + ") no puede ser mayor que maximo (" + maximo + ")"
            );
        }
    }
}

// Uso:
new Rango(1, 10);    // OK
new Rango(10, 1);    // IllegalArgumentException
```

### Static factory methods

```java
public record Email(String direccion) {
    public Email {
        if (direccion == null || !direccion.contains("@")) {
            throw new IllegalArgumentException("Email invalido: " + direccion);
        }
    }

    // Factory method: controla la creacion
    public static Email de(String direccion) {
        return new Email(direccion);
    }

    // Normalizacion con factory
    public static Email normalizado(String direccion) {
        return new Email(direccion.toLowerCase().trim());
    }
}
```

### Records como DTOs

Los records son ideales para transportar datos entre capas:

```java
// DTO de respuesta API:
public record RespuestaUsuario(
    long id,
    String nombre,
    String email,
    boolean activo
) { }

// En el controlador:
public RespuestaUsuario convertir(Usuario usuario) {
    return new RespuestaUsuario(
        usuario.getId(),
        usuario.getNombre(),
        usuario.getEmail(),
        usuario.isActivo()
    );
}
```

### Records locales (Java 16+)

Los records pueden declararse dentro de un metodo:

```java
public double calcularPromedio(List<Double> notas) {
    record NotaAlumno(String alumno, double nota) { }

    List<NotaAlumno> conNombres = notas.stream()
        .map(nota -> new NotaAlumno("Alumno", nota))
        .toList();

    return conNombres.stream()
        .mapToDouble(NotaAlumno::nota)
        .average()
        .orElse(0);
}
```

### Records en colecciones

```java
public record Producto(String nombre, double precio) { }

List<Producto> productos = List.of(
    new Producto("Laptop", 1200.0),
    new Producto("Mouse", 25.5)
);

// Buscar por igualdad de valor:
boolean existe = productos.contains(new Producto("Mouse", 25.5));  // true!

// Como claves de HashMap (gracias a equals/hashCode generados):
Map<Producto, Integer> stock = new HashMap<>();
stock.put(new Producto("Laptop", 1200.0), 5);
stock.put(new Producto("Mouse", 25.5), 20);
// Puedes buscar por valor:
stock.get(new Producto("Laptop", 1200.0));  // 5
```

---

## NIVEL 3 - Senior

### Value Objects en DDD

En Domain-Driven Design, un Value Object es un objeto inmutable definido por sus atributos, no por una identidad. Un record es la implementacion ideal:

```java
// Value Object: no tiene identidad propia, dos instancias con mismo valor SON iguales
public record Dinero(double cantidad, String moneda) {

    public Dinero {
        if (cantidad < 0) {
            throw new IllegalArgumentException("La cantidad no puede ser negativa");
        }
        if (moneda == null || moneda.isBlank()) {
            throw new IllegalArgumentException("Moneda requerida");
        }
    }

    // Operaciones que devuelven NUEVOS value objects (inmutabilidad)
    public Dinero sumar(Dinero otro) {
        if (!this.moneda.equals(otro.moneda)) {
            throw new IllegalArgumentException("No puedes sumar monedas distintas");
        }
        return new Dinero(this.cantidad + otro.cantidad, this.moneda);
    }

    public Dinero multiplicar(double factor) {
        return new Dinero(this.cantidad * factor, this.moneda);
    }
}

// Uso:
Dinero sueldo = new Dinero(2000, "EUR");
Dinero aumento = new Dinero(500, "EUR");
Dinero total = sueldo.sumar(aumento);   // Dinero[2500.0, EUR] — nuevo objeto
// sueldo sigue siendo 2000 — nunca se modifica
```

### Inmutabilidad profunda (referencias mutables)

Un record es inmutable a nivel de campos, pero si contiene una referencia mutable, el contenido puede cambiar:

```java
// PROBLEMA: la lista interna se puede modificar
public record Pedido(String id, List<String> productos) { }

List<String> lista = new ArrayList<>();
lista.add("Laptop");
Pedido p = new Pedido("P-1", lista);
lista.add("Mouse");               // El record se modifica desde fuera!
p.productos().add("Teclado");     // Y tambien desde el accessor!

// SOLUCION: copia defensiva en el compact constructor
public record Pedido(String id, List<String> productos) {
    public Pedido {
        // Copia inmutable: nadie puede modificar la lista
        productos = List.copyOf(productos);
    }

    @Override
    public List<String> productos() {
        return productos;  // Ya es inmutable, seguro devolverla
    }
}
```

### Record patterns (Java 21)

Los records funcionan perfecto con pattern matching:

```java
record Punto(int x, int y) { }

Object objeto = new Punto(3, 4);

// Pattern matching tradicional:
if (objeto instanceof Punto p) {
    System.out.println("X: " + p.x() + ", Y: " + p.y());
}

// Destructuring (extrae los componentes directamente):
if (objeto instanceof Punto(int x, int y)) {
    System.out.println("X: " + x + ", Y: " + y);
}

// Con switch expression:
String descripcion = switch (objeto) {
    case Punto(int x, int y) -> "Punto en (" + x + ", " + y + ")";
    case null -> "Nulo";
    default -> "Otro tipo";
};
```

### Records anidados con pattern matching

```java
record Coordenada(int x, int y) { }
record Rectangulo(Coordenada esquina, int ancho, int alto) { }

Object figura = new Rectangulo(new Coordenada(1, 2), 10, 20);

// Destructuring anidado:
if (figura instanceof Rectangulo(Coordenada(int x, int y), int ancho, int alto)) {
    System.out.println("Esquina: (" + x + "," + y + "), " + ancho + "x" + alto);
}
```

### Records con sealed hierarchies

```java
sealed interface Figura permits Circulo, Cuadrado {}
record Circulo(double radio) implements Figura {}
record Cuadrado(double lado) implements Figura {}

double area(Figura f) {
    return switch (f) {
        case Circulo c -> Math.PI * c.radio() * c.radio();
        case Cuadrado c -> c.lado() * c.lado();
    };
    // El compilador sabe que no falta ningun caso (exhaustivo)
}
```

### Serializacion segura

Los records son la forma mas segura de serializar en Java:

```java
// Los records se serializan sin problemas:
// - No ejecutan constructores en deserializacion (evita ataques)
// - El estado siempre es consistente con el constructor
// - No necesitan readResolve() ni readObject() manual

import java.io.*;

public record Configuracion(String nombre, int version) implements Serializable { }

// Serializar:
Configuracion cfg = new Configuracion("app", 3);
try (var out = new ObjectOutputStream(new FileOutputStream("cfg.ser"))) {
    out.writeObject(cfg);
}

// Deserializar:
try (var in = new ObjectInputStream(new FileInputStream("cfg.ser"))) {
    Configuracion leida = (Configuracion) in.readObject();
    System.out.println(leida);  // Configuracion[nombre=app, version=3]
}
```

---

# METODO PROFUNDO (Curso completo de Records y Value Objects)

## 1. Origen y contexto

Los records llegaron como preview en Java 14 (JEP 359), segunda preview en Java 15 (JEP 384) y **finales en Java 16 (JEP 395)**.

### El problema que resuelven

Antes de records, modelar un "objeto de datos" requería mucho codigo repetitivo:

```java
// ~50 lineas de boilerplate para un objeto de solo datos:
public class Direccion {
    private final String calle;
    private final String ciudad;
    private final String codigoPostal;

    public Direccion(String calle, String ciudad, String codigoPostal) {
        this.calle = calle;
        this.ciudad = ciudad;
        this.codigoPostal = codigoPostal;
    }

    public String getCalle() { return calle; }
    public String getCiudad() { return ciudad; }
    public String getCodigoPostal() { return codigoPostal; }

    // equals, hashCode, toString... todos escritos a mano
    // Y ademas: errores comunes al escribirlos mal
}

// Con record (3 lineas):
public record Direccion(String calle, String ciudad, String codigoPostal) { }
```

### Filosofia

Un record dice: "soy solo datos". El compilador entiende esta intencion y genera todo lo necesario de forma correcta y consistente. Es menos codigo y menos bugs.

---

## 2. Anatomia interna de un record

### Que pasa cuando declaras `record Punto(int x, int y) { }`

| Se genera | Detalle |
|-----------|---------|
| Campos | `private final int x;` y `private final int y;` |
| Constructor | `Punto(int x, int y)` que asigna ambos |
| Accessors | `x()` y `y()` (sin `get`) |
| `equals` | Compara `x` y `y` de ambos objetos |
| `hashCode` | Combina los hashCode de `x` y `y` |
| `toString` | `Punto[x=0, y=0]` |

### Reglas de los campos

```java
public record Persona(String nombre, int edad) {
    // Campos adicionales estaticos: permitidos
    private static final int EDAD_MAXIMA = 150;

    // Campos de instancia adicionales: PROHIBIDOS
    // private String apodo;  // ERROR: no puedes añadir campos de instancia

    // Pero puedes calcular valores en el compact constructor o metodos:
    public String inicial() {
        return nombre.substring(0, 1);  // calculado bajo demanda
    }
}
```

### Reglas de herencia

```java
// Un record NO puede heredar de una clase:
// public record Empleado(String nombre) extends Persona { }  // ERROR

// Pero puede implementar interfaces:
public interface Identificable {
    long id();
}

public record Usuario(long id, String nombre) implements Identificable { }
// Ya implementa id() gracias al accessor generado automaticamente

// Y puede ser parte de una jerarquia sealed:
sealed interface Notificacion permits Email, SMS {}
record Email(String direccion, String mensaje) implements Notificacion {}
record SMS(String telefono, String mensaje) implements Notificacion {}
```

---

## 3. Compact constructor en detalle

### Por que se llama "compacto"

No repites los parametros: el compilador los conoce.

```java
public record Temperatura(double celsius) {

    // Constructor compacto: los parametros ya estan implicitos
    public Temperatura {
        if (celsius < -273.15) {
            throw new IllegalArgumentException("Temperatura absurda: " + celsius);
        }
    }

    // Los parametros se asignan DESPUES del bloque compacto
    // Las validaciones corren ANTES de la asignacion
}
```

### Lo que el compilador hace por ti

```java
// Tu escribes:
public Temperatura {
    if (celsius < -273.15) throw new IllegalArgumentException();
}

// El compilador genera (aproximadamente):
public Temperatura(double celsius) {
    if (celsius < -273.15) throw new IllegalArgumentException();
    this.celsius = celsius;  // Asignacion automatica al final
}
```

### Multiples constructores

```java
public record Rango(int minimo, int maximo) {

    public Rango {
        if (minimo > maximo) throw new IllegalArgumentException();
    }

    // Constructores adicionales DEBEN delegar al principal:
    public Rango(int maximo) {
        this(0, maximo);  // delegacion obligatoria
    }

    public Rango() {
        this(0, 100);
    }
}
```

---

## 4. equals, hashCode y toString generados

### Como funcionan

```java
public record Coordenada(int x, int y) { }

Coordenada a = new Coordenada(1, 2);
Coordenada b = new Coordenada(1, 2);
Coordenada c = new Coordenada(3, 4);

a.equals(b);   // true: mismo valor
a.equals(c);   // false: distinto valor
a.hashCode();  // = b.hashCode() (valores iguales => hash igual)

// toString:
System.out.println(a);  // Coordenada[x=1, y=2]
```

### La regla de oro de Value Objects

> Dos objetos son iguales si tienen el mismo valor, aunque sean instancias distintas.

```java
// Con equals generado, esto funciona naturalmente:
List<Coordenada> puntos = List.of(
    new Coordenada(1, 2),
    new Coordenada(5, 7)
);

puntos.contains(new Coordenada(5, 7));  // true (igualdad por valor)
```

### Por que importa para los Value Objects

La igualdad por valor es el corazon de los Value Objects. Sin equals generado, tendrias que escribirlo a mano y cometer errores sutiles:

```java
// ERROR clasico al escribir equals a mano:
// - Olvidar un campo
// - Usar == en vez de equals para campos
// - Romper la simetria (a.equals(b) != b.equals(a))
// - Romper hashCode (igualdad sin hash igual)
```

---

## 5. Inmutabilidad y Value Objects

### Que es un Value Object (DDD)

Segun Eric Evans en "Domain-Driven Design" (2003), un Value Object:
- **No tiene identidad propia** (dos "EUR 50" son el mismo valor)
- **Es inmutable** (nunca cambia, solo se reemplaza)
- **Se compara por valor** (equals/hashCode)
- **Se define por sus atributos**

### La diferencia con Entity

| Entity | Value Object |
|--------|-------------|
| Tiene identidad (id) | No tiene identidad |
| Es mutable (estado cambia) | Es inmutable |
| Igualdad por identidad (`==`) | Igualdad por valor (`equals`) |
| Ejemplo: un Usuario, un Pedido | Ejemplo: Dinero, Direccion, Email |

```java
// Entity: tiene id, su estado cambia
public class Usuario {
    private final long id;      // identidad
    private String nombre;      // mutable
    // equals() por id, NO por valor
}

// Value Object: inmutable, igualdad por valor
public record Email(String valor) { }
// new Email("a@b.com").equals(new Email("a@b.com"))  // true
```

### Modelo rico vs modelo anemico

```java
// MAL (modelo anemico): el VO solo guarda datos, la logica vive fuera
record DineroAnemico(double cantidad, String moneda) { }

class ServicioDinero {
    DineroAnemico sumar(DineroAnemico a, DineroAnemico b) {
        // logica fuera del VO
    }
}

// BIEN (modelo rico): el VO encapsula su propia logica
record Dinero(double cantidad, String moneda) {
    Dinero sumar(Dinero otro) {
        validarMismaMoneda(otro);
        return new Dinero(cantidad + otro.cantidad, moneda);
    }
    // la logica de sumar vive DENTRO del VO
}
```

---

## 6. Records vs clases normales

| Aspecto | Record | Clase normal |
|---------|--------|-------------|
| Boilerplate | Cero (todo generado) | Todo a mano |
| Inmutabilidad | Forzada | Opcional |
| equals/hashCode/toString | Generados correctamente | Hay que escribirlos |
| Herencia | No puede extender clases | Puede |
| Interfaces | Puede implementar | Puede |
| Campos de instancia extra | Prohibidos | Permitidos |
| Constructor | Generado (con compact) | A tu gusto |
| Mejor para | Datos inmutables (VO, DTO) | Objetos con comportamiento complejo |

### Cuando usar record

- DTOs y respuestas de API
- Value Objects de dominio (Dinero, Email, Direccion)
- Claves compuestas de Map
- Params de metodos con muchos argumentos (record Parametros)
- Resultados de metodos (par de valores, tuplas)

### Cuando usar clase normal

- Objetos con estado mutable (una cuenta, un carrito)
- Objetos con comportamiento complejo y metodos de negocio
- Cuando necesitas herencia o campos de instancia adicionales
- Cuando el objeto tiene identidad (Entity)

---

## 7. Records y pattern matching (Java 21)

### Destructuring completo

```java
record Circulo(double radio) { }
record Rectangulo(double ancho, double alto) { }
record Triangulo(double base, double altura) { }

double calcularArea(Figura f) {
    return switch (f) {
        case Circulo(double r) -> Math.PI * r * r;
        case Rectangulo(double w, double h) -> w * h;
        case Triangulo(double b, double a) -> 0.5 * b * a;
    };
}
```

### Pattern anidado

```java
record Punto(int x, int y) { }
record Linea(Punto inicio, Punto fin) { }

// Extrae todo el camino hasta los ints:
if (linea instanceof Linea(Punto(int x1, int y1), Punto(int x2, int y2))) {
    System.out.println("(" + x1 + "," + y1 + ") -> (" + x2 + "," + y2 + ")");
}
```

### Guardas con when

```java
record Temperatura(double celsius) { }

String estado = switch (temp) {
    case Temperatura t when t.celsius() < 0 -> "Congelado";
    case Temperatura t when t.celsius() < 20 -> "Frio";
    case Temperatura t when t.celsius() < 30 -> "Templado";
    case Temperatura t when t.celsius() >= 30 -> "Caliente";
};
```

---

## 8. Records con generics

```java
// Records genericos: muy utiles para resultados y pares
public record Par<K, V>(K clave, V valor) { }

Par<String, Integer> p1 = new Par<>("edad", 30);
Par<String, Integer> p2 = new Par<>("edad", 30);
System.out.println(p1.equals(p2));  // true (mismo tipo y valor)

// Resultado de una operacion:
public record Resultado<T>(boolean exito, T dato, String error) { }

Resultado<Integer> ok = new Resultado<>(true, 42, null);
Resultado<Integer> fallo = new Resultado<>(false, null, "No encontrado");

// Record generico con restriccion:
public record Cajon<T extends Comparable<T>>(T contenido) { }
```

---

## 9. Records y el patrón copyWith

Los records son inmutables, asi que para "modificar" uno, creas una copia con cambios:

```java
public record Usuario(String nombre, String email, int edad) {

    // Metodos de "copia con cambio" — el patron copyWith
    public Usuario conNombre(String nuevoNombre) {
        return new Usuario(nuevoNombre, email, edad);
    }

    public Usuario conEmail(String nuevoEmail) {
        return new Usuario(nombre, nuevoEmail, edad);
    }

    public Usuario cumplirAnios() {
        return new Usuario(nombre, email, edad + 1);
    }
}

// Uso:
Usuario ana = new Usuario("Ana", "ana@mail.com", 29);
Usuario ana30 = ana.cumplirAnios();        // nueva instancia con edad 30
Usuario anaCasa = ana.conEmail("ana@home.com");  // nueva con email cambiado
// ana sigue intacta
```

---

## 10. Limitaciones y alternativas

### Limitaciones de los records

1. **No pueden heredar clases** — solo interfaces
2. **No pueden tener campos de instancia extra** — solo los componentes
3. **No pueden tener setters** — inmutables por diseno
4. **No lazy initialization** — todo se calcula al construir
5. **Java 16+** — no disponible en versiones anteriores

### Alternativas cuando un record no alcanza

```java
// 1. Cuando necesitas estado mutable: clase normal
public class Sesion {
    private String token;
    private int intentos;
    public void incrementarIntentos() { intentos++; }
}

// 2. Cuando necesitas mas campos (metadatos internos): clase normal
public class CacheEntry {
    private final String clave;
    private volatile Object valor;  // puede cambiar
    private final long creadoEn;
}

// 3. Lombok @Value como alternativa (misma idea, mas flexibilidad)
// @Value class Punto { int x; int y; }
```

---

## Errores Comunes

> Pensar que `new Usuario("Ana", 30).equals(new Usuario("Ana", 30))` es `false`. En records, la igualdad es por valor: dos records con los mismos componentes son iguales.

> Confundir el accessor de record (`nombre()`) con un getter tradicional (`getNombre()`). Los records NO generan metodos `get`.

> Guardar referencias mutables en records sin copia defensiva. Un `List<String>` dentro de un record puede modificarse desde fuera si no usas `List.copyOf()`.

> Intentar añadir campos de instancia a un record. Solo permite campos estaticos. Si necesitas estado mutable, usa una clase normal.

> Olvidar que el compact constructor recibe los componentes como parametros implicitos. No debes repetir los parametros ni usar `this.campo = campo` manualmente (la asignacion es automatica al final).

> Hacer que un record herede de una clase. Los records no pueden extender clases, solo implementar interfaces.

> Usar records para objetos con identidad (Entities). Un record que cambia de estado no tiene sentido: es un Value Object o un DTO, no una Entity.

---

## Buenas Practicas

1. **Usa records para Value Objects y DTOs** — datos inmutables que se comparan por valor.
2. **Valida en el compact constructor** — nunca dejes que un record exista en estado invalido.
3. **Copia defensiva de referencias mutables** — `List.copyOf()`, `Map.copyOf()` para componentes tipo coleccion.
4. **Usa static factory methods** — con nombres descriptivos y logica de normalizacion.
5. **Pon la logica del VO dentro del record** — modelo rico, no anemico.
6. **Usa `copyWith`** para "modificar" records inmutables.
7. **Combina con pattern matching** (Java 21) — destructuring de records en switch y instanceof.
8. **Usa records como claves de Map** — gracias a equals/hashCode generados.
9. **No abuses**: si el objeto necesita estado mutable o comportamiento complejo, usa clase normal.
10. **Seria records** — la serializacion es segura por diseño (sin constructores en deserializacion).

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] — componentes de tipos primitivos y referencia
- [[06 - Atributos y Campos]] — campos private final vs atributos normales
- [[07 - Constructores y this]] — compact constructor y constructores delegados
- [[08 - Instanciacion y new]] — como se crean los records
- [[10 - Metodos de Instancia]] — accessors como metodos de instancia
- [[21 - Getters y Setters]] — por que los records NO usan get/set sino nombre()
- [[22 - Separacion de Responsabilidades]] — VO vs Entity, modelo rico vs anemico
- [[26 - Private en Profundidad]] — campos private final generados
- [[30 - Principio fundamental de Java (y POO)]] — objetos definidos por valor
- [[34 - Clases Wrapper (envolventes)]] — inmutabilidad compartida con los Wrapper
- [[36 - Switch expression]] — pattern matching exhaustivo con sealed records
- [[99 - Glosario Rapido]] — terminos VO, DTO, Entity

---

## Tags
`#java #fundamentos #record #value-object #inmutabilidad #ddd #java16 #java21 #poo`