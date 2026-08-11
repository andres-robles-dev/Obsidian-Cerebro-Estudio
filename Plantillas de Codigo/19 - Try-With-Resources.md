# 19 - Try-With-Resources

```java
try (TipoRecurso nombreRecurso = new TipoRecurso(argumentos)) {
    codigoQueUsaElRecurso;
} catch (NombreExcepcion e) {
    codigoDeManejoDelError;
}

try (TipoRecurso primerRecurso = new TipoRecurso(argumentos);
     TipoRecurso segundoRecurso = new TipoRecurso(argumentos)) {
    codigoQueUsaLosRecursos;
}
```

**Cuando se usa:** SIEMPRE que abras un recurso que debe cerrarse: archivos, Scanner, conexiones, streams.

**Reglas:**
- El recurso debe implementar AutoCloseable (archivos y Scanner lo implementan)
- close() se llama automaticamente al salir del bloque, incluso con excepciones
- No necesitas finally para cerrar
- Varios recursos van separados con ; y se cierran en orden inverso
- Sin esto, un recurso olvidado se queda abierto (fuga de recursos)

---

## Ejemplo de uso

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class LeerArchivo {
    public static void main(String[] args) {
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
```

**Ver tema:** [[40 - Manejo de Excepciones]]