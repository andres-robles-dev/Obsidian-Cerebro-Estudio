# 04 - Getters y Setters

```java
public class Empleado {
    private String nombre;
    private boolean activo;

    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    // Los booleanos usan is en vez de get:
    public boolean isActivo() {
        return activo;
    }

    public void setActivo(boolean activo) {
        this.activo = activo;
    }
}
```

**Cuando se usa:** para exponer y modificar el estado de un objeto respetando el encapsulamiento (campos `private` accesibles solo por metodos).

**Reglas:**
- Convencion JavaBeans: `getCampo()` / `setCampo(valor)`
- Los `boolean` usan `isCampo()` (no `getCampo()`)
- Solo genera setters si el campo debe cambiar (un campo `final` no lleva setter)
- Puedes validar dentro del setter (ej: rechazar edades negativas)
- Usa getters con logica cuando la informacion se calcula (ej: `getNombreCompleto()`)

**Ver temas:** [[21 - Getters y Setters]] - [[35 - Getters booleanos]]
