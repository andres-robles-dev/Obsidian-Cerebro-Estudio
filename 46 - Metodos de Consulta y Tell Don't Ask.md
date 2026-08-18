---
tags: [java, poo, tell-dont-ask, metodos-consulta, encapsulamiento, diseno, anti-patron]
---

# 46 - Metodos de Consulta y Tell Don't Ask

Un **metodo de consulta** (query method) es un metodo que responde una pregunta sobre el estado del objeto sin modificarlo y sin efectos secundarios. Un **metodo de comando** (command method) es un metodo que cambia el estado del objeto. **Tell Don't Ask** es el principio que dice: dile al objeto que haga algo en vez de preguntarle por sus datos para decidir tu. Juntos forman la base del diseno orientado a objetos correcto.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Que es un query method?

Es un metodo que **devuelve informacion** sobre el objeto. No cambia nada. Solo responde.

```java
public class CuentaBancaria {
    private double saldo;

    public CuentaBancaria(double saldo) {
        this.saldo = saldo;
    }

    // QUERY: solo lee, no cambia nada
    public double getSaldo() {
        return saldo;
    }

    // QUERY con logica: responde una pregunta
    public boolean estaEnRojo() {
        return saldo < 0;
    }

    // COMMAND: cambia el estado
    public void depositar(double cantidad) {
        saldo += cantidad;
    }
}
```

### La diferencia basica

```java
CuentaBancaria cuenta = new CuentaBancaria(1000);

// Query: "Cual es tu saldo?" -> respuesta, sin cambios
double saldo = cuenta.getSaldo();      // 1000.0
boolean rojo = cuenta.estaEnRojo();    // false

// Command: "Deposita 500" -> cambia el estado
cuenta.depositar(500);                 // saldo ahora es 1500
```

### Por que importa?

Si tu codigo siempre pregunta y luego actua, es fragil. Si el objeto puede actuar solo, es robusto.

```java
// MAL: preguntar y decidir fuera
if (cuenta.getSaldo() > 0) {
    System.out.println("Tiene dinero");
}

// BIEN: el objeto sabe responder
if (!cuenta.estaEnRojo()) {
    System.out.println("Tiene dinero");
}
```

---

## NIVEL 2 - Mid

### Refactorizar de Ask a Tell

**Antes (Ask)**: el codigo externo pregunta por datos y decide.

```java
public class TicketSoporte {
    private String estado;   // "abierto", "en_progreso", "cerrado"
    private int prioridad;   // 1=alta, 2=media, 3=baja

    public String getEstado() { return estado; }
    public int getPrioridad() { return prioridad; }
}

// El servicio pregunta y decide
public class SoporteServicio {
    public String asignarAgente(TicketSoporte ticket) {
        if (ticket.getEstado().equals("abierto")) {
            if (ticket.getPrioridad() == 1) {
                return "Agente Senior";
            } else {
                return "Agente Junior";
            }
        }
        return "Sin asignar";
    }
}
```

**Despues (Tell)**: el objeto sabe que responder.

```java
public class TicketSoporte {
    private String estado;
    private int prioridad;

    public TicketSoporte(String estado, int prioridad) {
        this.estado = estado;
        this.prioridad = prioridad;
    }

    // QUERY: el objeto responde la pregunta
    public boolean estaAbierto() {
        return "abierto".equals(estado);
    }

    // QUERY: el objeto sabe que agente le corresponde
    public String agenteAsignado() {
        if (!estaAbierto()) {
            return "Sin asignar";
        }
        return prioridad == 1 ? "Agente Senior" : "Agente Junior";
    }
}

// El servicio solo le dice que haga algo
public class SoporteServicio {
    public String asignarAgente(TicketSoporte ticket) {
        return ticket.agenteAsignado();   // Tell, no Ask
    }
}
```

### Queries que componen

Un query puede usar otros queries internamente:

