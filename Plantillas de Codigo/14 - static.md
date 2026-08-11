# 14 - static

```java
public class Configuracion {
    // Constante de clase: compartida por todos los objetos
    public static final double IVA = 0.19;

    // Contador compartido: NO pertenece a ningun objeto
    private static int instanciasCreadas = 0;

    public Configuracion() {
        instanciasCreadas++;
    }

    // Metodo de clase: se llama sin instanciar
    public static void mostrarInfo() {
        System.out.println("IVA: " + IVA);
    }

    public static int getInstancias() {
        return instanciasCreadas;
    }
}

// Uso: se accede por el NOMBRE DE LA CLASE
Configuracion.mostrarInfo();
double iva = Configuracion.IVA;
```

**Cuando se usa:** para datos y comportamientos que pertenecen a la CLASE y no a cada objeto (constantes, contadores, utilidades, singleton).

**Reglas:**
- Se accede con `Clase.miembro`, sin crear objetos
- Un metodo `static` NO puede tocar `this` ni campos de instancia (no tiene objeto)
- `static final` = constante de clase (UPPER_SNAKE)
- Los metodos estaticos de utilidad se llaman desde la clase (ej: `Integer.parseInt`)

**Ver temas:** [[13 - Static vs Instancia]] - [[28 - Static en Profundidad]]
