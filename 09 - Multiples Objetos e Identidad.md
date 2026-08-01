---
tags: [java, fundamentos, objetos, identidad, igualdad, equals, hashCode]
---

# 09 - Multiples Objetos e Identidad

---

## NIVEL JUNIOR

### Cada new crea un objeto distinto

Cuando usas `new`, siempre obtienes un objeto nuevo y diferente:

```java
public class Alumno {
    String nombre;
}

public class Principal {
    public static void main(String[] args) {
        Alumno a1 = new Alumno();
        Alumno a2 = new Alumno();

        a1.nombre = "Ana";
        a2.nombre = "Ana";

        System.out.println(a1.nombre);  // "Ana"
        System.out.println(a2.nombre);  // "Ana"
        // Pero a1 y a2 son objetos DIFERENTES
    }
}
```

### Identidad vs Igualdad

- **Identidad**: dos variables apuntan al mismo objeto (`==`)
- **Igualdad**: dos objetos distintos tienen el mismo contenido (`.equals()`)

```java
Alumno a1 = new Alumno();
Alumno a2 = new Alumno();
Alumno a3 = a1;  // a3 apunta al mismo objeto que a1

System.out.println(a1 == a2);   // false (distintos objetos)
System.out.println(a1 == a3);   // true (mismo objeto)
```

---

## NIVEL MID

### El operador == con objetos

`==` compara si dos variables apuntan al MISMO objeto en memoria:

```java
String s1 = new String("Hola");
String s2 = new String("Hola");

System.out.println(s1 == s2);  // false (objetos distintos)
```

### El metodo equals

`.equals()` compara el CONTENIDO de los objetos:

```java
String s1 = new String("Hola");
String s2 = new String("Hola");

System.out.println(s1.equals(s2));  // true (mismo contenido)
```

### Como implementar equals en tus clases

```java
public class Persona {
    String dni;
    String nombre;

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;

        Persona otra = (Persona) obj;
        return dni.equals(otra.dni);  // Dos personas son iguales si tienen el mismo DNI
    }
}
```

### Estado independiente

Cada objeto tiene su propio estado. Modificar uno no afecta al otro:

```java
public class Principal {
    public static void main(String[] args) {
        Persona p1 = new Persona();
        p1.nombre = "Ana";

        Persona p2 = new Persona();
        p2.nombre = "Luis";

        p1.nombre = "Maria";  // Solo cambia p1

        System.out.println(p1.nombre);  // "Maria"
        System.out.println(p2.nombre);  // "Luis" (no cambio)
    }
}
```

---

## NIVEL SENIOR

### Records: equals y hashCode automaticos

```java
public record Persona(String dni, String nombre, int edad) {
    // equals, hashCode, toString vienen ya implementados
    // Dos Persona son iguales si tienen el mismo dni, nombre Y edad
}

public class Demo {
    public static void main(String[] args) {
        var p1 = new Persona("12345678A", "Ana", 30);
        var p2 = new Persona("12345678A", "Ana", 30);
        var p3 = new Persona("87654321B", "Luis", 25);

        System.out.println(p1.equals(p2));  // true
        System.out.println(p1.equals(p3));  // false
        System.out.println(p1 == p2);       // false (objetos distintos, aunque sean iguales)
    }
}
```

### Pattern matching con identidad (Java 16+)

```java
public class DemoPatternMatching {
    public static void main(String[] args) {
        Object obj = new Persona("123A", "Ana", 30);

        if (obj instanceof Persona p) {
            System.out.println("Persona: " + p.nombre());
        }
    }
}
```

### equals con colecciones

Para usar objetos en colecciones como `HashSet` o `HashMap`, debes implementar `equals` Y `hashCode`:

```java
import java.util.Objects;

public class Producto {
    private String codigo;
    private String nombre;

    public Producto(String codigo, String nombre) {
        this.codigo = codigo;
        this.nombre = nombre;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Producto otro = (Producto) obj;
        return Objects.equals(codigo, otro.codigo);
    }

    @Override
    public int hashCode() {
        return Objects.hash(codigo);
    }
}
```

Si no implementas `hashCode`, dos objetos "iguales" podrian aparecer dos veces en un `HashSet`.

### Inmutabilidad y seguridad en identidad

```java
public record Inmutable(String nombre, int edad) {
    // Al ser record, es inmutable.
    // Dos instancias con mismos valores se consideran iguales.
    // Se pueden compartir sin riesgo de que alguien las modifique.
}

public class DemoInmutabilidad {
    public static void main(String[] args) {
        var original = new Inmutable("Ana", 30);
        var copia = new Inmutable("Ana", 30);

        System.out.println(original.equals(copia));  // true
        // Podemos usar original en cualquier lado sin miedo a que lo modifiquen
        procesar(original);
        System.out.println(original.nombre());  // Sigue siendo "Ana"
    }

    static void procesar(Inmutable i) {
        // No se puede modificar i porque es inmutable
        System.out.println("Procesando: " + i);
    }
}
```

---

## Errores Comunes

> Usar `==` para comparar objetos. `==` compara referencias, no contenido. Usa `.equals()`.

> Olvidar implementar `hashCode` cuando implementas `equals`. Las colecciones basadas en hash (HashSet, HashMap) funcionan mal sin el.

> Crear `String` con `new`. `String s = new String("Hola")` crea dos objetos (uno en el pool y otro en heap). Usa `String s = "Hola"`.

> Pensar que asignar copia el objeto. `obj2 = obj1` copia la referencia, no el objeto. Ambos apuntan a lo mismo.

---

## Buenas Practicas

1. Usa `record` para clases de datos: `equals`, `hashCode` y `toString` vienen ya hechos.
2. Usa `.equals()` siempre para comparar objetos, nunca `==`.
3. `==` solo para comparar primitivos (`int`, `boolean`, `double`, etc.).
4. Si implementas `equals`, implementa `hashCode` siempre.
5. Para `String`, usa literales: `"texto"` en vez de `new String("texto")`.
6. Objetos inmutables son mas seguros y predecibles.

---

## Conexiones

- [[03 - Tipos Primitivos y Referencia]] - Primitivos con ==, objetos con .equals
- [[07 - Constructores y this]] - Cada new ejecuta su constructor
- [[08 - Instanciacion y new]] - Cada new crea un objeto en el Heap
- [[10 - Metodos de Instancia]] - Metodos actuan sobre cada objeto
- [[13 - Static vs Instancia]] - Static es compartido, instancia es propio
- [[15 - Listas y ArrayList]] - Colecciones usan equals para contains/indexOf
- [[24 - ArrayList - Metodos Avanzados]] - contains, indexOf con equals

---

## Tags
`#java #fundamentos #objetos #identidad #igualdad #equals #hashCode #inmutabilidad`
