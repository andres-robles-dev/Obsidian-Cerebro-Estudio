# 24 - Singleton

```java
public class ConexionBD {
    // 1. Instancia unica, creada una sola vez
    private static final ConexionBD INSTANCIA = new ConexionBD();

    // 2. Constructor privado: nadie puede hacer new
    private ConexionBD() {
        System.out.println("Conexion creada");
    }

    // 3. Acceso global al unico objeto
    public static ConexionBD getInstancia() {
        return INSTANCIA;
    }

    public void conectar() {
        System.out.println("Conectando...");
    }
}

// Uso:
ConexionBD c1 = ConexionBD.getInstancia();
ConexionBD c2 = ConexionBD.getInstancia();
System.out.println(c1 == c2);   // true: es el MISMO objeto
```

**Cuando se usa:** cuando debe existir UNA sola instancia en toda la aplicacion (conexiones a BD, configuraciones globales, loggers). Patron util y comun en entrevistas.

**Reglas:**
- Constructor `private` (imposible instanciar con `new` desde afuera)
- Instancia `private static final` creada con el campo
- Acceso por metodo estatico `getInstancia()`
- Todo el programa comparte el MISMO objeto (misma referencia)
- Para este caso de uso (sin threads), la version eager-field es la correcta

**Ver tema:** [[27 - Singleton en Profundidad]]
