---
tags: [java, fundamentos, poo, oop, programacion, principios]
---

# 30 - Principio fundamental de Java (y POO)

Este documento no explica una caracteristica de Java. Explica como TIENES QUE PENSAR antes de escribir codigo. La Programacion Orientada a Objetos no es un monton de reglas. Es una forma de ver el mundo y traducirla a codigo.

---

# METODO 1,2,3 (Progresivo)

## NIVEL 1 - Junior

### Como piensa una computadora?

Una computadora solo entiende instrucciones muy simples, una detras de otra:

```
1. Guarda 5 en memoria
2. Guarda 3 en memoria
3. Suma los dos numeros
4. Muestra el resultado
```

Esto se llama **programacion imperativa**: le dices paso a paso lo que tiene que hacer.

### Como piensa una persona?

Las personas no piensan en pasos. Piensan en **objetos del mundo real**:

- "Tengo un perro. Se llama Max. Es marron. Puede ladrar y correr."
- "Tengo un coche. Es rojo. Puede acelerar y frenar."

La Programacion Orientada a Objetos (POO) une estas dos formas de pensar. Tomas objetos del mundo real y los conviertes en codigo.

### Que es un objeto en programacion?

Un objeto tiene dos cosas:

| Lo que tiene (datos) | Lo que hace (comportamiento) |
|---------------------|------------------------------|
| nombre | ladrar() |
| raza | correr() |
| edad | comer() |
| color | dormir() |

```java
// Esto es un objeto Perro traducido a Java
public class Perro {
    // Datos (atributos)
    String nombre;
    String raza;
    int edad;

    // Comportamiento (metodos)
    public void ladrar() {
        System.out.println("Guau! Me llamo " + nombre);
    }

    public void correr() {
        System.out.println(nombre + " esta corriendo");
    }
}
```

```java
public class Principal {
    public static void main(String[] args) {
        Perro p = new Perro();
        p.nombre = "Max";
        p.raza = "Labrador";
        p.edad = 3;

        p.ladrar();  // "Guau! Me llamo Max"
        p.correr();  // "Max esta corriendo"
    }
}
```

### La idea principal

La programacion orientada a objetos es COPIAR EL MUNDO REAL dentro del codigo.

En el mundo real hay objetos (personas, coches, perros, facturas). Cada objeto tiene datos (nombre, color, precio) y puede hacer cosas (caminar, acelerar, ladrar, imprimir).

En Java, haces lo mismo: creas clases que representan esos objetos, les pones atributos (datos) y metodos (acciones).

### El error del principiante

Los programadores nuevos suelen pensar en FUNCIONES y VARIABLES sueltas:

```java
// Forma de pensar NO orientada a objetos:
String nombrePerro = "Max";
int edadPerro = 3;
String razaPerro = "Labrador";

void ladrar() { System.out.println("Guau"); }
void correr() { System.out.println("Corriendo"); }
```

Esto funciona, pero cuando tienes 2 perros, 10 perros, 1000 perros, se vuelve un caos.

```java
// Forma orientada a objetos:
Perro p1 = new Perro("Max", "Labrador", 3);
Perro p2 = new Perro("Luna", "Pastor Aleman", 2);
Perro p3 = new Perro("Chispa", "Chihuahua", 5);
// Cada perro tiene sus datos y sus metodos. Todo ordenado.
```

### La regla de oro del nivel 1

> **Piensa en objetos del mundo real y traduCelos a clases de Java. Cada objeto tiene datos (atributos) y acciones (metodos).**

---

## NIVEL 2 - Mid

### Como piensa un programador antes de escribir codigo?

Antes de escribir UNA SOLA LINEA de codigo, el programador se hace estas preguntas:

1. **Que objetos hay en mi problema?**
2. **Que datos tiene cada objeto?**
3. **Que acciones puede hacer cada objeto?**
4. **Como se relacionan los objetos entre si?**

### Ejemplo: modelar una biblioteca

**Paso 1: Identificar los objetos**

En una biblioteca hay:
- Libros
- Socios
- Prestamos

**Paso 2: Datos de cada objeto**

```
Libro:    titulo, autor, isbn, disponible
Socio:    nombre, numeroSocio, email
Prestamo: libro, socio, fechaPrestamo, fechaDevolucion
```

**Paso 3: Acciones de cada objeto**

```
Libro:    prestar(), devolver(), estaDisponible()
Socio:    obtenerNombre(), obtenerEmail()
Prestamo: calcularDiasRetraso(), estaVencido()
```

