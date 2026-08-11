# 09 - Metodos de Instancia

```java
public class NombreClase {

    public TipoRetorno nombreMetodo(TipoDato nombreParametro) {
        return valorResultado;
    }

    public void nombreMetodo() {
        codigoDelMetodo;
    }

    public boolean nombreMetodo(TipoDato nombreParametro) {
        return condicion;
    }

    public TipoRetorno nombreMetodo(TipoDato nombreParametro) {
        if (condicionInvalida) {
            throw new IllegalArgumentException("mensaje de error");
        }
        return valorResultado;
    }
}
```

**Cuando se usa:** para definir el comportamiento de los objetos (acciones, calculos, validaciones).

**Reglas:**
- Con return: el tipo del retorno debe coincidir con el declarado
- void: no devuelve nada (puede usar return; para cortar)
- Los parametros se pasan por valor (copias)
- Dentro tienes acceso a los campos via this
- return corta la ejecucion del metodo al instante
- Guard clause: valida la entrada al inicio y lanza o devuelve temprano

---

## Ejemplo de uso

```java
public class Calculadora {
    public int sumar(int a, int b) {
        return a + b;
    }

    public void imprimirResultado(int valor) {
        System.out.println("Resultado: " + valor);
    }

    public boolean esMayor(int a, int b) {
        return a > b;
    }

    public double dividir(double a, double b) {
        if (b == 0) {
            throw new IllegalArgumentException("No se puede dividir por cero");
        }
        return a / b;
    }
}
```

**Ver temas:** [[10 - Metodos de Instancia]] - [[23 - Metodos - Parametros, Retorno y Return]]