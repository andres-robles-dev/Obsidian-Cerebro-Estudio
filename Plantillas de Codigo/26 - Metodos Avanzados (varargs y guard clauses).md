# 26 - Metodos Avanzados (varargs y guard clauses)

```java
public static TipoRetorno nombreMetodo(TipoDato... nombreParametro) {
    TipoDato resultado = valorInicial;
    for (TipoDato elemento : nombreParametro) {
        codigoConCadaElemento;
    }
    return resultado;
}

public static void nombreMetodo(TipoDato nombreParametro, TipoDato... nombreParametroVarargs) {
    if (condicionInvalida) {
        throw new IllegalArgumentException("mensaje de error");
    }
    codigoDelMetodo;
}
```

**Cuando se usa:** cuando un metodo debe aceptar una cantidad variable de valores (sumas, promedios, formateo) y para construir metodos defensivos con validacion al inicio.

**Reglas:**
- Varargs: TipoDato... nombre, SIEMPRE el ultimo parametro (solo puede haber uno)
- Dentro del metodo el varargs se usa como array (tiene .length, se recorre igual)
- Se puede llamar sin argumentos: metodo() -> array vacio
- Guard clause: si la entrada es invalida, lanza excepcion o devuelve temprano ANTES de la logica
- Con guard clauses el metodo queda: validaciones arriba, logica debajo

---

## Ejemplo de uso

```java
public class Util {

    public static int sumar(int... numeros) {
        int total = 0;
        for (int n : numeros) {
            total += n;
        }
        return total;
    }

    public static void procesar(String nombre, int... valores) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre es obligatorio");
        }
        if (valores == null) {
            throw new IllegalArgumentException("valores no puede ser null");
        }
    }
}

int total = Util.sumar(1, 2, 3, 4);   // 10
int vacio = Util.sumar();             // 0
Util.procesar("Ana", 5, 6);
```

**Ver tema:** [[23 - Metodos - Parametros, Retorno y Return]]