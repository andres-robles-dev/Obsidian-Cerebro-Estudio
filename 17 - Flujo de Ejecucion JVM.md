---
tags: [java, fundamentos, jvm, memoria, heap, stack, ejecucion]
---

# 17 - Flujo de Ejecucion JVM

---

## NIVEL JUNIOR

### Que es la JVM?

La JVM (Java Virtual Machine) es la maquina virtual que ejecuta tu codigo Java. Cuando compilas (`javac`), tu codigo se convierte en bytecode (archivo `.class`). La JVM lee ese bytecode y lo ejecuta.

### Como arranca un programa Java

1. Compilas: `javac MiPrograma.java` -> genera `MiPrograma.class`
2. Ejecutas: `java MiPrograma` -> la JVM se inicia
3. La JVM busca el metodo `main` en `MiPrograma`
4. Empieza a ejecutar las instrucciones del `main`

### Memoria: Stack y Heap

| Donde | Que se guarda |
|-------|--------------|
| **Stack** (pila) | Variables locales, llamadas a metodos |
| **Heap** (monton) | Objetos creados con `new` |

Piensa en el Stack como una pila de platos: cada metodo que se llama apila un plato nuevo. Cuando el metodo termina, se quita el plato.

El Heap es como un almacen donde viven los objetos hasta que ya no se usan.

---

## NIVEL MID

### Flujo detallado de ejecucion

```java
public class Ejemplo {
    public static void main(String[] args) {
        int x = 5;
        Saludar s = new Saludar();
        s.decir("Hola");
    }
}

class Saludar {
    String mensaje;

    public void decir(String texto) {
        mensaje = texto;
        System.out.println(mensaje);
    }
}
```

Paso a paso:

1. JVM carga `Ejemplo.class`
2. Busca `main` y lo ejecuta
3. En el Stack: se crea un marco (frame) para `main`
4. Dentro de `main`: se guarda `x = 5` en el Stack
5. `new Saludar()` crea un objeto `Saludar` en el Heap
6. La variable `s` (en Stack) guarda la direccion del objeto en Heap
7. Se llama a `decir("Hola")` -> nuevo frame en el Stack
8. Dentro de `decir`: `texto = "Hola"` esta en el Stack
9. `mensaje = texto` -> el objeto en Heap ahora tiene "Hola"
10. `System.out.println` -> otro frame en el Stack
11. Al terminar `decir`, su frame se elimina del Stack
12. Al terminar `main`, su frame se elimina
13. El objeto `Saludar` queda en Heap sin referencias

### Garbage Collector

Cuando un objeto en el Heap ya no tiene referencias (nadie apunta a el), el Garbage Collector lo elimina automaticamente:

```java
public class DemoGC {
    public static void main(String[] args) {
        crearObjeto();
        // Aqui el objeto ya no es accesible
        // El GC lo eliminara cuando toque
    }

    static void crearObjeto() {
        String temp = new String("Temporal");
        System.out.println(temp);
        // Al salir del metodo, temp desaparece del Stack
        // El String "Temporal" queda en Heap sin referencias
    }
}
```

---

## NIVEL SENIOR

### Text blocks y memoria

```java
public class DemoMemoria {
    // String Pool: los literales se reutilizan
    public static void main(String[] args) {
        String a = "Hola";
        String b = "Hola";
        System.out.println(a == b);  // true (misma referencia en String Pool)

        String c = new String("Hola");
        System.out.println(a == c);  // false (objeto distinto en Heap)
    }
}
```

### Stack y recursion

```java
public class DemoRecursion {
    public static void main(String[] args) {
        try {
            factorial(100000);  // StackOverflowError
        } catch (StackOverflowError e) {
            System.out.println("El Stack se desbordo");
        }
    }

    static int factorial(int n) {
        if (n <= 1) return 1;
        return n * factorial(n - 1);
        // Cada llamada apila un frame en el Stack
        // Si son demasiadas, el Stack se desborda
    }
}
```

### Clases que no se descargan

```java
public class DemoClassLoader {
    public static void main(String[] args) {
        // Las clases cargadas por el ClassLoader estandar
        // NO se descargan aunque ya no se usen
        // Solo el ClassLoader que las cargo puede descargarlas

        System.out.println("Clase cargada: " + DemoClassLoader.class.getName());
        // La clase sigue en Metaspace aunque solo se use una vez
    }
}
```

### JVM moderna y ZGC

```java
public class DemoModerna {
    public static void main(String[] args) {
        // En Java 2026, la JVM moderna usa:
        // - ZGC o Shenandoah para pausas de ms (no minutos)
        // - Metaspace en vez de PermGen
        // - Compact Strings (String con byte[] en vez de char[])
        // - AppCDS para arranque mas rapido

        var inicio = System.nanoTime();
        var lista = new int[10_000_000];
        System.out.println("Array creado");
        // ZGC recolectara sin pausas notables
    }
}
```

---

## Errores Comunes

> Crear demasiados objetos sin necesidad. Si creas objetos en un bucle sin fin, eventualmente el Heap se llena y obtienes `OutOfMemoryError`.

> StackOverflowError por recursion infinita. Si un metodo se llama a si mismo sin condicion de salida, el Stack se desborda.

> Mantener referencias a objetos que ya no necesitas. El GC no puede eliminar objetos que aun tienen referencias, aunque no los uses.

> Pensar que `System.gc()` fuerza la recoleccion. `System.gc()` SOLO SUGIERE al GC que recolecte. No garantiza que lo haga.

> Usar `finalize()` para limpiar recursos. `finalize()` esta deprecado y no se sabe cuando se ejecuta. Usa `try-with-resources` o `AutoCloseable`.

---

## Buenas Practicas

1. No confies en `finalize()`. Usa `try-with-resources` para recursos (archivos, conexiones).
2. Libera referencias cuando ya no necesites un objeto (asigna `null` si es necesario).
3. Para objetos grandes que se usan una vez, considera `WeakReference`.
4. Usa `String` literales en vez de `new String()` para aprovechar el String Pool.
5. Para aplicaciones que necesitan baja latencia, considera ZGC como recolector.

---

## Conexiones

- [[02 - Punto de Entrada main]] - La JVM busca el main
- [[08 - Instanciacion y new]] - Los objetos se crean en Heap
- [[09 - Multiples Objetos e Identidad]] - Referencias en Stack, objetos en Heap
- [[13 - Static vs Instancia]] - Static en Metaspace, instancia en Heap

---

## Tags
`#java #fundamentos #jvm #memoria #heap #stack #ejecucion #garbage-collector`