```java
public class CarritoCompras {
    private List<ItemCarrito> items;

    public CarritoCompras(List<ItemCarrito> items) {
        this.items = List.copyOf(items);
    }

    // Query basico
    public int totalItems() {
        return items.size();
    }

    // Query que compone: usa stream + map + sum
    public double totalPagar() {
        return items.stream()
            .mapToDouble(ItemCarrito::subtotal)
            .sum();
    }

    // Query boolean
    public boolean estaVacio() {
        return items.isEmpty();
    }

    // Query que filtra
    public List<ItemCarrito> itemsConDescuento() {
        return items.stream()
            .filter(ItemCarrito::tieneDescuento)
            .toList();
    }
}
```

### Commands que usan queries internamente

```java
public class CarritoCompras {
    private List<ItemCarrito> items = new ArrayList<>();

    // COMMAND: usa query interno para decidir
    public void agregarItem(ItemCarrito item) {
        if (item.cantidad() <= 0) {
            throw new IllegalArgumentException("Cantidad invalida");
        }
        items.add(item);
    }

    // COMMAND: usa query para validar
    public void eliminarItem(int indice) {
        if (indice < 0 || indice >= items.size()) {
            throw new IndexOutOfBoundsException("Indice invalido: " + indice);
        }
        items.remove(indice);
    }
}
```

---

## NIVEL 3 - Senior

### Patron completo: Modelo rico vs anemico

```java
// ============ MODELO ANEMICO (MAL) ============
// Solo getters y setters, toda la logica esta fuera
public record PedidoAnemico(String id, List<String> productos, double total) {}

public class PedidoLogica {
    public boolean esValido(PedidoAnemico pedido) {
        return pedido.total() > 0 && !pedido.productos().isEmpty();
    }

    public String resumen(PedidoAnemico pedido) {
        return pedido.id() + ": " + pedido.productos().size()
             + " items, $" + pedido.total();
    }

    public double descuento(PedidoAnemico pedido) {
        return pedido.total() > 100 ? pedido.total() * 0.1 : 0;
    }
}

// ============ MODELO RICO (BIEN) ============
// El objeto sabe todo sobre si mismo
public class Pedido {
    private final String id;
    private final List<String> productos;
    private final double total;

    public Pedido(String id, List<String> productos, double total) {
        this.id = id;
        this.productos = List.copyOf(productos);
        this.total = total;
    }

    // QUERIES: el objeto responde preguntas sobre si mismo
    public boolean esValido() {
        return total > 0 && !productos.isEmpty();
    }

    public int cantidadProductos() {
        return productos.size();
    }

    public String resumen() {
        return id + ": " + cantidadProductos() + " items, $" + total;
    }

    public double descuento() {
        return total > 100 ? total * 0.1 : 0;
    }

    public double totalConDescuento() {
        return total - descuento();
    }

    public boolean esPedidoGrande() {
        return cantidadProductos() > 10;
    }

    // GETTERS solo para datos que otros necesitan leer
    public String id() { return id; }
    public List<String> productos() { return productos; }
    public double total() { return total; }
}
```

### Tell Don't Ask en la practica

```java
// ============ ANTES (Ask) ============
public void procesarPedido(Pedido pedido) {
    if (pedido.total() > 0 && !pedido.productos().isEmpty()) {
        double descuento = pedido.total() > 100 ? pedido.total() * 0.1 : 0;
        double final = pedido.total() - descuento;
        System.out.println("Pedido " + pedido.id() + ": $" + final);
    }
}

// ============ DESPUES (Tell) ============
public void procesarPedido(Pedido pedido) {
    if (pedido.esValido()) {
        System.out.println("Pedido " + pedido.id()
            + ": $" + pedido.totalConDescuento());
    }
}
```

### Ley de Demeter (no hablar con extraños)

No accedas a objetos a traves de multiples puntos. Cada metodo solo debe hablar con sus "amigos" directos.

```java
// MAL: cadena de getters (viola Ley de Demeter)
String ciudad = pedido.cliente().direccion().ciudad();

// BIEN: el objeto expone lo que necesitas
String ciudad = pedido.ciudadEnvio();
```

```java
public class Pedido {
    private final Cliente cliente;

    // QUERY que encapsula la cadena
    public String ciudadEnvio() {
        return cliente.direccion().ciudad();
    }
}
```

### Query methods en records

