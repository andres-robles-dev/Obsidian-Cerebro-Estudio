---
tags: [java, fundamentos, optional, null, null-safety, java-moderno, profundidad]
---

# 41 - Optional en Profundidad

`Optional<T>` es un contenedor que puede tener un valor o estar vacio. Es la forma moderna de Java para manejar la AUSENCIA de un valor sin usar `null` peligroso: te obliga a decidir que hacer cuando no hay valor (default, lanzar error, o ignorar). Todo empezó con Java 8.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es Optional?

Es una caja que puede contener un valor... o estar vacia. En vez de devolver `null` (que revienta con NullPointerException si lo usas sin cuidado), un metodo devuelve `Optional` y el que llama SABE que debe revisar si hay valor.

```java
import java.util.Optional;

public class EjemploBasico {
    public static void main(String[] args) {
        Optional<String> conValor = Optional.of("Hola");   // caja con valor
        Optional<String> vacio = Optional.empty();         // caja vacia

        System.out.println(conValor.isPresent());   // true
        System.out.println(vacio.isPresent());      // false
    }
}
```

### La regla mental mas importante

```java
// SIN Optional: retorno null = bomba de tiempo
public String buscarNombre(long id) {
    return id < 1 ? null : "Ana";   // quien llama puede olvidar revisar null
}

// CON Optional: el llamador NO PUEDE olvidar que puede no haber valor
public Optional<String> buscarNombre(long id) {
    return id < 1 ? Optional.empty() : Optional.of("Ana");
}
```

### Las 3 formas de crearlo y la lectura basica

```java
Optional.of(valor);            // NO acepta null (lanza NPE si le pasas null)
Optional.ofNullable(valor);    // acepta null: si es null, queda vacio
Optional.empty();              // vacio directo

// Como se usa:
Optional<String> nombre = buscarNombre(5);

if (nombre.isPresent()) {          // hay valor?
    System.out.println(nombre.get());
}

String valor = nombre.orElse("Sin nombre");   // valor o default
```

### Por que NO usar null directamente

| Problema con null | Solucion con Optional |
|-------------------|----------------------|
| `null` no dice nada: podria ser "no hay resultado" o un bug | Optional comunica explicitamente la ausencia |
| Olvidas revisar y explota NullPointerException | `orElse`, `ifPresent` y `orElseThrow` te obligan a decidir |
| Codigo lleno de `if (x != null)` | Pipelines encadenados sin ifs |

---

## NIVEL 2 - Mid

### Los metodos que resuelven el 95% de los casos

```java
Optional<String> nombre = buscarNombre(5);

// Hacer algo solo si hay valor:
nombre.ifPresent(n -> System.out.println("Hola " + n));

// Valor por defecto:
String a = nombre.orElse("Invitado");

// Valor por defecto calculado (solo se ejecuta si esta vacio):
String b = nombre.orElseGet(() -> generarNombre());

// Exigir el valor: lanza excepcion si no hay:
String c = nombre.orElseThrow(() -> new IllegalStateException("Sin nombre"));

// Transformar el valor si existe:
String mayusculas = nombre.map(String::toUpperCase).orElse("---");
```

### Transformacion: map y filter

```java
// map: transforma el valor DENTRO de la caja (si hay valor)
Optional<Integer> longitud = nombre.map(String::length);

// filter: deja pasar el valor solo si cumple la condicion
Optional<String> corto = nombre.filter(n -> n.length() < 5);

// Encadenado:
String resultado = nombre
    .map(String::trim)
    .filter(n -> !n.isBlank())
    .orElse("Sin nombre");
```

### flatMap: para no crear cajas dentro de cajas

```java
// Problema: un metodo que devuelve Optional llamado dentro de map
Optional<Optional<String>> anidado = nombre.map(n -> buscarApodo(n));
//                                        ^^^^^^^^^^^^^^^^^^^^^^^^ caja de caja

// Solucion: flatMap aplana (la funcion ya devuelve Optional)
Optional<String> plano = nombre.flatMap(n -> buscarApodo(n));
```

### Optional como retorno (el uso correcto)

