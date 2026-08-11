# 15 - enum Simple

```java
public enum NombreEnum {
    CONSTANTE_NOMBRE1, CONSTANTE_NOMBRE2, CONSTANTE_NOMBRE3
}

NombreEnum nombreVariable = NombreEnum.CONSTANTE_NOMBRE1;

if (nombreVariable == NombreEnum.CONSTANTE_NOMBRE1) {
    codigoSiCoincide;
}
```

**Cuando se usa:** para un conjunto FIJO de constantes relacionadas (dias, estados, niveles, tipos de pago). Mejor que String o int sueltos.

**Reglas:**
- Constantes en MAYUSCULAS separadas por coma
- Se comparan con == (a diferencia de String)
- Tipo seguro: el compilador solo acepta los valores definidos
- Todo enum trae: values(), valueOf("NOMBRE"), name(), ordinal()

---

## Ejemplo de uso

```java
public enum Dia {
    LUNES, MARTES, MIERCOLES, JUEVES, VIERNES, SABADO, DOMINGO
}

public void agendar(Dia dia) {
    if (dia == Dia.SABADO || dia == Dia.DOMINGO) {
        System.out.println("Es fin de semana");
    }
}

Dia hoy = Dia.MARTES;
System.out.println(hoy);   // MARTES
```

**Ver tema:** [[33 - enum en Profundidad]]