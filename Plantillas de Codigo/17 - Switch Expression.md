# 17 - Switch Expression

```java
// Con flecha: cada caso devuelve el valor (Java 14+)
int dia = 3;
String tipo = switch (dia) {
    case 1, 2, 3, 4, 5 -> "Laboral";
    case 6, 7 -> "Fin de semana";
    default -> "Dia invalido";
};

// Con bloques y yield para logica extra
String mensaje = switch (estado) {
    case ACTIVO -> "Cuenta activa";
    case BLOQUEADO -> {
        int dias = calcularDias();
        yield "Bloqueada por " + dias + " dias";
    }
    default -> "Estado desconocido";
};

// Con enum es EXHAUSTIVO: si falta un caso, no compila
switch (dia) {
    case LUNES, MARTES, MIERCOLES, JUEVES, VIERNES -> System.out.println("Curso");
    case SABADO, DOMINGO -> System.out.println("Descanso");
}
```

**Cuando se usa:** para elegir entre multiples opciones. Reemplaza al switch clasico y a cadenas de if-else cuando hay 3+ ramas.

**Reglas:**
- La flecha `->` no necesita `break` (a diferencia del switch clasico)
- Como es expresion, se asigna: `String x = switch (...) {...}`
- `yield` devuelve un valor desde un bloque con `{ }`
- `default` es obligatorio cuando la entrada no es un enum completo (int, String)
- Varios casos juntos: `case 1, 2, 3 ->`

**Ver tema:** [[36 - Switch expression]]
