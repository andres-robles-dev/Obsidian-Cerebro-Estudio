**TENGO → NECESITO → MÉTODO → ¿QUÉ DEVUELVE? → SIGUIENTE**

Cuando programes:

1. **¿Qué tengo ahora?**
2. **¿Qué necesito hacer con eso?**
3. **¿Qué método hace esa operación?**
4. **¿Qué devuelve ese método?**
5. **Ahora ese resultado es mi nuevo “TENGO”.**

La idea principal es:

> **No tengo que saber el código entero. Tengo que saber cuál es el siguiente paso y qué herramienta de Java me permite hacerlo.**

### 1. Empieza por el resultado

Pregúntate:

> **¿Qué tiene que devolver mi método?**

Eso te ayuda a saber el tipo de retorno.

---

### 2. Mira qué tienes

Después:

> **¿Qué tipo de dato tengo ahora mismo?**

Puede ser un `String`, un objeto, una `List`, un `Stream`, un `Optional`, etc.

---

### 3. Decide qué necesitas hacer

Pregúntate:

> **¿Qué operación necesito hacer con ese dato?**

Por ejemplo:

- comparar
- buscar
- filtrar
- recorrer
- transformar
- ordenar
- obtener uno
- obtener varios

---

### 4. Busca la herramienta adecuada

Ahora piensa:

> **¿Qué método o herramienta de este tipo me permite hacer esa operación?**

No pienses:

> "¿Qué código debería escribir?"

Piensa:

> "Tengo un `Stream` y necesito filtrar → ¿qué método de `Stream` hace eso?"

Ahí aparece `filter()`.

---

### 5. Mira qué necesita el método

Una vez elegido el método:

> **¿Qué necesita recibir?**

Puede necesitar:

- ningún dato
- un valor
- varios valores
- un objeto
- una expresión lambda

Entonces construyes **solo esa parte**.

---

### 6. Mira qué devuelve

Esta es una de las preguntas más importantes:

> **¿Qué me devuelve lo que acabo de ejecutar?**

Porque ese resultado será tu nuevo punto de partida.

Por ejemplo:

```
Tengo X
   ↓
uso método
   ↓
obtengo Y
   ↓
¿qué puedo hacer con Y?
   ↓
uso otro método
```

Así es como se construyen las cadenas de métodos.

---

## La plantilla completa

Cuando estés programando por tu cuenta:

```
1. ¿Qué quiero conseguir?
        ↓
2. ¿Qué tengo?
        ↓
3. ¿Qué necesito hacer?
        ↓
4. ¿Qué método/herramienta hace eso?
        ↓
5. ¿Qué necesita ese método?
        ↓
6. Lo ejecuto
        ↓
7. ¿Qué devuelve?
        ↓
8. Ahora, ¿qué tengo?
        ↓
9. ¿Qué necesito hacer después?
        ↓
10. Repito
```

### La frase que quiero que recuerdes

> **TIPO QUE TENGO → OPERACIÓN QUE NECESITO → MÉTODO → ARGUMENTOS → RESULTADO → SIGUIENTE PASO**

Y cuando te bloquees:

> **"¿Qué tengo ahora mismo y qué quiero conseguir a partir de esto?"**

Esa pregunta te devuelve al camino.