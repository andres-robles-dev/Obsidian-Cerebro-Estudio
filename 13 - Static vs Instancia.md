---
tags: [java, fundamentos, static, instancia, clase, objeto, memoria]
---

# 13 - Static vs Instancia

---

## NIVEL JUNIOR

### Que es static?

`static` significa que algo pertenece a la **clase**, no a los objetos. Existe una sola vez y se comparte entre todos los objetos.

### La diferencia clave

**Instancia** (sin `static`): cada objeto tiene su propia copia
**Static**: hay una sola copia compartida

### Ejemplo visual

```java
public class Contador {
    static int total = 0;   // Uno para todos
    int numero;              // Uno por cada objeto

    public Contador() {
        total++;
        numero = total;
    }
}

public class Principal {
    public static void main(String[] args) {
        Contador c1 = new Contador();  // total=1, c1.numero=1
        Contador c2 = new Contador();  // total=2, c2.numero=2
        Contador c3 = new Contador();  // total=3, c3.numero=3

        System.out.println(c1.numero);  // 1
        System.out.println(c2.numero);  // 2
        System.out.println(c3.numero);  // 3
        System.out.println(Contador.total);  // 3 (compartido)
    }
}
```

### Metodos static

Los metodos `static` se llaman sin crear un objeto:

```java
public class Calculos {
    public static int sumar(int a, int b) {
        return a + b;
    }
}

public class Principal {
    public static void main(String[] args) {
        int r = Calculos.sumar(5, 3);  // No hace falta new
        System.out.println(r);
    }
}
```

---

## NIVEL MID

### Cuando usar static y cuando instancia

```java
public class Ejemplo {
    // STATIC: compartido, pertenece a la clase
    static String nombreApp = "MiApp";
    static final int VERSION = 1;

    // INSTANCIA: propio de cada objeto
    String nombre;
    int edad;
}

// Acceso:
System.out.println(Ejemplo.nombreApp);     // static: por el nombre de la clase
Ejemplo obj = new Ejemplo();
System.out.println(obj.nombre);            // instancia: por el objeto
```

### Reglas importantes

Lo que NO puede hacer un metodo `static`:
- Usar `this` (no hay objeto)
- Acceder a campos de instancia directamente
- Llamar a metodos de instancia directamente

Lo que SI puede hacer:
- Acceder a otros campos y metodos `static`
- Recibir objetos como parametros y trabajar con ellos

```java
public class Reglas {
    int numero = 10;
    static int estatico = 20;

    public void metodoInstancia() {
        System.out.println(numero);      // OK
        System.out.println(estatico);     // OK
    }

    public static void metodoEstatico() {
        // System.out.println(numero);   // ERROR
        System.out.println(estatico);     // OK
    }
}
```

### Metodo main: el static mas importante

```java
public class Aplicacion {
    public static void main(String[] args) {
        // main es static para que la JVM lo llame sin crear objetos
        Aplicacion app = new Aplicacion();
        app.iniciar();
    }

    public void iniciar() {
        System.out.println("Aplicacion iniciada");
    }
}
```

---

## NIVEL SENIOR

### Constantes static final

```java
public class Configuracion {
    public static final String VERSION = "2.0.0";
    public static final int MAX_USUARIOS = 1000;
    public static final double IVA = 0.21;
    public static final List<String> PERMISOS_DEFECTO = List.of("lectura", "escritura");

    // Para valores que necesitan calculo al iniciar
    public static final LocalDate FECHA_LANZAMIENTO = LocalDate.of(2026, 1, 15);
}
```

### Singleton con static

```java
public class GestorBaseDatos {
    private static final GestorBaseDatos INSTANCIA = new GestorBaseDatos();
    private String urlConexion;

    private GestorBaseDatos() {
        this.urlConexion = System.getenv("DB_URL");
    }

    public static GestorBaseDatos obtener() {
        return INSTANCIA;
    }

    public void conectar() {
        System.out.println("Conectando a: " + urlConexion);
    }
}

// Uso:
// GestorBaseDatos.obtener().conectar();
```

### Metodos static utilitarios con Stream

```java
import java.util.List;
import java.util.stream.Stream;

public class Utilidades {
    public static <T> List<T> filtrarNoNulos(List<T> lista) {
        return lista.stream()
            .filter(java.util.Objects::nonNull)
            .toList();
    }

    public static String unirConComa(List<String> elementos) {
        return String.join(", ", elementos);
    }

    public static boolean algunoCumple(List<Integer> numeros, int minimo) {
        return numeros.stream().anyMatch(n -> n >= minimo);
    }
}
```

### Bloque static para inicializacion

```java
import java.io.*;
import java.nio.file.*;
import java.util.*;

public class ConfiguracionArchivo {
    public static final Map<String, String> PROPIEDADES = new HashMap<>();

    static {
        try {
            var lineas = Files.readAllLines(Path.of("config.properties"));
            for (var linea : lineas) {
                if (linea.contains("=")) {
                    var partes = linea.split("=", 2);
                    PROPIEDADES.put(partes[0].trim(), partes[1].trim());
                }
            }
        } catch (IOException e) {
            System.err.println("No se pudo cargar config: " + e.getMessage());
        }
    }
}
```

---

## Errores Comunes

> Llamar a metodos de instancia desde un metodo `static`. Un metodo `static` no puede acceder directamente a miembros de instancia. Necesitas un objeto.

> Usar `this` en contexto `static`. `this` solo existe en metodos de instancia.

> Poner logica de negocio en metodos `static`. Static es para utilidades y constantes, no para logica que deberia estar en objetos.

> Mutar campos `static` desde multiples hilos sin sincronizacion. Puede causar condiciones de carrera.

---

## Buenas Practicas

1. `static final` para constantes. Es la unica forma de constante en Java.
2. Metodos `static` solo para utilidades puras: reciben parametros, devuelven resultado, sin efectos secundarios.
3. No abuses de `static` mutable. El estado global es dificil de testear y mantener.
4. Accede a miembros `static` por el nombre de la clase, no por un objeto: `Clase.metodo()` no `objeto.metodo()`.
5. `main` es `static` por necesidad. Desde alli crea el primer objeto y arranca.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Miembros de clase
- [[02 - Punto de Entrada main]] - main es static
- [[06 - Atributos y Campos]] - Campos static vs de instancia
- [[07 - Constructores y this]] - Bloques static y de instancia
- [[08 - Instanciacion y new]] - Los campos de instancia nacen con new, los static ya existen
- [[10 - Metodos de Instancia]] - Metodos de instancia pueden llamar a static
- [[17 - Flujo de Ejecucion JVM]] - Carga de clase y bloques static
- [[22 - Separacion de Responsabilidades]] - Servicios stateless vs stateful

---

## Tags
`#java #fundamentos #static #instancia #clase #objeto #memoria #metodo-estatico #campo-estatico`
