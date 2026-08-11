# 02 - Punto de Entrada main

```java
public class NombreClase {
    public static void main(String[] args) {
        System.out.println(valor);
    }
}
```

**Cuando se usa:** en todo programa ejecutable. La JVM arranca siempre por el metodo main de la clase que le indicas.

**Reglas:**
- Firma exacta: public static void main(String[] args)
- static: se llama sin crear un objeto de la clase
- args: array que recibe los argumentos al ejecutar (java NombreClase arg1 arg2)
- Sin main no hay punto de entrada

---

## Ejemplo de uso

```java
public class Principal {
    public static void main(String[] args) {
        System.out.println("Hola mundo");

        if (args.length > 0) {
            System.out.println("Bienvenido " + args[0]);
        }
    }
}
```

**Ver tema:** [[02 - Punto de Entrada main]]