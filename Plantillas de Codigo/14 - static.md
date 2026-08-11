# 14 - static

```java
public class NombreClase {
    public static final TipoDato NOMBRE_CONSTANTE = valor;

    private static TipoDato variableCompartida;

    public static TipoRetorno nombreMetodo(TipoDato nombreParametro) {
        return valorResultado;
    }

    public static void nombreMetodo() {
        codigoDelMetodo;
    }
}

NombreClase.NOMBRE_CONSTANTE;
NombreClase.nombreMetodo(argumentos);
```

**Cuando se usa:** para datos y comportamientos que pertenecen a la CLASE y no a cada objeto (constantes, contadores, utilidades, singleton).

**Reglas:**
- Se accede con NombreClase.miembro, sin crear objetos
- Un metodo static NO puede tocar this ni campos de instancia (no tiene objeto)
- static final = constante de clase (UPPER_SNAKE)
- Metodos estaticos de utilidad se llaman desde la clase (ej: Integer.parseInt)

---

## Ejemplo de uso

```java
public class Configuracion {
    public static final double IVA = 0.19;
    private static int instanciasCreadas = 0;

    public Configuracion() {
        instanciasCreadas++;
    }

    public static void mostrarInfo() {
        System.out.println("IVA: " + IVA);
    }

    public static int getInstancias() {
        return instanciasCreadas;
    }
}

Configuracion.mostrarInfo();
double iva = Configuracion.IVA;
```

**Ver temas:** [[13 - Static vs Instancia]] - [[28 - Static en Profundidad]]