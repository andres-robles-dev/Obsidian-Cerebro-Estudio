
## 1. Bloque estudiado

Trabajamos con un método que determina si un objeto de transporte es pesado:

```java
public boolean esPesado() {
    Double peso = pesoManualKg != null
            ? pesoManualKg
            : tipo.getPesoPorDefectoKg();

    return peso > UMBRAL_PESADO;
}
```

La idea funcional del método es:

> Obtener el peso que corresponde al objeto y determinar si supera el umbral establecido.

---

## 2. Cómo plantear un problema como programador

Antes de escribir código, conviene transformar el problema en pasos lógicos.

En este caso:

1. Necesitamos saber qué peso utilizar.
    
2. Puede existir un `pesoManualKg`.
    
3. Si existe, usamos ese valor.
    
4. Si no existe (`null`), usamos el peso por defecto del `tipo`.
    
5. Guardamos el peso definitivo en una variable llamada `peso`.
    
6. Comparamos `peso` con `UMBRAL_PESADO`.
    
7. La comparación produce `true` o `false`.
    
8. El método devuelve ese resultado.
    

Plantilla mental:

```text
¿Qué resultado necesito?
        ↓
¿Qué datos necesito?
        ↓
¿Qué decisiones tengo que tomar?
        ↓
¿Qué operación permite expresar esas decisiones?
        ↓
¿Qué tengo que devolver?
```

---

## 3. Dificultad: recordar las partes de un método

Durante la sesión costó identificar qué partes debía tener el método.

Una plantilla mental útil es:

```text
visibilidad
+
tipo de retorno
+
nombre
+
parámetros
+
bloque de código
```

Ejemplo:

```java
public boolean esPesado() {
    ...
}
```

### `public`

Indica quién puede acceder al método.

### `boolean`

Indica qué tipo de resultado devuelve el método.

En este caso:

```text
boolean → true o false
```

### `esPesado`

Es el nombre del método.

Además, el nombre describe una pregunta:

> ¿Es pesado?

Eso encaja con que el método devuelva un `boolean`.

### `()`

Indica los parámetros del método.

En nuestro caso no necesitamos pasar `pesoManualKg` como parámetro porque ese dato ya pertenece al objeto `ObjetoTransporte`.

### `{ }`

Delimitan el bloque de instrucciones del método.

---

## 4. Dificultad: no saber por dónde empezar a construir el método

Al principio costaba decidir qué escribir primero.

La idea que apareció durante la sesión fue:

> Primero identificar qué resultado queremos obtener y después construir las operaciones necesarias para conseguirlo.

En este caso:

```text
Resultado deseado:
¿el objeto es pesado?

↓

Necesitamos conocer el peso que debemos utilizar.

↓

Necesitamos decidir de dónde sale ese peso.

↓

Una vez obtenido, lo comparamos con el umbral.

↓

Devolvemos true o false.
```

Esto evita empezar directamente escribiendo código sin haber pensado la solución.

---

## 5. El operador ternario `?:`

Este fue uno de los puntos que más costaron.

El operador ternario permite elegir entre dos valores dependiendo de una condición.

Su estructura es:

```java
condición ? valorSiTrue : valorSiFalse
```

### `?`

Separa la condición del valor que se utilizará cuando la condición sea verdadera.

### `:`

Separa el valor para `true` del valor para `false`.

### Ejemplo del código

```java
pesoManualKg != null
    ? pesoManualKg
    : tipo.getPesoPorDefectoKg()
```

Se lee:

> ¿`pesoManualKg` tiene un valor?

Si la respuesta es `true`:

```text
usar pesoManualKg
```

Si la respuesta es `false`:

```text
usar tipo.getPesoPorDefectoKg()
```

### Lo importante

No necesitamos guardar el `true` o el `false` en una variable.

El operador ternario utiliza ese resultado para elegir uno de los dos valores.

Por eso:

```java
Double peso = pesoManualKg != null
        ? pesoManualKg
        : tipo.getPesoPorDefectoKg();
```

significa:

> La variable `peso` recibirá `pesoManualKg` si existe; de lo contrario recibirá el peso por defecto.

---

## 6. `pesoManualKg != null`

Esta expresión fue importante entenderla.

```java
pesoManualKg != null
```

Se puede leer de forma sencilla como:

> ¿`pesoManualKg` tiene un valor?

O:

> ¿El usuario ha introducido un peso manual?

El resultado de esta comparación es un `boolean`:

```text
true
o
false
```

