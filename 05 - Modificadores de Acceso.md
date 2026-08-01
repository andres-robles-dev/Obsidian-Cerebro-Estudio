---
tags: [java, fundamentos, modificadores, acceso, encapsulamiento]
---

# 05 - Modificadores de Acceso

---

## NIVEL JUNIOR

### Que son los modificadores de acceso?

Controlan quien puede ver y usar tus clases, metodos y atributos. Piensa en ellos como niveles de privacidad.

### Los cuatro niveles

| Modificador                   | Quien puede acceder           |
| ----------------------------- | ----------------------------- |
| `public`                      | Todos (todo el mundo)         |
| `private`                     | Solo esta clase               |
| *sin escribir nada* (default) | Solo clases del mismo paquete |
| `protected`                   | Mismo paquete + subclases     |

### El mas importante ahora: private

Los atributos de una clase deben ser `private` para que nadie los modifique directamente:

```java
public class CuentaBancaria {
    private double saldo;

    public void depositar(double cantidad) {
        if (cantidad > 0) {
            saldo = saldo + cantidad;
        }
    }

    public double obtenerSaldo() {
        return saldo;
    }
}
```

Desde fuera no puedes hacer `cuenta.saldo = 500;` porque `saldo` es `private`. Solo puedes usar los metodos publicos.

### Ejemplo basico

```java
public class Persona {
    public String nombre;     // Lo ve todo el mundo
    private int edad;         // Solo lo ve esta clase

    public void cumplirAnios() {
        edad = edad + 1;
    }

    public int obtenerEdad() {
        return edad;
    }
}
```

En el main:
```java
Persona p = new Persona();
p.nombre = "Ana";          // OK, es public
p.edad = 30;               // ERROR: edad es private
System.out.println(p.obtenerEdad());  // OK, el metodo es public
```

---

## NIVEL MID

### Public: acceso total

```java
public class Calculadora {
    public int sumar(int a, int b) {
        return a + b;
    }
}
```

`public` significa que cualquiera desde cualquier parte del proyecto puede usar esta clase o metodo.

### Private: solo yo

```java
public class Usuario {
    private String contrasenia;

    private String encriptar(String texto) {
        // Logica de encriptacion
        return "***" + texto + "***";
    }

    public void cambiarContrasenia(String nueva) {
        this.contrasenia = encriptar(nueva);
    }
}
```

`encriptar` es `private` porque es un detalle interno. Nadie fuera de la clase necesita saber como encriptas.

### Default (sin modificador): mismo paquete

```java
// Archivo: modelo/Producto.java
package modelo;

class Producto {
    String nombre;  // default: solo visible en el paquete modelo
}
```

Si no pones `public` ni `private`, solo las clases en el mismo paquete (`modelo`) pueden verlo.

### Protected: herencia

```java
public class Animal {
    protected String especie;

    protected void hacerSonido() {
        System.out.println("Sonido de " + especie);
    }
}

public class Perro extends Animal {
    public void ladrar() {
        especie = "Canino";  // OK, Perro hereda de Animal
        hacerSonido();
        System.out.println("Guau!");
    }
}
```

---

## NIVEL SENIOR

### Encapsulamiento real con records

Los `record` son inherentemente encapsulados: sus campos son `private final` y solo se accede por los metodos del componente:

```java
public record Usuario(String nombre, String email, int edad) {
    // Los campos son private final por defecto
    // Solo accedes con usuario.nombre(), usuario.email(), usuario.edad()
}

public class DemoRecord {
    public static void main(String[] args) {
        var usuario = new Usuario("Ana", "ana@email.com", 30);
        System.out.println(usuario.nombre());  // Acceso por metodo, no por campo
        // usuario.nombre = "otro"; // ERROR: no se puede modificar
    }
}
```

### Modularidad con module-info (Java 9+)

En proyectos grandes puedes controlar que paquetes se exportan:

```java
// module-info.java
module com.miapp.modelo {
    exports com.miapp.modelo;           // Solo esto es visible fuera
    exports com.miapp.servicios;
    // Lo no exportado queda oculto
}
```

### Interfaces con miembros privados (Java 9+)

Las interfaces pueden tener metodos privados para compartir codigo:

```java
public interface Repositorio {
    Usuario buscarPorId(long id);

    default Usuario buscarOCrear(long id, String nombre) {
        var encontrado = buscarPorId(id);
        return encontrado != null ? encontrado : crear(id, nombre);
    }

    private Usuario crear(long id, String nombre) {
        // Logica compartida solo dentro de la interfaz
        return new Usuario(nombre, id);
    }
}
```

### Clases selladas y acceso (Java 17+)

Controlas exactamente que clases pueden heredar:

```java
public sealed class Resultado permite Exito, Error {
    // ...
}

public final class Exito extends Resultado {
    private final Object dato;

    public Exito(Object dato) {
        this.dato = dato;
    }
}

public final class Error extends Resultado {
    private final String mensaje;

    public Error(String mensaje) {
        this.mensaje = mensaje;
    }
}
```

---

## Errores Comunes

> Poner todo `public`. Los atributos deben ser `private`. Solo los metodos que forman parte de la API publica deben ser `public`.

> Pensar que `private` en un metodo lo oculta a todos. Las clases anidadas (inner classes) pueden acceder a los `private` de la clase contenedora.

> Usar `protected` sin necesidad de herencia. Si no vas a heredar, usa `private` o `public`.

> Olvidar que `default` (sin modificador) no es lo mismo que `public`. Default = solo mismo paquete.

---

## Buenas Practicas

1. Atributos siempre `private`. Acceso mediante [[21 - Getters y Setters]].
2. Metodos `public` solo si forman parte de la API. Metodos de ayuda interna van `private`.
3. Clases `public` solo si se usan fuera del paquete. Si solo se usan internamente, no pongas modificador.
4. Prefiere `record` para datos simples: ya son inmutables y encapsulados.
5. No expongas colecciones mutables. Devuelve copias o listas inmutables con `List.copyOf()`.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Toda clase tiene un modificador
- [[06 - Atributos y Campos]] - Campos private con getters
- [[07 - Constructores y this]] - Constructores publicos para crear objetos
- [[10 - Metodos de Instancia]] - Metodos publicos vs privados
- [[19 - Separacion de Responsabilidades]] - Que va en public vs private
- [[21 - Getters y Setters]] - Acceso controlado a atributos private
- [[22 - Separacion de Responsabilidades]] - SRP y encapsulamiento
- [[25 - Paquetes y Organizacion]] - Visibilidad por paquetes

---

## Tags
`#java #fundamentos #modificadores #acceso #encapsulamiento #public #private #protected`
