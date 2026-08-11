# 10 - String Metodos

```java
String nombreVariable = valor;

int longitud = nombreVariable.length();
String textoMayusculas = nombreVariable.toUpperCase();
String textoMinusculas = nombreVariable.toLowerCase();
String textoSinEspaciosExtremos = nombreVariable.trim();
String textoRecortado = nombreVariable.substring(indiceInicio, indiceFin);
String textoReemplazado = nombreVariable.replace(antiguo, nuevo);

boolean contiene = nombreVariable.contains(valor);
boolean empiezaCon = nombreVariable.startsWith(valor);
boolean terminaCon = nombreVariable.endsWith(valor);

String[] partes = nombreVariable.split(separador);
int indice = nombreVariable.indexOf(valor);
```

**Cuando se usa:** para manipular texto: validar entradas, formatear, buscar, dividir.

**Reglas:**
- String es INMUTABLE: cada metodo devuelve una NUEVA String, no modifica la original
- Las String se comparan con equals (o equalsIgnoreCase), NUNCA con ==
- trim() quita espacios de los extremos, no los internos
- substring con dos indices: el inicio se incluye, el fin se EXCLUYE
- split devuelve un array

---

## Ejemplo de uso

```java
String texto = "  Hola Mundo Java  ";

int longitud = texto.length();
String mayusculas = texto.toUpperCase();
String sinEspacios = texto.trim();
String recortado = texto.substring(5, 10);
String reemplazado = texto.replace("o", "0");

boolean contiene = texto.contains("Mundo");
boolean termina = texto.endsWith("Java");

String[] palabras = texto.trim().split(" ");
int indice = texto.indexOf("Mundo");
```

**Ver tema:** [[03 - Tipos Primitivos y Referencia]]