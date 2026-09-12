---
tags: [java, docker, compose, contenedores, devops, base-de-datos]
---

# 59 - Docker Compose

Docker Compose levanta todo el entorno que tu aplicación necesita (base de datos, caché, colas) con un solo archivo YAML y un solo comando. Es la respuesta al "en mi máquina funciona": el entorno deja de ser algo que cada desarrollador monta a mano y pasa a ser código versionado junto al proyecto.

---

# METODO EXPLICATIVO

## 1. El problema: mi maquina no es tu maquina

Tu aplicación Spring Boot necesita una base de datos PostgreSQL para funcionar. Opciones tradicionales: instalar PostgreSQL en tu máquina (versión concreta, puerto concreto, usuario concreto), rezar para que coincida con la del resto del equipo y con la de producción. Cada discrepancia es un bug que solo aparece en un entorno y horas perdidas en "a mí me funciona".

Docker Compose elimina la instalación local: la base de datos corre en un **contenedor** (un proceso aislado con su propio sistema de archivos y red, creado desde una imagen versionada). Todos usan la misma imagen, la misma versión, la misma configuración, porque todo está escrito en el archivo `compose.yaml` del proyecto.

## 2. El archivo compose: servicios, imagenes y puertos

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: midb
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secreto
    ports:
      - "5432:5432"
    volumes:
      - datos-postgres:/var/lib/postgresql/data

volumes:
  datos-postgres:
```

Cada `service` es un contenedor: `image` dice qué software y versión, `environment` pasa configuración, `ports` publica puertos hacia tu máquina (`"5432:5432"` = el 5432 del contenedor accesible en tu 5432), y `volumes` da persistencia. Sin el volumen, los datos morirían al parar el contenedor; con él, sobreviven entre arranques.

Con este archivo, `docker compose up -d` levanta PostgreSQL 16 con la base `midb` creada y accesible en `localhost:5432`. Tu `application.properties` apunta ahí y la aplicación no sabe (ni le importa) si la base es local o un contenedor.

## 3. El ciclo de vida diario

Tres comandos cubren el día a día:

- **`docker compose up -d`**: levanta los servicios en segundo plano (`-d` = detached). La primera vez descarga las imágenes; las siguientes arranca en segundos.
- **`docker compose ps` / `docker compose logs db`**: ver qué corre y leer los logs de un servicio cuando algo falla.
- **`docker compose down`**: para y elimina los contenedores (los volúmenes sobreviven salvo que pidas `-v`, que los borra y resetea los datos a cero).

Ese último detalle es una herramienta, no solo limpieza: `down -v` te devuelve una base vacía, perfecto para reproducir un bug "desde cero" o para arrancar los tests de integración contra un estado conocido.

## 4. Conectar la aplicacion: red y variables

Dentro de la red que Compose crea, los servicios se ven por su nombre: si añades tu app como servicio, su JDBC URL apunta a `jdbc:postgresql://db:5432/midb` (nombre `db`, no `localhost`). Desde fuera del Compose (app en tu IDE, base en Compose), usas `localhost` con el puerto publicado.

Las credenciales no deberían vivir en el `compose.yaml` versionado en git si es un proyecto real: se pasan por archivo `.env` (que Compose lee automáticamente) o variables de entorno:

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: ${DB_NAME:-midb}
      POSTGRES_USER: ${DB_USER:-app}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
```

`${VAR:-defecto}` usa el valor de la variable o el defecto si no existe. El `.env` con los secretos reales va al `.gitignore`; el repo lleva un `.env.example` con la estructura vacía.

## 5. Compose para desarrollo y para tests

Dos usos que van más allá de "levantar la base":

- **Entorno de desarrollo completo**: además de la base, defines servicios para Redis (caché), Mailhog (correo falso local), o la propia app contenerizada. `up` levanta el mundo entero y `down` lo recoge. Onboarding de un compañero nuevo: clonar, `up`, programar.
- **Bases para tests de integración**: Testcontainers puede leer el `compose.yaml` y levantar exactamente esos servicios durante la suite de tests, contra la base real en vez de H2. Así los tests de integración prueban contra el mismo PostgreSQL 16 que usará producción.

---

## Errores Comunes

> **Mapear el puerto sin comprobar que esta libre**. Si ya tienes un PostgreSQL instalado ocupando el 5432, el contenedor no arranca. O paras el local o mapeas otro puerto (`"5433:5432"`) y ajustas la JDBC URL.

> **Perder datos por no definir volumenes**. Sin volumen, `docker compose down` borra los datos con el contenedor. En desarrollo duele (hay que recargar fixtures); si el contenedor es de produccion, es un desastre. Volumen siempre para datos que importan.

> **Versionar secretos en el compose.yaml**. Usuario y contrasena en git es una credencial comprometida desde el primer push. Variables de entorno y `.env` ignorado por git; el repo solo lleva `.env.example`.

> **Usar `localhost` desde dentro de otro contenedor**. Cada contenedor tiene su propio localhost: desde la app contenerizada, la base esta en `db`, no en `localhost`. `localhost` solo funciona desde fuera del Compose (tu IDE, tu terminal).

> **Olvidar fijar la version de la imagen (`postgres` sin tag)**. Sin tag, `latest` cambia con el tiempo y un dia descargas una version mayor incompatible. Fija siempre la version (`postgres:16`): reproducible hoy y dentro de un ano.

---

## Conexiones

- [[49 - Spring Boot arranque de aplicacion]] - La app que se conecta a los servicios del Compose
- [[54 - Plugin de construccion de Spring Boot]] - build-image como alternativa para contenerizar la app
- [[58 - Pruebas Spring Boot MockMvc y SpringBootTest]] - Bases reales para tests de integracion
- [[60 - Spotless y formato automatico]] - Otra herramienta que se ejecuta igual en todas las maquinas
- [[65 - Keycloak (identidad y seguridad)]] - Keycloak como servicio del compose de desarrollo

---

## Tags
`#java #docker #compose #contenedores #devops`
