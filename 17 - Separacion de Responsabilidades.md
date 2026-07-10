---
tags: [java, fundamentos, arquitectura, solid, separacion-responsabilidades, cohesion, acoplamiento, capas]
---

# 17 - Separación de Responsabilidades

## Concepto Central

**Separación de responsabilidades** (Separation of Concerns, SoC) es el principio de dividir un sistema en **partes distintas**, cada una abordando una **preocupación (concern) única**. En Java se materializa como: **una clase = una responsabilidad**, **paquetes por funcionalidad**, **capas arquitectónicas** (Modelo/Vista/Controlador, DTO/Service/Repository). Su objetivo: **alta cohesión** (todo en la clase sirve a su fin) y **bajo acoplamiento** (clases dependen poco unas de otras).

## Para Qué Sirve / Cuándo Usarlo

- Código **fácil de cambiar**: tocar una regla de negocio no rompe la UI ni la BD
- **Testable**: unidad aislada = test rápido sin BD, red, UI
- **Reutilizable**: servicio de dominio usable en CLI, Web, Batch
- **Escalable**: equipos trabajan en capas distintas sin pisarse
- **Mantenible**: bugs localizados, onboarding rápido

## Sintaxis General: Patrones de Organización

### 1. Por Capas (Layered Architecture) — Común en Apps Empresariales

```
com.empresa.app
├── domain/           # Núcleo: Entidades, Value Objects, Reglas de negocio PURAS
│   ├── model/        #   Cliente, Pedido, Producto (sin dependencias externas)
│   ├── repository/   #   Interfaces: ClienteRepository (puertos)
│   └── service/      #   Servicios de dominio: TransferenciaService
├── application/      # Casos de uso / Orquestación (Input/Output ports)
│   ├── dto/          #   Data Transfer Objects (Request/Response)
│   ├── service/      #   Application Services: RealizarPedidoUseCase
│   └── mapper/       #   Domain ↔ DTO
├── infrastructure/   # Adaptadores técnicos (Implementaciones de puertos)
│   ├── persistence/  #   JPA Repositories, Spring Data, Flyway
│   ├── web/          #   Controllers REST, DTOs API, Security
│   ├── messaging/    #   Kafka, RabbitMQ producers/consumers
│   └── config/       #   Beans, DataSource, SecurityConfig
└── presentation/     # (Opcional) CLI, Swing, JavaFX, Thymeleaf
```

### 2. Por Funcionalidad (Modular / Feature-based) — Microservicios / Módulos Grandes

```
com.empresa.app
├── catalogo/
│   ├── domain/
│   ├── application/
│   └── infrastructure/
├── pedidos/
│   ├── domain/
│   ├── application/
│   └── infrastructure/
└── usuarios/
    ├── domain/
    ├── application/
    └── infrastructure/
```

### 3. Principios Guía (SOLID + extras)

| Principio | Qué Dice | Aplicación Java |
|-----------|----------|-----------------|
| **S**RP (Single Responsibility) | Una clase, una razón para cambiar | `Pedido` (datos) ≠ `PedidoService` (lógica) ≠ `PedidoController` (HTTP) |
| **O**CP (Open/Closed) | Abierto a extensión, cerrado a modificación | Interfaces + Implementaciones (`PagoStrategy`) |
| **L**SP (Liskov Substitution) | Subtipo sustituible por base | `ArrayList` donde `List`; no romper contratos |
| **I**SP (Interface Segregation) | Interfaces pequeñas y específicas | `interface Leer { }` `interface Escribir { }` no `interface CRUD { }` |
| **D**IP (Dependency Inversion) | Depende de abstracciones, no concreciones | `PedidoService` usa `ClienteRepository` (interface), no `ClienteRepositoryJpa` |
| **Tell, Don't Ask** | Manda al objeto, no le preguntas datos | `pedido.pagar()` no `if (p.getEstado()==PENDIENTE) p.setEstado(PAGADO)` |
| **Law of Demeter** | Habla solo con amigos inmediatos | `cliente.getDireccion().getCiudad()` ❌ → `cliente.ciudad()` ✅ |

