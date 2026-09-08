---
tags: [java, spring-boot, testing, mockmvc, springboottest, integracion]
---

# 58 - Pruebas Spring Boot MockMvc y SpringBootTest

Probar una aplicación Spring Boot significa probar sus endpoints HTTP sin levantar un servidor real: `MockMvc` simula peticiones contra tus controllers y `@SpringBootTest` levanta el contexto completo cuando necesitas probar el sistema de verdad. Son las dos herramientas entre las que se mueve todo el testing en Boot.

---

# METODO EXPLICATIVO

## 1. Dos niveles de prueba en Spring Boot

Con JUnit puro probabas clases sueltas con `new`. En Boot necesitas además probar que los endpoints responden, que la seguridad deja pasar a quien debe y que la validación rechaza lo inválido. Hay dos niveles, de más rápido y aislado a más lento y real:

- **Capa web con `@WebMvcTest` + `MockMvc`**: levanta solo la capa de controllers (sin servicios reales, sin base de datos). Rápido, ideal para probar rutas, códigos de estado, JSON y seguridad a nivel de endpoint.
- **Contexto completo con `@SpringBootTest`**: levanta toda la aplicación (todos los beans, base de datos real o de test). Lento, ideal para probar que el sistema entero funciona junto.

La estrategia sana: muchos tests de capa, pocos de contexto completo. Los segundos son caros (arrancan todo) y si tienes cientos, la suite tarda minutos y la gente deja de ejecutarla.

## 2. MockMvc: peticiones falsas contra controllers reales

`MockMvc` ejecuta peticiones HTTP simuladas contra tu capa web sin abrir ningún puerto. Con `@WebMvcTest` solo se cargan los controllers y la infraestructura web; las dependencias (servicios) se sustituyen por mocks con `@MockitoBean`:

```java
@WebMvcTest(PedidoController.class)
class PedidoControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockitoBean
    private PedidoService pedidoService;

    @Test
    void devuelvePedidoExistente() throws Exception {
        when(pedidoService.buscar(1L))
            .thenReturn(new Pedido(1L, "Ana"));

        mockMvc.perform(get("/pedidos/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.nombre").value("Ana"));
    }

    @Test
    void pedidoInexistenteDevuelve404() throws Exception {
        when(pedidoService.buscar(99L))
            .thenThrow(new PedidoNoEncontradoException());

        mockMvc.perform(get("/pedidos/99"))
            .andExpect(status().isNotFound());
    }
}
```

El patrón es siempre el mismo: preparas el mock (`when...thenReturn`), disparas la petición (`perform`), compruebas el resultado (`andExpect`). `jsonPath` permite afirmar sobre el cuerpo JSON con expresiones de ruta (`$.nombre`, `$.items[0].precio`). Observa que aquí no se prueba el servicio: se prueba que el controller enruta, serializa y traduce errores a códigos HTTP correctos.

## 3. Probar la entrada: validacion y seguridad en MockMvc

La capa web es también donde se prueba que la validación y la seguridad están bien conectadas, dos cosas que con JUnit puro no se ven:

```java
@Test
void rechazaPedidoSinNombre() throws Exception {
    mockMvc.perform(post("/pedidos")
            .contentType(MediaType.APPLICATION_JSON)
            .content("{\"cantidad\": 2}"))  // falta el nombre obligatorio
        .andExpect(status().isBadRequest());
}

@Test
void rutaAdminExigeRolAdmin() throws Exception {
    mockMvc.perform(get("/admin/reportes"))
        .andExpect(status().isUnauthorized());  // sin autenticar: 401
}
```

Estos tests son baratos y capturan los olvidos clásicos: el `@Valid` que falta, la ruta que quedó abierta, el mensaje de error que no llega. Si la seguridad importa (y siempre importa), cada regla de acceso merece su test de capa.

## 4. @SpringBootTest: el sistema completo

Cuando necesitas probar que las piezas funcionan juntas (controller + servicio + repositorio + base de datos), `@SpringBootTest` levanta el contexto entero. Combinado con `@AutoConfigureMockMvc` puedes seguir usando `MockMvc` pero ahora contra servicios reales:

```java
@SpringBootTest
@AutoConfigureMockMvc
class PedidoIntegracionTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void flujoCompletoCrearYConsultar() throws Exception {
        String respuesta = mockMvc.perform(post("/pedidos")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"nombreCliente\":\"Ana\",\"cantidad\":2}"))
            .andExpect(status().isOk())
            .andReturn().getResponse().getContentAsString();

        // El pedido creado de verdad existe y se puede consultar
        mockMvc.perform(get("/pedidos/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.nombreCliente").value("Ana"));
    }
}
```

Para la base de datos en estos tests lo habitual es una base ligera (H2 en memoria) o Testcontainers con la base real en Docker. La regla: el test de integración debe poder ejecutarse en cualquier máquina sin depender de una base compartida con datos impredecibles.

## 5. Que probar en cada nivel

La pirámide de tests aplicada a Boot queda así:

- **Base (muchos)**: tests JUnit puros de servicios y lógica, con `new` y mocks. Rápidos, precisos, localizan el fallo exacto.
- **Medio (bastantes)**: tests de capa con `@WebMvcTest`: rutas, JSON, validación, seguridad. Verifican el contrato HTTP.
- **Cima (pocos)**: tests `@SpringBootTest` de flujos críticos completos: crear, consultar, pagar. Verifican que el conjunto funciona.

Si un bug se te escapa, pregunta en qué nivel debería haberse cazado y escribe ahí el test que lo habría detectado. Esa disciplina convierte cada bug en una mejora permanente de la red de seguridad.

---

## Errores Comunes

> **Probarlo todo con @SpringBootTest**. La suite tarda minutos, nadie la ejecuta y los fallos son dificiles de localizar. Reserva el contexto completo para flujos criticos; lo demas va en tests de capa o unitarios.

> **No mockear el servicio en @WebMvcTest**. Sin `@MockitoBean` Spring intenta crear el servicio real con sus dependencias (repositorios, datasources) y el test de capa se convierte en un test de integracion accidental que falla por la base de datos.

> **Afirmar sobre el JSON con comparacion de Strings**. `andExpect(content().string("{...}"))` se rompe con cualquier cambio de formato o de orden de campos. Usa `jsonPath` para afirmar sobre valores concretos.

> **Olvidar probar los codigos de error**. Solo probar el 200 deja sin vigilancia el 400, el 404 y el 401. Cada camino de error del controller merece su test: son los que mas se rompen al evolucionar.

> **Tests de integracion contra una base compartida**. Si el test depende de datos que otro equipo modifica, falla aleatoriamente. Base dedicada por ejecucion: H2 en memoria o Testcontainers.

---

## Conexiones

- [[48 - JUnit y pruebas de unidad]] - La base sobre la que se construye el testing en Boot
- [[53 - Dependencias de prueba y alcance test]] - De donde salen MockMvc y SpringBootTest
- [[56 - Validacion con Spring Boot]] - Lo que se prueba con peticiones invalidas
- [[57 - Spring Security autenticacion y cadena de filtros]] - Lo que se prueba con peticiones sin permiso
- [[62 - Spring Security OAuth2 Resource Server y JWT]] - Probar endpoints con @WithJwt
- [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]] - Que se levanta en el contexto
- [[59 - Docker Compose]] - Levantar bases reales para tests con Testcontainers

---

## Tags
`#java #spring-boot #testing #mockmvc #integracion`
