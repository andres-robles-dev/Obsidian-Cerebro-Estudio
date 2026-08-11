---
tags: [java, fundamentos, enum, catalogo, patron, lookup, inmutabilidad, profundidad]
---

# 44 - Patron Catalogo Inmutable con Enum

Un catalogo es un conjunto FIJO de datos que se consulta constantemente: monedas, categorias de producto, tipos de plan, codigos de pais, estados de pedido. El patron catalogo inmutable usa un enum con campos como fuente unica de verdad, mas metodos de busqueda (lookup) seguros y tipo-estrictos. Combina enum + static + streams + Optional + equalsIgnoreCase.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### El problema que resuelve

```java
// SIN catalogo: datos sueltos por todo el codigo, facil equivocarse
public static final String MONEDA_USD = "USD";
public static final String MONEDA_EUR = "EUR";
public static final double MONEDA_USD_FACTOR = 1.0;
public static final double MONEDA_EUR_FACTOR = 0.92;
// ... cada constante vive en un sitio distinto, sin validacion

// CON catalogo: los datos viven en UN solo lugar, con tipo y validacion
public enum Moneda {
    USD("Dolar", 1.0),
    EUR("Euro", 0.92);

    private final String nombre;
    private final double factor;

    Moneda(String nombre, double factor) {
        this.nombre = nombre;
        this.factor = factor;
    }

    public String getNombre() {
        return nombre;
    }

    public double getFactor() {
        return factor;
    }
}

Moneda m = Moneda.EUR;
System.out.println(m.getNombre() + " -> " + m.getFactor());
```

### Por que "inmutable"?

- Los valores del enum se crean UNA vez al cargar la clase y NUNCA cambian
- Los campos son `final`: imposible modificarlos
- Todo el programa comparte el MISMO catalogo (una sola fuente de verdad)
- El compilador solo acepta valores validos: `Moneda.EUR`, nunca `"eur"` mal escrito

### Recorrer el catalogo con values()

```java
// values() devuelve TODAS las constantes:
for (Moneda moneda : Moneda.values()) {
    System.out.println(moneda + " = " + moneda.getFactor());
}
// USD = 1.0
// EUR = 0.92
```

---

## NIVEL 2 - Mid

### Busqueda segura: el metodo lookup (fromCodigo)

```java
public enum TipoPlan {
    BASICO("BAS", "Plan basico", 9.99),
    PREMIUM("PRE", "Plan premium", 19.99),
    EMPRESA("EMP", "Plan empresa", 49.99);

    private final String codigo;
    private final String descripcion;
    private final double precio;

    TipoPlan(String codigo, String descripcion, double precio) {
        this.codigo = codigo;
        this.descripcion = descripcion;
        this.precio = precio;
    }

    // LOOKUP: busca una constante por su codigo
    public static Optional<TipoPlan> fromCodigo(String codigo) {
        for (TipoPlan plan : values()) {
            if (plan.codigo.equals(codigo)) {
                return Optional.of(plan);
            }
        }
        return Optional.empty();
    }

    public String getCodigo() {
        return codigo;
    }

    public String getDescripcion() {
        return descripcion;
    }

    public double getPrecio() {
        return precio;
    }
}

// Uso:
Optional<TipoPlan> plan = TipoPlan.fromCodigo("PRE");
// Devuelve Optional: la entrada puede ser invalida (tema 41)

TipoPlan elegido = plan.orElseThrow(
    () -> new IllegalArgumentException("Codigo de plan invalido: PRE")
);
```

### Busqueda moderna con stream (filter + findFirst)

```java
// La misma busqueda con la Stream API (tema 43):
public static Optional<TipoPlan> fromCodigo(String codigo) {
    return java.util.Arrays.stream(values())
        .filter(plan -> plan.codigo.equals(codigo))
        .findFirst();
}

// Busquedas multiples criterios son directas:
public static Optional<TipoPlan> porPrecioMenorOIgual(double maximo) {
    return java.util.Arrays.stream(values())
        .filter(plan -> plan.precio <= maximo)
        .findFirst();
}
```

### Busqueda tolerante con equalsIgnoreCase

