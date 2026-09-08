---
tags: [java, interfaces, override, contratos, polimorfismo, poo, abstraccion]
---

# 63 - Interfaces y @Override (implementar contratos)

Una interfaz es un **contrato de comportamiento**: promete qué se puede hacer con un objeto, sin decir cómo. Una clase implementa el contrato con `implements` y firma cada promesa con `@Override`. Juntos son la herramienta de Java para programar contra capacidades y no contra clases concretas.

---

# METODO EXPLICATIVO

## 1. La interfaz como contrato de comportamiento

Una clase describe **qué es** un objeto (sus atributos y su estado). Una interfaz describe **qué sabe hacer**: solo métodos, sin estado y sin implementación (salvo excepciones que verás después). No define un tipo de objeto, define una **capacidad**.

```java
public interface Pagable {
    double totalAPagar();        // promesa: puedes preguntarme el total
    boolean estaPagado();        // promesa: puedes preguntarme si esta pagado
}
```

La interfaz es 100% ambigua sobre el cómo: puede ser un pedido, una factura, una suscripción. Lo único que garantiza es que **todo lo que sea `Pagable` tiene esos dos métodos**. Esa garantía es el contrato: otros componentes pueden usar la capacidad sin saber la clase.

Un recordatorio de sintaxis: los métodos de interfaz son implícitamente `public abstract` (no hace falta escribirlo), y una interfaz no puede tener estado (no atributos de instancia, solo `static final`). Si necesitas estado, no es una interfaz: es una clase abstracta o una clase normal.

## 2. implements: la obligacion de cumplir el contrato

```java
public class Pedido implements Pagable {

    private final List<Double> lineas;

    @Override
    public double totalAPagar() {
        return lineas.stream().mapToDouble(Double::doubleValue).sum();
    }

    @Override
    public boolean estaPagado() {
        return pagado;
    }
}
```

`implements` crea una obligación verificada por el compilador: **si declaras implementar el contrato, debes implementar todos sus métodos**, o tu clase debe ser `abstract` (y entonces otra clase hija acabará de pagar la deuda). Esta obligación es lo que hace útil al contrato: quien trabaja con un `Pagable` sabe con certeza absoluta que esos métodos existen y responden.

## 3. @Override: por que siempre

`@Override` no es obligatorio: es una **anotación de verificación** que dice al compilador "este método implementa o sobrescribe algo de arriba". Sin ella, un error de dedo se convierte en bug silencioso:

```java
public class Pedido implements Pagable {

    // SIN @Override: firma mal escrita, compila perfecto, bug silencioso
    public double totalAPagarMal() {   // el contrato NO esta cumplido
        return lineas.stream().mapToDouble(Double::doubleValue).sum();
    }

    // CON @Override: el compilador lo detecta al momento
    @Override
    public double totalAPagar() { ... }   // verifico que realmente implemento algo
}
```

El caso trampa: escribes `totalAPagar()` y el compilador no se queja (el archivo es sintácticamente válido), pero tu clase no implementa el contrato. El fallo aparece en ejecución, lejos del origen. Con `@Override`, el error es inmediato y en la línea exacta: **la anotación convierte un bug de runtime en error de compilación**. Además es documentación viva: leyendo la clase, sabes de un vistazo qué métodos son implementación de contratos y cuáles son propios.

Regla: `@Override` en **todo** método que implemente una interfaz o sobrescriba una superclase. Sin excepciones.

## 4. Programar contra contratos: el uso real

Aquí está la verdadera potencia. Una clase que **declara su dependencia por la interfaz** acepta cualquier implementación presente o futura:

```java
public class Caja {

    private final Pagable documentoActual;   // contrato, no clase concreta

    public void cobrar() {
        double total = documentoActual.totalAPagar();  // no me importa como lo calcula
        System.out.println("Cobrando " + total);
    }
}
```

`Caja` no sabe si cobra un pedido o una factura: sabe que cobra un `Pagable`. Esto es el principio DIP (depender de abstracciones) llevado a la práctica, y es la base de la inyección de dependencias en Spring: los componentes se conectan por contrato. En el mundo real lo vives constantemente: `List<T>` es una interfaz, y tu código que la usa funciona igual con `ArrayList` hoy y con cualquier otra implementación mañana.

El patrón canónico de arquitectura en capas lo usa a fondo: un `PedidoRepository` (interfaz) con una implementación en memoria para tests y otra en base de datos para producción. La lógica de negocio no cambia; se cambia la implementación inyectada.

## 5. Metodos default y private: el contrato con implementacion por defecto

Java 8 añadió métodos con cuerpo en las interfaces (`default`) y Java 9 los privados:

