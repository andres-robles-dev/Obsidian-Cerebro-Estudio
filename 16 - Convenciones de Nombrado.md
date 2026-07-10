---
tags: [java, fundamentos, convenciones, naming, clean-code, estilo, legibilidad]
---

# 16 - Convenciones de Nombrado

## Concepto Central

Las **convenciones de nombrado** (naming conventions) son reglas de estilo **no obligatorias por el compilador** pero **universalmente adoptadas** en el ecosistema Java. Hacen que el código sea **legible, predecible y mantenible** por cualquier desarrollador. Java usa **CamelCase** (palabras unidas, mayúsculas en bordes) y **UPPER_SNAKE_CASE** (todo mayúsculas, underscore) según el tipo de elemento.

## Para Qué Sirve / Cuándo Usarlo

- Leer código ajeno sin sorpresa
- Evitar colisiones (clase vs variable vs constante)
- Herramientas automáticas (linters, IDEs, generadores) funcionan mejor
- Estándar de facto: Oracle Code Conventions, Google Java Style, Spring, Apache

## Sintaxis General: Patrones

| Elemento | Convención | Patrón Regex | Ejemplos |
|----------|------------|--------------|----------|
| **Clase / Interface / Enum / Record** | `PascalCase` (UpperCamelCase) | `^[A-Z][a-zA-Z0-9]*$` | `Usuario`, `HttpRequest`, `Color`, `Punto3D` |
| **Método / Variable / Parámetro / Campo instancia** | `camelCase` (lowerCamelCase) | `^[a-z][a-zA-Z0-9]*$` | `calcularTotal`, `nombreUsuario`, `maxIntentos` |
| **Constante (static final)** | `UPPER_SNAKE_CASE` | `^[A-Z][A-Z0-9_]*$` | `MAX_INTENTOS`, `URL_BASE`, `PI` |
| **Paquete** | `minúsculas.punto.separado` (dominio inverso) | `^[a-z]+(\.[a-z]+)*$` | `com.empresa.proyecto.modulo` |
| **Type Parameter (Genérico)** | `Letra mayúscula simple` | `^[A-Z]$` | `T`, `E`, `K`, `V`, `R`, `S`, `U` |
| **Annotation** | `PascalCase` + `@` | `@[A-Z][a-zA-Z0-9]*$` | `@Override`, `@Deprecated`, `@Autowired` |
| **Módulo (Java 9+)** | `minúsculas.punto` | igual que paquete | `com.empresa.app` |

## Reglas Detalladas por Categoría

### 1. Clases, Interfaces, Enums, Records
- **Sustantivo** o **frase nominal**: `Usuario`, `GestorPedidos`, `Serializable`
- **Evita** prefijos de tipo: `CUsuario`, `IUsuario`, `UsuarioClass` ❌
- **Interfaces** a veces terminan en `-able`: `Comparable`, `Runnable`, `Serializable`
- **Records** (Java 14+): igual que clase, inmutables por defecto

### 2. Métodos
- **Verbo** o **frase verbal**: `calcular`, `obtenerNombre`, `esValido`, `guardar`
- **Getters/Setters**: `getX()`, `setX()`, `isX()` (boolean), `hasX()` (colección)
- **Booleanos**: prefijo `is`, `has`, `can`, `should`, `will`: `isActivo()`, `hasPermiso()`
- **Factory**: `createX()`, `of()`, `valueOf()`, `from()`
- **Builders**: `builder()`, `toBuilder()`
- **Conversores**: `toX()`, `asX()`

### 3. Variables y Campos
- **Sustantivo** descriptivo: `saldo`, `listaUsuarios`, `fechaCreacion`
- **Booleanos**: igual que métodos: `isActivo`, `hasError`, `puedeEditar`
- **Evita** abreviaturas crípticas: `usr`, `cnt`, `tmp` ❌ → `usuario`, `contador`, `temporal` ✅
- **Una letra** solo para: bucles (`i`, `j`, `k`), coordenadas (`x`, `y`, `z`), genéricos (`T`, `E`)
- **Campos `static final`** → `UPPER_SNAKE_CASE` (ver constantes)

### 4. Constantes (`static final` inmutables)
- **Todo mayúsculas**, `_` separador: `MAX_SIZE`, `DEFAULT_TIMEOUT_MS`
- **Primitivos + String + inmutables** (`LocalDate`, `BigDecimal`)
- **NO** mutables (`ArrayList`, `HashMap`, arrays) aunque sean `final` (la ref es final, no el contenido)

### 5. Paquetes
- **Dominio inverso**: `com.empresa.proyecto.modulo`
- **Todo minúsculas**, sin guiones, sin números al inicio
- **Evita** nombres genéricos: `util`, `common`, `core` → `com.empresa.facturacion.util`
- **Un paquete = una responsabilidad cohesiva**

