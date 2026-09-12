---
tags: [java, spring-boot, spring-security, oauth2, keycloak, jwt, identidad, docker]
---

# 65 - Keycloak (identidad y seguridad)

Keycloak es un **servidor de autorización de código abierto**: hace el login, guarda los usuarios, emite los tokens JWT y aplica las reglas de identidad, para que tu aplicación no tenga que hacer nada de eso. Es la pieza que completa el modelo OAuth2 que ya conoces: tu API es el resource server y Keycloak es quien dice quién eres.

---

# METODO EXPLICATIVO

## 1. El problema: no reinventar la identidad cada proyecto

Todo sistema necesita: login, contraseñas seguras, recuperación de contraseña, roles, MFA, sesiones, auditoría. Escribir eso a mano en cada proyecto es repetir código con riesgo de seguridad alto: un fallo en tu gestión de contraseñas es una brecha directa.

La solución moderna es **delegar la identidad** a un sistema dedicado. Keycloak es el más usado de código abierto: un servicio completo que gestiona usuarios, roles y emisión de tokens. Tu aplicación deja de preguntarse "¿cómo autentico?" y pasa a solo una pregunta: "¿este token es válido?".

Recuerda el reparto de roles de OAuth2: **Keycloak hace el login y emite los tokens**; tu API (resource server) **solo valida**. Si en tu código estás guardando contraseñas hasheadas, en el 2026, con un IDP disponible, casi siempre estás resolviendo mal el problema.

## 2. Realm y Client: como se organiza

Keycloak organiza todo en dos niveles jerárquicos:

- **Realm** (reino): un espacio de identidad aislado, con sus usuarios, roles y configuración. Un despliegue de Keycloak puede servir varios proyectos (cada uno su realm), o separar entornos (dev/prod).
- **Client**: tu aplicación **registrada** dentro del realm. Cada app que quiera usar Keycloak necesita un client con su configuración: URLs de retorno, qué tokens puede pedir, scopes.

```text
Realm "miempresa"
├── Client "app-pedidos"     (tu API web)
├── Client "app-movil"       (la app movil, publico)
├── Client "servicio-facturacion" (backend, confidential, machine-to-machine)
└── Usuarios: ana, luis, ...
```

El client es la ficha de identidad de tu app ante Keycloak: sin él, Keycloak no conoce tu aplicación y no sabe a quién entregar tokens ni con qué reglas.

## 3. Los flujos de OAuth2 que usaras

Dependiendo de quién llama, hay dos flujos que cubren el 95% de los casos:

**Authorization Code** (usuario presente): la app redirige al navegador del usuario a Keycloak, el usuario se logea, Keycloak redirige de vuelta con un código que la app canjea por tokens. Es el flujo para apps web y móviles: la app nunca ve la contraseña, solo el token. Es el estándar del login.

**Client Credentials** (servicio a servicio): cuando la que llama no es una persona sino otro backend. El servicio se autentica con su client-id y client-secret y recibe un token directamente, sin humano de por medio. Es el flujo para comunicación entre microservicios.

El detalle práctico en Spring: tu resource server no elige el flow, solo **valida el token que le llega**. El flow lo ejecuta el cliente (con librerías de OAuth2 en la app web) o lo lanza el servicio entre máquinas con sus credenciales. Para desarrollo y pruebas, Keycloak permite también el password grant (usuario+contraseña directos al token endpoint) vía curl, suficiente para arrancar.

## 4. Usuarios, roles y mappers: como llegan los roles al token

Un error muy común: creas roles en Keycloak, asignas roles al usuario, haces login, miras el JWT... y los roles no aparecen. Esto es porque **por defecto los roles no siempre viajan en el token**: Keycloak controla qué claims entran al JWT mediante **mappers** (mapeadores).

```json
{
  "sub": "f3a1...",            // id del usuario en Keycloak
  "preferred_username": "ana", // nombre visible
  "realm_access": { "roles": ["admin", "empleado"] },
  "exp": 1755000000
}
```

Cada claim (`realm_access.roles`, `preferred_username`, ...) sale de un mapper configurable en el client. Si tu API necesita un claim que no llega, no cambias el código: añades/activas el mapper en Keycloak. La decisión de diseño importante: el token debe llevar **identidad y roles**; los datos de negocio (direcciones, configuración del usuario) se piden a la app o al propio Keycloak vía API, no se meten en el token (que, recuerda, es legible por cualquiera).

## 5. Roles de realm vs roles de client

Hay dos tipos de roles y confundirlos es la fuente número uno de "el token no trae mis roles":

- **Realm roles**: roles globales del reino (`admin`, `usuario`). Los usa cualquier app del realm.
- **Client roles**: roles específicos de UNA app registrada (el client `app-pedidos` puede tener su rol `aprobador` que no significa nada en otra app).

En el JWT, los realm roles van en `realm_access.roles` y los client roles en `resource_access.<client>.roles`. Para tu API, lo sano es decidir UNA fuente de verdad (normalmente realm roles para lo general) y configurar el mapper que los copie al formato que tu decoder espera. La regla de arquitectura: los roles de dominio (lo que el usuario ES en tu negocio) mejor como realm roles; los permisos muy específicos de una app, como client roles de esa app.