Los records son ideales para query methods porque son inmutables:

```java
public record Punto(double x, double y) {

    // QUERY: distancia a otro punto
    public double distanciaA(Punto otro) {
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }

    // QUERY: esta dentro de un radio?
    public boolean dentroDeRadio(Punto centro, double radio) {
        return distanciaA(centro) <= radio;
    }

    // QUERY: punto medio entre este y otro
    public Punto puntoMedio(Punto otro) {
        return new Punto((this.x + otro.x) / 2, (this.y + otro.y) / 2);
    }

    // QUERY: es igual a otro dentro de una tolerancia
    public boolean casiIgual(Punto otro, double tolerancia) {
        return distanciaA(otro) < tolerancia;
    }
}
```

### Composicion de queries con Streams

```java
public class Equipo {
    private final String nombre;
    private final List<Desarrollador> miembros;

    public Equipo(String nombre, List<Desarrollador> miembros) {
        this.nombre = nombre;
        this.miembros = List.copyOf(miembros);
    }

    // Query compuesto:_STREAM pipeline como query
    public long cantidadSeniors() {
        return miembros.stream()
            .filter(d -> d.nivel() == Nivel.SENIOR)
            .count();
    }

    public Optional<Desarrollador> buscarPorNombre(String nombre) {
        return miembros.stream()
            .filter(d -> d.nombre().equalsIgnoreCase(nombre))
            .findFirst();
    }

    public Map<Nivel, List<Desarrollador>> agruparPorNivel() {
        return miembros.stream()
            .collect(Collectors.groupingBy(Desarrollador::nivel));
    }

    public boolean estaCompleto() {
        return miembros.size() >= 3 && cantidadSeniors() >= 1;
    }
}
```

---

# METODO PROFUNDO

## 1. Que es un query method

Un **query method** es un metodo que:
- **Devuelve un valor** (no es void)
- **No modifica el estado** del objeto
- **No tiene efectos secundarios** (no escribe en disco, no imprime, no llama servicios externos)
- **Es determinista**: dados los mismos datos, siempre devuelve lo mismo

```java
public class Producto {
    private String nombre;
    private double precio;
    private int stock;

    // QUERIES: solo devuelven informacion
    public String getNombre() { return nombre; }
    public double getPrecio() { return precio; }
    public int getStock() { return stock; }
    public boolean estaDisponible() { return stock > 0; }
    public boolean esCaro() { return precio > 100; }
    public double precioConIva(double iva) { return precio * (1 + iva); }
}
```

**No es un query method:**

```java
// COMMAND: cambia estado
public void setPrecio(double precio) { this.precio = precio; }

// COMMAND: tiene efecto secundario
public void mostrarEnPantalla() { System.out.println(nombre); }

// NO es query ni command: es invalido
public void procesar() { throw new UnsupportedOperationException(); }
```

---

## 2. Command vs Query (CQS)

El principio **CQS** (Command-Query Separation) de Bertrand dice: cada metodo debe ser O command O query, nunca ambos.

```java
public class Buffer {
    private String contenido = "";

    // QUERY: solo lee
    public String getContenido() { return contenido; }
    public boolean estaVacio() { return contenido.isEmpty(); }
    public int longitud() { return contenido.length(); }

    // COMMAND: solo modifica
    public void escribir(String texto) { contenido += texto; }
    public void limpiar() { contenido = ""; }

    // VIOLACION: command que devuelve valor
    // (允される例外: pop, remove, etc.)
    public String escribirYDevolver(String texto) {
        contenido += texto;
        return contenido;  // command + query en uno
    }
}
```

**Excepciones validas**: metodos como `Set.add()` devuelven `boolean` indicando si cambio el set. Son commands que dan feedback util.

---

## 3. Tell Don't Ask - Principio completo

El principio dice: **no preguntes a un objeto por su estado para luego tomar una decision fuera de el. Dile que tome la decision el.**