## Ejemplo Propio: TiendaOnline (Capas Simplificadas)

### CAPAS)

```java
// ============================================================
// 1. DOMAIN: Núcleo puro (sin Spring, JPA, Jackson, etc.)
// ============================================================
package com.tienda.domain.model;

import java.math.BigDecimal;
import java.time.LocalDateTime;
import java.util.Objects;

// Value Object inmutable (record Java 14+)
public record Dinero(BigDecimal cantidad, String moneda) {
    public Dinero { // Compact constructor validación
        Objects.requireNonNull(cantidad);
        Objects.requireNonNull(moneda);
        if (cantidad.compareTo(BigDecimal.ZERO) < 0)
            throw new IllegalArgumentException("Dinero no negativo");
    }
    public Dinero sumar(Dinero otro) {
        if (!this.moneda.equals(otro.moneda)) throw new IllegalArgumentException("Moneda distinta");
        return new Dinero(this.cantidad.add(otro.cantidad), this.moneda);
    }
}

// Entidad con identidad y ciclo de vida
public class Producto {
    private final String sku;           // Identidad (final)
    private String nombre;              // Mutable controlado
    private Dinero precio;              // Value Object
    private int stock;                  // Estado

    public Producto(String sku, String nombre, Dinero precio, int stock) {
        this.sku = Objects.requireNonNull(sku);
        this.nombre = Objects.requireNonNull(nombre);
        this.precio = Objects.requireNonNull(precio);
        this.stock = Math.max(0, stock);
    }

    // Comportamiento: Tell, Don't Ask
    public void reservarStock(int cantidad) {
        if (cantidad <= 0) throw new IllegalArgumentException("Cantidad > 0");
        if (cantidad > stock) throw new IllegalStateException("Stock insuficiente: " + stock);
        this.stock -= cantidad;
    }

    public void reponerStock(int cantidad) {
        if (cantidad <= 0) throw new IllegalArgumentException("Cantidad > 0");
        this.stock += cantidad;
    }

    // Getters (solo lectura)
    public String getSku() { return sku; }
    public String getNombre() { return nombre; }
    public Dinero getPrecio() { return precio; }
    public int getStock() { return stock; }

    public void setNombre(String nombre) { this.nombre = Objects.requireNonNull(nombre); }
    public void setPrecio(Dinero precio) { this.precio = Objects.requireNonNull(precio); }
}

// Puerto (Interface) - Domain define QUÉ necesita, no CÓMO
public interface ProductoRepository {
    Optional<Producto> findBySku(String sku);
    void save(Producto producto);
    void delete(String sku);
}

// Servicio de dominio: lógica de negocio pura
public class InventarioService {
    private final ProductoRepository repo; // Depende de abstracción (DIP)

    public InventarioService(ProductoRepository repo) { this.repo = repo; }

    public void reponer(String sku, int cantidad) {
        Producto p = repo.findBySku(sku).orElseThrow(() -> new IllegalArgumentException("No existe: " + sku));
        p.reponerStock(cantidad);
        repo.save(p);
    }
}

// ============================================================
// 2. APPLICATION: Casos de uso / Orquestación
// ============================================================
package com.tienda.application;

import com.tienda.domain.model.*;
import java.util.List;

// DTO: Data Transfer Object (sin lógica, solo datos)
public record RegistrarProductoRequest(String sku, String nombre, BigDecimal precio, String moneda, int stock) {}
public record ProductoResponse(String sku, String nombre, String precio, String moneda, int stock) {}

// Mapper: Domain ↔ DTO (separado, testeable)
public class ProductoMapper {
    public static Producto toDomain(RegistrarProductoRequest r) {
        return new Producto(r.sku(), r.nombre(), new Dinero(r.precio(), r.moneda()), r.stock());
    }
    public static ProductoResponse toResponse(Producto p) {
        return new ProductoResponse(p.getSku(), p.getNombre(),
            p.getPrecio().cantidad().toPlainString(), p.getPrecio().moneda(), p.getStock());
    }
}

// Use Case / Application Service: Orquesta, transacciona, no lógica de negocio compleja
public class RegistrarProductoUseCase {
    private final ProductoRepository repo; // Puerto

    public RegistrarProductoUseCase(ProductoRepository repo) { this.repo = repo; }

    public ProductoResponse ejecutar(RegistrarProductoRequest request) {
        // Validación básica entrada
        if (repo.findBySku(request.sku()).isPresent())
            throw new IllegalArgumentException("SKU duplicado: " + request.sku());

        Producto producto = ProductoMapper.toDomain(request);
        repo.save(producto);
        return ProductoMapper.toResponse(producto);
    }
}

// ============================================================
// 3. INFRASTRUCTURE: Adaptadores técnicos
// ============================================================
package com.tienda.infrastructure.persistence;

import com.tienda.domain.model.*;
import org.springframework.stereotype.Repository; // Ejemplo Spring
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.Optional;

// Implementación EN MEMORIA (para test/dev) - Sin BD real
@Repository
public class InMemoryProductoRepository implements ProductoRepository {
    private final Map<String, Producto> almacen = new ConcurrentHashMap<>();

    @Override public Optional<Producto> findBySku(String sku) { return Optional.ofNullable(almacen.get(sku)); }
    @Override public void save(Producto p) { almacen.put(p.getSku(), p); }
    @Override public void delete(String sku) { almacen.remove(sku); }
}

// ============================================================
// 4. PRESENTATION: Controlador REST (Spring MVC)
// ============================================================
package com.tienda.presentation.web;

import com.tienda.application.*;
import org.springframework.web.bind.annotation.*;
import java.math.BigDecimal;

@RestController
@RequestMapping("/api/productos")
public class ProductoController {
    private final RegistrarProductoUseCase registrarUseCase;
    private final ProductoRepository repo; // Para consultas simples

    public ProductoController(RegistrarProductoUseCase registrarUseCase, ProductoRepository repo) {
        this.registrarUseCase = registrarUseCase;
        this.repo = repo;
    }

    @PostMapping
    public ProductoResponse registrar(@RequestBody RegistrarProductoRequest request) {
        return registrarUseCase.ejecutar(request);
    }

    @GetMapping("/{sku}")
    public ProductoResponse obtener(@PathVariable String sku) {
        Producto p = repo.findBySku(sku).orElseThrow(() -> new NotFoundException(sku));
        return ProductoMapper.toResponse(p);
    }
}

// ============================================================
// 5. MAIN / BOOTSTRAPPING (Spring Boot lo hace auto)
// ============================================================
package com.tienda;

import com.tienda.domain.model.*;
import com.tienda.application.*;
import com.tienda.infrastructure.persistence.*;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class TiendaOnlineApplication {
    public static void main(String[] args) { SpringApplication.run(TiendaOnlineApplication.class, args); }

    // Wiring manual si no Spring (ej: main simple)
    @Bean ProductoRepository productoRepository() { return new InMemoryProductoRepository(); }
    @Bean InventarioService inventarioService(ProductoRepository r) { return new InventarioService(r); }
    @Bean RegistrarProductoUseCase registrarUseCase(ProductoRepository r) { return new RegistrarProductoUseCase(r); }
}
```

