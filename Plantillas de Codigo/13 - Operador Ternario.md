# 13 - Operador Ternario

```java
TipoDato nombreVariable = condicion ? valorSiVerdadero : valorSiFalso;
```

**Cuando se usa:** para asignar un valor entre dos opciones segun una condicion, en una sola linea (reemplaza al if/else simple).

**Reglas:**
- Es una EXPRESION: siempre devuelve un valor (se asigna, se imprime, se pasa como argumento)
- Ambos resultados deben ser del mismo tipo
- NUNCA anides ternarios: condicion ? a : condicion2 ? b : c es ilegible, usa if/else
- Si hay mas de dos opciones o logica extra, usa if/else normal

---

## Ejemplo de uso

```java
int edad = 20;
String categoria = edad >= 18 ? "Adulto" : "Menor";

int max = a > b ? a : b;

String mensaje = activo ? "Bienvenido" : "Cuenta suspendida";
```

**Ver tema:** [[12 - Operador Ternario]]