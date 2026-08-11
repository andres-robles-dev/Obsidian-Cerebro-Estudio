# 02 - Punto de Entrada main

```java
public class Principal {
    public static void main(String[] args) {
        // Codigo principal del programa
        System.out.println("Hola mundo");

        // args recibe los argumentos al ejecutar:
        // java Principal Ana 30
        if (args.length > 0) {
            System.out.println("Bienvenido " + args[0]);
        }
    }
}
```

**Cuando se usa:** en todo programa ejecutable. La JVM arranca siempre por el metodo `main` de la clase que le indicas.

**Reglas:**
- Firma exacta: `public static void main(String[] args)`
- `static`: se llama sin crear un objeto de la clase
- `args` contiene los argumentos pasados al ejecutar (`java Clase arg1 arg2`)
- Sin `main` no hay punto de entrada

**Ver tema:** [[02 - Punto de Entrada main]]
