# 16 - enum con Constructor y Metodos

```java
public enum Nivel {
    BASICO("Basico", 1),
    INTERMEDIO("Intermedio", 2),
    AVANZADO("Avanzado", 3);

    private final String nombre;
    private final int orden;

    Nivel(String nombre, int orden) {
        this.nombre = nombre;
        this.orden = orden;
    }

    public String getNombre() {
        return nombre;
    }

    public int getOrden() {
        return orden;
    }
}

// Uso:
Nivel nivel = Nivel.INTERMEDIO;
System.out.println(nivel.getNombre());   // Intermedio
System.out.println(nivel.getOrden());    // 2
```

**Cuando se usa:** cuando cada constante necesita datos asociados (nombre mostrable, precio, codigo, icono) o comportamiento propio.

**Reglas:**
- El constructor es siempre `private` (Java lo asume; NO se puede usar `new`)
- Las constantes van PRIMERO, luego `;`, despues campos, constructor y metodos
- Cada constante llama al constructor con sus argumentos: `BASICO("Basico", 1)`
- Los campos suelen ser `final` (el enum es inmutable)
- `equals` y `==` funcionan igual (una sola instancia por constante)

**Ver tema:** [[33 - enum en Profundidad]]