```java
// ============ ASK: preguntar y decidir fuera ============
public class GeneradorFactura {
    public String generar(Cliente cliente) {
        if (cliente.esVip()) {                        // Pregunta
            return "Factura VIP: " + cliente.nombre()  // Decide fuera
                 + " - sin IVA";
        } else {
            return "Factura normal: " + cliente.nombre()
                 + " - con IVA";
        }
    }
}

// ============ TELL: el objeto decide ============
public class Cliente {
    private final String nombre;
    private final boolean vip;

    public Cliente(String nombre, boolean vip) {
        this.nombre = nombre;
        this.vip = vip;
    }

    // El objeto sabe responder
    public String formatoFactura() {
        return vip ? "Factura VIP: " + nombre + " - sin IVA"
                   : "Factura normal: " + nombre + " - con IVA";
    }

    public String nombre() { return nombre; }
    public boolean esVip() { return vip; }
}

public class GeneradorFactura {
    public String generar(Cliente cliente) {
        return cliente.formatoFactura();  // Tell, no Ask
    }
}
```

### Escalabilidad del patron

Con Ask, cada cambio implica modificar el generador. Con Tell, el objeto crece solo.

```java
// Con Tell, agregar un nuevo tipo de factura NO toca el generador
public class Cliente {
    // ... codigo anterior ...

    public String formatoFactura() {
        if (vip) return "VIP: " + nombre + " - sin IVA";
        if (orporativo()) return "Corp: " + nombre + " - ref: " + idCorporativo;
        return "Normal: " + nombre + " - con IVA";
    }
}
```

---

## 4. Refactorizar de ask a tell (receta)

### Paso 1: Identifica el codigo Ask

Busca patrones como:

```java
if (objeto.getDato() == valor) { ... }
if (objeto.getDato() > limite) { ... }
String tipo = objeto.getTipo();
switch (tipo) { ... }
```

### Paso 2: Mueve la logica al objeto

```java
// ANTES
public class Email {
    private String asunto;
    private String contenido;
    private boolean leido;

    public String getAsunto() { return asunto; }
    public String getContenido() { return contenido; }
    public boolean isLeido() { return leido; }
}

// Servicio que pregunta
public String resumen(Email email) {
    String estado = email.isLeido() ? "leido" : "nuevo";
    return "[" + estado + "] " + email.getAsunto();
}

// DESPUES
public class Email {
    private String asunto;
    private String contenido;
    private boolean leido;

    // Query: el objeto sabe su resumen
    public String resumen() {
        String estado = leido ? "leido" : "nuevo";
        return "[" + estado + "] " + asunto;
    }

    public String asunto() { return asunto; }
    public String contenido() { return contenido; }
    public boolean leido() { return leido; }
}

// Servicio simple
public String resumen(Email email) {
    return email.resumen();
}
```

### Paso 3: Verifica que no quede logica Ask

```java
// MAL: mezclar Tell y Ask
String r = email.resumen();              // Tell
if (email.getAsunto().contains("urgente")) {  // Ask: todavia pregunto
    enviarNotificacion(email);
}

// BIEN: Tell completo
if (email.esUrgente()) {   // Query method en el objeto
    enviarNotificacion(email);
}
```

---

## 5. Modelo anemico vs rico

**Modelo anemico**: clases con solo getters/setters, toda la logica vive en "servicios" o "utilidades". Es un anti-patron.

**Modelo rico**: el objeto contiene sus propias reglas de negocio como query methods y commands.

```java
// ============ ANEMICO ============
public class CuentaAnemica {
    private double saldo;
    private double limite;

    public double getSaldo() { return saldo; }
    public void setSaldo(double saldo) { this.saldo = saldo; }
    public double getLimite() { return limite; }
    public void setLimite(double limite) { this.limite = limite; }
}

// Logica dispersa en servicios
public class CuentaServicio {
    public boolean puedeRetirar(CuentaAnemica c, double monto) {
        return c.getSaldo() - monto >= -c.getLimite();  // Ask
    }

    public void retirar(CuentaAnemica c, double monto) {
        if (puedeRetirar(c, monto)) {
            c.setSaldo(c.getSaldo() - monto);           // Ask + Command
        }
    }
}

// ============ RICO ============
public class Cuenta {
    private double saldo;
    private double limite;

    public Cuenta(double saldo, double limite) {
        this.saldo = saldo;
        this.limite = limite;
    }

    // QUERIES
    public double saldo() { return saldo; }
    public double limite() { return limite; }

    public boolean puedeRetirar(double monto) {
        return saldo - monto >= -limite;   // Logica DENTRO del objeto
    }

    public double saldoDisponible() {
        return saldo + limite;
    }

    // COMMANDS
    public void retirar(double monto) {
        if (!puedeRetirar(monto)) {
            throw new IllegalStateException(
                "Saldo insuficiente. Disponible: " + saldoDisponible());
        }
        saldo -= monto;
    }

    public void depositar(double monto) {
        if (monto <= 0) throw new IllegalArgumentException("Monto invalido");
        saldo += monto;
    }
}
```