**Paso 4: Relaciones entre objetos**

```
Un Socio puede tener varios Prestamos
Un Prestamo tiene UN Libro y UN Socio
Un Libro puede estar en cero o un Prestamo
```

### Esto traducido a Java

```java
public class Libro {
    private String titulo;
    private String autor;
    private String isbn;
    private boolean disponible;

    public Libro(String titulo, String autor, String isbn) {
        this.titulo = titulo;
        this.autor = autor;
        this.isbn = isbn;
        this.disponible = true;
    }

    public boolean prestar() {
        if (!disponible) return false;
        disponible = false;
        return true;
    }

    public void devolver() {
        disponible = true;
    }

    public boolean estaDisponible() { return disponible; }
    public String getTitulo() { return titulo; }
}
```

```java
public class Socio {
    private String nombre;
    private int numeroSocio;
    private String email;

    public Socio(String nombre, int numeroSocio, String email) {
        this.nombre = nombre;
        this.numeroSocio = numeroSocio;
        this.email = email;
    }

    public String getNombre() { return nombre; }
    public int getNumeroSocio() { return numeroSocio; }
}
```

```java
import java.time.LocalDate;

public class Prestamo {
    private Libro libro;
    private Socio socio;
    private LocalDate fechaPrestamo;
    private LocalDate fechaDevolucion;

    public Prestamo(Libro libro, Socio socio) {
        this.libro = libro;
        this.socio = socio;
        this.fechaPrestamo = LocalDate.now();
        this.fechaDevolucion = null;
    }

    public void devolver() {
        this.fechaDevolucion = LocalDate.now();
        libro.devolver();
    }

    public boolean estaVencido() {
        if (fechaDevolucion != null) return false;
        return LocalDate.now().isAfter(fechaPrestamo.plusDays(14));
    }

    public long diasRetraso() {
        if (!estaVencido()) return 0;
        return LocalDate.now().toEpochDay() - fechaPrestamo.plusDays(14).toEpochDay();
    }
}
```

```java
public class Biblioteca {
    public static void main(String[] args) {
        var libro = new Libro("Cien anios de soledad", "Garcia Marquez", "978-84-376-0494-7");
        var socio = new Socio("Ana Martinez", 101, "ana@email.com");

        if (libro.prestar()) {
            var prestamo = new Prestamo(libro, socio);
            System.out.println("Libro prestado a " + socio.getNombre());
            System.out.println("Vence en 14 dias");
        }
    }
}
```

### La pregunta clave en cada decision

Cuando disenas una clase, preguntate:

> "Esto es responsabilidad de este objeto o de otro?"

- `Libro` sabe si esta disponible -> metodo `estaDisponible()` en `Libro`
- `Prestamo` sabe si esta vencido -> metodo `estaVencido()` en `Prestamo`
- Socio sabe su nombre -> metodo `getNombre()` en `Socio`

Cada objeto es responsable de sus propios datos.

### La regla de oro del nivel 2

> **Antes de escribir codigo, identifica los objetos del problema, sus datos, sus acciones y como se relacionan. Cada objeto es responsable de si mismo.**

---

## NIVEL 3 - Senior

### Los 4 pilares de la POO

Son las bases sobre las que se construye todo el diseno orientado a objetos.

### 1. Abstraccion: solo mostrar lo necesario

Un coche tiene muchas piezas (motor, ruedas, electronica). Pero cuando conduces, solo ves el volante, los pedales y la palanca de cambios.

En programacion es igual: una clase muestra solo lo que otros necesitan saber y oculta el resto.

```java
public class Coche {
    private Motor motor;      // Oculto
    private Rueda[] ruedas;   // Oculto
    private int velocidad;    // Oculto

    // Esto es lo que el conductor ve y usa:
    public void acelerar() { motor.aumentarPotencia(); }
    public void frenar() { motor.reducirPotencia(); }
    public void girarVolante(int grados) { ruedas[0].girar(grados); }
    public int getVelocidad() { return velocidad; }
}
```

El conductor NO necesita saber como funciona el motor. Solo necesita `acelerar()`, `frenar()` y `girarVolante()`.

### 2. Encapsulamiento: proteger los datos

Un cajero automatico tiene dinero dentro. Tu no puedes abrir el cajero y coger el dinero directamente. Tienes que usar los metodos del cajero: insertar tarjeta, poner PIN, retirar dinero.

