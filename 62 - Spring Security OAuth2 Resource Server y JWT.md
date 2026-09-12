---
tags: [java, spring-boot, spring-security, oauth2, jwt, resource-server, seguridad, apis]
---
****
# 62 - Spring Security OAuth2 Resource Server y JWT

Un **Resource Server** es tu API: protege recursos (endpoints, datos) y confía en la autenticación a un servidor externo. Los clientes se identifican con un **token JWT** firmado que tu API valida en cada petición sin preguntar a nadie más. Es el modelo estándar para separar el "quién eres" del login en una API moderna.

---

# METODO EXPLICATIVO

## 1. De sesiones a tokens: por que OAuth2 y JWT

En el modelo clásico de sesión, tras el login el servidor guarda el estado del usuario en memoria (o en Redis) y le da al cliente una cookie. Cada petición se valida consultando ese estado. Funciona, pero acopla el login a la app: no puedes dar acceso a tus datos a una app móvil o a otro servicio sin reinventar la rueda de la sesión cada vez.

El modelo OAuth2 lo invierte: hay un **servidor de autorización** dedicado que hace el login y emite un **token**. Tu API deja de guardar sesiones: solo valida el token que trae cada petición. El cliente (web, móvil, otro servicio) presenta el token a tu API, que lo verifica y responde. Tu aplicación pasa a ser un **Resource Server**: custodia los recursos, confía la autenticación.

La separación trae beneficios directos: la identidad vive fuera de tu app (puedes cambiar de proveedor de login sin tocar la lógica), y los tokens están **firmados**, así que la validación es local y sin estado: no hay que consultar nada por cada petición.

## 2. Que es un JWT: tres partes y una firma

Un JWT (JSON Web Token) es un texto codificado en tres segmentos separados por puntos:

```
eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJhbmEtMTIzIiwicm9sZSI6IkFETUlOIn0.c2lnbmF0dXJh...
   HEADER             PAYLOAD                                SIGNATURE
```

- **Header**: el algoritmo de firma (`RS256`, `HS256`) y el tipo de token.
- **Payload (claims)**: los datos en formato JSON: `sub` (quién es el usuario), `exp` (expiración), `iss` (quién lo emitió), y tus claims propios (`roles`, `scope`).
- **Signature**: la firma criptográfica del emisor sobre header + payload.

La característica decisiva: el payload **no está cifrado, solo codificado**. Cualquiera puede leerlo (pegando el token en un decodificador). Lo que impide la firma es **modificarlo**: si alguien cambia `sub` o los roles, la firma deja de cuadrar y el token es rechazado. El JWT es como una tarjeta plastificada con datos impresos: todo el mundo la lee, pero nadie puede retocarla sin que se note.

## 3. El Resource Server: quien valida y como

Tu API es un resource server: en cada petición, la cadena de filtros de Spring Security toma la cabecera `Authorization: Bearer <jwt>`, la decodifica, **verifica la firma** con la clave pública del emisor, comprueba que no haya expirado y que el emisor sea el esperado, y extrae los claims para construir el `Authentication`.

La dependencia y la configuración mínima:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
```

```properties
spring.security.oauth2.resourceserver.jwt.issuer-uri=https://mi-emisor.com/realms/miapp
```

Esa sola propiedad activa el mecanismo más robusto: en el arranque, Spring descarga automáticamente las claves públicas del emisor (vía el documento de discovery en `/.well-known/openid-configuration`) y las usa para verificar firmas. Tu `SecurityFilterChain` cambia poco respecto a una API normal:

```java
http
    .authorizeHttpRequests(auth -> auth
        .requestMatchers(HttpMethod.GET, "/publico/**").permitAll()
        .requestMatchers("/admin/**").hasAuthority("SCOPE_admin")
        .anyRequest().authenticated())
    .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()));
