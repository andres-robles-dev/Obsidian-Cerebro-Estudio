---
tags: [java, fundamentos, indice, mapa-mental]
---

# 00 - Indice Mapa Mental - Java Fundamentos

> Cada archivo es una nota atomica con 3 niveles: Junior (basico), Mid (intermedio), Senior (avanzado). Usa [[wiki-links]] para navegar.

---

## Mapa Mental

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
      this_implicito
    System_out_y_Concatenacion
      Println_Print_Format
      Concatenacion_+
    Operador_Ternario
      Condicion_Verdadero_Falso
      Expresion_no_sentencia
    Static_vs_Instancia
      Clase_vs_Objeto
      Compartido_vs_Propio
    Arrays_y_args
      Indice_0_based
      Length_fijo
      Args_main
    Listas_ArrayList
      List_Interface
      ArrayList_Dinamico
      Add_Remove_Get
    Genericos_y_Tipos
      Tipos_parametrizados_T
      List_T_Map_KV
      Optional_T_ejemplo_real
      Clases_genericas
      Metodos_genericos
      Interfaces_genericas
      Bounded_extends
      Wildcards
      PECS
      Type_erasure
      Records_genericos
      Diamond
    Bucles_Control_Flujo
      For_Tradicional
      For_Each
      While_DoWhile
      Break_Continue
    Flujo_Ejecucion_JVM
      Carga_Clase
      Main_Thread
      Stack_Heap
    Convenciones_Nombrado
      PascalCase_camelCase_UPPER_SNAKE
      Clases_Metodos_Variables_Constantes
    Separacion_Responsabilidades
      SRP_Cohesion_Acoplamiento
      Capas_basicas
    Javadoc_Documentacion
      Comentarios_/**
      @param_@return_@throws
    Getters_Setters
      Convencion_JavaBeans
      Copia_defensiva
      Records
    Separacion_Responsabilidades_Avanzada
      SRP_DIP_Tell_Dont_Ask
      Domain_Repository_Service_App
      Inyeccion_constructor
    Metodos_Parametros_Retorno
      Paso_por_valor
      void_return
      Varargs_Sobrecarga_Optional
      Guard_clauses
    ArrayList_Metodos_Avanzados
      removeIf_Iterator
      subList_toArray
      stream_toList
    Paquetes_Organizacion
      Package_Import
      module_info
      Estructura_Maven_Gradle
    Clases_Wrapper_envolventes
      Que_es_Wrapper
      Integer_Long_Double_Wrapper
      Autoboxing_Unboxing
      Cache_Integer
      Inmutabilidad
      Comparaciones_equals_vs
      Conversión_Tipos
      Optional_con_Wrapper
      Streams_y_Wrapper
    Getters_booleanos
      isXxx_convencion
      boolean_vs_Boolean_wrapper
      Methods_utiles_boolean
      Getters_con_logica
      Boolean_en_Colecciones
      Combinaciones_estado
      Predicate_y_Supplier
      Boolean_en_enums
      Conversiones_JSON
      Bitwise_vs_logical
    Switch_expression
      Sintaxis_flecha_vs_tradicional
      Yield_multiple_sentencias
      Pattern_matching
      Sealed_classes_exhaustivo
      Guardas_when
      Nested_switch
      Record_destructuring
      Null_handling
      Excepciones_en_case
      Compilacion_JIT
    Record_y_Value_Objects
      Sintaxis_minima
      Accessors_sin_get
      Compact_constructor
      Inmutabilidad
      Value_Objects_DDD
      Pattern_matching
      Sealed_y_exhaustividad
      copyWith
      Serializacion_segura
      Limitaciones
    List_of_Colecciones_Inmutables
      List_of
      Set_of_Map_of
      copyOf
      vs_Arrays_asList
      Null_y_duplicados
      Inmutabilidad_estructural
      Rendimiento_memoria
      Programacion_defensiva
    Tipos_y_Wrappers_Guia
      Decision_Rapida_30seg
      Tabla_Maestra
      Patrones_uso_real
      Antipatrones
      Migracion_checklist
      Herramientas_modernas
    Manejo_de_Excepciones
      Jerarquia_Throwable
      Error_vs_Exception
      Checked_Unchecked
      Try_Catch_Finally
      Try_with_resources
      Throws_vs_Throw
      Excepciones_personalizadas
      Propagacion_Stack_trace
      Optional_y_Streams
      Anti-patrones
    Optional_y_Null_Safety
      Contenedor_valor_o_vacio
      of_ofNullable_empty
      orElse_orElseGet_orElseThrow
      map_filter_flatMap
      ifPresent_ifPresentOrElse
      Primitivos_OptionalInt
      Optional_y_Streams
      Reglas_API_retorno
      Anti-patrones
      Null_explicito
    Lambda_y_Funcional
      Funcion_anonima
      Sintaxis_flecha
      Interfaces_funcionales
      Predicate_Function_Consumer_Supplier
      Method_references
      Efectivamente_final
      Composicion_andThen
      this_en_lambdas
    Stream_API
      Pipeline
      Fuente_intermedias_terminal
      filter_map_flatMap
      findFirst_Optional
      sorted_distinct_limit
      reduce_toList_count
      Pereza_lazy
      Primitivos_IntStream
      Parallel_streams
    Patron_Catalogo
      Enum_con_datos
      Inmutable
      Lookup_static
      fromCodigo
      Streams_y_busquedas
      equalsIgnoreCase
      Optional_como_retorno
       vs_Map_externo_vs_BD
     Metodos_Consulta_Tell_Dont_Ask
       Query_vs_Command
       Modelo_rico_vs_anemico
       Ley_de_Demeter
       CQS_principio
       Boolean_queries
       Composicion_de_queries
   ```

---

## Orden de Lectura Sugerido

### Fase 1: Fundamentos Sintacticos
1. [[01 - Clases y Estructura Basica]]
2. [[02 - Punto de Entrada main]]
3. [[03 - Tipos Primitivos y Referencia]]
4. [[04 - Variables y Literales]]

### Fase 2: Programacion Orientada a Objetos Basica
5. [[05 - Modificadores de Acceso]]
6. [[06 - Atributos y Campos]]
7. [[07 - Constructores y this]]
8. [[08 - Instanciacion y new]]
9. [[09 - Multiples Objetos e Identidad]]

### Fase 3: Comportamiento y Flujo
10. [[10 - Metodos de Instancia]]
11. [[11 - System.out y Concatenacion]]
12. [[12 - Operador Ternario]]
13. [[13 - Static vs Instancia]]

### Fase 4: Estructuras de Datos y JVM
14. [[14 - Arrays Basicos y args]]
15. [[15 - Listas y ArrayList]]
16. [[16 - Bucles y Control de Flujo]]
17. [[17 - Flujo de Ejecucion JVM]]

### Fase 5: Buenas Practicas y Arquitectura
18. [[18 - Convenciones de Nombrado]]
19. [[19 - Separacion de Responsabilidades]]
20. [[20 - Javadoc y Documentacion]]
21. [[21 - Getters y Setters]]
22. [[22 - Separacion de Responsabilidades]]

### Fase 6: Profundizacion Avanzada
23. [[23 - Metodos - Parametros, Retorno y Return]]
24. [[24 - ArrayList - Metodos Avanzados]]
25. [[25 - Paquetes y Organizacion]]
26. [[26 - Private en Profundidad]]
27. [[27 - Singleton en Profundidad]]
28. [[28 - Static en Profundidad]]
29. [[29 - Scanner en Profundidad]]
30. [[30 - Principio fundamental de Java (y POO)]]
31. [[31 - Curso Completo de .method()]]
32. [[32 - do-while en Profundidad]]
33. [[33 - enum en Profundidad]]
34. [[34 - Clases Wrapper (envolventes)]]
35. [[35 - Getters booleanos]]
36. [[36 - Switch expression]]
37. [[37 - Record (y Value Objects)]]
38. [[38 - List.of y Colecciones Inmutables]]
39. [[39 - Tipos y Wrappers - La Guia Definitiva]]
40. [[40 - Manejo de Excepciones]]
41. [[41 - Optional en Profundidad]]
42. [[42 - Lambda en Profundidad]]
43. [[43 - Stream API en Profundidad]]
44. [[44 - Patron Catalogo Inmutable con Enum]]
45. [[45 - Genericos en Profundidad]]
46. [[46 - Metodos de Consulta y Tell Don't Ask]]

### Referencia Rapida
- [[99 - Glosario Rapido]] - Definiciones con enlaces

### Plantillas de Codigo
> Estructuras listas para copiar, ordenadas de mas usadas a menos. Cada una tiene el codigo, cuando se usa y sus reglas.

- [[01 - Clase Basica]] · [[02 - Punto de Entrada main]] · [[03 - Constructor]] · [[04 - Getters y Setters]] · [[05 - ArrayList]] · [[06 - Bucles]] · [[07 - Scanner]] · [[08 - System.out]] · [[09 - Metodos de Instancia]] · [[10 - String Metodos]] · [[11 - Variables y Literales]] · [[12 - Arrays]] · [[13 - Operador Ternario]] · [[14 - static]] · [[15 - enum Simple]] · [[16 - enum con Constructor y Metodos]] · [[17 - Switch Expression]] · [[18 - Try-Catch-Finally]] · [[19 - Try-With-Resources]] · [[20 - Excepcion Personalizada]] · [[21 - Record]] · [[22 - List.of Set.of Map.of]] · [[23 - Optional]] · [[24 - Singleton]] · [[25 - Wrapper Integer]] · [[26 - Metodos Avanzados (varargs y guard clauses)]] · [[27 - Paquete e Imports]] · [[28 - do-while]] · [[29 - Clase Generica]]

---

## Como Leer Cada Tema

Cada tema tiene 3 niveles de dificultad:

| Nivel | Para quien es | Que contiene |
|-------|--------------|-------------|
| Junior | Nunca has visto el tema | Explicacion simple, 1 ejemplo minimo |
| Mid | Ya sabes lo basico | Profundizacion, ejemplo realista |
| Senior | Quieres saber todo | Java moderno, mejores practicas, rendimiento |

Al final de cada tema encontraras:
- Errores comunes (para evitarlos)
- Buenas practicas (como hacerlo bien)
- Conexiones con otros temas ([[wiki-links]])

---

## Tags por Categoria

| Tag | Archivos |
|-----|----------|
| `#clases` | 01, 06, 07, 08, 09 |
| `#tipos` | 03, 04, 14, 39 |
| `#metodos` | 07, 10, 12, 13, 23 |
| `#jvm` | 02, 08, 17 |
| `#buenas-practicas` | 05, 18, 19, 21, 22 |
| `#colecciones` | 15, 24, 38 |
| `#bucles` | 16 |
| `#javadoc` | 20 |
| `#paquetes` | 25 |
| `#wrapper` | 34, 39 |
| `#getters` | 35 |
| `#switch` | 36 |
| `#record` | 37 |
| `#excepciones` | 40 |
| `#optional` | 41, 23, 31 |
| `#lambda` | 42 |
| `#streams` | 43, 24, 34 |
| `#catalogo` | 44, 33 |
| `#generics` | 45, 15, 41 |
| `#tell-dont-ask` | 46, 22 |

---

## Tags
`#java #fundamentos #indice #mapa-mental`