```java
// Cuando la entrada viene del USUARIO, puede llegar en cualquier formato:
// "pre", "PRE", "Pre", " pre " -> todas deben encontrar PREMIUM

public static Optional<TipoPlan> fromTextoUsuario(String texto) {
    if (texto == null || texto.isBlank()) {
        return Optional.empty();
    }
    String normalizado = texto.strip();
    return java.util.Arrays.stream(values())
        .filter(plan -> plan.codigo.equalsIgnoreCase(normalizado)   // case-insensitive
                     || plan.name().equalsIgnoreCase(normalizado))  // tambien por nombre
        .findFirst();
}
```

---

## NIVEL 3 - Senior

### El catalogo completo: datos + busquedas + validaciones

```java
public enum Pais {
    COLOMBIA("CO", "Colombia", 57, List.of("peso")),
    ESPANA("ES", "Espana", 34, List.of("euro")),
    MEXICO("MX", "Mexico", 52, List.of("peso")),
    ARGENTINA("AR", "Argentina", 54, List.of("peso"));

    private final String codigoISO;
    private final String nombre;
    private final int codigoTelefonico;
    private final List<String> monedas;      // coleccion inmutable con List.of

    Pais(String codigoISO, String nombre, int codigoTelefonico, List<String> monedas) {
        this.codigoISO = codigoISO;
        this.nombre = nombre;
        this.codigoTelefonico = codigoTelefonico;
        this.monedas = monedas;              // List.of ya la hizo inmutable
    }

    // Lookup principal por codigo ISO (entrada confiable: BD, API)
    public static Optional<Pais> fromCodigoISO(String codigo) {
        return java.util.Arrays.stream(values())
            .filter(p -> p.codigoISO.equalsIgnoreCase(codigo))
            .findFirst();
    }

    // Lookup tolerante por nombre (entrada de usuario)
    public static Optional<Pais> buscarPorNombre(String texto) {
        if (texto == null || texto.isBlank()) {
            return Optional.empty();
        }
        String normalizado = texto.strip().toLowerCase();
        return java.util.Arrays.stream(values())
            .filter(p -> p.nombre.equalsIgnoreCase(normalizado)
                      || p.name().equalsIgnoreCase(normalizado))
            .findFirst();
    }

    // Busqueda por atributo: todos los paises que usan una moneda
    public static List<Pais> queUsanMoneda(String moneda) {
        return java.util.Arrays.stream(values())
            .filter(p -> p.monedas.contains(moneda))
            .toList();
    }

    // Valida si un codigo existe sin lanzar:
    public static boolean existe(String codigo) {
        return fromCodigoISO(codigo).isPresent();
    }

    public String getCodigoISO() {
        return codigoISO;
    }

    public String getNombre() {
        return nombre;
    }

    public int getCodigoTelefonico() {
        return codigoTelefonico;
    }

    public List<String> getMonedas() {
        return monedas;
    }
}
```

### El lookup rapido: Map estatico (cuando hay MUCHAS constantes)

```java
public enum EstadoPedido {
    CREADO("C"),
    PAGADO("P"),
    ENVIADO("E"),
    ENTREGADO("D"),
    CANCELADO("X");

    private final String codigo;

    EstadoPedido(String codigo) {
        this.codigo = codigo;
    }

    // Map estatico construido UNA vez (tema 28): O(1) en vez de recorrer
    private static final Map<String, EstadoPedido> POR_CODIGO = java.util.Arrays.stream(values())
        .collect(Collectors.toUnmodifiableMap(EstadoPedido::getCodigo, p -> p));

    public static Optional<EstadoPedido> fromCodigo(String codigo) {
        return Optional.ofNullable(POR_CODIGO.get(codigo));
    }

    public String getCodigo() {
        return codigo;
    }
}

// Uso en un servicio:
EstadoPedido estado = EstadoPedido.fromCodigo("E")
    .orElseThrow(() -> new EstadoInvalidoException("E"));
```

---

# METODO PROFUNDO (Curso completo del patron catalogo)

## 1. El problema que el patron resuelve