```java
public class Cajero {
    private double dineroDisponible;  // Nadie toca esto directamente

    public boolean retirar(double cantidad, String pin) {
        if (!validarPin(pin)) return false;
        if (cantidad > dineroDisponible) return false;
        dineroDisponible -= cantidad;
        return true;
    }

    private boolean validarPin(String pin) {
        return pin.equals("1234");
    }
}
```

`dineroDisponible` es `private`. Nadie puede hacer `cajero.dineroDisponible = 999999;` porque esta protegido.

### 3. Herencia: reutilizar lo comun

Un coche, una moto y un camion son todos "vehiculos". Comparten cosas (marca, modelo, velocidad) pero cada uno tiene sus particularidades.

```java
public class Vehiculo {
    protected String marca;
    protected String modelo;
    protected int velocidad;

    public void acelerar(int incremento) {
        velocidad += incremento;
    }

    public void frenar(int decremento) {
        velocidad = Math.max(0, velocidad - decremento);
    }
}
```

```java
public class Coche extends Vehiculo {
    private int numPuertas;

    public void abrirMaletero() {
        System.out.println("Maletero abierto");
    }
}

public class Moto extends Vehiculo {
    private boolean tieneSidecar;

    public void hacerCaballito() {
        System.out.println("Haciendo caballito!");
    }
}
```

`Coche` tiene todo lo de `Vehiculo` (marca, modelo, acelerar, frenar) MAS lo suyo (numPuertas, abrirMaletero). No tienes que escribir dos veces lo mismo.

### 4. Polimorfismo: mismo mensaje, distinto comportamiento

El boton "reproducir" funciona en Spotify, en YouTube, en un DVD. El mensaje es el mismo ("reproducir"), pero cada uno hace algo distinto.

```java
public interface Reproducible {
    void reproducir();
}

public class CancionSpotify implements Reproducible {
    public void reproducir() {
        System.out.println("Streaming desde la nube...");
    }
}

public class DVD implements Reproducible {
    public void reproducir() {
        System.out.println("Leyendo disco optico...");
    }
}

public class Radio implements Reproducible {
    public void reproducir() {
        System.out.println("Sintonizando frecuencia...");
    }
}
```

```java
public class Reproductor {
    public static void main(String[] args) {
        List<Reproducible> lista = List.of(
            new CancionSpotify(),
            new DVD(),
            new Radio()
        );

        for (var item : lista) {
            item.reproducir();  // Mismo mensaje, comportamiento distinto
        }
    }
}
```

Esto imprime:
```
Streaming desde la nube...
Leyendo disco optico...
Sintonizando frecuencia...
```

El codigo que llama a `reproducir()` no sabe ni le importa QUE tipo de objeto es. Solo sabe que puede reproducirlo. Eso es polimorfismo.

### Como pensar como senior

Un programador senior no empieza escribiendo codigo. Empieza con preguntas:

1. **Cual es el problema real?** (No el que te pidieron, el que NECESITAN resolver)
2. **Cuales son los objetos principales?** (Los que cambian, los que tienen datos)
3. **Que contratos hay entre objetos?** (Quien necesita que de quien)
4. **Que puede cambiar en el futuro?** (Si anadimos un nuevo tipo, cuanto codigo hay que tocar)
5. **Que puede fallar?** (Y como evitarlo)

### La regla de oro del nivel 3

> **Disena pensando en el futuro. Programa contra interfaces, no contra implementaciones. Cada clase debe tener una sola responsabilidad. Protege los datos. Reutiliza lo comun. Permite que distintos objetos respondan al mismo mensaje de forma distinta.**

---

# METODO PROFUNDO (Curso de pensamiento OOP)

## 1. Por que existe la POO?

### La historia en dos minutos

Al principio, los programas eran una lista de instrucciones (programacion lineal). Funcionaba para programas pequenos.

Luego los programas crecieron. Miles de lineas. Todo mezclado. Cambiar una cosa rompia otra. Era un caos.

Los programadores se dieron cuenta de que el problema no era tecnico, era de **organizacion**. Necesitaban una forma de organizar el codigo que reflejara como pensamos los humanos.

La solucion fue la POO: en vez de escribir un monton de funciones sueltas, creas "objetos" que representan cosas del mundo real y cada objeto se encarga de si mismo.

### El cambio mental

