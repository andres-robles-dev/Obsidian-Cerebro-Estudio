# 04 - Getters y Setters

```java
public class NombreClase {
    private TipoDato nombreAtributo;

    public TipoDato getNombreAtributo() {
        return nombreAtributo;
    }

    public void setNombreAtributo(TipoDato nombreAtributo) {
        this.nombreAtributo = nombreAtributo;
    }

    public boolean isAtributoBooleano() {
        return atributoBooleano;
    }
}
```

**Cuando se usa:** para exponer y modificar el estado de un objeto respetando el encapsulamiento (campos private accesibles solo por metodos).

**Reglas:**
- Convencion JavaBeans: getNombreAtributo() / setNombreAtributo(valor)
- Los booleanos usan isAtributoBooleano() en vez de get
- Un campo final no lleva setter
- Puedes validar dentro del setter (ej: rechazar valores invalidos)

---

## Ejemplo de uso

```java
public class Usuario {
    private String nombre;
    private boolean activo;

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public boolean isActivo() {
        return activo;
    }

    public void setActivo(boolean activo) {
        this.activo = activo;
    }
}
```

**Ver temas:** [[21 - Getters y Setters]] - [[35 - Getters booleanos]]