# 17 - Switch Expression

```java
TipoDato nombreVariable = switch (expresion) {
    case valor1, valor2 -> valorResultado;
    case valor3 -> valorResultado;
    default -> valorPorDefecto;
};

TipoDato nombreVariable = switch (expresion) {
    case valor1 -> valorResultado;
    case valor2 -> {
        codigoExtra;
        yield valorResultado;
    }
    default -> valorPorDefecto;
};

switch (expresion) {
    case valor1 -> codigoSinRetorno;
    case valor2 -> codigoSinRetorno;
    default -> codigoSinRetorno;
}
```

**Cuando se usa:** para elegir entre multiples opciones. Reemplaza al switch clasico y a las cadenas de if-else con 3+ ramas.

**Reglas:**
- La flecha -> no necesita break (a diferencia del switch clasico)
- Es una expresion: se asigna con TipoDato variable = switch (...)
- yield devuelve un valor desde un bloque con llaves
- default es obligatorio cuando la entrada no cubre todos los casos (int, String)
- Varios casos juntos: case valor1, valor2 ->

---

## Ejemplo de uso

```java
int dia = 3;
String tipo = switch (dia) {
    case 1, 2, 3, 4, 5 -> "Laboral";
    case 6, 7 -> "Fin de semana";
    default -> "Dia invalido";
};

String mensaje = switch (estado) {
    case ACTIVO -> "Cuenta activa";
    case BLOQUEADO -> {
        int dias = 5;
        yield "Bloqueada " + dias + " dias";
    }
    default -> "Estado desconocido";
};
```

**Ver tema:** [[36 - Switch expression]]