```
Programacion lineal:
  - "Que tengo que hacer?" -> Funciones
  - Todo suelto, todo global
  - Cambiar algo = riesgo de romper todo

Programacion orientada a objetos:
  - "Que objetos hay?" -> Clases
  - Cada uno se encarga de si mismo
  - Cambiar un objeto no afecta a los demas
```

---

## 2. El mundo esta lleno de objetos

### Como observar la realidad para modelarla

Para programar orientado a objetos, tienes que aprender a ver el mundo como objetos.

Mira a tu alrededor. Todo lo que ves puede ser un objeto:

```
Tu:               Persona (nombre, edad, altura, caminar(), hablar(), comer())
Tu telefono:      Telefono (marca, modelo, bateria, llamar(), enviarMensaje())
Tu mochila:       Mochila (color, peso, capacidad, abrir(), cerrar(), guardar())
Una factura:      Factura (numero, fecha, total, imprimir(), calcularIVA())
Un pedido:        Pedido (id, cliente, productos, total, agregarProducto(), pagar())
```

### El ejercicio mental

Antes de escribir codigo para cualquier cosa, haz este ejercicio:

1. **Enumera los objetos** que aparecen en el problema
2. **Para cada objeto**, escribe en papel:
   - Que datos tiene
   - Que acciones puede hacer
3. **Dibuja flechas** entre objetos que se relacionan

```
Ejemplo: sistema de restaurante

Objetos:
  Cliente:     nombre, mesa, pedido / hacerPedido(), pagar()
  Camarero:    nombre, seccion / tomarPedido(), servirMesa(), cobrar()
  Cocinero:    nombre, especialidad / cocinarPlato(), prepararPedido()
  Plato:       nombre, precio, ingredientes / estaDisponible()
  Pedido:      numero, cliente, platos, total / agregarPlato(), calcularTotal()

Relaciones:
  Cliente -> hace -> Pedido
  Camarero -> toma -> Pedido
  Camarero -> lleva -> Plato
  Cocinero -> prepara -> Plato
  Pedido -> contiene -> Plato
```

Este dibujo es tu MAPA. Con el mapa, escribir codigo es solo traducir.

---

## 3. Los 4 pilares con analogias de la vida real

### Abstraccion: el control remoto de la TV

Tu control remoto tiene botones: volumen, canal, encendido. No ves los circuitos electronicos dentro del control. No ves como la senal infrarroja llega a la TV. Solo ves los botones.

**Eso es abstraccion**: mostrar solo lo que el usuario necesita y ocultar la complejidad.

```java
public class ControlRemoto {
    // Esto esta OCULTO (circuitos internos)
    private void emitirInfrarrojo(String codigo) {
        // Complejidad electronica...
    }

    // Esto esta VISIBLE (botones)
    public void subirVolumen() { emitirInfrarrojo("VOL+"); }
    public void bajarVolumen() { emitirInfrarrojo("VOL-"); }
    public void cambiarCanal(int canal) { emitirInfrarrojo("CH" + canal); }
    public void encender() { emitirInfrarrojo("POWER"); }
}
```

Tu como usuario solo necesitas `subirVolumen()`, `cambiarCanal()`. No te importa `emitirInfrarrojo()`.

### Encapsulamiento: el cajero automatico

El cajero tiene dinero dentro. No puedes abrir la puerta trasera y cogerlo. Solo puedes usar la pantalla y los botones: introducir tarjeta, poner PIN, retirar dinero.

**Eso es encapsulamiento**: proteger los datos para que solo se acceda a traves de metodos controlados.

```java
public class CuentaBancaria {
    private double saldo;  // Protegido

    public void depositar(double cantidad) {
        if (cantidad > 0) saldo += cantidad;
    }

    public boolean retirar(double cantidad) {
        if (cantidad > 0 && saldo >= cantidad) {
            saldo -= cantidad;
            return true;
        }
        return false;
    }

    public double getSaldo() {
        return saldo;
    }
}
```

Si `saldo` fuera publico, cualquiera podria hacer `cuenta.saldo = 1000000;`. Con encapsulamiento, solo puedes cambiar el saldo a traves de `depositar()` y `retirar()`, que tienen reglas.

### Herencia: los vehiculos

Un coche, una moto, un camion y un autobus son todos "vehiculos". Todos tienen ruedas, aceleran, frenan, tienen marca y modelo. Pero cada uno tiene cosas distintas: el coche tiene puertas, la moto puede hacer caballito, el camion tiene remolque.

**Eso es herencia**: poner lo comun en una clase padre y lo especifico en cada clase hija.

