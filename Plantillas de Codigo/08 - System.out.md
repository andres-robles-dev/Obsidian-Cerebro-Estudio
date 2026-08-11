# 08 - System.out

```java
String nombre = "Ana";
int edad = 30;
double precio = 19.99;

System.out.println(nombre);              // texto + salto de linea
System.out.print(nombre);                // texto SIN salto de linea
System.out.println();                    // solo un salto de linea

// printf: formato con especificadores
System.out.printf("%s tiene %d anios y paga %.2f%n", nombre, edad, precio);

// Concatenacion con +
System.out.println("Hola " + nombre + " (" + edad + ")");
```

**Cuando se usa:** para mostrar resultados y depurar en consola.

**Reglas:**
- `println` agrega salto de linea, `print` no
- Especificadores de `printf`: `%s` String, `%d` entero, `%f` decimal, `%.2f` 2 decimales
- `%n` es salto de linea portable (mejor que `\n`)
- La concatenacion con `+` convierte automaticamente numeros a String

**Ver tema:** [[11 - System.out y Concatenacion]]
