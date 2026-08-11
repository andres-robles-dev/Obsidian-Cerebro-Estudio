# 06 - Bucles

```java
// 1. FOR: cuando sabes cuantas veces (o recorres por indice)
for (int i = 0; i < 5; i++) {
    System.out.println("Iteracion " + i);
}

// 2. FOR-EACH: para recorrer colecciones y arrays (el mas usado)
for (String nombre : nombres) {
    System.out.println(nombre);
}

// 3. WHILE: cuando no sabes cuantas veces, solo la condicion
int saldo = 100;
while (saldo > 0) {
    saldo -= 30;
}

// 4. DO-WHILE: al menos una vez (menus, validacion)
int intentos = 0;
do {
    System.out.println("Intento " + intentos);
    intentos++;
} while (intentos < 3);
```

**Cuando se usa:** para repetir codigo: recorrer listas, procesar entradas, esperar una condicion.

**Reglas:**
- Prefiere for-each para recorrer colecciones (mas simple y seguro)
- `break` corta el bucle; `continue` salta a la siguiente iteracion
- Cuidado con bucles infinitos: la condicion DEBE poder volverse falsa
- No modifiques la lista mientras la recorres con for-each (usa `removeIf`)

**Ver tema:** [[16 - Bucles y Control de Flujo]]
