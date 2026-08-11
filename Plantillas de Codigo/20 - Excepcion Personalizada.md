# 20 - Excepcion Personalizada

```java
// Definicion (una clase por archivo: PedidoInvalidoException.java)
public class PedidoInvalidoException extends RuntimeException {

    public PedidoInvalidoException(String mensaje) {
        super(mensaje);
    }

    public PedidoInvalidoException(String mensaje, Throwable causa) {
        super(mensaje, causa);   // conserva el stack trace original
    }
}

// Uso en el dominio:
public void procesarPedido(Pedido pedido) {
    if (pedido.productos().isEmpty()) {
        throw new PedidoInvalidoException("El pedido no tiene productos");
    }
    if (pedido.total() <= 0) {
        throw new PedidoInvalidoException("El total debe ser positivo");
    }
}

// El llamador la captura de forma especifica:
try {
    procesarPedido(pedido);
} catch (PedidoInvalidoException e) {
    System.out.println("Pedido rechazado: " + e.getMessage());
}
```

**Cuando se usa:** cuando el error pertenece a TU dominio (regla de negocio) y los demas deben capturarlo de forma especifica con contexto.

**Reglas:**
- Nombre termina en `Exception`
- Extiende `RuntimeException` para errores de logica (unchecked, no obliga a capturar)
- Extiende `Exception` si el llamador DEBE manejarla (checked)
- Siempre incluye constructor con mensaje y con causa (`Throwable`)
- No la crees si una excepcion estandar ya describe el error (`IllegalArgumentException`)

**Ver tema:** [[40 - Manejo de Excepciones]]
