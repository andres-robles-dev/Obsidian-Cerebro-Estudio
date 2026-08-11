# 25 - Wrapper Integer

```java
Integer nombreVariable = valorPrimitivo;
TipoDato valorPrimitivo = nombreVariable;

int numeroParseado = Integer.parseInt(texto);
Integer numeroDesdeTexto = Integer.valueOf(texto);

boolean iguales = numeroUno.equals(numeroDos);

int valorMaximo = Integer.MAX_VALUE;
```

**Cuando se usa:** cuando necesitas un int como OBJETO: guardarlo en ArrayList/List (no aceptan primitivos), parsear texto a numero, o trabajar con null.

**Reglas:**
- List<Integer> NUNCA List<int>
- Compara wrappers con equals, NUNCA con == (== compara referencias; la cache solo cubre -128 a 127)
- parseInt devuelve int; valueOf devuelve Integer
- parseInt("texto invalido") lanza NumberFormatException: valida antes o captura
- Hay wrapper para cada primitivo: Integer, Long, Double, Boolean, Character...

---

## Ejemplo de uso

```java
Integer wrapper = 42;
int primitivo = wrapper;

int parseado = Integer.parseInt("42");
Integer desdeTexto = Integer.valueOf("42");

Integer a = Integer.valueOf(200);
Integer b = Integer.valueOf(200);
System.out.println(a.equals(b));      // true (compara valor)

int max = Integer.MAX_VALUE;
```

**Ver temas:** [[34 - Clases Wrapper (envolventes)]] - [[39 - Tipos y Wrappers - La Guia Definitiva]]