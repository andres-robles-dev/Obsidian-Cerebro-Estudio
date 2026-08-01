---
tags: [leetcode, arrays, bucles, for, hashmap]
---

# 002 - Two Sum

---

## Descripcion del Problema

Dado un array de enteros `nums` y un entero `target`, devuelve los indices de los dos numeros que sumados den `target`.

Puedes asumir que cada entrada tiene exactamente una solucion, y no puedes usar el mismo elemento dos veces.

Puedes devolver la respuesta en cualquier orden.

**Ejemplo 1:**
- Entrada: `nums = [2,7,11,15]`, `target = 9`
- Salida: `[0,1]`
- Explicacion: Porque `nums[0] + nums[1] == 9`, devolvemos `[0, 1]`

**Ejemplo 2:**
- Entrada: `nums = [3,2,4]`, `target = 6`
- Salida: `[1,2]`

**Ejemplo 3:**
- Entrada: `nums = [3,3]`, `target = 6`
- Salida: `[0,1]`

**Restricciones:**
- `2 <= nums.length <= 10^4`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`
- Solo existe una respuesta valida

---

## Solucion (Codigo)

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for (int numInicial = 0; numInicial < nums.length; numInicial++) {
            int numFaltante = target - nums[numInicial];
            for (int numQFalta = numInicial + 1; numQFalta < nums.length; numQFalta++) {
                if (numFaltante == nums[numQFalta]) {
                    return new int[]{numInicial, numQFalta};
                }
            }
        }
        return new int[]{};
    }
}
```

---

## Mi Intento

El problema me pide buscar dentro de una lista de numeros llamada `nums` dos numeros que sumados den el numero `target`. No me pide que devuelva los numeros, sino las posiciones donde estan. Lo primero que hice fue crear un bucle con una variable llamada `numInicial` que empieza en 0 y recorre toda la lista. En cada vuelta, tomo el numero que esta en esa posicion y lo resto con `target` para saber que numero necesito encontrar, y guardo ese resultado en una variable llamada `numFaltante`. Luego creo un segundo bucle con una variable llamada `numQFalta` que empieza desde la posicion siguiente a `numInicial` (para no usar el mismo elemento dos veces) y va recorriendo el resto de la lista. Dentro de este bucle pongo una condicion que pregunta si el numero en esa posicion es igual a `numFaltante`. Si es igual, significa que encontre el par que suma `target`, y devuelvo las posiciones de ambos numeros con `return new int[]{numInicial, numQFalta}`. Si el segundo bucle termina sin encontrar nada, el primer bucle avanza a la siguiente posicion y repite todo el proceso. Si recorro toda la lista y no encuentro ningun par, devuelvo un array vacio.

---

## Explicacion

### Paso 1: Entender el problema

El ejercicio nos da dos cosas: una lista de numeros llamada `nums` y un numero objetivo llamado `target`. Lo que tenemos que hacer es encontrar dos numeros dentro de la lista que sumados den exactamente `target`. No devolvemos los numeros en si, sino sus posiciones dentro de la lista.

Por ejemplo, si tenemos `nums = [2, 7, 11, 15]` y `target = 9`, el `2` esta en la posicion `0` y el `7` en la posicion `1`, y `2 + 7 = 9`. La respuesta es `[0, 1]`.

### Paso 2: El primer bucle (elige el primer numero)

```java
for (int numInicial = 0; numInicial < nums.length; numInicial++)
```

Se crea un bucle con una variable llamada `numInicial`. Esta variable empieza en `0` y representa la posicion actual que estamos revisando. La condicion `numInicial < nums.length` le dice al bucle que siga mientras `numInicial` sea menor que la cantidad total de casillas que tiene la lista. En cada vuelta, `numInicial` se incrementa en 1 (`numInicial++`), pasando a la siguiente posicion.

Este bucle se encarga de **elegir el primer numero** del par que buscamos. Por ejemplo, en la primera vuelta `numInicial = 0` y estamos mirando el numero en la casilla 0, que es `2`. En la segunda vuelta `numInicial = 1` y miramos el `7`, y asi sucesivamente.

### Paso 3: Calcular el numero que falta

```java
int numFaltante = target - nums[numInicial];
```

Dentro del primer bucle, creamos una variable llamada `numFaltante`. En ella guardamos el resultado de restar `target` menos el numero que esta en la posicion actual. Esto nos dice que numero necesitamos encontrar para completar la suma.

Por ejemplo, si `target = 9` y `nums[0] = 2`, entonces `numFaltante = 9 - 2 = 7`. Necesitamos encontrar un `7` en la lista.

### Paso 4: El segundo bucle (busca el complemento)

```java
for (int numQFalta = numInicial + 1; numQFalta < nums.length; numQFalta++)
```

Se crea un segundo bucle, esta vez con una variable llamada `numQFalta`. Nota que esta variable **no empieza en 0**, sino en `numInicial + 1`. Esto es importante porque no podemos usar el mismo elemento dos veces. Si el primer numero esta en la posicion 0, empezamos a buscar el segundo desde la posicion 1. Si el primero esta en la posicion 1, buscamos desde la 2, y asi.

Este segundo bucle recorre las casillas siguientes y en cada vuelta va a comparar el numero que hay ahi con el `numFaltante` que calculamos antes.

### Paso 5: La condicion que encuentra la solucion

```java
if (numFaltante == nums[numQFalta]) {
    return new int[]{numInicial, numQFalta};
}
```

Dentro del segundo bucle, preguntamos: "El numero que esta en esta casilla es igual al numero que estamos buscando?" Si la respuesta es si, significa que encontramos el par. En ese momento usamos `return` para devolver inmediatamente un array con las dos posiciones: la del primer numero (`numInicial`) y la del segundo (`numQFalta`). El `return` hace que el metodo termine ahi mismo, no sigue buscando.

### Paso 6: Si no se encuentra nada

```java
return new int[]{};
```

Si el primer bucle termina de recorrer todas las posiciones sin que la condicion se cumpla nunca, significa que no existe ningun par de numeros que sumen `target`. En ese caso, salimos del bucle y devolvemos un array vacio.

### Ejemplo completo paso a paso

```
nums = [2, 7, 11, 15], target = 9

Vuelta 1 del primer bucle: numInicial = 0
  nums[0] = 2
  numFaltante = 9 - 2 = 7
  
  Segundo bucle empieza desde numInicial + 1 = 1:
    numQFalta = 1 -> nums[1] = 7 -> 7 == 7? SI -> return [0, 1]
```

En la primera vuelta del primer bucle ya se encontro la solucion. El `return` termina el metodo y devuelve `[0, 1]`.

### Resumen del proceso

| Paso | Accion |
|------|--------|
| 1 | Entiende el problema: buscar dos numeros que sumen target |
| 2 | Primer bucle elige un numero como primer elemento del par |
| 3 | Calcula el complemento restando target - numero actual |
| 4 | Segundo bucle busca el complemento en las casillas siguientes |
| 5 | Si lo encuentra, return con ambas posiciones |
| 6 | Si no encuentra nada en toda la lista, return array vacio |

---

## Conceptos Relacionados

- [[16 - Bucles y Control de Flujo]] - Bucles for anidados
- [[03 - Tipos Primitivos y Referencia]] - Arrays de enteros
- [[23 - Metodos - Parametros, Retorno y Return]] - Sentencia return
- [[14 - Arrays Basicos y args]] - Acceso a posiciones del array

---

## Tags

`#leetcode #arrays #bucles #for #hashmap`
