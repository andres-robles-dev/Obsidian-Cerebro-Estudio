# 10 - String Metodos

```java
String texto = "  Hola Mundo Java  ";

int longitud = texto.length();               // 18
String mayusculas = texto.toUpperCase();     // HOLA MUNDO JAVA
String minusculas = texto.toLowerCase();     // hola mundo java
String sinEspacios = texto.trim();           // quita espacios de los extremos
String recortado = texto.substring(5, 10);   // "Mundo"
String reemplazado = texto.replace("o", "0");

boolean contiene = texto.contains("Mundo");  // true
boolean empieza = texto.startsWith("Hola");  // false (hay espacios)
boolean termina = texto.endsWith("Java");

String[] palabras = texto.trim().split(" "); // ["Hola","Mundo","Java"]
int indice = texto.indexOf("Mundo");         // posicion o -1 si no existe
```

**Cuando se usa:** para manipular texto: validar entradas, formatear, buscar, dividir.

**Reglas:**
- `String` es INMUTABLE: cada metodo devuelve una NUEVA String, no modifica la original
- Se comparan con `equals` (o `equalsIgnoreCase`), NUNCA con `==`
- `trim()` quita espacios de los extremos, no los internos
- `substring` con 2 indices: inicio incluido, fin EXCLUIDO
- `split` devuelve un array

**Ver tema:** [[03 - Tipos Primitivos y Referencia]]
