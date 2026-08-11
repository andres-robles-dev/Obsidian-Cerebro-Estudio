# 05 - ArrayList

```java
import java.util.ArrayList;
import java.util.List;

public class EjemploLista {
    public static void main(String[] args) {
        // Declarar: siempre con la interfaz List
        List<String> nombres = new ArrayList<>();

        // Agregar
        nombres.add("Ana");
        nombres.add("Luis");
        nombres.add(0, "Primero");     // inserta en posicion

        // Leer
        String primero = nombres.get(0);
        int total = nombres.size();

        // Quitar
        nombres.remove("Luis");        // quita por valor
        nombres.remove(0);             // quita por indice

        // Recorrer
        for (String nombre : nombres) {
            System.out.println(nombre);
        }
    }
}
```

**Cuando se usa:** siempre que necesites una coleccion dinamica que crece y decrece (lista de usuarios, tareas, resultados).

**Reglas:**
- Declara con `List<T>` (interfaz), instancia con `new ArrayList<>()`
- El tipo va en los `<>` (generics): solo guarda objetos, NO primitivos
- `remove` compara con `equals` (funciona con String, Integer, records...)
- Los indices van de 0 a `size()-1`; un indice invalido lanza `IndexOutOfBoundsException`

**Ver temas:** [[15 - Listas y ArrayList]] - [[24 - ArrayList - Metodos Avanzados]]