```
Vehiculo (clase padre)
  |- marca
  |- modelo
  |- acelerar()
  |- frenar()
  |
  +-- Coche (hereda todo + anyade)
  |     |- numPuertas
  |     |- abrirMaletero()
  |
  +-- Moto (hereda todo + anyade)
  |     |- tieneSidecar
  |     |- hacerCaballito()
  |
  +-- Camion (hereda todo + anyade)
        |- capacidadCarga
        |- cargarMercancia()
```

### Polimorfismo: el boton "reproducir"

Piensa en todos los dispositivos que tienen un boton de "reproducir":
- En Spotify: reproduce musica desde internet
- En YouTube: reproduce video desde internet
- En un DVD: reproduce disco
- En una radio: sintoniza emisora
- En un movil: reproduce un archivo

El boton tiene la misma forma, el mismo nombre, pero CADA UNO HACE ALGO DISTINTO.

**Eso es polimorfismo**: el mismo mensaje (`reproducir()`) se comporta de forma distinta segun el objeto que lo recibe.

```java
// Todos entienden "reproducir", pero cada uno lo hace a su manera
List<Reproducible> dispositivos = List.of(
    new Spotify(), new DVD(), new Radio()
);

for (var d : dispositivos) {
    d.reproducir();  // Cada uno hace lo suyo
}
```

---

## 4. Como pensar antes de escribir codigo (el proceso mental)

### Paso 1: Entiende el problema

No escribas una linea hasta que entiendas QUE necesitas resolver.

```
"Necesito un sistema para una veterinaria que gestione las mascotas,
sus duenos, las visitas y las vacunas."
```

### Paso 2: Identifica los objetos del problema

```
- Mascota (perro, gato, etc.)
- Dueno
- Visita (cada vez que viene)
- Vacuna
```
### Paso 3: Define los datos de cada objeto

```
Mascota: nombre, especie, raza, edad, dueno
Dueno:   nombre, telefono, email, direccion
Visita:  mascota, fecha, motivo, diagnostico
Vacuna:  nombre, fechaAplicacion, fechaProximaDosis
```

### Paso 4: Define las acciones de cada objeto

```
Mascota:  vacunar(), mostrarInformacion(), esMayorDeEdad()
Dueno:    agregarMascota(), obtenerMascotas()
Visita:   registrarDiagnostico(), obtenerResumen()
Vacuna:   estaVencida(), diasParaProximaDosis()
```

### Paso 5: Define las relaciones entre objetos

```
Un Dueno tiene varias Mascotas
Una Mascota tiene un Dueno
Una Mascota tiene varias Vacunas
Una Mascota tiene varias Visitas
```

### Paso 6: Ahora y solo ahora, escribe codigo

```java
// 1. Define la clase con sus datos
public class Mascota {
    private String nombre;
    private String especie;
    private String raza;
    private int edad;
    private Dueno dueno;
    private List<Vacuna> vacunas = new ArrayList<>();
    private List<Visita> visitas = new ArrayList<>();
    // ...
}

// 2. Anyade el constructor
public Mascota(String nombre, String especie, String raza, int edad, Dueno dueno) {
    this.nombre = nombre;
    this.especie = especie;
    this.raza = raza;
    this.edad = edad;
    this.dueno = dueno;
}

// 3. Anyade los metodos
public void vacunar(Vacuna vacuna) {
    vacunas.add(vacuna);
    System.out.println(nombre + " vacunado contra " + vacuna.getNombre());
}

public boolean necesitaVacuna() {
    return vacunas.stream().anyMatch(Vacuna::estaVencida);
}
```

### El mapa mental completo

```
Problema real
    |
    v
Identificar objetos (sustantivos)
    |
    v
Identificar datos de cada objeto (adjetivos/sustantivos)
    |
    v
Identificar acciones de cada objeto (verbos)
    |
    v
Identificar relaciones entre objetos
    |
    v
Escribir codigo (traducir el mapa a Java)
```

---

## 5. De la idea al codigo (ejemplo completo)

Vamos a modelar algo sencillo: un sistema de **pedidos de pizza**.

### Paso 1: objetos del problema

```
Pizza, Pedido, Cliente, Repartidor
```

### Paso 2: datos

```
Pizza:      nombre, tamano, precio, ingredientes
Pedido:     numero, cliente, pizzas, total, estado, direccion
Cliente:    nombre, telefono, direccion
Repartidor: nombre, telefono, vehiculo, estaDisponible
```