| Sin patron (anti-patrones) | Problema |
|----------------------------|----------|
| `static final` sueltos en varias clases | Datos dispersos, sin fuente unica |
| Strings como codigos sin validar | `"eur"` vs `"EUR"` vs `"Euro"` rompen el programa |
| Switch con magic numbers por todo el codigo | Cambiar un valor = buscar TODAS las copias |
| Map mutable global (`HashMap` estatico) | Cualquiera puede mutar el catalogo |
| Consultar una BD para datos que nunca cambian | Lento e innecesario |

## 2. La estructura del patron

```
1. enum con constantes = los ITEMS del catalogo
2. campos final = los DATOS de cada item (codigo, nombre, precio, atributos)
3. constructor private = solo las constantes lo usan
4. getters = lectura segura (inmutable, sin setters)
5. metodos static de busqueda = fromCodigo, buscarPorNombre, etc.
6. Optional como retorno = la busqueda puede fallar (tema 41)
7. streams + filter + findFirst = busquedas expresivas (tema 43)
```

```java
public enum NombreCatalogo {
    ITEM1(valorDato1),
    ITEM2(valorDato2);

    private final TipoDato dato1;

    NombreCatalogo(TipoDato dato1) {
        this.dato1 = dato1;
    }

    public static Optional<NombreCatalogo> fromDato(TipoDato valor) {
        return java.util.Arrays.stream(values())
            .filter(item -> item.dato1.equals(valor))
            .findFirst();
    }

    public TipoDato getDato1() {
        return dato1;
    }
}
```

## 3. Lookup por codigo (la busqueda mas comun)

```java
// Dos estrategias:
// A) Recorrer values() con stream: simple, O(n), perfecto para < 20 items
// B) Map estatico: O(1), mejor para catalogos grandes o criticos

// A) Stream:
public static Optional<TipoPlan> fromCodigo(String codigo) {
    return Arrays.stream(values())
        .filter(p -> p.codigo.equals(codigo))
        .findFirst();
}

// B) Map estatico inmutable (construido una vez):
private static final Map<String, TipoPlan> POR_CODIGO = Arrays.stream(values())
    .collect(Collectors.toUnmodifiableMap(TipoPlan::getCodigo, p -> p));

public static Optional<TipoPlan> fromCodigo(String codigo) {
    return Optional.ofNullable(POR_CODIGO.get(codigo));
}
```

## 4. equalsIgnoreCase: busquedas tolerantes a entrada de usuario

```java
// Los codigos internos son exactos ("PRE"); la entrada del usuario no lo es.
// equalsIgnoreCase hace la comparacion sin importar mayusculas/minusculas:

.filter(p -> p.codigo.equalsIgnoreCase(entrada))

// Combinalo con normalizacion para entradas con espacios:
String normalizado = entrada.strip().toLowerCase();
```

## 5. Optional como retorno de busqueda (puente al 41)

```java
// La busqueda PUEDE no encontrar nada -> Optional, nunca null:

public static Optional<EstadoPedido> fromCodigo(String codigo) {
    return Optional.ofNullable(POR_CODIGO.get(codigo));
}

// El llamador decide que hacer:
EstadoPedido estado = EstadoPedido.fromCodigo(texto)
    .orElse(EstadoPedido.CREADO);                              // default
// o
EstadoPedido estado = EstadoPedido.fromCodigo(texto)
    .orElseThrow(() -> new EstadoInvalidoException(texto));    // exigir
```

## 6. Metodos static de utilidad en el catalogo (puente al 28)

```java
// Todo el catalogo es estatico: se accede sin instancias

public static boolean existe(String codigo) { ... }            // validar sin lanzar
public static List<TipoPlan> activos() { ... }                 // subconjuntos
public static TipoPlan masCaro() { ... }                       // agregaciones
public static String codigosUnidos() { ... }                   // para mensajes/API
public static void imprimirCatalogo() { ... }                  // documentacion

// Recuerda (tema 28): static final Map + Collectors.toUnmodifiableMap
// garantiza que NADIE pueda mutar el catalogo despues de crearlo
```

## 7. Catalogo vs otras opciones (cuando usar cada una)

