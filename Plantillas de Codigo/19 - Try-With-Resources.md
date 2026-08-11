# 19 - Try-With-Resources

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class LeerArchivo {
    public static void main(String[] args) {
        // El recurso se cierra SOLO al salir del bloque
        try (var lector = new BufferedReader(new FileReader("datos.txt"))) {
            String linea;
            while ((linea = lector.readLine()) != null) {
                System.out.println(linea);
            }
        } catch (IOException e) {
            System.out.println("Error al leer el archivo: " + e.getMessage());
        }
    }
}

// Varios recursos, separados por ;
try (var entrada = new FileReader("in.txt");
     var salida = new FileWriter("out.txt")) {
    // ...
}
```

**Cuando se usa:** SIEMPRE que abras un recurso que debe cerrarse: archivos, Scanner, conexiones, streams.

**Reglas:**
- El recurso debe implementar `AutoCloseable` (los archivos y Scanner lo hacen)
- `close()` se llama automaticamente al salir del bloque, incluso con excepciones
- No necesitas `finally` para cerrar (se cierra solo)
- Varios recursos van separados con `;` y se cierran en orden inverso
- Sin try-with-resources, un recurso olvidado se queda abierto (fuga de recursos)

**Ver tema:** [[40 - Manejo de Excepciones]]
