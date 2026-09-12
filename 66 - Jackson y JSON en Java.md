---
tags: [java, jackson, json, serializacion, spring-boot, apis]
---

# 66 - Jackson y JSON en Java

Jackson es la librería que convierte objetos Java a JSON y JSON a objetos Java (serialización y deserialización). Es la librería por defecto en Spring Boot: cuando tu `@RestController` devuelve un objeto, Jackson es quien lo transforma a JSON sin que escribas una sola línea.

---

# METODO EXPLICATIVO

## 1. Que es Jackson y por que esta en todas partes

Toda API REST habla JSON. En algún punto del flujo, un objeto Java (`Pedido`, con sus campos y listas) tiene que cruzar la red como texto JSON, y cada JSON que entra tiene que convertirse en objeto Java de vuelta. Esa conversión bidireccional no es trivial (tipos, nombres, fechas, nulos, ciclos) y es exactamente lo que hace Jackson.

En Spring Boot llega automáticamente: el starter-web trae Jackson como dependencia transitiva (recuerda 52), y el framework lo conecta como conversor HTTP por defecto. Ese es el motivo por el que un controller que devuelve un objeto "funciona": alguien serializa por debajo, y ese alguien es Jackson.

## 2. ObjectMapper: el nucleo de la libreria

Toda la librería orbita alrededor de una clase: `ObjectMapper`. Dos operaciones básicas:

```java
ObjectMapper mapper = new ObjectMapper();

// Serializacion: objeto -> JSON
Pedido pedido = new Pedido("Ana", List.of("libro", "boligrafo"));
String json = mapper.writeValueAsString(pedido);
// {"cliente":"Ana","items":["libro","boligrafo"]}

// Deserializacion: JSON -> objeto
Pedido reconstruido = mapper.readValue(json, Pedido.class);
```

Reglas por defecto que conviene memorizar: un campo Java `nombreCliente` se convierte en JSON `nombreCliente` (mismo nombre), los getters determinan qué se serializa, y en la deserialización Jackson usa el constructor sin parámetros + setters (o uno anotado). En Spring Boot no usas el `ObjectMapper` directamente la mayoría de las veces: el framework trae uno ya configurado como bean, y lo personalizas con propiedades (`spring.jackson.*`) en lugar de crear el tuyo.

## 3. Como lo usa Spring por ti

En una API REST típica no verás ni una línea de código Jackson: el conversor HTTP actúa en dos fronteras. Cuando tu controller devuelve un objeto, Spring lo pasa por Jackson antes de escribir la respuesta. Cuando un `@RequestBody` llega, Jackson lo convierte al objeto de tu método (y si falla el JSON, tu controller ni se entera: responden 400 o 415 automáticamente):

```java
@RestController
public class PedidoController {

    @PostMapping("/pedidos")     // JSON entrante -> Pedido (Jackson)
    public Pedido crear(@RequestBody Pedido pedido) {
        return pedido;           // Pedido -> JSON de salida (Jackson)
    }
}
```

Esto tiene una consecuencia arquitectónica: **la forma de tu JSON la decide tu DTO**, porque Jackson deriva los nombres de tus campos y getters. Si el JSON que produce tu API es feo o revela cosas de más, no es un problema de Jackson: es el diseño de tus DTOs.

## 4. Anotaciones basicas: nombres, privacidad y formato

Las anotaciones de Jackson se ponen en los DTOs para controlar la conversión:

```java
public class CrearPedidoRequest {

    @JsonProperty("nombre_cliente")   // nombre distinto en JSON
    private String nombreCliente;

    @JsonIgnore                       // nunca viaja en el JSON
    private String hashPassword;

    @JsonFormat(shape = JsonFormat.Shape.STRING)
    private LocalDate fecha;          // formato como texto, no array de numeros
}
```

Las tres que resuelven casi todo: `@JsonProperty` (renombra un campo, indispensable para APIs externas con convenciones distintas como snake_case), `@JsonIgnore` (excluye datos sensibles o internos de la serialización), y `@JsonFormat` (controla cómo se escriben fechas y números).

## 5. Records: la pareja moderna de Jackson

Desde Jackson 2.12+, los records funcionan de serie: Jackson entiende que el constructor canónico es el punto de entrada de la deserialización y que los accessors (`nombre()`, sin `get`) son la salida. En Spring Boot moderno, un DTO record es la opción por defecto:

```java
public record CrearPedidoRequest(
    @NotBlank String cliente,
    @JsonProperty("total") BigDecimal totalPedido
) {}
```

