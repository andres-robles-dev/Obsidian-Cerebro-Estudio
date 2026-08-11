# 15 - enum Simple

```java
public enum Dia {
    LUNES, MARTES, MIERCOLES, JUEVES, VIERNES, SABADO, DOMINGO
}

// Uso:
public void agendar(Dia dia) {
    if (dia == Dia.SABADO || dia == Dia.DOMINGO) {
        System.out.println("Es fin de semana");
    }
}

Dia hoy = Dia.MARTES;
System.out.println(hoy);               // MARTES
```

**Cuando se usa:** para un conjunto FIJO de constantes relacionadas (dias, estados, niveles, colores, tipos de pago). Mejor que String o int sueltos.

**Reglas:**
- Constantes en MAYUSCULAS separadas por coma
- No lleva `;` al final si no tiene mas miembros
- Tipo seguro: el compilador solo acepta los valores definidos
- Se comparan con `==` (a diferencia de String)
- Todo enum trae: `values()`, `valueOf("LUNES")`, `ordinal()`, `name()`

**Ver tema:** [[33 - enum en Profundidad]]