```java
public class RepositorioUsuarios {
    private final List<Usuario> usuarios = new ArrayList<>();

    // AUSENCIA esperable -> Optional
    public Optional<Usuario> buscarPorEmail(String email) {
        return usuarios.stream()
            .filter(u -> u.getEmail().equals(email))
            .findFirst();          // devuelve Optional<Usuario>
    }
}

// El llamador decide:
Usuario u = repo.buscarPorEmail("ana@mail.com")
    .orElseThrow(() -> new UsuarioNoEncontradoException("ana@mail.com"));
```

---

## NIVEL 3 - Senior

### Pipelines completos (null-safety end-to-end)

```java
// Cadena de transformaciones opcionales sin un solo if:
int descuento = cliente
    .obtenerCategoria()                       // Optional<Categoria>
    .flatMap(Categoria::obtenerNivel)         // Optional<Nivel>
    .map(Nivel::getDescuento)                 // Optional<Integer>
    .orElse(0);                               // 0 si falta algo en la cadena

// Con valores que dependen unos de otros (or):
Integer resultado = Optional.ofNullable(a)
    .or(() -> Optional.ofNullable(b))         // si a es null, prueba b (Java 9+)
    .orElse(0);
```

### Optional primitivos: OptionalInt, OptionalLong, OptionalDouble

```java
// Evitan el autoboxing innecesario
OptionalInt opt = OptionalInt.of(42);
boolean hay = opt.isPresent();
int v = opt.orElse(0);

// Los devuelven metodos de streams numericos:
OptionalDouble promedio = puntuaciones.stream().average();
OptionalInt maximo = puntuaciones.stream().mapToInt(n -> n).max();
```

### Optional y streams

```java
// findFirst/findAny ya devuelven Optional:
Optional<String> primero = lista.stream()
    .filter(s -> s.startsWith("A"))
    .findFirst();

// Optional tambien es un Stream de 0 o 1 elemento:
nombre.stream().forEach(System.out::println);

// max/min/reduce con Optional:
Optional<Integer> mayor = lista.stream().max(Integer::compareTo);
```

### Anti-patrones que un senior NUNCA comete

```java
// 1. Optional.get() a ciegas:
Optional<String> opt = buscarNombre(0);
opt.get();   // NoSuchElementException si esta vacio. SIEMPRE usar orElse/orElseThrow

// 2. Optional como CAMPO de clase:
public class Usuario {
    private Optional<String> telefono;   // MAL: las colecciones de campos Optional son desastrosas
}

// 3. Optional como PARAMETRO:
public void saludar(Optional<String> nombre) { }   // MAL: el que llama puede pasar null igual

// 4. Optional en COlecciones:
List<Optional<String>> items;   // MAL: mejor List<String> y decidir la ausencia al crear

// 5. Optional.of(valorQuePuedeSerNull):
Optional.of(podriaSerNull());   // MAL: lanza NPE. Usa ofNullable
```

---

# METODO PROFUNDO (Curso completo de Optional)

## 1. Anatomia de Optional

```java
// Optional es una clase final (no se extiende) con un unico campo:
public final class Optional<T> {
    private static final Optional<?> EMPTY = new Optional<>();  // instancia unica vacia
    private final T value;                                       // null = vacio

    // Internamente un Optional VACIO es simplemente un Optional con value = null
    // Todo el diseño gira en torno a: "no exponer nunca el null directamente"
}
```

- **Value-based**: dos Optional con el mismo contenido son intercambiables; no se debe comparar con `==`
- No implementa `Serializable` de forma segura para campos, otra razon para no usarlo como campo
- Su diseño premia los **metodos de terminal** (orElse, ifPresent) sobre el acceso directo

## 2. Los creadores

| Metodo | Acepta null? | Cuando usarlo |
|--------|-------------|---------------|
| `Optional.of(valor)` | NO (lanza NPE) | Cuando SABES que el valor existe si o si |
| `Optional.ofNullable(valor)` | SI (queda vacio) | Cuando NO sabes si llega null (BD, JSON, API) |
| `Optional.empty()` | - | Cuando no hay resultado |
| `Optional.of(...).or(...)` | - | Encadenar fuentes alternativas (Java 9+) |

```java
// La decision of vs ofNullable:
public Optional<String> nombreCompleto(Persona p) {
    // El nombre siempre existe por las reglas del dominio:
    return Optional.of(p.getNombre());
    // El apellido puede faltar:
    return Optional.ofNullable(p.getApellido());
}
```

