# 09 - Metodos de Instancia

```java
public class Calculadora {
    // Con retorno
    public int sumar(int a, int b) {
        return a + b;
    }

    // Sin retorno (void)
    public void imprimirResultado(int valor) {
        System.out.println("Resultado: " + valor);
    }

    // Con condicion
    public boolean esMayor(int a, int b) {
        return a > b;
    }

    // Guard clause: validar entrada al inicio
    public double dividir(double a, double b) {
        if (b == 0) {
            throw new IllegalArgumentException("No se puede dividir por cero");
        }
        return a / b;
    }
}

// Uso:
Calculadora calc = new Calculadora();
int total = calc.sumar(2, 3);
calc.imprimirResultado(total);
```

**Cuando se usa:** para definir el comportamiento de los objetos (acciones, calculos, validaciones).

**Reglas:**
- Con `return valor`: el tipo del retorno debe coincidir con el declarado
- `void`: no devuelve nada (puede tener `return;` para cortar)
- Los parametros se pasan por valor (copias)
- Dentro del metodo tienes acceso a los campos via `this`
- `return` corta la ejecucion del metodo al instante

**Ver temas:** [[10 - Metodos de Instancia]] - [[23 - Metodos - Parametros, Retorno y Return]]
