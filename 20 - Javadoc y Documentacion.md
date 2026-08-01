---
tags: [java, fundamentos, javadoc, documentacion, comentarios]
---

# 20 - Javadoc y Documentacion

---

## NIVEL JUNIOR

### Que es Javadoc?

Javadoc es una forma de escribir documentacion en el codigo usando comentarios especiales `/** ... */`. Con ellos puedes generar HTML que explica tu API.

### Comentarios normales vs Javadoc

```java
// Esto es un comentario de linea (no genera documentacion)

/*
   Esto es un comentario de bloque (no genera documentacion)
*/

/**
 * Esto es Javadoc. Genera documentacion automatica.
 */
```

### Javadoc basico

```java
/**
 * Saluda a una persona
 */
public void saludar(String nombre) {
    System.out.println("Hola " + nombre);
}
```

### Etiquetas basicas

```java
/**
 * Calcula el area de un rectangulo
 *
 * @param ancho el ancho del rectangulo (debe ser positivo)
 * @param alto  el alto del rectangulo (debe ser positivo)
 * @return el area calculada
 */
public double calcularArea(double ancho, double alto) {
    return ancho * alto;
}
```

---

## NIVEL MID

### Javadoc completo para una clase

```java
/**
 * Representa un producto en el sistema de inventario.
 * <p>
 * Cada producto tiene un codigo unico que lo identifica,
 * un nombre, un precio y una cantidad en stock.
 * </p>
 *
 * @author Ana Martinez
 * @version 1.0
 */
public class Producto {
    private String codigo;
    private String nombre;
    private double precio;
    private int stock;

    /**
     * Crea un nuevo producto.
     *
     * @param codigo codigo unico del producto (no puede ser null)
     * @param nombre nombre del producto
     * @param precio precio unitario (debe ser mayor que 0)
     * @throws IllegalArgumentException si precio es menor o igual a 0
     */
    public Producto(String codigo, String nombre, double precio) {
        if (precio <= 0) {
            throw new IllegalArgumentException("El precio debe ser positivo");
        }
        this.codigo = codigo;
        this.nombre = nombre;
        this.precio = precio;
        this.stock = 0;
    }

    /**
     * Obtiene el nombre del producto.
     *
     * @return el nombre del producto
     */
    public String getNombre() {
        return nombre;
    }

    /**
     * Anade stock al producto.
     *
     * @param cantidad cantidad a anadir (debe ser positiva)
     * @throws IllegalArgumentException si cantidad es negativa
     */
    public void anadirStock(int cantidad) {
        if (cantidad < 0) {
            throw new IllegalArgumentException("La cantidad debe ser positiva");
        }
        this.stock += cantidad;
    }
}
```

### Etiquetas mas comunes

| Etiqueta | Donde se usa | Que hace |
|----------|-------------|----------|
| `@param` | Metodos/constructores | Describe un parametro |
| `@return` | Metodos | Describe el valor devuelto |
| `@throws` | Metodos | Describe una excepcion que puede lanzar |
| `@see` | Cualquier sitio | Referencia a otra clase/metodo |
| `@since` | Cualquier sitio | Desde que version existe |
| `@deprecated` | Cualquier sitio | Marca como obsoleto |
| `{@code}` | Dentro de descripcion | Muestra texto en fuente de codigo |
| `{@link}` | Dentro de descripcion | Enlace a otra clase/metodo |

---

## NIVEL SENIOR

### Javadoc con referencias a codigo

```java
/**
 * Procesa un pago utilizando el {@link ProcesadorPago} configurado.
 * <p>
 * El metodo sigue estos pasos:
 * <ol>
 *   <li>Valida que el importe sea positivo</li>
 *   <li>Verifica que el {@code usuario} tenga saldo suficiente</li>
 *   <li>Delega en el {@link ProcesadorPago#procesar(Pago)}</li>
 *   <li>Registra la transaccion en el historial</li>
 * </ol>
 * </p>
 *
 * @param usuario  el usuario que realiza el pago
 * @param importe  cantidad a pagar, debe ser {@code > 0}
 * @return el identificador de la transaccion generada
 * @throws PagoException       si el procesador rechaza el pago
 * @throws IllegalArgumentException si {@code importe <= 0}
 * @since 2.0
 */
public String procesarPago(Usuario usuario, double importe) {
    // ...
}
```

