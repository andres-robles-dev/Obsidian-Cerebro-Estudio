# 25 - Wrapper Integer

```java
// Autoboxing: int -> Integer (automatico)
Integer wrapper = 42;
int primitivo = wrapper;              // unboxing (automatico)

// String <-> numero
int parseado = Integer.parseInt("42");        // String -> int
Integer desdeTexto = Integer.valueOf("42");   // String -> Integer

// Comparaciones CORRECTAS
Integer a = Integer.valueOf(200);
Integer b = Integer.valueOf(200);
System.out.println(a.equals(b));      // true  (compara valor)

// Constantes y utilidades
int max = Integer.MAX_VALUE;
int digitos = Integer.toHexString(255);
```

**Cuando se usa:** cuando necesitas un int como OBJETO: guardarlo en ArrayList/List (no aceptan primitivos), parsear texto a numero, o trabajar con null.

**Reglas:**
- Lista `List<Integer>` NUNCA `List<int>`
- Compara wrappers con `equals`, NUNCA con `==` (== compara referencias; la cache solo cubre -128 a 127)
- `parseInt` devuelve int; `valueOf` devuelve Integer (usa la cache)
- `parseInt("abc")` lanza `NumberFormatException`: valida antes o captura
- Hay wrapper para cada primitivo: `Integer`, `Long`, `Double`, `Boolean`, `Character`...

**Ver temas:** [[34 - Clases Wrapper (envolventes)]] - [[39 - Tipos y Wrappers - La Guia Definitiva]]
