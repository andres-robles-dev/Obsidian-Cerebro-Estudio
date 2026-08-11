# 26 - Metodos Avanzados (varargs y guard clauses)

```java
public class Util {

    // VARARGS: cantidad variable de argumentos (se usa como array)
    public static int sumar(int... numeros) {
        int total = 0;
        for (int n : numeros) {
            total += n;
        }
        return total;
    }

    // Guard clause: valida la entrada ANTES de la logica
    public static void procesar(String nombre, int... valores) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre es obligatorio");
        }
        if (valores == null) {
            throw new IllegalArgumentException("valores no puede ser null");
        }
        // ... logica del metodo a partir de aqui
    }
}

// Uso:
int total = Util.sumar(1, 2, 3, 4);   // 10
int vacio = Util.sumar();             // 0 (varargs vacio es valido)
Util.procesar("Ana", 5, 6);
```

**Cuando se usa:** cuando un metodo debe aceptar una cantidad variable de valores (sumas, promedios, formateo) y para construir metodos defensivos con validacion al inicio.

**Reglas:**
- Varargs: `tipo... nombre`, SIEMPRE el ultimo parametro (solo puede haber uno)
- Dentro del metodo el varargs se trata como array (tiene `.length`, se recorre igual)
- Puedes llamarlo sin argumentos: `sumar()` -> array vacio
- Guard clauses: si la entrada es invalida, lanza excepcion o devuelve temprano ANTES de ejecutar la logica
- Con guard clauses el metodo queda plano: validaciones arriba, logica debajo

**Ver tema:** [[23 - Metodos - Parametros, Retorno y Return]]
