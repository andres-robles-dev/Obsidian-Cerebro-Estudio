---
tags: [leetcode, strings, substring, equals, bucles]
---

# 001 - Buscar Subcadena en Cadena

---

## Descripcion del Problema

Dadas dos cadenas `haystack` y `needle`, devuelve el indice de la primera aparicion de `needle` en `haystack`, o `-1` si `needle` no forma parte de `haystack`.

**Ejemplo 1:**
- Entrada: `haystack = "triste pero triste"`, `needle = "triste"`
- Salida: `0`
- Explicacion: `"triste"` aparece en los indices 0 y 6. La primera aparicion esta en el indice 0.

**Ejemplo 2:**
- Entrada: `haystack = "leetcode"`, `needle = "leeto"`
- Salida: `-1`
- Explicacion: `"leeto"` no aparecio en `"leetcode"`, por lo que devolvemos `-1`.

**Restricciones:**
- `1 <= haystack.length, needle.length <= 10^4`
- `haystack` y `needle` constan unicamente de caracteres ingleses en minuscula.

---

## Solucion (Codigo)

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (needle.length() > haystack.length()) {
            return -1;
        }

        for (int inicio = 0; inicio <= haystack.length() - needle.length(); inicio++) {
            String parte = haystack.substring(inicio, inicio + needle.length());
            if (parte.equals(needle)) {
                return inicio;
            }
        }

        return -1;
    }
}
```

---

## Mi Intento

Me costo entender que era cadena, pero por ahora comprendo que el ejercicio me pide comparar 2 cadenas de texto que estan dentro de unas variables nombradas `needle` y `haystack`. Se busca la primera aparicion de una dentro de la otra, es decir, buscar si hay una palabra que coincida con otra dentro de un texto mas largo.

Primero descartamos algo: si `needle` es mas largo que `haystack`, es fisicamente imposible que quepa dentro, asi que no tiene sentido buscar. Por eso creamos una condicion que retorna `-1` directamente.

Si esa condicion no se cumple, pasamos al bucle `for`. Le decimos que empiece en la posicion 0 y que avance mientras `inicio` no se pase del punto donde `needle` todavia quepa en `haystack`. Es decir, paramos antes de que no tenga sentido seguir comparando.

Dentro del bucle creamos una variable llamada `parte` de tipo `String`. En esta variable guardamos el corte que hacemos con `.substring(inicio, inicio + needle.length())`, es decir, recortamos exactamente el mismo largo que `needle` desde la posicion actual.

Luego comparamos si `parte` tiene el mismo contenido que `needle` usando `.equals()`. Si coinciden, devolvemos `inicio` porque ya encontramos la primera aparicion.

Si el bucle termina y no se encontro nada, devolvemos `-1`.

---

## Explicacion

### Paso 1: Entender el problema

Un programador primero se hace preguntas:
- Que me dan? Dos cadenas de texto (`haystack` y `needle`)
- Que me piden? Encontrar en que posicion aparece `needle` dentro de `haystack`
- Que devuelvo? Un numero (el indice) o `-1` si no existe

> Regla de oro: Antes de escribir UNA sola linea de codigo, entiende perfectamente que se pide.

### Paso 2: Piensa en ejemplos simples (antes de codigo)

```
haystack = "triste pero triste"
needle   = "triste"

Paso a paso:
  Posicion 0: "triste" cabe aqui? Si -> coincide? Si! -> Devuelvo 0
```

```
haystack = "leetcode"
needle   = "leeto"

Paso a paso:
  Posicion 0: "leeto" cabe aqui? Si -> coincide? l==l, e==e, e!=t -> No
  Posicion 1: "leeto" cabe aqui? Si -> coincide? e!=l -> No
  ... y asi sucesivamente hasta que no quepa mas -> No encontre nada -> Devuelvo -1
```

> Clave: Estas simulando lo que harias SI TU mismo buscaras la palabra manualmente.

### Paso 3: Descompone el problema en partes pequenas

Un programador divide el problema grande en preguntas pequenas:

1. **Como recorro `haystack` letra por letra?** Con un bucle `for`
2. **Como verifico si `needle` cabe en esa posicion?** Comparando longitudes
3. **Como comparo caracter por caracter?** Con `substring()` y `equals()`
4. **Cuando se que encontre la palabra?** Cuando todos los caracteres coinciden
5. **Que hago si llego al final sin encontrar nada?** Devuelvo `-1`

### Paso 4: Por que verificar si needle es mas largo que haystack?

Si `needle` mide 10 caracteres y `haystack` solo 5, es **fisicamente imposible** que `needle` quepa dentro. Es como intentar meter un palo de 10 metros en una caja de 5 metros. No tiene sentido buscar, asi que devolvemos `-1` directamente.

```java
if (needle.length() > haystack.length()) {
    return -1;  // No puede caber, no busco
}
```

### Paso 5: Por que el for para en haystack.length() - needle.length()?

Porque si `inicio` avanza demasiado, la subcadena que cortas seria **mas corta** que `needle`, y no tendria sentido comparar.

Ejemplo:
```
haystack = "abc" (largo 3)
needle = "ab" (largo 2)

inicio puede ser 0 o 1 (3-2=1)
Si inicio = 2, solo queda "c" (1 letra) -> no puede ser igual a "ab" (2 letras)
```

### Paso 6: Que hace substring(inicio, inicio + needle.length())?

Corta **exactamente** el mismo largo que `needle` desde la posicion `inicio`:

```
haystack = "triste pero triste"
needle = "triste" (largo 6)

inicio = 0 -> substring(0, 6) -> "triste"
inicio = 7 -> substring(7, 13) -> "pero t"
```

`substring(a, b)` = Recorta texto desde la posicion `a` hasta `b` (sin incluir `b`).

### Paso 7: Que hace .equals()?

**Compara si dos textos son iguales.**

```java
String a = "hola";
String b = "hola";
String c = "mundo";

a.equals(b)  -> true
a.equals(c)  -> false
```

No uses `==` para comparar textos, porque `==` compara si son el **mismo objeto**, no si tienen el **mismo contenido**.

### Resumen del proceso

| Paso | Accion |
|------|--------|
| 1 | Entiende el problema (lee bien que te piden) |
| 2 | Piensa en ejemplos a mano |
| 3 | Divide en partes pequenas |
| 4 | Escribe pseudocodigo |
| 5 | Traduce a codigo real |
| 6 | Verifica con los ejemplos |

---

## Conceptos Relacionados

- [[16 - Bucles y Control de Flujo]] - Bucle for para recorrer la cadena
- [[03 - Tipos Primitivos y Referencia]] - Tipo String
- [[23 - Metodos - Parametros, Retorno y Return]] - Sentencia return
- [[11 - System.out y Concatenacion]] - Operaciones con String

---

## Tags

`#leetcode #strings #substring #equals #bucles`