### 6. Parámetros de Tipo (Generics)
- **Una letra mayúscula**: `T` (Type), `E` (Element), `K` (Key), `V` (Value), `R` (Return), `S` (Source), `U` (Second type)
- **Múltiples**: `T`, `U`, `V`... o descriptivos `TRequest`, `TResponse` si >1 y confuso

## Ejemplo Propio: GuiaNombrado

```java
package com.empresa.inventario; // Paquete: minúsculas, dominio inverso

// Clase: PascalCase, sustantivo
public final class GestorInventario {

    // Constantes: UPPER_SNAKE_CASE
    private static final int CAPACIDAD_MAXIMA = 1000;
    private static final String CODIGO_PREFIJO = "INV-";
    private static final java.time.LocalDate FECHA_LIMITE = java.time.LocalDate.of(2025, 12, 31);

    // Campos instancia: camelCase, private
    private final List<Producto> productos; // Lista mutable, pero ref final
    private int contadorOperaciones;
    private boolean inicializado;

    // Constructor: mismo nombre clase
    public GestorInventario() {
        this.productos = new ArrayList<>(CAPACIDAD_MAXIMA);
        this.contadorOperaciones = 0;
        this.inicializado = true;
    }

    // Métodos: camelCase, verbo
    public void agregarProducto(Producto producto) { // Parámetro camelCase
        validarNoNulo(producto, "producto");
        validarCapacidad();
        productos.add(producto);
        incrementarContador();
    }

    public Producto buscarPorCodigo(String codigo) { // Verbo + preposición
        return productos.stream()
            .filter(p -> p.codigo().equals(codigo))
            .findFirst()
            .orElse(null);
    }

    public boolean existeProducto(String codigo) { // is/has para boolean
        return buscarPorCodigo(codigo) != null;
    }

    public int obtenerTotalProductos() { // get + sustantivo
        return productos.size();
    }

    public List<Producto> listarProductos() { // Colección → plural
        return List.copyOf(productos); // Copia defensiva
    }

    // Privados: mismo estilo, verbo
    private void validarNoNulo(Object obj, String nombre) {
        if (obj == null) throw new IllegalArgumentException(nombre + " no puede ser nulo");
    }

    private void validarCapacidad() {
        if (productos.size() >= CAPACIDAD_MAXIMA) {
            throw new IllegalStateException("Capacidad máxima alcanzada: " + CAPACIDAD_MAXIMA);
        }
    }

    private void incrementarContador() {
        contadorOperaciones++;
    }

    // Record (Java 14+): PascalCase, inmutable, datos puros
    public record Producto(
        String codigo,      // camelCase componentes
        String nombre,
        double precio,
        int stock
    ) {
        // Compact constructor para validación
        public Producto {
            if (codigo == null || codigo.isBlank()) throw new IllegalArgumentException("Código obligatorio");
            if (precio < 0) throw new IllegalArgumentException("Precio >= 0");
        }

        // Método en record: camelCase
        public boolean hayStock() {
            return stock > 0;
        }

        public Producto conStock(int nuevoStock) {
            return new Producto(codigo, nombre, precio, nuevoStock); // Inmutabilidad
        }
    }

    // MAIN demo
    public static void main(String[] args) {
        GestorInventario gestor = new GestorInventario();

        // Variables locales: camelCase
        Producto portatil = new Producto(CODIGO_PREFIJO + "001", "Portátil Pro", 1299.99, 5);
        Producto raton = new Producto(CODIGO_PREFIJO + "002", "Ratón Ergonómico", 49.90, 20);

        gestor.agregarProducto(portatil);
        gestor.agregarProducto(raton);

        System.out.println("Total: " + gestor.obtenerTotalProductos());
        System.out.println("Existe portátil: " + gestor.existeProducto(portatil.codigo()));

        // Bucle: i, j, k aceptables
        for (int i = 0; i < gestor.obtenerTotalProductos(); i++) {
            Producto p = gestor.listarProductos().get(i);
            System.out.printf("  %s: %s (%.2f€) stock=%d%n",
                p.codigo(), p.nombre(), p.precio(), p.stock());
        }
    }
}
```

## Explicación Detallada

