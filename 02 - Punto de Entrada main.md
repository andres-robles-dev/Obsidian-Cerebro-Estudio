---
tags: [java, fundamentos, main, punto-entrada, jvm]
---

# 02 - Punto de Entrada main

---

## NIVEL JUNIOR

### Que es el main?

El metodo `main` es por donde Java empieza a ejecutar tu programa. Es como la puerta de entrada de una casa: todo empieza ahi.

### Como se escribe

Siempre exactamente igual:

```java
public static void main(String[] args) {

}
```

### Tu primer programa

```java
public class MiPrimerPrograma {
    public static void main(String[] args) {
        System.out.println("Hola mundo desde Java!");
    }
}
```

### Como ejecutarlo

1. Guarda el archivo como `MiPrimerPrograma.java`
2. Abre la terminal
3. Escribe: `javac MiPrimerPrograma.java` (compila)
4. Escribe: `java MiPrimerPrograma` (ejecuta)
5. Veras: `Hola mundo desde Java!`

### Solo esto debes recordar por ahora

- `public` - para que Java pueda acceder desde fuera
- `static` - para que se ejecute sin necesidad de crear un objeto
- `void` - no devuelve nada al terminar
- `main` - el nombre exacto que Java busca
- `String[] args` - espacio para recibir texto al iniciar

---

## NIVEL MID

### Recibir argumentos

Cuando ejecutas tu programa, puedes pasarle datos:

```java
public class SaludoPersonalizado {
    public static void main(String[] args) {
        String nombre = args[0];
        int edad = Integer.parseInt(args[1]);

        System.out.println("Hola " + nombre + ", tienes " + edad + " anios");
    }
}
```

Ejecutar con: `java SaludoPersonalizado Carlos 25`

Resultado: `Hola Carlos, tienes 25 anios`

### Verificar que llegaron argumentos

```java
public class Saludador {
    public static void main(String[] args) {
        if (args.length == 0) {
            System.out.println("Debes escribir tu nombre");
            return;
        }

        System.out.println("Hola " + args[0]);
    }
}
```

### Organizar el main

El `main` no debe tener mucha logica. Solo debe:

1. Crear los objetos principales
2. Llamar al metodo que inicia el programa
3. Opcionalmente capturar errores generales

```java
public class Aplicacion {
    public static void main(String[] args) {
        // 1. Configurar
        Configuracion config = new Configuracion();

        // 2. Crear el programa
        Sistema sistema = new Sistema(config);

        // 3. Iniciar
        sistema.iniciar();
    }
}
```

---

## NIVEL SENIOR

### Java moderno: main simplificado (Java 21+)

Desde Java 21 puedes escribir `main` sin tanto boilerplate:

```java
public class Hola {
    void main() {
        System.out.println("Hola Java moderno!");
    }
}
```

Sin necesidad de `public static void`. Esto es gracias a las *launch protocols* mejorados.

### Args como entrada de configuracion

```java
public class Servidor {
    public static void main(String[] args) {
        // Usando record para configuracion
        var config = procesarArgumentos(args);
        iniciarServidor(config);
    }

    record ConfigServidor(String host, int puerto, boolean modoDebug) {}

    static ConfigServidor procesarArgumentos(String[] args) {
        String host = "localhost";
        int puerto = 8080;
        boolean debug = false;

        for (int i = 0; i < args.length; i++) {
            switch (args[i]) {
                case "--host" -> host = args[++i];
                case "--puerto" -> puerto = Integer.parseInt(args[++i]);
                case "--debug" -> debug = true;
            }
        }

        return new ConfigServidor(host, puerto, debug);
    }

    static void iniciarServidor(ConfigServidor config) {
        System.out.printf("Iniciando servidor en %s:%d (debug: %b)%n",
            config.host(), config.puerto(), config.modoDebug());
    }
}
```

### Codigos de salida

```java
public class Procesador {
    public static void main(String[] args) {
        try {
            procesar(args);
            System.exit(0); // Todo bien
        } catch (Exception e) {
            System.err.println("Error: " + e.getMessage());
            System.exit(1); // Algo fallo
        }
    }
}
```

### System.in para entrada interactiva

```java
import java.util.Scanner;

public class Interactivo {
    public static void main(String[] args) {
        try (var entrada = new Scanner(System.in)) {
            System.out.print("Escribe tu nombre: ");
            String nombre = entrada.nextLine();
            System.out.println("Hola " + nombre);
        }
    }
}
```

---

## Errores Comunes

> Olvidar `static`. Sin `static` Java no puede llamar al main porque no hay objeto todavia.

> Escribir `Main` con mayuscula. Java busca exactamente `main` en minusculas.

> Poner `void` donde no toca. El main lleva `void`, los constructores no llevan `void`.

> Usar `int main` o `String main`. La firma exacta es `public static void main(String[])`.

> Poner el main dentro de otro metodo. El main debe estar al nivel de la clase, no anidado.

> Confundir `args.length` (numero de argumentos) con `args[0]` (primer argumento).

---

## Buenas Practicas

1. Main ligero. Que solo arranque y delegue a otros metodos.
2. Validar `args` al principio si esperas argumentos.
3. Una clase main por aplicacion. No despersiges los puntos de entrada.
4. Usar `System.exit(codigo)` para scripts donde 0 = bien, != 0 = error.
5. En produccion usa logging (SLF4J) no `System.out`.
6. Capturar excepciones en main para dar un mensaje amigable antes de cerrar.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - El main vive dentro de una clase
- [[08 - Instanciacion y new]] - En el main suele ir el primer `new`
- [[13 - Static vs Instancia]] - main es static, no tiene `this`
- [[14 - Arrays Basicos y args]] - `args` es un array de String
- [[17 - Flujo de Ejecucion JVM]] - La JVM busca el main al arrancar
- [[23 - Metodos - Parametros, Retorno y Return]] - main es un metodo

---

## Tags
`#java #fundamentos #main #punto-entrada #jvm #static`