```java
public interface Pagable {

    double totalAPagar();
    boolean estaPagado();

    // Implementacion por defecto: las clases pueden usarla o sobrescribirla
    default String descripcion() {
        return "Documento por " + totalAPagar();
    }
}
```

Los `default` permiten añadir métodos nuevos a interfaces viejas **sin romper las implementaciones existentes** (todo el ecosistema de streams existentes en Java 8 dependía de esto: `stream()` se añadió a `Collection` como `default`). Pero son una excepción con propósito: la interfaz sigue siendo principalmente contrato. Si tu interfaz tiene más lógica implementada que promesas, probablemente deberías tener una clase abstracta.

## 6. Interfaces funcionales: una promesa y una lambda

Una interfaz con **un solo método abstracto** es una "interfaz funcional", y ahí es donde Java moderno cambia de forma de usarla: en lugar de escribir una clase con `implements`, pasas directamente el comportamiento como lambda, y el `@Override` es implícito:

```java
// La interfaz funcional de siempre
public interface Validador<T> {
    boolean esValido(T valor);
}

// Modo antiguo: clase anonima con implements y @Override explicito
Validador<String> antiguo = new Validador<String>() {
    @Override
    public boolean esValido(String valor) {
        return valor != null && !valor.isBlank();
    }
};

// Modo moderno: lambda, el @Override lo genera el compilador
Validador<String> moderno = valor -> valor != null && !valor.isBlank();
```

Ambos códigos son equivalentes: el compilador infiere que la lambda implementa `esValido`. Comprender esta equivalencia es lo que conecta la POO clásica de interfaces con el Java funcional: una lambda no es magia, es un `implements` comprimido.

## 7. Polimorfismo por interfaz en accion

El polimorfismo clásico por herencia compara subtipos de una superclase; el polimorfismo por interfaz agrupa por **capacidad** sin importar la jerarquía:

```java
List<Pagable> porCobrar = List.of(pedido, factura, suscripcion, contrato);
for (Pagable doc : porCobrar) {
    if (!doc.estaPagado()) {
        System.out.println(doc.descripcion());
    }
}
```

Cuatro clases sin relación entre sí (ni jerarquía compartida, ni herencia) responden al mismo mensaje. La lista no distingue clases: distingue la promesa. Esto es lo que hace escalable el diseño: agregar un nuevo tipo "pagable" no exige tocar nada del código que ya cobra, solo crear la clase que implemente el contrato.

---

## Errores Comunes

> **Olvidar @Override y no detectar el error de firma**. Escribe el metodo con una letra distinta y la clase "implementa" un metodo que no es del contrato: bug de runtime. La anotacion lo convierte en error de compilacion. Ponla en todo metodo que implemente o sobrescriba.

> **Reducir la visibilidad al implementar**. Los metodos de interfaz son `public`: no puedes implementarlos como `private` o sin modificador. La promesa es publica para todos.

> **Interfaces gigantes con muchas responsabilidades**. Un contrato con 20 metodos obliga a cualquier clase que lo implemente a cumplirlo todo, aunque necesite 3. Mejor varios contratos pequenos (ISP: Interface Segregation): cada cliente depende de lo que realmente usa.

> **Usar default methods para meter logica de negocio en la interfaz**. El `default` es para evolucionar APIs sin romper implementaciones, no para poner la logica en el contrato. Si la interfaz "hace cosas", probablemente es una clase disfrazada.

> **Confundir implementar (interfaz) con heredar (clase)**. `implements` cumple promesas de comportamiento; `extends` hereda estado y comportamiento. Un record, ademas, implementa interfaces pero no extiende clases.

> **Interfaz con una sola implementacion y cero intencion de cambiarla**. Crear una interfaz "por si acaso" anade indireccion sin beneficio. La interfaz vale la pena cuando hay (o habra) mas de una implementacion, o cuando separates capas para testear con mocks.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - La clase que implementa el contrato
- [[10 - Metodos de Instancia]] - Los metodos que la interfaz promete
- [[09 - Multiples Objetos e Identidad]] - equals/hashCode: contratos que Object ya define
- [[22 - Separacion de Responsabilidades]] - DIP: depender de abstracciones
- [[28 - Static en Profundidad]] - @Override no funciona con static
- [[37 - Record (y Value Objects)]] - Records que implementan contratos
- [[42 - Lambda en Profundidad]] - La lambda como implements implicito
- [[45 - Genericos en Profundidad]] - Interfaces genericas y bounded types
- [[64 - Inyeccion de dependencias (Configuration, Bean, Autowired)]] - Los beans se conectan por contrato

---

## Tags
`#java #interfaces #override #contratos #polimorfismo #poo`