Compacto, inmutable, y con la validación de 56 aplicada encima. Dato útil: la anotación de Jackson va sobre el **componente del record** (después del tipo o en el parámetro, con `@JsonProperty`), y Spring Boot moderno ya la propaga al constructor sin configuración extra. Entre records y DTOs con `@Valid`, el pipeline de entrada de una API queda casi sin código de plomería.

## 6. Fechas y Java.time: el caso que todos resuelven dos veces

`LocalDate`, `LocalDateTime` y compañía necesitan un módulo extra si configuras Jackson a mano, pero en Spring Boot ya está incluido y funciona con ISO-8601:

```java
record Pedido(LocalDateTime fechaCreacion, LocalDate fechaEntrega) {}
// serializa: {"fechaCreacion":"2026-09-12T10:30:00","fechaEntrega":"2026-09-20"}
```

Dos reglas para no sufrir: **ISO-8601 como formato por defecto** (legible y ordenable), y nunca exponer un `Date` antiguo en un DTO nuevo. Si necesitas un formato custom (por una API que exige `dd/MM/yyyy`), `@JsonFormat(pattern = "dd/MM/yyyy")` en el campo resuelve localmente; cambiar la configuración global del ObjectMapper es para convenciones de API completas, no para parches de un campo.

## 7. Errores tipicos de deserializacion y como evitarlos

Tres fallos cubren el 90% de los problemas reales con Jackson:

```java
// 1. JSON con campos que tu DTO no conoce
mapper.readValue(json, Pedido.class);
// UnrecognizedPropertyException por el campo extra

// 2. Objeto sin constructor accesible ni getters
// InvalidDefinitionException: cannot construct instance

// 3. Relaciones circulares (A tiene lista de B, B tiene A)
// StackOverflowError: recursion infinita al serializar
```

Los tres tienen arreglo conocido: para campos desconocidos, `@JsonIgnoreProperties(ignoreUnknown = true)` en el DTO (o `FAIL_ON_UNKNOWN_PROPERTIES = false` global) — tolerante con la evolución de la API. Para el constructor, records o clases con constructor vacío. Para ciclos, `@JsonIgnore` en el lado de vuelta de la relación o mejor, **DTOs de vista** que nunca exponen la bidireccionalidad completa (la solución de arquitectura; la anotación es el parche).

---

## Errores Comunes

> **Crear un ObjectMapper propio en un proyecto Spring Boot**. Pierdes toda la configuracion global (fechas, nulls, naming) y creas inconsistencia. Usa el bean que Spring gestiona: `ObjectMapper` inyectado, o propiedades `spring.jackson.*`.

> **Devolver entidades de base de datos directo desde el controller**. La entidad arrastra relaciones, lazy-loading y campos internos que Jackson intentara serializar (recursion infinita con lazy loading incluida). DTOs en la frontera, siempre.

> **Olvidar que el JSON solo serializa lo publico**. Un objeto sin getters (o un record con componentes privados de acceso no estandar) serializa vacio o lanza InvalidDefinitionException. Si el JSON sale incompleto, revisa getters/visibilidad antes de tocar configuracion.

> **Cambiar el formato de fechas campo por campo sin criterio**. Mezclar ISO en unos endpoints y dd/MM/yyyy en otros hace imposible consumir tu API. Un formato global para la API y excepciones justificadas y anotadas.

> **Ignorar los campos desconocidos del cliente**. Por defecto Jackson RECHAZA JSON con propiedades extra (FAIL_ON_UNKNOWN_PROPERTIES). En APIs que evolucionan, ignoralos (ignoreUnknown) y valida lo importante con Bean Validation.

> **Serializar campos sensibles por accidente**. hashPassword, tokens internos, datos de auditoria: si el getter existe, viaja en el JSON. Cada DTO que se serializa merece una pasada de "que esta exponiendo?" antes de prod.

---

## Conexiones

- [[49 - Spring Boot arranque de aplicacion]] - La autoconfiguracion conecta Jackson como conversor HTTP
- [[52 - Starters de Spring Boot en Maven]] - El starter-web que trae Jackson como transitiva
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - jsonPath para afirmar sobre el JSON que produce Jackson
- [[37 - Record (y Value Objects)]] - Los records como DTOs ideales para serializar
- [[35 - Getters booleanos]] - isXxx() determina el nombre del campo en el JSON
- [[56 - Validacion con Spring Boot]] - El DTO es lo que Jackson convierte y @Valid verifica
- [[26 - Private en Profundidad]] - Jackson usa reflection sobre tus campos

---

## Tags
`#java #jackson #json #serializacion #spring-boot #apis`
