# 24 - Singleton

```java
public class NombreClase {
    private static final NombreClase INSTANCIA = new NombreClase();

    private NombreClase() {
    }

    public static NombreClase getInstancia() {
        return INSTANCIA;
    }

    public TipoDato nombreMetodo() {
        return valorResultado;
    }
}

NombreClase objeto = NombreClase.getInstancia();
```

**Cuando se usa:** cuando debe existir UNA sola instancia en toda la aplicacion (conexiones a BD, configuraciones globales, loggers). Patron clasico y comun en entrevistas.

**Reglas:**
- Constructor private: imposible instanciar con new desde afuera
- Instancia private static final creada con el campo
- Acceso por metodo estatico getInstancia()
- Todo el programa comparte el MISMO objeto (misma referencia)
- Para este caso de uso (sin threads) la version eager-field es la correcta

---

## Ejemplo de uso

```java
public class ConexionBD {
    private static final ConexionBD INSTANCIA = new ConexionBD();

    private ConexionBD() {
        System.out.println("Conexion creada");
    }

    public static ConexionBD getInstancia() {
        return INSTANCIA;
    }

    public void conectar() {
        System.out.println("Conectando...");
    }
}

ConexionBD c1 = ConexionBD.getInstancia();
ConexionBD c2 = ConexionBD.getInstancia();
System.out.println(c1 == c2);   // true: es el MISMO objeto
```

**Ver tema:** [[27 - Singleton en Profundidad]]