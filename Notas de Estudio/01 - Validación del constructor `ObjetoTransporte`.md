# Notas

## 1. Constructor

`public ObjetoTransporte` indica un **constructor público**.

- `public` → permite acceder desde otras clases.
    
- `ObjetoTransporte` → es el nombre del constructor y coincide con el nombre de la clase.
    
- Un constructor **no tiene tipo de retorno**.
    
- `{ }` → delimitan el bloque de instrucciones del constructor.
    

> En el código estudiado falta la lista de parámetros `(...)`, así que no la hemos trabajado.

---

## 2. `if`

`if` es una **estructura de control condicional**.

Su idea básica:

> Si una condición es verdadera, ejecuta el bloque de código.

Ejemplo:

```java
if (cantidad < 1) {
    // ...
}
```

---

## 3. `null`

`null` representa la **ausencia de un valor o de una referencia a un objeto**.

No significa necesariamente que el dato sea incorrecto.

Por ejemplo:

```java
pesoManualKg != null
```

significa que existe un valor de peso.

---

## 4. Operadores

### `==`

Compara si dos valores son iguales.

### `!=`

Compara si dos valores son diferentes.

### `<`

Significa **menor que**.

### `<=`

Significa **menor o igual que**.

### `&&`

Es el operador lógico **Y**.

Las dos condiciones deben ser verdaderas.

Ejemplo:

```java
pesoManualKg != null && pesoManualKg <= 0
```

Se lee:

> El peso no es `null` **y además** el peso es menor o igual que `0`.

---

## 5. `throw`

`throw` es una **palabra reservada de Java** que sirve para **lanzar una excepción**.

Ejemplo:

```java
throw new IllegalArgumentException(...)
```

Idea mental:

> "He encontrado una situación que no es válida, así que lanzo una excepción."

---

## 6. `new`

`new` sirve para **crear una nueva instancia de una clase**.

Por ejemplo:

```java
new IllegalArgumentException(...)
```

crea una nueva excepción.

### Diferencia importante

```text
new   → crea
throw → lanza
```

---

## 7. `IllegalArgumentException`

`IllegalArgumentException` es una **clase de excepción de Java**.

Se utiliza cuando un método o constructor recibe un **argumento no válido**.

Ejemplo:

```java
throw new IllegalArgumentException(
    "El peso debe ser mayor que 0"
);
```

---

## 8. Validación de `pesoManualKg`

La condición:

```java
pesoManualKg != null && pesoManualKg <= 0
```

distingue entre diferentes situaciones:

|Valor|Resultado|
|---|---|
|`null`|No se proporciona peso. En este diseño se permite.|
|`0`|Peso inválido → se lanza excepción.|
|`-5`|Peso inválido → se lanza excepción.|
|`25.5`|Peso válido → continúa.|

La idea importante es distinguir:

> **No hay dato** (`null`)  
> de  
> **Hay un dato, pero es incorrecto** (`0` o negativo).

---

## 9. ¿Qué hace todo el bloque?

El constructor valida los datos antes de permitir que el objeto se cree.

Comprueba:

```text
tipo       → no puede ser null
cantidad   → debe ser al menos 1
peso       → si existe, debe ser > 0
alto       → si existe, debe ser > 0
ancho      → si existe, debe ser > 0
largo      → si existe, debe ser > 0
```

Si una regla no se cumple:

```text
condición incorrecta
        ↓
       if
        ↓
      throw
        ↓
       new
        ↓
IllegalArgumentException
        ↓
se detiene la creación del objeto
```

---

## 10. Idea mental principal

La plantilla mental que podemos extraer de este bloque es:

> **"Antes de crear el objeto, compruebo que sus datos cumplen las reglas. Si algún dato es inválido, lanzo una excepción."**

---

## 11. ¿Se puede mejorar?

Sí, pero los `if` **no son necesariamente una mala solución**.

En este caso son claros y fáciles de leer.

Hay una repetición en estas validaciones:

```java
pesoManualKg != null && pesoManualKg <= 0
altoCm != null && altoCm <= 0
anchoCm != null && anchoCm <= 0
largoCM != null && largoCM <= 0
```

Una posible mejora sería crear un **método auxiliar** que permita reutilizar esa misma lógica.

Eso sería el siguiente paso de refactorización que podemos estudiar.