## Explicación Detallada

| Capa | Responsabilidad | Ejemplo | Depende de |
|------|-----------------|---------|------------|
| **Domain** | Reglas de negocio puras, invariantes | `Producto.reservarStock()`, `Dinero` | **Nada externo** (solo JDK) |
| **Application** | Orquesta caso de uso, transacción, DTOs | `RegistrarProductoUseCase` | Domain (interfaces) |
| **Infrastructure** | Implementa puertos: BD, HTTP, MQ, FS | `InMemoryProductoRepository` | Domain (interfaces), librerías técnicas |
| **Presentation** | Entrada/Salida usuario: REST, CLI, UI | `ProductoController` | Application (Use Cases) |

**Flujo petición POST `/api/productos`**:
1. `ProductoController` recibe JSON → `RegistrarProductoRequest` (DTO)
2. Llama `RegistrarProductoUseCase.ejecutar(request)`
3. Use Case: valida duplicado → `ProductoMapper.toDomain()` → `repo.save()` (puerto)
4. `InMemoryProductoRepository` (impl) guarda en `Map`
5. Use Case → `ProductoMapper.toResponse()` → `ProductoResponse`
6. Controller devuelve JSON 201

## Errores Comunes

> [!warning] **Error 1: Lógica de negocio en Controller**
> ```java
> @PostMapping void crear(@RequestBody PedidoRequest r) {
>     if (r.total() < 0) ... // ❌ Validación negocio en web layer
>     repo.save(new Pedido(...));
> }
> ```
> ✅ **Use Case** valida y orquesta. Controller solo HTTP.

