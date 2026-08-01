---
tags: [java, fundamentos, metodos, instancia, void, retorno, parametros]
---

# 10 - Metodos de Instancia

---

## NIVEL JUNIOR

### Que es un metodo?

Un metodo es una accion que un objeto puede hacer. Define **que puede hacer** un objeto.

```java
public class Perro {
    String nombre;

    public void ladrar() {
        System.out.println("Guau! Guau!");
    }
}
```

Para llamar al metodo:

```java
Perro p = new Perro();
p.ladrar();  // Imprime "Guau! Guau!"
```

### Partes de un metodo

```java
public void saludar() {
    System.out.println("Hola!");
}
```

- `public` - quien puede verlo
- `void` - no devuelve nada
- `saludar` - nombre del metodo
- `()` - parametros (vacios aqui)
- `{ }` - el codigo que ejecuta

### Metodo con parametros

Los parametros son datos que el metodo necesita para funcionar:

```java
public void saludarA(String nombre) {
    System.out.println("Hola " + nombre + "!");
}
```

```java
p.saludarA("Ana");  // Imprime "Hola Ana!"
```

### Metodo que devuelve algo

```java
public int sumar(int a, int b) {
    return a + b;
}
```

```java
int resultado = calculadora.sumar(5, 3);
System.out.println(resultado);  // 8
```

---

## NIVEL MID

### Tipos de metodos segun su proposito

```java
public class Utilidades {

    // Metodo que modifica el estado del objeto
    public void establecerNombre(String nombre) {
        this.nombre = nombre;
    }

    // Metodo que consulta el estado
    public String obtenerNombre() {
        return nombre;
    }

    // Metodo que hace un calculo y devuelve resultado
    public double calcularIVA(double precio) {
        return precio * 0.21;
    }

    // Metodo que muestra informacion (efecto secundario)
    public void mostrarEstado() {
        System.out.println("Nombre: " + nombre);
    }
}
```

### this en metodos

Dentro de un metodo, `this` es el objeto que esta ejecutando el metodo:

```java
public class Coche {
    String color;

    public void pintar(String color) {
        this.color = color;  // this.color = atributo, color = parametro
    }

    public void mostrarColor() {
        System.out.println("Color: " + this.color);  // this es opcional aqui
    }
}
```

### Sobrecarga de metodos

Varios metodos con el mismo nombre pero distintos parametros:

```java
public class Calculadora {
    public int sumar(int a, int b) {
        return a + b;
    }

    public double sumar(double a, double b) {
        return a + b;
    }

    public int sumar(int a, int b, int c) {
        return a + b + c;
    }
}
```

Java sabe cual llamar segun los tipos y cantidad de parametros.

---

## NIVEL SENIOR

### Metodos con records

```java
public record Producto(String nombre, double precio) {
    // Metodo de instancia en un record
    public double calcularIVA() {
        return precio * 0.21;
    }

    public String formatear() {
        return "%s - %.2f euros".formatted(nombre, precio);
    }
}
```

### Metodos con Optional

```java
import java.util.Optional;

public class RepositorioUsuarios {
    private List<Usuario> usuarios = new ArrayList<>();

    public Optional<Usuario> buscarPorEmail(String email) {
        return usuarios.stream()
            .filter(u -> u.email().equalsIgnoreCase(email))
            .findFirst();
    }

    public Usuario buscarOCrear(String email, String nombre) {
        return buscarPorEmail(email)
            .orElseGet(() -> {
                var nuevo = new Usuario(nombre, email);
                usuarios.add(nuevo);
                return nuevo;
            });
    }
}
```

### Metodos con varargs

```java
public class CalculadoraAvanzada {
    public double sumar(double... numeros) {
        double total = 0;
        for (double n : numeros) {
            total += n;
        }
        return total;
    }

    public double media(double... numeros) {
        if (numeros.length == 0) {
            return 0;
        }
        return sumar(numeros) / numeros.length;
    }
}

// Uso:
// calc.sumar(1, 2, 3);        // 6
// calc.sumar(1, 2, 3, 4, 5); // 15
```

### Metodos con Stream y lambdas internas

```java
public class GestorPedidos {
    private List<Pedido> pedidos = new ArrayList<>();

    public List<Pedido> filtrarUrgentes() {
        return pedidos.stream()
            .filter(Pedido::urgente)
            .toList();
    }

    public double totalPendiente() {
        return pedidos.stream()
            .filter(p -> !p.pagado())
            .mapToDouble(Pedido::total)
            .sum();
    }

    public void procesarPendientes(Consumer<Pedido> procesador) {
        pedidos.stream()
            .filter(p -> !p.pagado())
            .forEach(procesador);
    }
}
```

---

## Errores Comunes

> Olvidar la palabra `return` en metodos que no son `void`. El compilador dara error.

> Poner `return` en un metodo `void`. No puedes devolver un valor en un metodo `void`. Solo puedes poner `return;` para salir antes.

> Confundir parametros con argumentos. Los parametros son los que declaras en el metodo. Los argumentos son los valores que pasas al llamarlo.

> Llamar a un metodo de instancia sin crear el objeto. Si no es `static`, necesitas `new`.

---

## Buenas Practicas

1. Un metodo = una responsabilidad. Que haga solo una cosa.
2. Nombres verbo: `calcularTotal()`, `enviarEmail()`, `obtenerUsuario()`.
3. Parametros `final` si no los reasignas dentro.
4. Metodos cortos (< 20 lineas). Si es mas largo, dividelo.
5. `@Override` cuando sobreescribes un metodo de una superclase.
6. Documenta con [[20 - Javadoc y Documentacion]] los metodos publicos.
7. Prefiere `Optional` como retorno en vez de `null`.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Los metodos viven en la clase
- [[05 - Modificadores de Acceso]] - Control de acceso a metodos
- [[07 - Constructores y this]] - Constructores vs metodos
- [[13 - Static vs Instancia]] - Metodos de instancia vs metodos static
- [[20 - Javadoc y Documentacion]] - Documentar metodos
- [[23 - Metodos - Parametros, Retorno y Return]] - Profundizacion en metodos

---

## Tags
`#java #fundamentos #metodos #instancia #void #retorno #parametros #sobrecarga`
