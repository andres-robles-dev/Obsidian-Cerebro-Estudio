---
tags: [java, spring-boot, validacion, bean-validation, anotaciones, errores]
---

# 56 - Validacion con Spring Boot

Validar es comprobar que los datos que entran a tu aplicación cumplen las reglas antes de usarlos: que el email tiene formato, que la edad es positiva, que el nombre no está vacío. Spring Boot lo hace de forma declarativa con anotaciones: marcas los campos con las reglas y el framework las aplica automáticamente al recibir cada petición.

---

# METODO EXPLICATIVO

## 1. Por que validar en la entrada

Todo dato que entra desde fuera (un JSON en un endpoint, un formulario, parámetros de URL) es sospechoso por definición: puede venir incompleto, con tipos raros o directamente malicioso. Si tu lógica de negocio recibe basura, tienes dos males posibles: o falla a mitad con un error confuso, o peor, procesa la basura y corrompe el estado.

La validación es el **control de acceso de los datos**: una capa en la entrada que rechaza lo inválido con un error claro (400 Bad Request) antes de que toque la lógica. Sin ella, cada método tendría que comprobar sus parámetros a mano, repitiendo las mismas comprobaciones por todo el código.

## 2. Anotaciones sobre el DTO

El patrón estándar es validar un DTO (objeto de transferencia) con anotaciones de Jakarta Validation. El starter `spring-boot-starter-validation` las pone disponibles:

```java
public class CrearPedidoRequest {

    @NotBlank(message = "El nombre es obligatorio")
    private String nombreCliente;

    @Email(message = "El email no tiene formato valido")
    private String email;

    @Min(value = 1, message = "Debe pedir al menos 1 unidad")
    private int cantidad;

    @Size(max = 500, message = "Las notas no pueden superar 500 caracteres")
    private String notas;
}
```

Cada anotación es una regla legible junto al dato que protege. Las más usadas: `@NotNull` (no null), `@NotBlank` (no null, no vacío, no solo espacios; para Strings), `@Min`/`@Max` (números), `@Size` (longitud de String o tamaño de colección), `@Email` (formato), `@Pattern` (expresión regular), `@Positive`/`@PositiveOrZero`.

Fíjate en que las reglas viven **en el DTO, no en el servicio**. El servicio recibe datos ya validados y puede concentrarse en la lógica. Si mañana la regla cambia (de 500 a 1000 caracteres), cambias una anotación, no la lógica.

## 3. Activar la validacion con @Valid

Las anotaciones por sí solas no hacen nada: hay que pedirle a Spring que las evalúe con `@Valid` en el parámetro del controller:

```java
@RestController
public class PedidoController {

    @PostMapping("/pedidos")
    public ResponseEntity<?> crear(@Valid @RequestBody CrearPedidoRequest request) {
        // Si llegamos aqui, request ya cumple todas las reglas
        Pedido pedido = pedidoService.crear(request);
        return ResponseEntity.ok(pedido);
    }
}
```

Sin `@Valid`, Spring construye el objeto pero nunca comprueba las reglas, y la validación es decoración inútil. Este es el olvido más común: anotaciones perfectas que nunca se ejecutan por falta de una palabra en el controller.

## 4. Que pasa cuando algo falla

Si alguna regla no se cumple, Spring no entra al método: lanza `MethodArgumentNotValidException` y responde automáticamente con **400 Bad Request**. El cuerpo del error incluye qué campos fallaron y con qué mensaje. Puedes dejar ese comportamiento por defecto o personalizarlo con un manejador global:

```java
@RestControllerAdvice
public class ManejadorErrores {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> manejarErroresValidacion(
            MethodArgumentNotValidException ex) {
        Map<String, String> errores = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
            errores.put(error.getField(), error.getDefaultMessage()));
        return ResponseEntity.badRequest().body(errores);
    }
}
```

Esto convierte los fallos en un mapa `campo -> mensaje` que el cliente (frontend, otra API) puede mostrar directamente. Un solo manejador global cubre todos los endpoints: no hay que repetirlo por controller.

## 5. Validacion anidada y grupos

Dos técnicas para casos más avanzados. La **validación anidada**: si el DTO contiene otro objeto con sus propias reglas, hay que marcarlo también con `@Valid` para que se evalúen en cascada:

```java
public class CrearPedidoRequest {

    @NotBlank
    private String nombreCliente;

    @Valid  // sin esto, las reglas de Direccion nunca se evaluan
    @NotNull
    private Direccion direccionEnvio;
}
```

Y los **grupos de validación**: cuando el mismo DTO se usa en crear y en actualizar con reglas distintas (al crear el id debe ser null, al actualizar obligatorio), defines interfaces marcador y asignas cada anotación a un grupo. Es menos frecuente, pero conviene saber que existe antes de duplicar DTOs por una sola regla distinta.

---

## Errores Comunes

> **Anotar el DTO pero olvidar @Valid en el controller**. Las reglas existen pero nadie las ejecuta. El endpoint acepta basura en silencio. Cada `@RequestBody` que deba validarse lleva su `@Valid`.

> **Usar @NotNull para Strings en vez de @NotBlank**. `@NotNull` acepta `""` y `"   "` como validos. Para texto que el usuario escribe, casi siempre quieres `@NotBlank`.

> **Validar en el servicio en vez de en la entrada**. Si el servicio comprueba nulls y rangos a mano, duplicas reglas y mezclas responsabilidades. La entrada se valida con anotaciones; el servicio asume datos validos.

> **Olvidar @Valid en objetos anidados**. El DTO padre se valida pero el hijo no, y los datos invalidos se cuelan por dentro. Cada nivel de anidamiento necesita su propio `@Valid`.

> **Devolver el stack trace al cliente en errores de validacion**. El 400 debe llevar mensajes legibles para el consumidor de la API, no trazas internas. Usa un `@RestControllerAdvice` que traduzca a `campo -> mensaje`.

---

## Conexiones

- [[22 - Separacion de Responsabilidades]] - El DTO como objeto de la capa de entrada
- [[40 - Manejo de Excepciones]] - MethodArgumentNotValidException y su manejo global
- [[46 - Metodos de Consulta y Tell Don't Ask]] - El servicio recibe datos validos y no pregunta
- [[52 - Starters de Spring Boot en Maven]] - El starter de validacion
- [[57 - Spring Security autenticacion y cadena de filtros]] - Validacion de datos vs autenticacion de personas
- [[62 - Spring Security OAuth2 Resource Server y JWT]] - Los claims del token son datos que tambien se validan
- [[66 - Jackson y JSON en Java]] - El DTO que Jackson construye es el que se valida

---

## Tags
`#java #spring-boot #validacion #anotaciones #errores`
