# 05 - ArrayList

```java
List<TipoDato> nombreColeccion = new ArrayList<>();

nombreColeccion.add(elemento);
nombreColeccion.add(indice, elemento);

TipoDato valorObtenido = nombreColeccion.get(indice);
int cantidad = nombreColeccion.size();

nombreColeccion.remove(elemento);
nombreColeccion.remove(indice);

for (TipoDato elemento : nombreColeccion) {
    System.out.println(elemento);
}
```

**Cuando se usa:** siempre que necesites una coleccion dinamica que crece y decrece (listas de usuarios, tareas, resultados).

**Reglas:**
- Se declara con List (interfaz) y se instancia con new ArrayList<>()
- El tipo va en los <> y solo guarda objetos, no primitivos
- remove compara con equals (funciona con String, Integer, records...)
- Indices de 0 a size()-1; un indice invalido lanza IndexOutOfBoundsException

---

## Ejemplo de uso

```java
List<String> nombres = new ArrayList<>();

nombres.add("Ana");
nombres.add("Luis");

String primero = nombres.get(0);
int total = nombres.size();

nombres.remove("Luis");

for (String nombre : nombres) {
    System.out.println(nombre);
}
```

**Ver temas:** [[15 - Listas y ArrayList]] - [[24 - ArrayList - Metodos Avanzados]]