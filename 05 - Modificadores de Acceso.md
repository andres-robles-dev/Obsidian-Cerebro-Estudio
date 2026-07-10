---
tags: [java, fundamentos, modificadores, acceso, encapsulamiento, visibilidad]
---

# 05 - Modificadores de Acceso

## Concepto Central

Los **modificadores de acceso** controlan **qué código puede ver y usar** una clase, campo, método o constructor. Java define cuatro niveles de visibilidad (de más a menos restrictivo): `private`, *default (package-private)*, `protected`, `public`. Son la base del **encapsulamiento**: ocultar detalles internos y exponer solo una interfaz controlada.

## Para Qué Sirve / Cuándo Usarlo

- Proteger invariantes de la clase (ej: `saldo >= 0`)
- Definir API pública clara y estable
- Permitir cambios internos sin romper código cliente
- Controlar herencia (`protected`)
- Organizar módulos por paquetes (`default`)

## Sintaxis General

```java
// Clases (solo public o default)
public class Publica { }           // Visible en todo el universo
class PackagePrivate { }           // Solo mismo paquete

// Miembros (campos, métodos, constructores)
public    // Acceso desde cualquier clase
protected // Mismo paquete + subclases (incluso en otros paquetes)
default   // (sin modificador) Solo mismo paquete
private   // Solo dentro de la MISMA clase
```

### Tabla de Visibilidad

| Modificador | Misma Clase | Mismo Paquete | Subclase (otro paquete) | Cualquier Clase |
|-------------|:-----------:|:-------------:|:-----------------------:|:---------------:|
| `private`   | ✅          | ❌            | ❌                      | ❌              |
| `default`   | ✅          | ✅            | ❌                      | ❌              |
| `protected` | ✅          | ✅            | ✅                      | ❌              |
| `public`    | ✅          | ✅            | ✅                      | ✅              |

> **Regla de oro**: Empieza con `private`. Expande solo si es necesario.

## Ejemplo Propio: CuentaBancaria

```java
package banco;

// Clase pública: API del dominio
public class CuentaBancaria {
    // --- PRIVADO: Estado interno, invariantes protegidos ---
    private String titular;
    private String iban;
    private double saldo;           // Invariante: saldo >= 0
    private int contadorOperaciones; // Detalle implementación

    // --- CONSTRUCTOR PÚBLICO: Punto de creación controlado ---
    public CuentaBancaria(String titular, String iban, double saldoInicial) {
        if (saldoInicial < 0) {
            throw new IllegalArgumentException("Saldo inicial no puede ser negativo");
        }
        this.titular = titular;
        this.iban = iban;
        this.saldo = saldoInicial;
        this.contadorOperaciones = 0;
    }

    // --- PÚBLICO: API oficial (Contrato) ---
    public String getTitular() { return titular; }
    public String getIban() { return iban; }
    public double getSaldo() { return saldo; }

    public void ingresar(double cantidad) {
        validarPositivo(cantidad, "ingreso");
        saldo += cantidad;
        registrarOperacion("INGRESO", cantidad);
    }

    public void retirar(double cantidad) {
        validarPositivo(cantidad, "retiro");
        if (cantidad > saldo) {
            throw new IllegalStateException("Fondos insuficientes: " + saldo);
        }
        saldo -= cantidad;
        registrarOperacion("RETIRO", cantidad);
    }

    public void transferir(CuentaBancaria destino, double cantidad) {
        if (destino == null) throw new IllegalArgumentException("Destino nulo");
        this.retirar(cantidad);        // Valida saldo y registra
        destino.ingresar(cantidad);    // Usa API pública del destino
    }

    // --- PROTECTED: Para subclases (ej: CuentaAhorro, CuentaCorriente) ---
    protected void registrarOperacion(String tipo, double cantidad) {
        contadorOperaciones++;
        // Hook para logging, auditoría en subclases
        System.out.println("[AUDIT] " + tipo + " " + cantidad + " en " + iban);
    }

    // Acceso controlado a contador (subclases pueden leer)
    protected int getContadorOperaciones() {
        return contadorOperaciones;
    }

    // --- PRIVADO: Lógica interna, detalle de implementación ---
    private void validarPositivo(double cantidad, String operacion) {
        if (cantidad <= 0) {
            throw new IllegalArgumentException(operacion + " debe ser > 0");
        }
    }

    // --- DEFAULT (package-private): Utilidades para clases hermanas del paquete ---
    /* package-private */ boolean esCuentaNueva() {
        return contadorOperaciones == 0;
    }
}

// Clase package-private: solo visible dentro de paquete 'banco'
class UtilidadesBanco {
    static void imprimirResumen(CuentaBancaria c) {
        // Accede a default esCuentaNueva()
        System.out.println("Cuenta: " + c.getIban() + " | Nueva: " + c.esCuentaNueva());
    }
}

// Subclase en MISMO paquete
class CuentaAhorro extends CuentaBancaria {
    private double interesAnual;

    public CuentaAhorro(String titular, String iban, double saldoInicial, double interes) {
        super(titular, iban, saldoInicial);
        this.interesAnual = interes;
    }

    public void aplicarInteres() {
        double interes = getSaldo() * interesAnual / 100;
        ingresar(interes); // Usa API pública
        // Accede a protected de padre
        System.out.println("Operaciones totales: " + getContadorOperaciones());
    }
}
```