## 3. Lectura segura: preguntar antes de usar

```java
Optional<String> opt = ...;

opt.isPresent();            // hay valor? (Java 8)
opt.isEmpty();              // esta vacio? (Java 11, la negacion de isPresent)

opt.ifPresent(valor -> ...);               // hacer algo si hay
opt.ifPresentOrElse(valor -> ..., () -> ...);  // ambas ramas (Java 9)
```

## 4. Valores por defecto: orElse vs orElseGet

La diferencia CRITICA: cuando se evalua el argumento.

```java
// orElse: el argumento se evalua SIEMPRE, aunque haya valor
String x = opt.orElse(metodoCostoso());   // metodoCostoso() corre aunque opt tenga valor

// orElseGet: solo se evalua si el Optional esta vacio
String y = opt.orElseGet(() -> metodoCostoso());   // corre SOLO si no hay valor

// Regla practica:
// - Valor simple o constante -> orElse
// - Valor que requiere calculo o llamada -> orElseGet

// orElseThrow: exige el valor o lanza
String z = opt.orElseThrow();                              // NoSuchElementException
String w = opt.orElseThrow(() -> new MiExcepcion("msg"));  // excepcion propia
```

## 5. Transformacion: map, filter, flatMap en pipeline

```java
public class EjemploPipeline {
    public static void main(String[] args) {
        Optional<Cliente> cliente = buscarCliente(7);

        String resumen = cliente
            .map(Cliente::getCuenta)                  // Optional<Cuenta>
            .filter(cuenta -> cuenta.getSaldo() > 0)  // descarta saldos negativos
            .map(Cuenta::getTitular)                  // Optional<String>
            .orElseThrow(() -> new IllegalStateException("Cliente sin cuenta valida"));

        System.out.println("Titular: " + resumen);
    }
}
```

- `map`: transforma valor -> valor nuevo (si hay valor)
- `filter`: descarta el valor si no cumple la condicion (queda vacio)
- `flatMap`: como map pero la funcion ya devuelve Optional (evita anidar)

## 6. Optional primitivos

```java
OptionalInt i = OptionalInt.of(5);
OptionalLong l = OptionalLong.of(100L);
OptionalDouble d = OptionalDouble.of(2.5);

// Solo tienen: isPresent, isEmpty, ifPresent, orElse, orElseGet, orElseThrow
// (NO tienen map/filter/flatMap: para eso vuelve al boxed)

int v = i.orElse(0);
d.ifPresent(x -> System.out.println(x));

// De donde salen: metodos de streams numericos
OptionalInt min = ints.stream().mapToInt(n -> n).min();
OptionalDouble promedio = doubles.stream().average();
```

## 7. Optional y streams

```java
// Los terminales que devuelven Optional:
Optional<String> primero = lista.stream()
    .filter(s -> s.startsWith("A"))
    .findFirst();          // tambien findAny()

Optional<String> mayor = lista.stream().max(Comparator.naturalOrder());
Optional<Integer> suma = lista.stream().reduce((a, b) -> a + b);

// Optional como stream de 0/1 elemento:
Optional<String> opt = buscar(5);
opt.stream().map(String::toUpperCase).forEach(System.out::println);

// Junto con streams: lista vacia vs Optional
public List<String> buscarTodos() { return List.of(); }       // 0 a N -> List
public Optional<String> buscarUno()  { return Optional.empty(); }  // 0 o 1 -> Optional
```

## 8. Reglas de API: cuando usarlo (regla de Oracle)

| Situacion | Que usar |
|-----------|----------|
| El resultado puede ser 0 o 1 valor | `Optional<T>` como RETORNO |
| El resultado puede ser 0 o N valores | Lista (vacia) |
| El valor es obligatorio (un error si falta) | Excepcion, no Optional |
| Campo de una clase | NO Optional: usa el tipo + null o un default |
| Parametro de un metodo | NO Optional: documenta que acepta null o usa overloading |
| Coleccion de Optionals | NO: diseña el modelo para que no exista |

```java
// MAL
public class Persona {
    private Optional<String> email;   // campo Optional = desastre de serializacion
}

// BIEN
public class Persona {
    private String email;             // null = sin email, se valida en la logica
}
```

