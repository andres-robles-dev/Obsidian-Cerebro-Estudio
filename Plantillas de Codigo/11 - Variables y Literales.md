# 11 - Variables y Literales

```java
// Primitivos: 8 tipos
byte b = 100;
short s = 30000;
int entero = 42;
long largo = 1_000_000L;          // sufijo L obligatorio para long
float flotante = 2.5f;            // sufijo f obligatorio para float
double doble = 3.14;              // decimal por defecto
boolean activo = true;
char letra = 'A';

// Referencia
String nombre = "Ana";

// Inferencia de tipo (Java 10+): solo para variables locales
var numero = 10;                  // se infiere int
var lista = new ArrayList<String>();

// Constantes
final double IVA = 0.19;          // no se puede reasignar

// Longitud y separador de miles (Java 7+)
int poblacion = 8_000_000;
```

**Cuando se usa:** en toda declaracion de datos: numeros, texto, booleanos, listas.

**Reglas:**
- Sufijos: `L` (long), `f` (float), `d` (double, opcional)
- Comillas simples para `char`, dobles para `String`
- El `_` en numeros largos solo mejora la lectura (1_000_000)
- `var` infiere el tipo pero no puede usarse en campos ni parametros
- `final` crea constante: debe inicializarse y no se reasigna
- Nombres: camelCase (variables), UPPER_SNAKE (constantes)

**Ver tema:** [[04 - Variables y Literales]]
