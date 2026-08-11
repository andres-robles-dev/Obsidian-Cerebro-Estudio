# 01 - Clase Basica

```java
public class NombreClase {
    private TipoDato nombreAtributo;
    private TipoDato otroAtributo;

    public NombreClase(TipoDato nombreAtributo, TipoDato otroAtributo) {
        this.nombreAtributo = nombreAtributo;
        this.otroAtributo = otroAtributo;
    }

    public TipoDato getNombreAtributo() {
        return nombreAtributo;
    }

    public void setNombreAtributo(TipoDato nombreAtributo) {
        this.nombreAtributo = nombreAtributo;
    }

    public TipoDato getOtroAtributo() {
        return otroAtributo;
    }

    @Override
    public String toString() {
        return "NombreClase{" +
                "nombreAtributo='" + nombreAtributo + '\'' +
                ", otroAtributo=" + otroAtributo +
                '}';
    }
}
```

**Cuando se usa:** para crear cualquier objeto del dominio. Es el esqueleto base de toda clase con estado: campos private, constructor, getters/setters y toString.

**Reglas:**
- La clase public se llama igual que el archivo y en PascalCase
- Los campos van private (encapsulamiento)
- El estado se protege con getters/setters
- toString() te deja ver el objeto al imprimirlo

---

## Ejemplo de uso

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

**Ver tema:** [[01 - Clases y Estructura Basica]] - [[21 - Getters y Setters]]