## 6. Conectar tu resource server: una propiedad

Desde el lado de Spring Boot, todo el trabajo de integración ya lo conoces (tema 62): el resource server apunta al emisor con `issuer-uri` y valida la firma con las claves que Keycloak publica:

```properties
spring.security.oauth2.resourceserver.jwt.issuer-uri=http://localhost:8081/realms/miempresa
```

Con esa línea, en el arranque Spring descarga el documento de discovery y las claves públicas del realm, y cada petición con `Bearer <jwt>` se valida sola. Cuando Keycloak cambia una clave (rotación), tu app la descarga sin reiniciar. Tu `SecurityFilterChain` mapea los roles del token a authorities, normalmente decodificando `realm_access.roles` para que `hasRole()` funcione:

```java
@Bean
public JwtAuthenticationConverter converter() {
    JwtGrantedAuthoritiesConverter gac = new JwtGrantedAuthoritiesConverter();
    gac.setAuthoritiesClaimName("realm_access.roles");
    gac.setAuthorityPrefix("ROLE_");
    return new JwtAuthenticationConverter(gac);
}
```

## 7. Keycloak en Docker Compose: el entorno local

Para desarrollo, Keycloak corre en contenedor junto al resto (esto conecta directamente con tu compose):

```yaml
services:
  keycloak:
    image: quay.io/keycloak/keycloak:26.0
    command: start-dev
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: admin
    ports:
      - "8081:8080"
```

En `start-dev` no hace base de datos externa (usa H2 embebido, datos no persistentes, perfecto para desarrollo). Con esto y tu Postgres, `docker compose up` levanta identidad + datos. Y un detalle de red que rompe sesiones enteras: si tu app corre en tu IDE y Keycloak en Docker, el `issuer-uri` debe apuntar a `localhost:8081`; si ambos están en el compose, el nombre es `keycloak:8080`. El emisor declarado DENTRO del token debe coincidir con lo que tu API espera: si el token dice `http://keycloak:8080/...` y tu app valida contra `http://localhost:8081/...`, todo token será rechazado aunque sea auténtico.

## 8. Probar con tokens reales en desarrollo

Para development, el flujo más rápido es pedir un token con curl (password grant, solo en realms de desarrollo):

```bash
curl -X POST "http://localhost:8081/realms/miempresa/protocol/openid-connect/token" \
  -d "grant_type=password" \
  -d "client_id=app-pedidos" \
  -d "username=ana" -d "password=secreto" \
  -d "scope=openid"
```

La respuesta trae `access_token` (el JWT): lo pegas en la cabecera `Authorization: Bearer` de tus peticiones con Postman o curl. Para tests automatizados, recuerda el camino de 62: `@WithJwt` con tokens firmados de test, sin levantar Keycloak en la suite. Complemento útil: Keycloak genera sus propios clients de demo (admin console) y si algún día montas apps frontend, la librería cliente maneja el flow por ti.

---

## Errores Comunes

> **Esperar que los roles aparezcan solos en el token**. Creas el rol, lo asignas, y el JWT no lo trae. Por defecto los mappers no siempre exponen todo: verifica en la consola de admin que el mapper del rol esta activo y mira el token decodificado antes de culpar a tu codigo.

> **Confundir realm roles con client roles**. Asignas el rol en el client equivocado y tu regla `hasRole` nunca dispara. Decidir la fuente de verdad (realm para roles de negocio, client para permisos de esa app) y documentarlo evita la pelea eterna.

> **Issuer de Docker vs localhost**. Token emitido con issuer `http://keycloak:8080/realms/x` cuando tu app valida `http://localhost:8081/realms/x`: rechazado al momento. El issuer declarado en el token debe coincidir con el que tu API espera: revisa la config del frontend URL y hostname del realm.

> **Cerrar un usuario en Keycloak pero que siga funcionando hasta que caduque el token**. Los tokens no se invalidan al desactivar un usuario (estan firmados y ahi estan hasta `exp`). Para revocacion inmediata se usan tokens de vida corta, introspeccion o logout de sesiones: es una decision que hay que tomar conscientemente.

> **Gestionar usuarios en tu base de datos Y en Keycloak a la vez**. Dos fuentes de verdad para lo mismo es el camino al caos: datos que divergen. Los datos de identidad viven en Keycloak; tu base guarda lo que pertenece al dominio (el pedido, no el email del usuario).

> **Exponer el client-secret de una app publica**. Las apps "public" (movil, SPA) no llevan secreto por diseno; los "confidential" (backend) si. Si tu backend usa client credentials, el secreto va en variables de entorno o vault, nunca en el pom ni el repositorio.

---

## Conexiones

- [[62 - Spring Security OAuth2 Resource Server y JWT]] - Tu API como resource server que valida los tokens de Keycloak
- [[57 - Spring Security autenticacion y cadena de filtros]] - La cadena de filtros que recibe el token
- [[59 - Docker Compose]] - Levantar Keycloak en el entorno de desarrollo
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Tests con @WithJwt sin Keycloak levantado
- [[56 - Validacion con Spring Boot]] - Los claims del token son datos de entrada que se validan
- [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]] - Los beans de seguridad (decoder, converter)

---

## Tags
`#java #spring-boot #oauth2 #keycloak #identidad #jwt`
