---
tags: [java, fundamentos, indice, mapa-mental]
---

# Índice Mapa Mental - Java Fundamentos

> **Vault de estudio**: Cada archivo es una nota atómica enlazada. Usa `[[wiki-links]]` para navegar.

---

## Mapa Mental Visual (Mermaid)

```mermaid
mindmap
  root((Java Fundamentos))
    Clases_y_Estructura
      Clase
      Archivo_.java
      public_class
    Punto_de_Entrada_main
      static_void_main
      String[]_args
      JVM
    Tipos_de_Dato
      Primitivos
        int_double_boolean_char_long
      Referencia
        String_Arrays_Objetos
    Variables_y_Literales
      Declaracion_Inicializacion
      Literales_sufijos
      Scope
    Modificadores_de_Acceso
      public_private_protected_default
      Encapsulamiento
    Atributos_y_Campos
      Variables_de_instancia
      Estado_del_objeto
    Constructores_y_this
      Inicializacion
      this_parametros
      Sobrecarga
    Instanciacion_y_new
      Heap_Memoria
      Referencia
      Constructor_call
    Multiples_Objetos
      Identidad_distinta
      Estado_independiente
    Metodos_de_Instancia
      void_return_parametros
      this_implícito
    System_out_y_Concatenacion
      Println_Print_Format
      Concatenacion_+
    Operador_Ternario
      Condicion_Verdadero_Falso
      Expresion_no_sentencia
    Static_vs_Instancia
      Clase_vs_Objeto
      Compartido_vs_Propio
      Metodos_estaticos
    Arrays_y_args
      Indice_0_based
      Length_fijo
      Args_main
    Flujo_Ejecucion_JVM
      Carga_Clase
      Main_Thread
      Stack_Heap
    Convenciones_Nombrado
      PascalCase_camelCase_UPPER_SNAKE
      Clases_Metodos_Variables_Constantes
    Separacion_Responsabilidades
      Modelo_Vista_Controlador_basico
      Cohesion_Acoplamiento
```

---

## Mapa Mental Texto (Fallback sin plugin)

```
Java Fundamentos
├── 01 Clases y Estructura Básica
│   ├── Definición de clase
│   ├── Archivo .java = una clase pública
│   └── public class
├── 02 Punto de Entrada main
│   ├── static void main(String[] args)
│   ├── Firma obligatoria
│   └── JVM la busca al arrancar
├── 03 Tipos Primitivos y Referencia
│   ├── Primitivos: int, double, boolean, char, long, byte, short, float
│   ├── Referencia: String, Arrays, Objetos
│   └── Valor vs Referencia
├── 04 Variables y Literales
│   ├── Declaración + Inicialización
│   ├── Literales y sufijos (L, f, ', ")
│   └── Scope / ámbito
├── 05 Modificadores de Acceso
│   ├── public, private, protected, default
│   ├── Encapsulamiento
│   └── Getters/Setters
├── 06 Atributos y Campos
│   ├── Variables de instancia
│   ├── Estado del objeto
│   └── Valores por defecto
├── 07 Constructores y this
│   ├── Inicialización obligatoria
│   ├── this. para desambiguar
│   └── Sobrecarga de constructores
├── 08 Instanciación y new
│   ├── Memoria Heap
│   ├── Referencia en Stack
│   └── Llamada al constructor
├── 09 Múltiples Objetos e Identidad
│   ├── Cada new = objeto distinto
│   ├── Identidad (==) vs Igualdad (.equals)
│   └── Estado independiente
├── 10 Métodos de Instancia
│   ├── void / return / parámetros
│   ├── this implícito
│   └── Sobrecarga de métodos
├── 11 System.out y Concatenación
│   ├── println / print / printf
│   ├── Concatenación con +
│   └── Conversión automática a String
├── 12 Operador Ternario
│   ├── condición ? siVerdadero : siFalso
│   ├── Es expresión (devuelve valor)
│   └── No es sustituto de if completo
├── 13 Static vs Instancia
│   ├── static = pertenece a la clase
│   ├── Instancia = pertenece al objeto
│   ├── Compartido vs Propio
│   └── Métodos utilitarios
├── 14 Arrays Básicos y args
│   ├── Índice 0-based
│   ├── Length fijo
│   ├── String[] args en main
│   └── Recorrido con for
├── 15 Flujo de Ejecución JVM
│   ├── Carga de clase → Main → Stack frames
│   ├── Heap para objetos
│   └── Garbage Collector
├── 16 Convenciones de Nombrado
│   ├── PascalCase: Clases
│   ├── camelCase: Variables, Métodos
│   ├── UPPER_SNAKE_CASE: Constantes
│   └── Nombres descriptivos
└── 17 Separación de Responsabilidades
    ├── Modelo (datos) vs Arranque (main)
    ├── Cohesión alta, acoplamiento bajo
    └── Escalabilidad
```

---

## Orden de Lectura Sugerido (Progresivo)

### Fase 1: Fundamentos Sintácticos
1. `[[01 - Clases y Estructura Basica]]`
2. `[[02 - Punto de Entrada main]]`
3. `[[03 - Tipos Primitivos y Referencia]]`
4. `[[04 - Variables y Literales]]`

### Fase 2: Programación Orientada a Objetos Básica
5. `[[05 - Modificadores de Acceso]]`
6. `[[06 - Atributos y Campos]]`
7. `[[07 - Constructores y this]]`
8. `[[08 - Instanciacion y new]]`
9. `[[09 - Multiples Objetos e Identidad]]`

### Fase 3: Comportamiento y Flujo
10. `[[10 - Metodos de Instancia]]`
11. `[[11 - System.out y Concatenacion]]`
12. `[[12 - Operador Ternario]]`
13. `[[13 - Static vs Instancia]]`

### Fase 4: Estructuras de Datos y JVM
14. `[[14 - Arrays Basicos y args]]`
15. `[[15 - Flujo de Ejecucion JVM]]`

### Fase 5: Buenas Prácticas y Arquitectura
16. `[[16 - Convenciones de Nombrado]]`
17. `[[17 - Separacion de Responsabilidades]]`

### Referencia Rápida
- `[[99 - Glosario Rapido]]` — Definiciones alfabéticas + enlaces

---

## Navegación Rápida por Tags

| Tag | Archivos |
|-----|----------|
| `#java #fundamentos #clases` | 01, 06, 07, 08, 09 |
| `#java #fundamentos #tipos` | 03, 04, 14 |
| `#java #fundamentos #metodos` | 07, 10, 12, 13 |
| `#java #fundamentos #jvm` | 02, 08, 15 |
| `#java #fundamentos #buenas-practicas` | 05, 16, 17 |

---

## Cómo Usar Este Vault

1. **Empieza por el índice** → Sigue el orden de lectura
2. **Cada nota es atómica** → Un tema, un archivo
3. **Usa wiki-links** → `[[07 - Constructores y this]]` salta al tema
4. **Busca por tags** → `tag:#constructores` filtra relacionado
5. **Glosario al final** → Dudas terminológicas rápidas

---

> **Tip**: En Obsidian, `Ctrl+Click` (o `Cmd+Click`) en cualquier `[[enlace]]` abre en panel lateral. `Ctrl+O` busca archivos por nombre.