### Paso 3: acciones

```
Pizza:      mostrarInfo()
Pedido:     agregarPizza(), calcularTotal(), cambiarEstado()
Cliente:    hacerPedido(), obtenerHistorial()
Repartidor: asignarPedido(), entregar()
```

### Paso 4: relaciones

```
Un Cliente hace un Pedido
Un Pedido tiene varias Pizzas
Un Pedido es entregado por un Repartidor
```

### Paso 5: codigo

```java
public class Pizza {
    private String nombre;
    private String tamano;  // "pequena", "mediana", "familiar"
    private double precio;
    private List<String> ingredientes;

    public Pizza(String nombre, String tamano, double precio, List<String> ingredientes) {
        this.nombre = nombre;
        this.tamano = tamano;
        this.precio = precio;
        this.ingredientes = ingredientes;
    }

    public double getPrecio() { return precio; }
    public String getNombre() { return nombre; }

    public void mostrarInfo() {
        System.out.println(nombre + " (" + tamano + ") - " + precio + " euros");
        System.out.println("  Ingredientes: " + String.join(", ", ingredientes));
    }
}

public class Cliente {
    private String nombre;
    private String telefono;
    private String direccion;
    private List<Pedido> historial = new ArrayList<>();

    public Cliente(String nombre, String telefono, String direccion) {
        this.nombre = nombre;
        this.telefono = telefono;
        this.direccion = direccion;
    }

    public Pedido hacerPedido(List<Pizza> pizzas) {
        var pedido = new Pedido(this, pizzas, direccion);
        historial.add(pedido);
        System.out.println("Pedido " + pedido.getNumero() + " creado para " + nombre);
        return pedido;
    }

    public String getNombre() { return nombre; }
}

public class Pedido {
    private static int contador = 0;
    private int numero;
    private Cliente cliente;
    private List<Pizza> pizzas;
    private double total;
    private String estado;  // "pendiente", "preparando", "en camino", "entregado"
    private String direccion;
    private Repartidor repartidor;

    public Pedido(Cliente cliente, List<Pizza> pizzas, String direccion) {
        this.numero = ++contador;
        this.cliente = cliente;
        this.pizzas = pizzas;
        this.direccion = direccion;
        this.estado = "pendiente";
        this.total = calcularTotal();
    }

    private double calcularTotal() {
        return pizzas.stream().mapToDouble(Pizza::getPrecio).sum();
    }

    public void cambiarEstado(String nuevoEstado) {
        this.estado = nuevoEstado;
        System.out.println("Pedido " + numero + " ahora esta: " + estado);
    }

    public void asignarRepartidor(Repartidor r) {
        this.repartidor = r;
        cambiarEstado("en camino");
    }

    public int getNumero() { return numero; }
    public String getEstado() { return estado; }
}

public class Repartidor {
    private String nombre;
    private String telefono;
    private String vehiculo;
    private boolean disponible;

    public Repartidor(String nombre, String telefono, String vehiculo) {
        this.nombre = nombre;
        this.telefono = telefono;
        this.vehiculo = vehiculo;
        this.disponible = true;
    }

    public void asignarPedido(Pedido pedido) {
        if (!disponible) {
            System.out.println(nombre + " no esta disponible");
            return;
        }
        disponible = false;
        pedido.asignarRepartidor(this);
        System.out.println(nombre + " recogio pedido " + pedido.getNumero());
    }

    public void entregar(Pedido pedido) {
        pedido.cambiarEstado("entregado");
        disponible = true;
        System.out.println(nombre + " entrego pedido " + pedido.getNumero());
    }
}
```

### Como se usa

```java
public class Pizzeria {
    public static void main(String[] args) {
        // Crear cliente
        var ana = new Cliente("Ana", "555-1234", "Calle Mayor 10");

        // Crear pizzas
        var margarita = new Pizza("Margarita", "mediana", 9.99,
            List.of("tomate", "mozzarella", "albahaca"));
        var pepperoni = new Pizza("Pepperoni", "familiar", 14.99,
            List.of("tomate", "mozzarella", "pepperoni"));

        // Hacer pedido
        var pedido = ana.hacerPedido(List.of(margarita, pepperoni));

        // Asignar repartidor
        var carlos = new Repartidor("Carlos", "555-5678", "Moto");
        carlos.asignarPedido(pedido);

        // Entregar
        carlos.entregar(pedido);
    }
}
```

