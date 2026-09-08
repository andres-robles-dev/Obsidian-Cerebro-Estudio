---
tags: [java, testing, junit, pruebas, aserciones, calidad]
---

# 48 - JUnit y pruebas de unidad

Una prueba de unidad es un pequeño programa que comprueba automáticamente que tu código se comporta como esperas. JUnit es la librería estándar para escribir esas pruebas en Java: escribes métodos anotados con `@Test`, y JUnit los ejecuta y te dice cuáles pasan y cuáles fallan. Es la primera herramienta que necesitas para escribir código confiable.

---

# METODO EXPLICATIVO

## 1. Que es una prueba de unidad

Imagina que tienes un método `sumar(int a, int b)`. ¿Cómo compruebas que funciona? Sin pruebas, lo harías a mano: ejecutar el programa, poner unos números, mirar la salida. Eso es lento, tedioso y no se puede repetir fácilmente cuando cambias el código.

Una prueba de unidad automatiza ese proceso: es código que **llama a tu código** y **comprueba el resultado**. Si mañana modificas `sumar()` y rompes algo, la prueba falla inmediatamente y te avisa. Esa es la magia: las pruebas son una **red de seguridad** que te deja tocar el código sin miedo.

Las tres propiedades de una buena prueba de unidad son:

- **Rápida**: se ejecuta en milisegundos. No habla con bases de datos ni red.
- **Automática**: no requiere intervención humana. Se decide sola si pasa o falla.
- **Aislada**: no depende de otras pruebas. Puedes ejecutarla sola o junto a las demás.

La palabra "unidad" significa que prueba **una unidad de comportamiento**: normalmente un método de una clase. No prueba el sistema entero (eso son pruebas de integración, que verás con Spring Boot).

## 2. La anatomia de una clase de prueba

En Java las pruebas viven en un paquete espejo: si tu clase está en `src/main/java/com/miapp/Calculadora.java`, su prueba va en `src/test/java/com/miapp/CalculadoraTest.java`. El nombre `XxxTest` es la convención universal.

```java
// src/test/java/com/miapp/CalculadoraTest.java
class CalculadoraTest {

    @Test
    void sumaDosNumeros() {
        Calculadora calc = new Calculadora();
        int resultado = calc.sumar(2, 3);
        assertEquals(5, resultado);
    }

    @Test
    void sumaNegativos() {
        Calculadora calc = new Calculadora();
        int resultado = calc.sumar(-2, -3);
        assertEquals(-5, resultado);
    }
}
```

Es normal que te parezca "demasiado código para comprobar una suma". Pero esa redundancia es el punto: cada `@Test` es un **escenario independiente**. Si `sumaNegativos` falla, sabes exactamente qué caso se rompió sin leer más nada.

## 3. Las anotaciones que controlan el ciclo de vida

JUnit es una librería "dirigida por anotaciones": tú no llamas a JUnit, JUnit te llama a ti. Estas son las anotaciones esenciales:

```java
class BancotecaTest {

    @BeforeAll
    static void antesDeTodo() {
        // Una vez antes de TODOS los tests de esta clase
        // Debe ser static porque se ejecuta antes de crear instancias
    }

    @BeforeEach
    void antesDeCadaTest() {
        // Se ejecuta ANTES de cada @Test: aqui creas objetos frescos
    }

    @Test
    void unEscenario() { /* ... */ }

    @Test
    void otroEscenario() { /* ... */ }

    @AfterEach
    void despuesDeCadaTest() {
        // Limpieza despues de cada test
    }

    @AfterAll
    static void despuesDeTodo() {
        // Una vez al final de todos los tests de la clase
    }
}
```

El orden de ejecución es: `@BeforeAll` -> (`@BeforeEach` -> `@Test` -> `@AfterEach`) por cada test -> `@AfterAll`. El que usarás el 90% del tiempo es `@BeforeEach`: ahí preparas el estado limpio que cada prueba necesita, de modo que ninguna prueba contamine a la siguiente.

Hay una razón de fondo para este diseño: las pruebas deben ser **repetibles**. Si la prueba de ayer deja estado sucio que rompe la prueba de hoy, ya no es una prueba, es una lotería. El ciclo `@BeforeEach` + objetos frescos garantiza que cada escenario arranca desde cero.

## 4. Assertions: la forma de comprobar

Una aserción (assertion) es una comprobación que **falla la prueba si no se cumple**. `Assertions` es la clase estática que JUnit te da para esto:

```java
import static org.junit.jupiter.api.Assertions.*;

@Test
void comprobacionesBasicas() {
    // Igualdad
    assertEquals(5, calc.sumar(2, 3));

    // Valores esperados vs obtenidos (ojo al orden: esperado primero)
    String nombre = "Ana";
    assertEquals("Ana", nombre);

    // Booleanos
    assertTrue(nombre.startsWith("A"));
    assertFalse(nombre.isEmpty());

    // Null
    assertNull(null);
    assertNotNull(nombre);

    // Comparacion de objetos (usa equals internamente)
    List<String> esperado = List.of("a", "b");
    List<String> obtenido = List.of("a", "b");
    assertIterableEquals(esperado, obtenido);
}
```

El orden de argumentos importa y es fuente de bugs en las propias pruebas: en `assertEquals(esperado, obtenido)` el **valor esperado va primero**. Si lo inviertes, la prueba funciona pero los mensajes de fallo te dirán lo contrario de lo que piensas, y perderás tiempo confundido.

