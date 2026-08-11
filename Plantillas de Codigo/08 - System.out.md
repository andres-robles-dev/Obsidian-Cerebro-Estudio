# 08 - System.out

```java
System.out.println(valor);
System.out.print(valor);
System.out.println();

System.out.printf(formato, argumentos);

System.out.println(texto + valor);
```

**Cuando se usa:** para mostrar resultados y depurar en consola.

**Reglas:**
- println agrega salto de linea, print no
- printf: formato con especificadores (%s texto, %d entero, %f decimal, %.2f dos decimales) y %n para salto de linea
- La concatenacion con + convierte automaticamente numeros a texto

---

## Ejemplo de uso

```java
String nombre = "Ana";
int edad = 30;
double precio = 19.99;

System.out.println(nombre);
System.out.print(nombre);
System.out.println();

System.out.printf("%s tiene %d anios y paga %.2f%n", nombre, edad, precio);

System.out.println("Hola " + nombre + " (" + edad + ")");
```

**Ver tema:** [[11 - System.out y Concatenacion]]