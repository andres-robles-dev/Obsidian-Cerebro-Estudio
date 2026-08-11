# 11 - Variables y Literales

```java
byte nombreVariable = valor;
short nombreVariable = valor;
int nombreVariable = valor;
long nombreVariable = valorSufijoL;
float nombreVariable = valorSufijoF;
double nombreVariable = valorDecimal;
boolean nombreVariable = valorVerdaderoOFalso;
char nombreVariable = valorCaracter;

String nombreTexto = valorTexto;

var nombreVariable = valor;

final TipoDato NOMBRE_CONSTANTE = valor;
```

**Cuando se usa:** en toda declaracion de datos: numeros, texto, booleanos, listas.

**Reglas:**
- Sufijos: L para long, f para float (obligatorios)
- Comillas simples para char, dobles para String
- var infiere el tipo: solo en variables locales, no en campos ni parametros
- final crea constante: debe inicializarse y no se reasigna
- Nombres: camelCase para variables, UPPER_SNAKE para constantes

---

## Ejemplo de uso

```java
byte nota = 10;
short temperatura = 30000;
int entero = 42;
long poblacion = 8_000_000L;
float flotante = 2.5f;
double doble = 3.14;
boolean activo = true;
char letra = 'A';

String nombre = "Ana";

var numero = 10;
var lista = new ArrayList<String>();

final double IVA = 0.19;
```

**Ver tema:** [[04 - Variables y Literales]]