### Fijate en algo importante

Cada objeto hace LO SUYO:
- `Pizza` sabe su precio e ingredientes
- `Cliente` sabe hacer pedidos y guarda su historial
- `Pedido` sabe calcular su total y cambiar estado
- `Repartidor` sabe asignarse a pedidos y entregarlos

Ningun objeto hace cosas de otro. Cada uno es responsable de si mismo.

---

## 6. Objetos que hablan entre si (colaboracion)

En la vida real, los objetos no existen aislados. Un cliente habla con un camarero. Un camarero habla con un cocinero. Un cocinero prepara platos.

En POO es igual: los objetos se ENVIAN MENSAJE entre ellos.

### Como se comunican los objetos

Un objeto se comunica con otro llamando a sus metodos:

```java
public class Camarero {
    public void tomarPedido(Cliente cliente, Cocinero cocinero) {
        var platos = cliente.elegirPlatos();
        // El camarero le PIDE al cocinero que cocine
        cocinero.cocinar(platos);
    }
}
```

A esto se le llama **enviar mensajes**. `cocinero.cocinar(platos)` es el camarero enviandole el mensaje "cocina estos platos" al cocinero.

### La regla de la colaboracion

Un objeto puede PEDIRLE a otro que haga algo, pero no debe METERSE en sus datos internos.

```java
// MAL: el camarero accede a datos internos del cocinero
public class CamareroMal {
    public void tomarPedido(Cocinero cocinero) {
        if (cocinero.estaOcupado) {  // Accede a campo publico
            cocinero.listaEspera.add("Pizza");  // Modifica lista interna
        }
    }
}

// BIEN: el camarero le PIDE al cocinero que haga algo
public class CamareroBien {
    public void tomarPedido(Cocinero cocinero, Plato plato) {
        cocinero.recibirPedido(plato);  // Le pide que lo haga
        // Como lo haga internamente, no es problema del camarero
    }
}
```

---

## 7. POO en el mundo real (proyectos profesionales)

### Como se organiza un proyecto real

En una empresa, el codigo no esta todo mezclado. Se organiza por capas:

```
carpeta proyecto/
  |-- dominio/       -> Objetos del negocio (Pizza, Pedido, Cliente)
  |-- repositorio/   -> Guardar y recuperar datos (en BD, archivos, etc.)
  |-- servicio/      -> Logica de negocio (RegistrarPedido, GestionarReparto)
  |-- presentacion/  -> Interfaz de usuario (pantallas, API web)
  |-- aplicacion/    -> Punto de entrada (main)
```

Cada capa habla con la siguiente a traves de interfaces, no directamente.

### Principio de Responsabilidad Unica (SRP)

En la vida real, un cocinero no lava los platos (hay lavaplatos para eso). Un camarero no cocina (hay cocineros para eso).

Cada objeto tiene UNA responsabilidad:

```java
// Bien: cada clase hace una cosa
public class PedidoRepositorio {
    public void guardar(Pedido p) { /* SQL */ }
}

public class PedidoServicio {
    public void procesar(Pedido p) {
        // Logica de negocio
        p.cambiarEstado("confirmado");
        new PedidoRepositorio().guardar(p);
        new EmailServicio().enviarConfirmacion(p);
    }
}

// Mal: una clase que hace de todo
public class TodoEnUno {
    public void procesarPedido(Pedido p) {
        // Valida, guarda, envia email, genera factura, imprime recibo...
        // TODO AQUI! Si algo cambia, hay que tocar esta clase
    }
}
```

### Inyeccion de dependencias

En vez de que un objeto cree sus dependencias dentro, las recibe de fuera:

```java
// Mal: el servicio crea sus dependencias
public class PedidoServicioMal {
    private PedidoRepositorio repositorio = new PedidoRepositorio();
    private EmailServicio email = new EmailServicio();
}

// Bien: las dependencias llegan de fuera
public class PedidoServicioBien {
    private final PedidoRepositorio repositorio;
    private final EmailServicio email;

    public PedidoServicioBien(PedidoRepositorio repositorio, EmailServicio email) {
        this.repositorio = repositorio;
        this.email = email;
    }
}
```

Esto permite cambiar el repositorio (por ejemplo, pasar de archivos a base de datos) sin tocar el servicio.

---

## 8. Cuando NO usar POO

No todo necesita ser un objeto. A veces es mejor usar algo mas simple.

### Cuando usar una clase normal

