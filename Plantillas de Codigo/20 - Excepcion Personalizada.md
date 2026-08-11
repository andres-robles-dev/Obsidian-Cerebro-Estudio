# 20 - Excepcion Personalizada

```java
public class NombreExcepcionException extends RuntimeException {

    public NombreExcepcionException(String mensajeDeError) {
        super(mensajeDeError);
    }

    public NombreExcepcionException(String mensajeDeError, Throwable causa) {
        super(mensajeDeError, causa);
    }
}

// Uso en el dominio:
public TipoDato nombreMetodo(TipoDato nombreParametro) {
    if (condicionInvalida) {
        throw new NombreExcepcionException("mensaje de error");
    }
    return valorResultado;
}
```

**Cuando se usa:** cuando el error pertenece a TU dominio (regla de negocio) y los demas deben capturarlo de forma especifica con contexto.

**Reglas:**
- Nombre termina en Exception
- Extiende RuntimeException para errores de logica (unchecked, no obliga a capturar)
- Extiende Exception si el llamador DEBE manejarla (checked)
- Siempre incluye constructor con mensaje y con causa (Throwable)
- No la crees si una excepcion estandar ya describe el error (IllegalArgumentException)

---

## Ejemplo de uso

```java
public class PedidoInvalidoException extends RuntimeException {

    public PedidoInvalidoException(String mensaje) {
        super(mensaje);
    }

    public PedidoInvalidoException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

public void procesarPedido(Pedido pedido) {
    if (pedido.productos().isEmpty()) {
        throw new PedidoInvalidoException("El pedido no tiene productos");
    }
}

try {
    procesarPedido(pedido);
} catch (PedidoInvalidoException e) {
    System.out.println("Pedido rechazado: " + e.getMessage());
}
```

**Ver tema:** [[40 - Manejo de Excepciones]]