```

El bloque `oauth2ResourceServer()` conecta la validación de JWT a la cadena: a partir de ahí, las peticiones con token válido llegan al controller y las demás mueren con 401 en el filtro, sin tocar tu código.

## 4. Extraer la identidad: claims y @AuthenticationPrincipal

Ya dentro del controller, la identidad llega como parámetro. Para APIs normales basta el nombre; para decidir con los claims completos, Spring te da el objeto `Jwt`:

```java
@GetMapping("/perfil")
public Map<String, Object> perfil(@AuthenticationPrincipal Jwt jwt) {
    return Map.of(
        "usuario", jwt.getSubject(),        // claim "sub"
        "emisor", jwt.getIssuer().toString(), // claim "iss"
        "roles", jwt.getClaimAsStringList("roles")
    );
}
```

Los claims son el contenido real del payload. Una advertencia de diseño: el payload es legible por cualquiera y viaja en cada petición, así que solo debe llevar **identidad y permisos** (`sub`, `roles`, `scope`, `exp`). Metas un email completo, datos personales o configuration en el payload y habrás filtrado información en cada cabecera HTTP de cada petición.

## 5. Autorizacion con scopes y roles

Los claims que traen los permisos son dos, y conviene no mezclarlos: los **scopes** (qué puede hacer la aplicación cliente en nombre del usuario) y los **roles** (qué puede hacer el usuario en tu dominio). Spring los expone como *authorities*, y las reglas del `SecurityFilterChain` los comprueban con `hasAuthority`:

```java
.requestMatchers("/pedidos/**").hasAuthority("SCOPE_pedidos")   // scope del token
.requestMatchers("/admin/**").hasRole("ADMIN")                  // rol: ROLE_ADMIN implicito
```

Detalle que confunde a todo el mundo: `hasRole("ADMIN")` comprueba la autoridad `ROLE_ADMIN` (con prefijo), mientras que los scopes llegan como `SCOPE_xxx` (con su propio prefijo, prefijado automáticamente por el decoder). Si la regla no dispara, lo primero es imprimir las authorities reales del token y comparar con lo que la regla espera: el error es casi siempre un prefijo o una mayúscula.

## 6. Probar endpoints con token sin servidor de autorizacion

Para tests no quieres depender de un servidor de autorización vivo. Spring Security 5.7+ da `@WithJwt` para declarar el token en el test:

```java
@Test
@WithJwt("admin-token.jwt")
void adminVeReportes() throws Exception {
    mockMvc.perform(get("/admin/reportes"))
        .andExpect(status().isOk());
}
```

El archivo `admin-token.jwt` vive en el classpath de test: es un token **firmado con la clave de test** del proyecto. Así pruebas con tokens de distintos usuarios/roles sin levantar Keycloak ni mockear nada: el mismo pipeline de validación real corre en el test.

## 7. Expiracion, rotacion y validacion de claims

El token caduca (`exp`) y el cliente debe pedir uno nuevo al servidor de autorización (con refresh token u otro flujo OAuth2, fuera del resource server). Tu API solo valida: si `exp` pasó, 401. Dos validaciones extra que se configuran en el `JwtDecoder` si el issuer-uri por defecto no basta: validar audiencia (`aud`) para que el token solo valga para TU API, y validaciones custom de claims. Nunca des por bueno un token solo porque "pasa la firma": la firma dice que es auténtico, la audiencia dice que es para ti.

---

## Errores Comunes

> **Confundir Resource Server con Authorization Server**. Tu API valida tokens, no los emite. El login y la emision son del servidor de autorizacion (Keycloak, Auth0, otro servicio). Cada rol con su dependencia: `oauth2-resource-server` en tu API, nunca "actuar" de los dos roles a la vez.

> **Creer que el payload esta cifrado**. Un JWT es legible por cualquiera (base64). No pongas contrasenas, correos, ni datos sensibles: solo identidad y permisos. Si necesitas privacidad, el JWT no es el sitio.

> **Confiar en la firma sin validar la audiencia ni el emisor**. Un token valido para OTRO servicio de la misma plataforma pasa la firma pero no te autoriza a ti. Valida `aud` y `iss`, no solo la firma.

> **Hardcodear claves o cambiar el algoritmo para "probar rapido"**. Desactivar la validacion de firma o aceptar el algoritmo `none` abre la puerta a la forja de tokens. La validacion se configura una vez y se deja sola.

> **Guardar roles en base de datos y no en el token (o viceversa) sin decidir la fuente de verdad**. Si los roles viven en el token, expiran con el: un usuario degradado conserva permisos hasta que caduque. Decide donde vive la verdad (token para respuesta rapida, revalidacion fina para acciones criticas) y documentalo.

> **Probar la seguridad con tokens falsos a mano**. Usa `@WithJwt` con tokens firmados de test: ejercitas el pipeline real de validacion sin levantar el servidor de autorizacion.

---

## Conexiones

- [[57 - Spring Security autenticacion y cadena de filtros]] - La cadena de filtros donde vive la validacion del token
- [[56 - Validacion con Spring Boot]] - Validar los datos del payload como los del body
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Probar endpoints con @WithJwt
- [[49 - Spring Boot arranque de aplicacion]] - Como el starter activa la validacion en el arranque
- [[52 - Starters de Spring Boot en Maven]] - El starter de resource server
- [[53 - Dependencias de prueba y alcance test]] - Tokens de test en el classpath de pruebas
- [[65 - Keycloak (identidad y seguridad)]] - El servidor de autorizacion que emite esos tokens

---

## Tags
`#java #spring-boot #spring-security #oauth2 #jwt #seguridad`