> [!warning] **Error 2: Entidad anémica (solo getters/setters)**
> ```java
> class Pedido { private Estado estado; // sin métodos
>   public Estado getEstado() { return estado; }
>   public void setEstado(Estado e) { this.estado = e; } }
> // Servicio: if (p.getEstado()==PENDIENTE) p.setEstado(PAGADO); // ❌ Tell, Don't Ask
> ```
> ✅ `p.pagar()` encapsula transición.

> [!warning] **Error 3: Domain depende de Infrastructure**
> ```java
> // En domain/model/Pedido.java
> import javax.persistence.Entity; // ❌ JPA contamina dominio
> @Entity class Pedido { ... }
> ```
> ✅ Domain **sin anotaciones** JPA. Infrastructure mapea (JPA entities aparte o Spring Data JDBC).

> [!warning] **Error 4: Servicio gigante (God Service)**
> ```java
> class TiendaService { // 50 métodos: usuarios, productos, pedidos, pagos, emails, reports
> }
> ```
> ✅ **Un servicio por caso de uso / agregado**: `RegistrarPedidoUseCase`, `CancelarPedidoUseCase`, `InventarioService`.

> [!warning] **Error 5: DTOs filtrando a Domain**
> ```java
> class PedidoService { void crear(PedidoRequest req) { // req es DTO web
>     Pedido p = new Pedido(req.getClienteId(), ...); // ❌ Domain conoce DTO
> }}
> ```
> ✅ **Mapper** en Application: `PedidoMapper.toDomain(req)` → Domain limpio.

## Buenas Prácticas

1. **Domain = JDK only** — Sin Spring, JPA, Jackson, Lombok (opcional `@Value`/`@Data` en records).
2. **Interfaces en Domain, Impl en Infrastructure** — DIP real.
3. **Constructores validan invariantes** — Objeto siempre válido (`requireNonNull`, checks).
4. **Records para Value Objects / DTOs** — Inmutables, `equals/hashCode/toString` gratis.
5. **Un caso de uso = una clase Application Service** — `RegistrarPedidoUseCase`, no `PedidoService.registrar()`.
6. **Mappers estáticos sin estado** — `ProductoMapper.toDomain()`, `toResponse()`.
7. **Tests: Domain unitarios rápidos, Application con mocks de puertos, Infrastructure de integración**.
8. **Paquetes por capa O por feature** — Consistente en todo el proyecto.

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Clase = una responsabilidad.
- `[[05 - Modificadores de Acceso]]` — `private` campos, `public` solo API.
- `[[06 - Atributos y Campos]]` — Encapsulamiento real (Tell, Don't Ask).
- `[[07 - Constructores y this]]` — Invariantes en constructor.
- `[[09 - Multiples Objetos e Identidad]]` — Entidades con identidad (`sku`), VOs sin identidad.
- `[[10 - Metodos de Instancia]]` — Comportamiento en objeto, no en servicio anémico.
- `[[13 - Static vs Instancia]]` — Servicios de aplicación sin estado (stateless) = `static` o singleton bean.
- `[[16 - Convenciones de Nombrado]]` — Paquetes `domain`, `application`, `infrastructure`.

## Resumen en Una Frase

> **Domain (reglas puras) → Application (orquesta casos de uso) → Infrastructure (impl. técnicas) → Presentation (HTTP/CLI); cada capa conoce solo a la inferior mediante interfaces.**

---

## Tags
`#java #fundamentos #arquitectura #solid #separacion-responsabilidades #capas #domain-driven-design #dip #clean-architecture #cohesion #acoplamiento`