Ejemplos:

```text
pesoManualKg = 25.5
→ true
```

```text
pesoManualKg = null
→ false
```

---

## 7. Por qué `pesoManualKg` no es un parámetro

Al principio se planteó:

```java
esPesado(Double pesoManualKg)
```

Pero después vimos que no es necesario.

La razón es:

> `pesoManualKg` ya pertenece al objeto `ObjetoTransporte`.

El método `esPesado()` está dentro de ese objeto y puede acceder directamente a sus propios atributos.

Regla mental:

> No preguntarse si el dato está en otro archivo, sino si el método tiene acceso al dato que necesita.

Si el método tiene acceso al atributo, no necesita recibirlo como parámetro.

---

## 8. Si el método estuviera en otra clase

Si el método estuviera en otra clase y no tuviera acceso a los datos de `ObjetoTransporte`, entonces necesitaría recibir la información de alguna manera.

Podría recibir:

- directamente `pesoManualKg`;
    
- o recibir el propio `ObjetoTransporte`.
    

La idea importante es:

> Si un método no tiene acceso a un dato que necesita, hay que proporcionárselo.

---

## 9. `return`

Otro punto que costó recordar fue cuándo utilizar `return`.

En nuestro método:

```java
public boolean esPesado()
```

el método promete devolver un:

```text
boolean
```

Por tanto, necesita terminar devolviendo un valor compatible con ese tipo.

La expresión:

```java
peso > UMBRAL_PESADO
```

produce:

```text
true
o
false
```

Por eso podemos devolver ese resultado.

### Plantilla mental

```text
Tipo de retorno del método
        ↓
¿Qué debe devolver?
        ↓
return + resultado
```

Ejemplos:

```text
boolean → true / false
double  → un número decimal
int     → un número entero
String  → texto
```

---

## 10. Regla mental para `return`

Una pregunta útil al programar:

> ¿Qué promete devolver el método?

Después:

> ¿Dónde obtengo ese resultado?

Y finalmente:

> ¿Tengo que devolverlo con `return`?

En el método estudiado:

```text
El método promete → boolean

La comparación produce → true / false

Por tanto → return del resultado de la comparación
```

---

## 11. Operador `>`

En:

```java
peso > UMBRAL_PESADO
```

`>` es el operador de comparación:

> mayor que

La expresión pregunta:

> ¿El peso es mayor que el umbral establecido?

Si sí:

```text
true
```

Si no:

```text
false
```

---

## 12. La solución que construimos mentalmente

El razonamiento completo quedó así:

```text
¿Tengo peso manual?
        ↓
      Sí → usar pesoManualKg
        ↓
      No → usar peso por defecto
        ↓
      Guardarlo en peso
        ↓
¿peso > UMBRAL_PESADO?
        ↓
   true / false
        ↓
     return
```

---

## 13. Lo que más costó en esta sesión

### A. Identificar las partes de un método

Costó recordar qué lleva un método y qué significa cada parte.

### B. Saber por dónde empezar

Costó pasar del enunciado a una secuencia de instrucciones.

La mejora fue pensar primero:

> resultado → datos → decisiones → operaciones → retorno

### C. Elegir operadores

Costó identificar qué operador servía para comparar y elegir entre dos valores.

El operador especialmente difícil fue:

```java
?:
```

### D. Recordar `return`

Costó recordar cuándo el resultado debe ser retornado.

La clave es mirar el **tipo de retorno del método**.

---

## 14. Patrones mentales que debemos reforzar

### Para construir métodos

```text
¿Qué debe devolver?
¿Qué datos necesita?
¿Qué decisiones hay?
¿Qué operaciones hacen falta?
¿Qué debo retornar?
```

### Para el operador ternario

```text
¿condición?
    ↓
true  → A
false → B
```

### Para `return`

```text
¿Qué promete devolver el método?
        ↓
Obtengo ese resultado
        ↓
return resultado
```

### Para decidir si un parámetro es necesario

```text
¿El método ya tiene acceso al dato?
        ↓
Sí → no necesito pasarlo
No → necesito proporcionárselo
```

---

## 15. Idea principal de la sesión

La principal lección no es memorizar la sintaxis del método.

Es aprender a transformar una necesidad en una secuencia lógica:

> **Primero pienso qué quiero conseguir; después identifico los datos y las decisiones necesarias; finalmente elijo la sintaxis de Java que expresa esa lógica.**

En esta sesión conseguimos construir el método a partir del problema sin copiar directamente la solución.