```java
// Cuando el objeto tiene estado que cambia y comportamiento
public class CuentaBancaria {
    private double saldo;
    public void depositar(double c) { saldo += c; }
    public void retirar(double c) { if (saldo >= c) saldo -= c; }
}
```

### Cuando usar un record (datos inmutables)

```java
// Cuando solo necesitas agrupar datos, sin comportamiento complejo
public record Coordenadas(double latitud, double longitud) {}
public record Direccion(String calle, String ciudad, String codigoPostal) {}
```

### Cuando usar metodos static (utilidades)

```java
// Cuando no hay estado, solo transformacion de datos
public class UtilidadesTexto {
    public static String capitalizar(String s) {
        if (s == null || s.isBlank()) return "";
        return s.substring(0, 1).toUpperCase() + s.substring(1).toLowerCase();
    }
}
```

### Cuando usar un enum (conjunto fijo de opciones)

```java
public enum EstadoPedido {
    PENDIENTE, PREPARANDO, EN_CAMINO, ENTREGADO, CANCELADO
}
```

### La regla practica

```
Pregunta: Esto tiene datos que cambian y comportamiento?
  Si  -> Clase (o record si es inmutable)
  No, solo agrupa datos -> Record
  No, solo transforma -> Metodos static
  No, es un conjunto fijo -> Enum
```

---

## Errores Comunes

> Crear una clase "Datos" que solo tiene getters y setters sin comportamiento. Eso es un "anemico". Una clase deberia tener comportamiento, no solo datos.

> Poner toda la logica en una sola clase (God Object). Una clase que hace de todo es el peor anti-patron. Divide y venceras.

> Olvidar el encapsulamiento y hacer todo `public`. Cualquiera puede modificar los datos sin control.

> Crear herencia cuando no es necesaria. No heredes solo para reutilizar codigo. Hereda cuando hay una relacion "es un" real (un Coche ES UN Vehiculo).

> Programar pensando en funciones sueltas en vez de objetos. Si tienes funciones que reciben y modifican datos de varias clases, probablemente esas funciones deberian estar dentro de las clases.

> No disenar antes de codificar. El mayor error es abrir el editor y empezar a escribir sin tener claro que objetos hay.

> Ignorar las relaciones entre objetos. Cada objeto existe en un contexto con otros objetos. Las relaciones son tan importantes como los objetos mismos.

---

## Buenas Practicas

1. **Piensa antes de escribir.** Identifica objetos, datos, acciones y relaciones. Haz un mapa mental o dibujo.
2. **Cada clase una responsabilidad.** Si no puedes explicar lo que hace una clase en una frase, dividela.
3. **Encapsula los datos.** Atributos `private`, acceso por metodos.
4. **Programa contra interfaces.** Usa `interface` o clases abstractas para definir contratos.
5. **Preferir composicion sobre herencia.** Un coche TIENE UN motor, no ES UN motor.
6. **Los objetos se comunican por mensajes.** Un objeto le pide a otro que haga algo, no accede a sus datos.
7. **Disena pensando en el futuro.** Que pasa si anadimos un nuevo tipo? Cuanto codigo hay que cambiar?
8. **Usa `record` para datos inmutables.** Menos codigo, menos errores.
9. **Inyeccion de dependencias.** Recibe las dependencias por constructor, no las crees dentro.
10. **No te cases con la POO.** A veces un metodo static o un enum es la solucion correcta.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Como se escribe una clase en Java
- [[05 - Modificadores de Acceso]] - Encapsulamiento (private, public)
- [[06 - Atributos y Campos]] - Datos de un objeto
- [[07 - Constructores y this]] - Como nace un objeto
- [[08 - Instanciacion y new]] - Crear objetos
- [[09 - Multiples Objetos e Identidad]] - Objetos distintos, misma clase
- [[10 - Metodos de Instancia]] - Comportamiento de los objetos
- [[19 - Separacion de Responsabilidades]] - Una clase, una responsabilidad
- [[21 - Getters y Setters]] - Acceso controlado a datos
- [[22 - Separacion de Responsabilidades]] - Arquitectura en capas, inyeccion de dependencias
- [[26 - Private en Profundidad]] - Proteger datos
- [[27 - Singleton en Profundidad]] - Objeto unico global
- [[30 - Principio fundamental de Java (y POO)]] - Esto es el documento que estas leyendo

---

## Tags
`#java #fundamentos #poo #oop #programacion #principios #pilares`