---

## 6. Query methods en records

Los records son inmutables por naturaleza, asi que todos sus metodos custom son queries.

```java
public record Rango(int inicio, int fin) {

    // Precondicion: validacion en compact constructor
    public Rango {
        if (inicio > fin) {
            throw new IllegalArgumentException(
                "Inicio (" + inicio + ") no puede ser mayor que fin (" + fin + ")");
        }
    }

    // QUERIES
    public int longitud() { return fin - inicio; }

    public boolean contiene(int valor) {
        return valor >= inicio && valor <= fin;
    }

    public boolean solapaCon(Rango otro) {
        return this.inicio <= otro.fin && otro.inicio <= this.fin;
    }

    public Rango union(Rango otro) {
        return new Rango(
            Math.min(this.inicio, otro.inicio),
            Math.max(this.fin, otro.fin)
        );
    }

    public Rango interseccion(Rango otro) {
        int nuevoInicio = Math.max(this.inicio, otro.inicio);
        int nuevoFin = Math.min(this.fin, otro.fin);
        if (nuevoInicio > nuevoFin) {
            return null;  // No hay interseccion
        }
        return new Rango(nuevoInicio, nuevoFin);
    }

    public boolean estaVacio() { return inicio == fin; }

    public List<Integer> aLista() {
        return IntStream.range(inicio, fin).boxed().toList();
    }
}
```

---

## 7. Boolean query methods

Los boolean queries son los mas comunes. Siguen la convencion `isXxx()` o `tieneXxx()`.

```java
public class Password {
    private final String valor;

    public Password(String valor) {
        this.valor = valor;
    }

    // BOOLEAN QUERIES: resuelven preguntas comunes
    public boolean esFuerte() {
        return tieneLongitudMinima() && tieneMayuscula()
             && tieneMinuscula() && tieneDigito();
    }

    public boolean tieneLongitudMinima() { return valor.length() >= 8; }
    public boolean tieneMayuscula() {
        return valor.chars().anyMatch(Character::isUpperCase);
    }
    public boolean tieneMinuscula() {
        return valor.chars().anyMatch(Character::isLowerCase);
    }
    public boolean tieneDigito() {
        return valor.chars().anyMatch(Character::isDigit);
    }
    public boolean tieneCaracterEspecial() {
        return valor.chars().anyMatch(c -> "!@#$%^&*".indexOf(c) >= 0);
    }

    // Combinaciones logicas como queries
    public boolean esDebil() { return !esFuerte(); }
    public boolean cumpleRequisitos(boolean requiereFuerte) {
        return requiereFuerte ? esFuerte() : tieneLongitudMinima();
    }
}
```

---

## 8. Composicion de queries

Un query puede construirse a partir de otros queries, formando una cadena legible.

```java
public class Estudiante {
    private String nombre;
    private List<Double> calificaciones;
    private int faltas;
    private boolean activo;

    public Estudiante(String nombre, List<Double> calificaciones,
                      int faltas, boolean activo) {
        this.nombre = nombre;
        this.calificaciones = List.copyOf(calificaciones);
        this.faltas = faltas;
        this.activo = activo;
    }

    // QUERIES basicos
    public String nombre() { return nombre; }
    public boolean estaActivo() { return activo; }
    public int faltas() { return faltas; }

    // QUERIES derivados
    public double promedio() {
        return calificaciones.stream()
            .mapToDouble(Double::doubleValue)
            .average()
            .orElse(0.0);
    }

    public boolean tieneBuenaNota() { return promedio() >= 8.0; }
    public boolean tieneMuchasFaltas() { return faltas > 5; }

    // QUERY compuesto: regla de negocio completa
    public boolean aprueba() {
        return estaActivo() && tieneBuenaNota() && !tieneMuchasFaltas();
    }

    public String estado() {
        if (!estaActivo()) return "Inactivo";
        if (aprueba()) return "Aprobado";
        if (tieneBuenaNota()) return "Aprobado por nota, revisar faltas";
        return "Reprobado";
    }
}
```

