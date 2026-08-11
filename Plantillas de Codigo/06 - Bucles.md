# 06 - Bucles

```java
for (TipoDato contador = valorInicial; condicion; incremento) {
    codigoARepetir;
}

for (TipoDato elemento : nombreColeccion) {
    codigoPorCadaElemento;
}

while (condicion) {
    codigoARepetir;
}

do {
    codigoARepetir;
} while (condicion);
```

**Cuando se usa:** para repetir codigo: recorrer listas, procesar entradas, esperar una condicion.

**Reglas:**
- Prefiere for-each (segundo) para recorrer colecciones: mas simple y seguro
- break corta el bucle; continue salta a la siguiente iteracion
- La condicion DEBE poder volverse falsa o habra un bucle infinito
- No modifiques la lista mientras la recorres con for-each

---

## Ejemplo de uso

```java
for (int i = 0; i < 5; i++) {
    System.out.println("Iteracion " + i);
}

for (String nombre : nombres) {
    System.out.println(nombre);
}

int saldo = 100;
while (saldo > 0) {
    saldo -= 30;
}

int intentos = 0;
do {
    System.out.println("Intento " + intentos);
    intentos++;
} while (intentos < 3);
```

**Ver tema:** [[16 - Bucles y Control de Flujo]]