### Javadoc en records

```java
/**
 * Representa una direccion postal.
 *
 * @param calle        nombre de la calle y numero
 * @param ciudad       nombre de la ciudad
 * @param codigoPostal codigo postal de 5 digitos
 * @param pais         nombre del pais
 */
public record Direccion(
    String calle,
    String ciudad,
    String codigoPostal,
    String pais
) {
    /**
     * Valida que el codigo postal tenga 5 digitos.
     */
    public Direccion {
        if (codigoPostal != null && !codigoPostal.matches("\\d{5}")) {
            throw new IllegalArgumentException("Codigo postal invalido: " + codigoPostal);
        }
    }

    /**
     * Devuelve la direccion formateada en una linea.
     *
     * @return "calle, ciudad, codigoPostal, pais"
     */
    public String formatear() {
        return "%s, %s, %s, %s".formatted(calle, ciudad, codigoPostal, pais);
    }
}
```

### Javadoc con {@inheritDoc}

```java
/**
 * Interfaz para repositorios de datos.
 */
public interface Repositorio<T> {
    /**
     * Guarda una entidad en el repositorio.
     *
     * @param entidad la entidad a guardar
     * @return la entidad guardada con su identificador
     */
    T guardar(T entidad);
}

/**
 * Implementacion concreta de {@link Repositorio} para usuarios.
 */
public class UsuarioRepositorio implements Repositorio<Usuario> {
    /**
     * {@inheritDoc}
     * <p>
     * Adicionalmente, valida que el email del usuario no exista ya.
     * </p>
     *
     * @param usuario el usuario a guardar
     * @return el usuario guardado
     * @throws IllegalArgumentException si el email ya esta registrado
     */
    @Override
    public Usuario guardar(Usuario usuario) {
        // ...
    }
}
```

### Generar documentacion HTML

```bash
# Desde terminal:
javadoc -d docs src/main/java/com/miempresa/*.java

# Con Maven:
mvn javadoc:javadoc

# Con Gradle:
./gradlew javadoc
```

---

## Errores Comunes

> Escribir Javadoc que solo repite lo obvio. `@return el nombre` para un metodo llamado `getNombre()` no anade valor. Explica el QUE y POR QUE, no el COMO.

> No actualizar el Javadoc cuando cambia el codigo. Un Javadoc desactualizado es peor que ninguno.

> Poner Javadoc en metodos privados. El Javadoc es para la API publica. Los metodos privados no generan documentacion.

> No documentar excepciones. Si un metodo lanza `@throws IllegalArgumentException`, documenta cuando ocurre.

> Usar `@author` y `@version` en clases que cambian frecuentemente. Se desactualizan rapido. Mejor usar control de versiones (git).

---

## Buenas Practicas

1. Documenta toda API publica (clases publicas, metodos publicos y protected).
2. Describe el QUE y el POR QUE, no el COMO. El codigo ya muestra el como.
3. Incluye `@param`, `@return` y `@throws` en metodos publicos.
4. Usa `{@code}` para referencias a codigo y `{@link}` para enlaces a otras clases.
5. Manten el Javadoc actualizado con el codigo.
6. No documentes lo obvio. `@return the name` en `getName()` no aporta nada.

---

## Conexiones

- [[01 - Clases y Estructura Basica]] - Documentar la clase
- [[07 - Constructores y this]] - Documentar constructores
- [[10 - Metodos de Instancia]] - Documentar metodos
- [[21 - Getters y Setters]] - Documentar accesores
- [[25 - Paquetes y Organizacion]] - Documentacion de paquetes

---

## Tags
`#java #fundamentos #javadoc #documentacion #comentarios #api`