---

## 9. Ley de Demeter

**No hables con extraños**: un metodo solo debe invocar metodos en:
1. Su propio objeto (`this`)
2. Parametros del metodo
3. Objetos que crea internamente
4. Variables de campo directas

```java
// ============ VIOLACION ============
public class Fabrica {
    private Departamento departamento;

    public String nombreGerenteFabrica() {
        return departamento.getGerente().getNombre();  // Habla con 2 extraños
    }
}

// ============ CUMPLIMIENTO ============
public class Fabrica {
    private Departamento departamento;

    public String nombreGerenteFabrica() {
        return departamento.nombreGerente();  // Habla solo con su amigo
    }
}

public class Departamento {
    private Gerente gerente;

    public String nombreGerente() {
        return gerente.nombre();  // Habla solo con su amigo
    }
}
```

**Por que importa**: si cambia la estructura de `Gerente` (ej: `getNombre()` -> `nombre()`), solo afecta a `Departamento`, no a `Fabrica`.

---

## 10. Checklist de diseno

Antes de entregar codigo, verifica:

- [ ] Cada query es **sin efectos secundarios** (no modifica estado ni produce efectos)
- [ ] Cada command **cambia el estado** del objeto (no es void vacio)
- [ ] No hay metodos que sean command + query (salvo excepciones como `Set.add()`)
- [ ] La logica de negocio vive **dentro del objeto**, no en servicios externos
- [ ] No se preguntan getters para decidir fuera: se usa un query method del objeto
- [ ] No se accede a objetos a traves de cadenas largas de getters (Ley de Demeter)
- [ ] Los records usan metodos custom para logica, no solo accessors
- [ ] Los boolean queries usan convencion `isXxx()`, `tieneXxx()`, `puedeXxx()`
- [ ] Las queries se componen entre si para forms queries complejas
- [ ] El modelo es rico (con comportamiento), no anemico (solo datos)

---

## Errores Comunes

> **Clase anemica con solo getters y setters**. Si tu clase no tiene ningun query method con logica, probablemente es un modelo anemico. La logica esta dispersa en servicios que preguntan y deciden.

> **Preguntar y decidir fuera del objeto**. `if (objeto.getEstado() == X) { hacer }` viola Tell Don't Ask. El objeto deberia tener un metodo que responda esa pregunta.

> **Cadena de getters**. `a.getB().getC().getD()` viola la Ley de Demeter. Cada nivel deberia exponer lo que necesitas directamente.

> **Query con efectos secundarios**. Un metodo que devuelve un valor pero tambien modifica estado, imprime o escribe en disco. Divide en dos metodos separados.

> **Logica de negocio en servicios, no en objetos**. Si tu "PedidoServicio" contiene toda la regla de negocio de Pedido, tu modelo es anemico. Mueve la logica a Pedido.

---

## Conexiones

- [[21 - Getters y Setters]] - Getters como queries basicos
- [[22 - Separacion de Responsabilidades]] - Tell Don't Ask y arquitectura
- [[26 - Private en Profundidad]] - Encapsulamiento de estado
- [[30 - Principio fundamental de Java (y POO)]], - Modelo anemico vs rico
- [[35 - Getters booleanos]] - Boolean query methods
- [[37 - Record (y Value Objects)]], - Queries en records inmutables
- [[42 - Lambda en Profundidad]] - Composicion funcional

---

## Tags
`#java #poo #tell-dont-ask #metodos-consulta #encapsulamiento #diseno #anti-patron #cqs`