| Opcion | Cuando usarla | Cuanto cambia |
|--------|---------------|---------------|
| **Enum catalogo** | Datos FIJOS conocidos en compilacion, pocos items | Nunca o raramente |
| Map estatico inmutable | Catalogos grandes o cargados de config/BD | En arranque |
| Clase con static final | Constantes simples sin datos asociados | Nunca |
| Tabla en BD | Datos que CAMBIAN en produccion (precios, promos) | Frecuente |
| Properties/JSON externo | Configuracion por entorno | En despliegue |

```java
// La regla de oro:
// Si los datos se conocen al escribir el codigo y no cambian -> enum.
// Si cambian en produccion sin recompilar -> BD o archivo de config.
```

## 8. Buenas practicas del patron

1. **Campos final siempre**: el catalogo es inmutable por definicion.
2. **Colecciones internas inmutables**: usa `List.of` / `Collectors.toUnmodifiableMap`.
3. **Lookup devuelve Optional**, nunca null ni excepcion por defecto.
4. **Busquedas por codigo exacto** (internas) y **tolerantes** (usuario) separadas.
5. **Normaliza la entrada** (strip, toLowerCase) antes de comparar.
6. **No uses ordinal()** para busquedas: si reordenas el enum, todo cambia (tema 33).
7. **EnumSet/EnumMap** para operar con subconjuntos del catalogo (tema 33).
8. **Documenta los codigos validos** en el Javadoc o con un metodo que los liste.

## 9. Checklist final

- [ ] Los datos del catalogo son FIJOS (si cambian en produccion, usa BD)
- [ ] Campos `final` + sin setters = inmutable
- [ ] Metodo `fromCodigo` / `buscarPorNombre` con Optional como retorno
- [ ] Busquedas con `stream().filter().findFirst()` o Map estatico O(1)
- [ ] `equalsIgnoreCase` + normalizacion para entradas de usuario
- [ ] `orElse` / `orElseThrow` en el llamador
- [ ] Colecciones internas inmutables (List.of, toUnmodifiableMap)
- [ ] Sin ordinal() en logica de busqueda

---

## Errores Comunes

> Devolver `null` cuando la busqueda no encuentra: el llamador recibe NPE en cualquier lado. Devuelve Optional y deja que el llamador decida.

> Codigos comparados con `equals` case-sensitive cuando la entrada es del usuario: `"pre"` no encuentra `"PRE"`. Usa equalsIgnoreCase y normaliza.

> Guardar el catalogo en un `HashMap` mutable estatico: cualquier metodo puede corromperlo. Usa `Collectors.toUnmodifiableMap` o `Map.copyOf`.

> Usar `ordinal()` como codigo: si agregas o reordenas constantes, todos los datos guardados cambian de significado.

> Campos no finales en el enum: rompe la inmutabilidad que define el patron.

> Catalogos gigantes en un solo enum: si pasa de ~20-30 items o los datos cambian en produccion, evalua BD/config.

> No normalizar entrada (`strip()`, `toLowerCase()`) antes de comparar: los espacios y mayusculas rompen la busqueda.

> Lanzar excepcion desde el lookup en vez de Optional: el lookup es una BUSQUEDA (puede no haber resultado), no una operacion que deba garantizarlo.

---

## Conexiones

- [[28 - Static en Profundidad]] - Metodos static, Map estatico con values() y name()
- [[33 - enum en Profundidad]] - Campos, constructor, metodos automaticos, EnumSet/EnumMap
- [[41 - Optional en Profundidad]] - Optional como retorno de las busquedas
- [[42 - Lambda en Profundidad]] - Las lambdas dentro de filter y map del lookup
- [[43 - Stream API en Profundidad]] - filter + findFirst + toList en las busquedas
- [[38 - List.of y Colecciones Inmutables]] - Colecciones internas inmutables del catalogo
- [[40 - Manejo de Excepciones]] - Excepciones de dominio cuando la ausencia es un error
- [[Plantillas de Codigo/16 - enum con Constructor y Metodos]] - La base del catalogo

---

## Tags
`#java #fundamentos #enum #catalogo #patron #lookup #inmutabilidad #funcional`