## 5. Probar que algo falla: assertThrows

Probar el camino feliz es fácil, pero un buen código se define por cómo se comporta cuando las cosas van mal. `assertThrows` comprueba que se lanza la excepción correcta:

```java
@Test
void rechazaDivisionPorCero() {
    Calculadora calc = new Calculadora();

    ArithmeticException ex = assertThrows(
        ArithmeticException.class,
        () -> calc.dividir(10, 0)
    );

    assertEquals("/ by zero", ex.getMessage());
}
```

Fíjate en la lambda: `() -> calc.dividir(10, 0)` es el código que **debería** lanzar la excepción. `assertThrows` lo ejecuta, captura la excepción y comprueba su tipo. Si no se lanza nada, la prueba falla. Si se lanza otra excepción, también falla. Este patrón conecta directamente con el manejo de excepciones: cada `throw` que escribes en producción debería tener al menos un `assertThrows` en las pruebas.

## 6. La estructura AAA: Arrange, Act, Assert

Casi toda prueba de unidad se puede leer como tres bloques separados, y separarlos mentalmente te ayuda a escribir pruebas claras:

```java
@Test
void retirarDineroReduceSaldo() {
    // Arrange (preparar): crear el escenario
    CuentaBancaria cuenta = new CuentaBancaria(100.0);

    // Act (actuar): ejecutar UNA accion
    cuenta.retirar(30.0);

    // Assert (comprobar): verificar el resultado
    assertEquals(70.0, cuenta.getSaldo());
}
```

Tres reglas que salen de esta estructura:

1. **Un concepto por test**: si necesitas varios bloques Act, probablemente son varios tests. Cada test comprueba UN escenario.
2. **Los asserts son lo último**: después de un `assertEquals` ya no debería haber código, porque si falla, nunca se ejecutaría.
3. **Sin lógica en la prueba**: nada de bucles ni ifs para "generar" el resultado esperado. Si el test tiene la misma lógica que el código, ambos pueden estar mal igual y la prueba no detecta nada. Los valores esperados deben ser **literales concretos** (`70.0`, no `100.0 - 30.0`).

## 7. Como nombrar las pruebas

El nombre del test es su documentación. Cuando falla a las 3 AM, el nombre es lo primero que lees. La convención más legible es describir **el comportamiento y su resultado**:

```java
// MAL: nombres tecnicos sin significado
@Test void test1() { }
@Test void testSaldo() { }

// BIEN: el nombre cuenta el escenario
@Test void saldoSeReduceAlRetirarDinero() { }
@Test void rechazaRetiroMayorQueSaldo() { }
@Test void saldoCeroNoEsNegativo() { }
```

Un truco útil: si no puedes nombrar la prueba con claridad, es señal de que el método que estás probando hace demasiadas cosas. Nombrar bien es también una herramienta de diseño.

## 8. Que probar y que no

No intentes conseguir un porcentaje de cobertura religioso. Prioriza por riesgo:

- **Sí**: lógica de negocio (cálculos, reglas, validaciones), casos límite (0, negativos, listas vacías, null donde aplique), caminos de error, código que ya se rompió una vez (cada bug corregido merece su test).
- **No**: getters y setters triviales, código generado (Lombok, records), `main()` de arranque, detalles de implementación que pueden cambiar.

La prueba debe comprobar **qué hace** el código (su contrato observable), no **cómo lo hace** (sus entrañas). Un test que comprueba el contrato te permite refactorizar las entrañas sin miedo; uno acoplado a la implementación se rompe con cada cambio y acaba borrado.

---

## Errores Comunes

> **Confundir el orden de assertEquals**. `assertEquals(obtenido, esperado)` compila igual pero los mensajes de fallo muestran al revés lo esperado y lo obtenido. Esperado SIEMPRE primero.

> **Escribir pruebas con lógica**. Un `if` o un cálculo dentro del test reproduce la misma lógica que el código bajo prueba: si ambos están mal igual, la prueba pasa en falso. Usa valores literales concretos.

> **Pruebas que dependen entre sí**. Si el test B falla cuando se ejecuta solo pero pasa cuando se ejecuta tras el test A, tienes estado compartido. Cada test debe construir su propio escenario en `@BeforeEach`.

> **Olvidar assertThrows para caminos de error**. Solo probar el camino feliz es dejar la mitad del código sin vigilancia. Cada validación y cada `throw` merecen su prueba.

> **Poner @BeforeEach como static**. Solo `@BeforeAll` y `@AfterAll` son static (se ejecutan antes/después de que existan instancias). `@BeforeEach` y `@AfterEach` son métodos de instancia.

---

## Conexiones

- [[02 - Punto de Entrada main]] - La prueba es como un mini-main que llama a tu código
- [[23 - Metodos - Parametros, Retorno y Return]] - Cada método con contrato es candidato a prueba
- [[40 - Manejo de Excepciones]] - assertThrows comprueba los throw de tu código
- [[42 - Lambda en Profundidad]] - Las lambdas aparecen en assertThrows y en ArgumentMatcher
- [[46 - Metodos de Consulta y Tell Don't Ask]] - Los métodos query son los más fáciles de probar
- [[53 - Dependencias de prueba y alcance test]] - Como llega JUnit a tu proyecto con Maven
- [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]] - Probar con new los componentes inyectados

---

## Tags
`#java #testing #junit #pruebas #calidad`