## Explicación Detallada Línea a Línea

| Línea | Explicación |
|-------|-------------|
| `private String titular;` | Solo `CuentaBancaria` accede. Ni subclases ni otras clases. |
| `public CuentaBancaria(...)` | Constructor público = cualquiera puede crear cuentas. Valida invariante (`saldo >= 0`). |
| `public double getSaldo()` | Getter público: lectura controlada. No setter → saldo solo cambia via `ingresar/retirar`. |
| `public void transferir(CuentaBancaria destino, ...)` | Orquesta operación compleja usando API pública de ambos objetos. |
| `protected void registrarOperacion(...)` | **Hook** para subclases. `CuentaAhorro` puede sobrescribir y añadir lógica. |
| `protected int getContadorOperaciones()` | Subclases leen contador; exterior no. |
| `private void validarPositivo(...)` | Detalle interno. Cambiar firma no rompe API. |
| `/* package-private */ boolean esCuentaNueva()` | Sin modificador = `default`. `UtilidadesBanco` (mismo paquete) accede. |
| `class UtilidadesBanco` | Sin `public` → package-private. Solo clases en `banco` la ven. |
| `class CuentaAhorro extends CuentaBancaria` | Hereda `protected` y `public`. No ve `private` del padre. |

## Errores Comunes

> [!warning] **Error 1: Todo `public` (anemia de encapsulamiento)**
> ```java
> public class Cuenta {
>     public double saldo; // ❌ Cualquiera hace: cuenta.saldo = -1000;
> }
> ```
> ✅ **Correcto**: `private saldo` + `public getSaldo()` + `public ingresar()/retirar()` con validación.

> [!warning] **Error 2: `protected` por defecto**
> ```java
> protected void helperInterno() { } // ❌ Expone a subclases innecesariamente
> ```
> ✅ **Correcto**: `private` salvo que **diseñes** para herencia (Template Method, hooks).

> [!warning] **Error 3: Getter que devuelve referencia mutable interna**
> ```java
> private List<String> movimientos = new ArrayList<>();
> public List<String> getMovimientos() { return movimientos; } // ❌ Fuga de encapsulamiento
> // Cliente: cuenta.getMovimientos().clear(); // Rompe invariantes
> ```
> ✅ **Correcto**: `return Collections.unmodifiableList(movimientos);` o copia defensiva `new ArrayList<>(movimientos)`.

> [!warning] **Error 4: `default` accidental en API pública**
> ```java
> // Archivo: com/app/servicio/Servicio.java
> class Servicio { // ❌ Sin public → package-private
>     public void hacerAlgo() { }
> }
> // Otro paquete: import com.app.servicio.Servicio; // ❌ No visible
> ```
> ✅ **Correcto**: `public class Servicio` si es API del módulo.

> [!warning] **Error 5: Subclase en otro paquete no ve `default`**
> ```java
> // Paquete A
> class Padre { void metodo() {} } // default
> // Paquete B
> class Hijo extends Padre {
>     void test() { metodo(); } // ❌ Error: no visible
> }
> ```
> ✅ **Correcto**: `protected void metodo()` en padre.

## Buenas Prácticas

1. **`private` por defecto** — Campos siempre `private`. Métodos internos `private`.
2. **API mínima pública** — Solo lo que el cliente **necesita**. Menos superficie = menos acoplamiento.
3. **Getters/Setters no automáticos** — Solo expón lo necesario. Setter valida; getter puede devolver copia/inmutable.
4. **`protected` solo para herencia diseñada** — Documenta *por qué* es protected (hook, template method).
5. **`default` para utilidades de paquete** — Clases helper, testing interno, factorías de paquete.
6. **Constructores: visibilidad acorde** — `private` para Singleton/Factory, `protected` para factorías en subclases, `public` normal.
7. **Clases `final` si no diseñas para herencia** — `public final class Cuenta` evita subclasificación accidental.

## Conexión con Otros Temas

- `[[01 - Clases y Estructura Basica]]` — Modificadores en declaración de clase.
- `[[06 - Atributos y Campos]]` — Campos `private` + getters/setters.
- `[[07 - Constructores y this]]` — Visibilidad de constructores.
- `[[10 - Metodos de Instancia]]` — Visibilidad de métodos.
- `[[13 - Static vs Instancia]]` — Miembros `static` también tienen visibilidad.
- `[[17 - Separacion de Responsabilidades]]` — Encapsulamiento = cohesión alta, acoplamiento bajo.

## Resumen en Una Frase

> **Empieza todo `private`; expone `public` solo la API esencial; usa `protected` para hooks de herencia; `default` para colaboradores de paquete.**

---

## Tags
`#java #fundamentos #modificadores #acceso #encapsulamiento #private #protected #public #package-private`