## 9. Integraciones con el resto del cerebro

### Optional + Wrappers (tema 34/39)

```java
// El wrapper puede ser null; Optional hace el null explicito:
Integer posibleNull = baseDeDatos.getInt("edad");
int edad = Optional.ofNullable(posibleNull).orElse(0);
```

### Optional + Excepciones (tema 40)

```java
// AUSENCIA esperable -> Optional
// ERROR real -> excepcion
public Usuario buscarOError(long id) {
    return repo.buscar(id)
        .orElseThrow(() -> new UsuarioNoEncontradoException("id: " + id));
}
```

### Optional + Record (tema 37)

```java
// Los records son inmutables y optional encaja perfecto en metodos de dominio:
public record Pedido(long id, List<Item> items) {
    public Optional<Item> buscarItem(String codigo) {
        return items.stream()
            .filter(i -> i.codigo().equals(codigo))
            .findFirst();
    }
}
```

### Optional + Colecciones inmutables (tema 38)

```java
// List.of rechaza null: decide la ausencia con Optional al leer configuracion
List<String> config = Optional.ofNullable(getConfigExterna())
    .orElse(List.of());
```

## 10. Checklist final de null-safety

- [ ] Los metodos que pueden no tener resultado devuelven `Optional<T>`, nunca `null`
- [ ] `of` solo cuando el valor existe seguro; `ofNullable` cuando puede llegar null
- [ ] Nunca `get()` a ciegas: siempre `orElse`, `orElseGet` u `orElseThrow`
- [ ] `orElseGet` cuando el default requiere calculo; `orElse` para constantes
- [ ] `flatMap` cuando encadenas metodos que ya devuelven Optional
- [ ] Optional solo como RETORNO: no en campos, parametros ni colecciones
- [ ] Ausencia esperable = Optional; error real = excepcion
- [ ] Optional primitivos (`OptionalInt`) en streams numericos para evitar autoboxing
- [ ] Los campos nullables se manejan al llegar (ofNullable), no se guardan como Optional

---

## Errores Comunes

> `Optional.get()` sin verificar. Si esta vacio lanza NoSuchElementException. Siempre decide con orElse / orElseGet / orElseThrow.

> `Optional.of(valor)` con un valor que puede ser null. Lanza NullPointerException al instante. Si hay duda, usa `ofNullable`.

> `orElse(metodoCostoso())`: el metodo corre SIEMPRE aunque haya valor. Usa `orElseGet(() -> metodoCostoso())` si el default es caro.

> Optional como campo o parametro. Serializacion rota, equals extraño, y el llamador puede pasar null de todos modos.

> Map con funciones que devuelven Optional: crea `Optional<Optional<T>>`. Usa `flatMap`.

> Confundir ausencia con error: usar Optional donde corresponde una excepcion (o al reves).

> Lista de Optionals (`List<Optional<T>>`): modelo el problema mal. Mejor decidir la ausencia al crear los elementos.

> Comparar Optionals con `==`: compara referencias, no contenido. Usa `equals` (o mejor, los metodos terminales).

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Conversion moderna con Optional (Integer.parseInt seguro)
- [[06 - Atributos y Campos]] - El debate de Optional como atributo (y por que no)
- [[07 - Constructores y this]] - Constructor con Optional.ofNullable para campos opcionales
- [[10 - Metodos de Instancia]] - Metodos que devuelven Optional
- [[23 - Metodos - Parametros, Retorno y Return]] - Optional como retorno en vez de null
- [[31 - Curso Completo de .method()]] - Tabla completa de metodos de Optional (El protector contra null)
- [[34 - Clases Wrapper (envolventes)]] - Optional con Wrapper: null explicito
- [[37 - Record (y Value Objects)]] - Records con metodos que devuelven Optional
- [[38 - List.of y Colecciones Inmutables]] - Ausencia vs colecciones vacias (Optional vs List.of)
- [[39 - Tipos y Wrappers - La Guia Definitiva]] - Null explicito: Optional en APIs/BD
- [[40 - Manejo de Excepciones]] - Optional como alternativa a excepciones y orElseThrow
- [[Plantillas de Codigo/23 - Optional]] - Plantilla generica de Optional

---

## Tags
`#java #fundamentos #optional #null #null-safety #java-moderno #funcional #streams`