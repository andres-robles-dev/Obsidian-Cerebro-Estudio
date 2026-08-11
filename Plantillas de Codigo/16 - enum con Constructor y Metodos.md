# 16 - enum con Constructor y Metodos

```java
public enum NombreEnum {
    CONSTANTE1(TipoDato dato1, TipoDato dato2),
    CONSTANTE2(TipoDato dato1, TipoDato dato2),
    CONSTANTE3(TipoDato dato1, TipoDato dato2);

    private final TipoDato dato1;
    private final TipoDato dato2;

    NombreEnum(TipoDato dato1, TipoDato dato2) {
        this.dato1 = dato1;
        this.dato2 = dato2;
    }

    public TipoDato getDato1() {
        return dato1;
    }

    public TipoDato getDato2() {
        return dato2;
    }
}
```

**Cuando se usa:** cuando cada constante necesita datos asociados (nombre mostrable, precio, codigo) o comportamiento propio.

**Reglas:**
- El constructor es siempre private (Java lo asume): NO se usa new
- Las constantes van PRIMERO, luego ;, despues campos, constructor y metodos
- Cada constante llama al constructor con sus argumentos: CONSTANTE(valor1, valor2)
- Los campos suelen ser final (el enum es inmutable)

---

## Ejemplo de uso

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

Nivel nivel = Nivel.INTERMEDIO;
System.out.println(nivel.getNombre());   // Intermedio
System.out.println(nivel.getOrden());    // 2
```

**Ver tema:** [[33 - enum en Profundidad]]