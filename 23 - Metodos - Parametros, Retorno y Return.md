---
tags: [java, fundamentos, metodos, parametros, retorno, return, void, sobrecarga, paso-valor, varargs, optional, record]
---

# 23 - Metodos - Parametros, Retorno y Return

---

## NIVEL JUNIOR

### Partes de un metodo

```java
public int sumar(int a, int b) {
    return a + b;
}
```

- `public` - quien puede verlo
- `int` - tipo de dato que devuelve
- `sumar` - nombre del metodo
- `(int a, int b)` - parametros que recibe
- `return a + b` - el resultado que devuelve

### Return: devolver un valor

Todo metodo que no sea `void` debe tener `return`:

```java
public int doblar(int numero) {
    return numero * 2;
}

public String saludar(String nombre) {
    return "Hola " + nombre;
}

public boolean esMayor(int edad) {
    return edad >= 18;
}
```

### Void: sin retorno

Cuando el metodo no devuelve nada:

```java
public void mostrarMensaje(String mensaje) {
    System.out.println(mensaje);
    // No lleva return
}
```

### Parametros

Los datos que el metodo necesita para funcionar:

```java
public void imprimirSuma(int a, int b) {
    System.out.println("Suma: " + (a + b));
}

public String crearSaludo(String nombre, int edad) {
    return "Me llamo " + nombre + " y tengo " + edad + " anios";
}
```

---

## NIVEL MID

### Paso por valor

En Java, TODO se pasa por valor. Pero el comportamiento es diferente segun el tipo:

```java
public class PasoPorValor {
    public static void main(String[] args) {
        // Primitivo: se copia el valor
        int x = 10;
        cambiarPrimitivo(x);
        System.out.println(x);  // 10 (no cambio)

        // Referencia: se copia la direccion
        StringBuilder sb = new StringBuilder("Hola");
        cambiarReferencia(sb);
        System.out.println(sb);  // "Hola Mundo" (si cambio el objeto)
    }

    static void cambiarPrimitivo(int valor) {
        valor = 99;  // Cambia la copia, el original sigue igual
    }

    static void cambiarReferencia(StringBuilder s) {
        s.append(" Mundo");  // Modifica el objeto original
    }
}
```

### Sobrecarga de metodos

Mismo nombre, diferentes parametros:

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

### Return temprano (guard clause)

Salir del metodo antes si no se cumplen las condiciones:

```java
public String clasificarNota(int nota) {
    if (nota < 0 || nota > 10) {
        return "Nota invalida";  // Salida temprana
    }

    if (nota >= 9) return "Sobresaliente";
    if (nota >= 7) return "Notable";
    if (nota >= 5) return "Aprobado";
    return "Suspenso";
}
```

---

## NIVEL SENIOR

### Optional como retorno

> Ver tambien: [[41 - Optional en Profundidad]] - curso completo de Optional

Mejor que devolver `null`:

```java
import java.util.Optional;

public class RepositorioUsuarios {
    private final Map<String, Usuario> usuarios = new HashMap<>();

    public Optional<Usuario> buscarPorEmail(String email) {
        return Optional.ofNullable(usuarios.get(email));
    }

    public Usuario buscarOCrear(String email, String nombre) {
        return buscarPorEmail(email)
            .orElseGet(() -> {
                var nuevo = new Usuario(nombre, email);
                usuarios.put(email, nuevo);
                return nuevo;
            });
    }
}

// Uso:
// repo.buscarPorEmail("ana@email.com")
//     .ifPresent(u -> System.out.println(u.nombre()));
```

### Record como retorno multiple

Cuando necesitas devolver varios valores:

```java
public record ResultadoBusqueda(boolean encontrado, Usuario usuario, String mensaje) {}

public class ServicioUsuarios {
    public ResultadoBusqueda buscar(String email) {
        if (email == null || !email.contains("@")) {
            return new ResultadoBusqueda(false, null, "Email invalido");
        }

        var usuario = new Usuario("Ana", email);
        return new ResultadoBusqueda(true, usuario, "Usuario encontrado");
    }
}
```

### Varargs

Parametros variables:

```java
public class Calculadora {
    public static double media(double... numeros) {
        if (numeros.length == 0) return 0;

        double suma = 0;
        for (double n : numeros) {
            suma += n;
        }
        return suma / numeros.length;
    }

    public static String unir(String separador, String... elementos) {
        return String.join(separador, elementos);
    }
}

// Uso:
// double m = Calculadora.media(1, 2, 3, 4);
// String u = Calculadora.unir(", ", "A", "B", "C");
```

### Metodos con Stream y lambdas

```java
public class Procesador {
    public List<String> filtrarNombresLargos(List<String> nombres, int longitudMinima) {
        return nombres.stream()
            .filter(n -> n.length() >= longitudMinima)
            .map(String::toUpperCase)
            .toList();
    }

    public double sumarPrecios(List<Producto> productos) {
        return productos.stream()
            .mapToDouble(Producto::precio)
            .sum();
    }

    public <T> List<T> paginar(List<T> lista, int pagina, int tamanoPagina) {
        return lista.stream()
            .skip((pagina - 1) * tamanoPagina)
            .limit(tamanoPagina)
            .toList();
    }
}
```

### Pattern matching con return

```java
public class ProcesadorPago {
    public String procesar(MedioPago medio) {
        return switch (medio) {
            case Tarjeta t when t.saldoSuficiente() -> "Pago con tarjeta aprobado";
            case Tarjeta t -> "Saldo insuficiente";
            case Paypal p -> "Redirigiendo a Paypal";
            case Cripto c -> "Procesando criptomoneda";
            case null -> "Medio de pago no especificado";
        };
    }
}
```

---

## Errores Comunes

> Olvidar el `return` en todas las ramas. Si un metodo no-void tiene un `if` sin `else`, puede faltar el `return` en algun camino.

> Poner `return` en un metodo `void` con valor. `return;` es valido en void para salir antes, pero `return 5;` no.

> Confundir sobrecarga con override. Sobrecarga = mismo nombre, diferentes parametros en la misma clase. Override = misma firma en clase hija.

> Usar `null` como retorno en vez de `Optional` o lista vacia. `return null` obliga a quien llama a comprobar nulos.

> Pasar null como argumento cuando Optional.empty() seria mas expresivo.

---

## Buenas Practicas

1. Prefiere `Optional<T>` como retorno en vez de `null`.
2. Usa `record` para devolver multiples valores relacionados.
3. Guard clauses: valida primero y sal temprano.
4. Metodos cortos (< 20 lineas). Si necesitas mas, divide.
5. `final` en parametros si no los reasignas.
6. `@Override` siempre que sobreescribas un metodo.
7. Documenta con [[20 - Javadoc y Documentacion]]: `@param`, `@return`, `@throws`.

---

## Conexiones

- [[07 - Constructores y this]] - Constructores vs metodos
- [[10 - Metodos de Instancia]] - Metodos de instancia basicos
- [[12 - Operador Ternario]] - Ternario como return
- [[13 - Static vs Instancia]] - Metodos static vs de instancia
- [[20 - Javadoc y Documentacion]] - Documentar parametros y retorno
- [[21 - Getters y Setters]] - Getters (retorno) y Setters (void)
- [[24 - ArrayList - Metodos Avanzados]] - Metodos con Stream y colecciones

---

## Tags
`#java #fundamentos #metodos #parametros #retorno #return #void #varargs #sobrecarga #optional #record #guard-clauses`
