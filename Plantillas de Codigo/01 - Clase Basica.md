# 01 - Clase Basica

```java
public class Usuario {
    private String nombre;
    private int edad;

    public Usuario(String nombre, int edad) {
        this.nombre = nombre;
        this.edad = edad;
    }

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public int getEdad() {
        return edad;
    }

    public void setEdad(int edad) {
        this.edad = edad;
    }

    @Override
    public String toString() {
        return "Usuario{nombre='" + nombre + "', edad=" + edad + "}";
    }
}
```

**Cuando se usa:** para crear cualquier objeto del dominio (Usuario, Producto, Pedido...). Es el esqueleto base de toda clase con estado.

**Reglas:**
- La clase `public` debe llamarse igual que el archivo (`.java`)
- Nombre en PascalCase (Usuario, Producto)
- Los campos van `private` (encapsulamiento)
- El estado se protege con getters/setters
- Sobrescribe `toString()` para ver el objeto en prints

**Ver temas:** [[01 - Clases y Estructura Basica]] - [[21 - Getters y Setters]]
