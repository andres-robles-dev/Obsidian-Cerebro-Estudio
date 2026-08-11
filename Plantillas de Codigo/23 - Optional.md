# 23 - Optional

```java
public Optional<TipoDato> nombreMetodo(TipoDato nombreParametro) {
    if (condicionDeAusencia) {
        return Optional.empty();
    }
    return Optional.of(valor);
}

Optional<TipoDato> nombreVariable = nombreMetodo(argumentos);

nombreVariable.ifPresent(elemento -> codigoConElValor);

TipoDato valorConDefecto = nombreVariable.orElse(valorPorDefecto);

TipoDato valorExigido = nombreVariable.orElseThrow(() -> new NombreExcepcionException("mensaje de error"));
```

**Cuando se usa:** como retorno de metodos que pueden "no tener" un resultado (busquedas, configuraciones opcionales). Evita el null silencioso.

**Reglas:**
- Representa AUSENCIA, no errores: si es un error de verdad, lanza excepcion
- NUNCA uses get() a ciegas (lanza NoSuchElementException si esta vacio)
- No lo uses como parametro ni campo: solo como retorno
- Optional.of(null) lanza: usa ofNullable si puede llegar null
- Prefiere orElseThrow cuando la ausencia es inaceptable

---

## Ejemplo de uso

```java
public Optional<String> buscarNombre(long id) {
    if (id < 1) {
        return Optional.empty();
    }
    return Optional.of("Ana");
}

Optional<String> nombre = buscarNombre(1);

nombre.ifPresent(n -> System.out.println("Hola " + n));

String valor = nombre.orElse("Sin nombre");

String resultado = nombre.orElseThrow(
    () -> new IllegalStateException("El usuario no existe")
);

String mayuscula = nombre.map(String::toUpperCase).orElse("---");
```

**Ver temas:** [[23 - Metodos - Parametros, Retorno y Return]] - [[41 - Optional en Profundidad]]