| Elemento | Convención Aplicada | Por Qué |
|----------|---------------------|---------|
| `package com.empresa.inventario` | Minúsculas, punto, dominio inverso | Estándar Java, evita colisiones globales |
| `class GestorInventario` | PascalCase, sustantivo | Identifica tipo de forma única |
| `CAPACIDAD_MAXIMA` | UPPER_SNAKE_CASE | Constante `static final` inmutable |
| `CODIGO_PREFIJO` | UPPER_SNAKE_CASE | Constante String |
| `FECHA_LIMITE` | UPPER_SNAKE_CASE | Constante inmutable (`LocalDate`) |
| `productos` | camelCase, plural (colección) | Campo instancia, legible |
| `contadorOperaciones` | camelCase, sustantivo | Campo mutable |
| `inicializado` | camelCase, `is`/`has` implícito | Boolean |
| `agregarProducto(Producto producto)` | Verbo + Objeto, parámetro camelCase | Acción clara |
| `buscarPorCodigo` | Verbo + Preposición + Sustantivo | Qué hace y cómo |
| `existeProducto` | `existe` + Sustantivo → boolean | Convención `is/has/existe/can` |
| `obtenerTotalProductos` | `obtener` + Sustantivo | Getter semántico |
| `listarProductos` | Verbo plural → devuelve lista | Convención colecciones |
| `validarNoNulo` | Privado, verbo + condición | Helper interno |
| `record Producto(...)` | PascalCase, componentes camelCase | Dato inmutable |
| `hayStock()` | `hay` + Sustantivo → boolean | En record, mismo estilo |
| `conStock(int)` | `con` + Propiedad → nuevo record | Patrón `withX` / `conX` inmutables |
| `for (int i = 0; ...)` | `i` índice bucle | Convención universal |

## Errores Comunes

> [!warning] **Error 1: Clase en camelCase**
> ```java
> class gestorInventario { } // ❌
> ```
> ✅ `class GestorInventario { }`

> [!warning] **Error 2: Constante en camelCase**
> ```java
> static final int maxSize = 100; // ❌
> ```
> ✅ `static final int MAX_SIZE = 100;`

> [!warning] **Error 3: Método como sustantivo**
> ```java
> void usuario() { } // ❌ ¿Qué hace?
> ```
> ✅ `void crearUsuario()`, `void obtenerUsuario()`

> [!warning] **Error 4: Boolean sin prefijo is/has**
> ```java
> boolean activo = true; // ❌ Campo
> boolean activo() { return true; } // ❌ Método
> ```
> ✅ `boolean isActivo`, `boolean tienePermiso()`

> [!warning] **Error 5: Paquete con mayúsculas/guiones**
> ```java
> package com.MiEmpresa.Proyecto; // ❌
> package com.mi-empresa.proyecto; // ❌
> ```
> ✅ `package com.miempresa.proyecto;`

> [!warning] **Error 6: Abreviaturas inconsistentes**
> ```java
> int nrUsers, numUsers, numberOfUsers; // ❌ Mezcla
> ```
> ✅ Elige una: `numberOfUsers` (descriptivo) o `userCount` (común).

## Buenas Prácticas

1. **Consistencia > Preferencia personal** — Sigue guía del proyecto (Google, Spring, Oracle).
2. **Nombres que revelan intención** — `diasVencimiento` mejor que `d`, `dv`, `days`.
3. **Evita encoding de tipo en nombre** — `strNombre`, `iContador`, `listUsuarios` ❌ (Hungarian notation).
4. **Longitud acorde al scope** — `i` en bucle 3 líneas OK. `i` en método 50 líneas ❌.
5. **Una palabra por concepto** — `fetch`, `retrieve`, `get` → elige **una** para todo el código base.
6. **Interfaces sin `I` prefijo** — `UsuarioRepository` no `IUsuarioRepository`. Implementación: `UsuarioRepositoryJpa`.
7. **Test naming** — `shouldXWhenY`, `givenXWhenYThenZ` (BDD) o `metodo_condicion_resultado`.
8. **Configura IDE/Formatter** — `Ctrl+Shift+F` aplica estilo. `EditorConfig` / `Checkstyle` / `Spotless` en CI.

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Nombrado de clases.
- `[[06 - Atributos y Campos]]` — Campos `private camelCase`, constantes `UPPER_SNAKE`.
- `[[07 - Constructores y this]]` — Constructor = nombre clase.
- `[[10 - Metodos de Instancia]]` — Verbos en métodos.
- `[[13 - Static vs Instancia]]` — Constantes static final.
- `[[17 - Separacion de Responsabilidades]]` — Paquetes por funcionalidad.

## Resumen en Una Frase

> **Clases `PascalCase`, métodos/variables `camelCase`, constantes `UPPER_SNAKE`, paquetes `minúsculas.punto`; nombres descriptivos, verbos para acciones, sustantivos para datos.**

---

## Tags
`#java #fundamentos #convenciones #naming #clean-code #estilo #pascalcase #camelcase #uppersnake #paquetes`