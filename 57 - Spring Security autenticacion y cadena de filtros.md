---
tags: [java, spring-boot, spring-security, autenticacion, filtros, seguridad]
---

# 57 - Spring Security autenticacion y cadena de filtros

Spring Security protege tu aplicación con una cadena de filtros: cada petición HTTP pasa por una serie de controles (¿quién eres? ¿puedes hacer esto?) antes de llegar a tu controller. Entender esa cadena es entender toda la seguridad de Spring.

---

# METODO EXPLICATIVO

## 1. Las dos preguntas de la seguridad

Toda seguridad se reduce a dos preguntas distintas. **Autenticación**: ¿quién eres? (comprobar identidad con usuario/contraseña, token, certificado). **Autorización**: ¿puedes hacer esto? (comprobar permisos sobre el recurso pedido). El orden importa: primero hay que saber quién llama para después decidir qué le está permitido.

Spring Security separa ambas: la autenticación establece la identidad en el contexto de seguridad, y la autorización la consulta para permitir o denegar. Confundirlas es el error conceptual más común: "está logueado" no significa "puede borrar pedidos".

## 2. La cadena de filtros: todo pasa por aqui

En una app web, Spring Security es una **cadena de filtros servlet** interpuesta entre el servidor y tus controllers. Cada petición la atraviesa filtro a filtro, en orden, antes de llegar a tu código:

1. Filtros de contexto: preparan el `SecurityContext` vacío para la petición.
2. Filtros de autenticación: intentan identificar quién llama (cabecera Authorization, formulario de login, token).
3. Filtros de autorización: comprueban si la identidad puede acceder al recurso.
4. Si todo pasa, la petición llega al controller. Si algo falla, se responde 401 (no identificado) o 403 (identificado pero sin permiso) sin tocar tu código.

La consecuencia clave: **tu controller nunca ve peticiones no autorizadas**. La seguridad no está en cada método, está en la puerta. Esto es defensa en profundidad aplicada a la arquitectura: un solo punto de control en vez de cien comprobaciones repartidas.

## 3. SecurityFilterChain: la configuracion moderna

Desde Spring Security 5.7 la configuración es un bean `SecurityFilterChain` construido con lambdas. Este ejemplo muestra el esqueleto típico de una API con JWT:

```java
@Configuration
@EnableWebSecurity
public class SeguridadConfig {

    @Bean
    public SecurityFilterChain cadenaSeguridad(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())  // APIs sin sesion no usan CSRF
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/publico/**").permitAll()  // abierto
                .requestMatchers("/admin/**").hasRole("ADMIN")  // solo admins
                .anyRequest().authenticated()  // resto: identificado
            )
            .sessionManagement(sesion ->
                sesion.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
        return http.build();
    }
}
```

Tres decisiones en tres bloques: sin CSRF (es protección para sesiones con cookies, no para APIs con tokens), reglas de acceso por ruta de más específica a más general, y sesión sin estado (cada petición trae su propio token, el servidor no guarda nada). El orden de las reglas importa: la primera que coincide decide.

## 4. Donde vive la identidad: el SecurityContext

Cuando un filtro autentica con éxito, guarda la identidad en el `SecurityContextHolder`: un objeto con el `Authentication` actual (principal, credenciales, autoridades/roles). Cualquier código posterior puede consultarlo:

```java
// Dentro de un controller o servicio con peticion autenticada
Authentication auth = SecurityContextHolder.getContext().getAuthentication();
String usuario = auth.getName();
boolean esAdmin = auth.getAuthorities().stream()
    .anyMatch(a -> a.getAuthority().equals("ROLE_ADMIN"));
```

El contexto vive atado al hilo de la petición y se limpia al terminar: nunca hay fugas entre peticiones. Y una regla de oro: la identidad se **lee**, no se escribe a mano. Si tu código mete un `Authentication` falso en el contexto para "saltarse" la seguridad en un test o un atajo, estás mintiendo al sistema sobre quién llama.

## 5. PasswordEncoder: las contrasenas nunca en claro

Si tu app guarda usuarios con contraseña, jamás se almacena el texto original: se guarda un **hash** producido por un `PasswordEncoder` (BCrypt por defecto). Al hacer login, Spring hashea la contraseña recibida y compara hashes, nunca textos:

```java
@Bean
public PasswordEncoder codificador() {
    return new BCryptPasswordEncoder();
}

// Al registrar: se guarda el hash, no la contrasena
usuario.setPassword(codificador().encode(passwordEnClaro));

// Al autenticar: Spring compara con matches(), nunca con equals
```

BCrypt incluye una sal aleatoria por contraseña: dos usuarios con la misma clave tienen hashes distintos, y el algoritmo es deliberadamente lento para que probar millones de combinaciones sea inviable. Guardar contraseñas en claro o con MD5/SHA1 no es "seguridad débil": es no tener seguridad.

## 6. 401 vs 403: leer el codigo de estado

Cuando la seguridad rechaza, el código dice exactamente qué pasó: **401 Unauthorized** significa "no sé quién eres" (falta token, token caducado, credenciales erróneas). **403 Forbidden** significa "sé quién eres y no puedes" (usuario válido sin el rol necesario). Ante un fallo de acceso, mirar el código ahorra la mitad del diagnóstico: 401 se arregla en el login/token, 403 en los roles/permisos.

---

## Errores Comunes

> **Confundir autenticacion con autorizacion**. Tener login no da permisos automaticamente. Disena las dos capas por separado: quien eres (filtros de autenticacion) y que puedes hacer (reglas de acceso).

> **Poner `.anyRequest().permitAll()` por comodidad durante el desarrollo y olvidarlo**. La aplicacion sale a produccion abierta. La regla por defecto debe ser denegar (`authenticated()`) y abrir solo rutas concretas.

> **Ordenar mal las reglas de acceso**. Las reglas se evaluan en orden y la primera coincidencia gana. Una regla amplia antes que una especifica la eclipsa y la especifica nunca se aplica.

> **Guardar contrasenas en claro o con hash rapido (MD5, SHA1)**. Las tablas arcoiris las rompen en segundos. Usa siempre `PasswordEncoder` con BCrypt o superior.

> **Desactivar CSRF sin entender por que**. En APIs stateless con tokens es correcto; en aplicaciones con sesiones y formularios es abrir la puerta a ataques. Cada desactivacion de seguridad debe tener su justificacion.

---

## Conexiones

- [[22 - Separacion de Responsabilidades]] - La seguridad como capa transversal fuera de la logica
- [[40 - Manejo de Excepciones]] - AccessDeniedException y AuthenticationException
- [[49 - Spring Boot arranque de aplicacion]] - El starter de seguridad activa la cadena en el arranque
- [[52 - Starters de Spring Boot en Maven]] - El starter de seguridad
- [[56 - Validacion con Spring Boot]] - Validar datos que entran vs autenticar personas que llaman
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Probar endpoints protegidos con usuarios simulados

---

## Tags
`#java #spring-boot #spring-security #seguridad #autenticacion`
