# Professional English for Software Engineers
# Documentación del Sistema

## 1. Propósito

Este documento explica cómo utilizar, mantener y continuar el sistema de aprendizaje:

**Professional English for Software Engineers**

El sistema está diseñado para entrenar inglés profesional orientado al trabajo como software engineer.

El profesor puede utilizarse en:

- ChatGPT normal;
- ChatGPT por voz;
- chats con archivos adjuntos;
- agentes de IA con acceso al sistema de archivos;
- OpenCode u otros entornos similares.

El método de aprendizaje es el mismo en todos los entornos.

La diferencia entre entornos está únicamente en las capacidades de acceso a los archivos.

---

# 2. Ubicación del proyecto

El proyecto está ubicado en:

```text
C:\Users\arobl\Desktop\Programacion estudio\English for Developers
```

Esta carpeta es la raíz del sistema de aprendizaje de inglés.

---

# 3. Relación con el cerebro principal

El proyecto está dentro de:

```text
C:\Users\arobl\Desktop\Programacion estudio
```

pero constituye una red de conocimiento independiente.

El cerebro principal contiene múltiples áreas, entre ellas programación e inglés.

La red de conocimientos de inglés no debe mezclarse con la red de programación.

Los `[[wikilinks]]` del sistema de inglés deben enlazar únicamente a conocimientos pertenecientes al proyecto:

```text
Professional English for Software Engineers
```

No se deben crear conexiones hacia:

- LeetCode;
- Pensamiento Programador;
- Plantillas de Codigo;
- otras notas de programación;
- otras áreas del cerebro principal.

---

# 4. Estructura del proyecto

La estructura mínima prevista es:

```text
English for Developers/
│
├── 00 - Course Dashboard.md
├── 01 - Documentación del Sistema.md
└── Sessions/
```

A medida que el sistema evolucione pueden aparecer otras notas o carpetas, pero no deben crearse estructuras innecesarias.

La estructura debe crecer según las necesidades reales del aprendizaje.

---

# 5. Archivos principales

## 5.1 `00 - Course Dashboard.md`

Es el estado actual del curso.

Contiene información resumida sobre:

- etapa actual;
- objetivo actual;
- fortalezas;
- dificultades;
- errores recurrentes;
- patrones activos;
- habilidades;
- conocimiento relevante;
- última sesión;
- próximo objetivo;
- progreso.

No contiene la transcripción completa de las clases.

Su función es permitir que una IA entienda rápidamente:

> "¿Dónde está actualmente el alumno?"

---

## 5.2 `01 - Documentación del Sistema.md`

Es este documento.

Explica:

- cómo funciona el sistema;
- estructura de archivos;
- comandos;
- diferencias entre entornos;
- comportamiento esperado;
- flujo de trabajo;
- reglas de mantenimiento.

No forma parte del contenido pedagógico.

---

## 5.3 `Sessions/`

Contiene las sesiones terminadas.

Ejemplo:

```text
Sessions/
├── Session 01 - Initial Production.md
├── Session 02 - Talking About Work.md
├── Session 03 - Explaining Problems.md
└── ...
```

Cada sesión representa una clase real.

---

# 6. Prompts necesarios

El sistema utiliza dos prompts principales.

## 6.1 Prompt Maestro - Profesor de Inglés

Archivo externo al proyecto de clases si se decide mantenerlo en:

```text
Programacion estudio/
Prompts/
Profesor - Método Estudio/
```

Este prompt contiene:

- filosofía de enseñanza;
- comportamiento del profesor;
- método;
- corrección;
- sesiones;
- voz;
- texto;
- comandos universales;
- manejo del Dashboard;
- reglas de `[[wikilinks]]`;
- generación de las clases.

Este prompt es el núcleo pedagógico.

---

## 6.2 Prompt de Integración - Agente de Obsidian

Este prompt complementa al Prompt Maestro.

Contiene reglas específicas para agentes que pueden:

- leer archivos;
- buscar archivos;
- crear archivos;
- modificar archivos.

No sustituye al Prompt Maestro.

Su función es proporcionar integración con el sistema de archivos.

---

# 7. Diagnóstico inicial

El diagnóstico inicial ya fue realizado.

No debe repetirse automáticamente.

El diagnóstico determinó principalmente:

- lectura funcional;
- pronunciación funcional;
- dificultad principal de producción espontánea;
- dificultad para formar frases desde cero;
- conocimiento pasivo superior al conocimiento producido espontáneamente;
- objetivo de comunicación profesional;
- contexto de software development.

El sistema debe utilizar esta información como punto de partida.

Una evaluación posterior solo debe realizarse mediante:

`Assess`

---

# 8. Comandos

Los comandos representan acciones del sistema.

Un comando describe:

**qué quiere hacer el alumno**

y no:

**qué herramienta está utilizando.**

Por eso no existen comandos duplicados para ChatGPT y agentes.

---

# 9. Comandos universales

Los siguientes comandos funcionan tanto en ChatGPT como en agentes:

```text
Run-FirstClass
Run-Session
Review
Assess
End-Session
```

Su significado es el mismo en todos los entornos.

---

# 10. `Run-FirstClass`

## Función

Iniciar la primera clase real del curso.

## Importante

El diagnóstico inicial ya existe.

Este comando:

- NO repite el test;
- NO vuelve a evaluar desde cero;
- NO comienza con inglés escolar básico.

Debe utilizar el perfil inicial existente y comenzar el entrenamiento de producción.

## Uso

```text
Run-FirstClass
```

## Resultado esperado

El profesor:

1. utiliza el diagnóstico inicial;
2. establece el primer objetivo;
3. inicia una sesión real;
4. al finalizar genera la primera sesión;
5. crea o actualiza el Dashboard.

---

# 11. `Run-Session`

## Función

Iniciar una nueva sesión normal.

Debe continuar desde el estado existente.

## Uso

```text
Run-Session
```

## El profesor debe

1. revisar el estado disponible;
2. revisar la última sesión;
3. recuperar conocimientos relevantes;
4. identificar errores recurrentes;
5. decidir el objetivo del día;
6. realizar la sesión;
7. adaptar la dificultad;
8. evitar comenzar desde cero.

---

# 12. `Review`

## Función

Realizar una sesión de recuperación y consolidación.

## Uso

```text
Review
```

## Objetivo

Recuperar activamente:

- estructuras;
- vocabulario;
- habilidades;
- errores;
- conocimientos anteriores.

No debe limitarse a mostrar apuntes.

Debe hacer que el alumno recuerde y produzca.

---

# 13. `Assess`

## Función

Evaluar el progreso actual.

## Uso

```text
Assess
```

## Debe evaluar

Según corresponda:

- producción espontánea;
- fluidez;
- precisión;
- vocabulario;
- pronunciación;
- comprensión;
- naturalidad;
- comunicación profesional;
- capacidad de explicar ideas;
- circumlocution.

No debe repetir automáticamente el diagnóstico inicial.

Debe comparar el estado actual con el progreso anterior.

---

# 14. `End-Session`

## Función

Cerrar una sesión y guardar la memoria del curso.

## Uso

```text
End-Session
```

## Este comando siempre implica

- evaluación de la sesión;
- generación del resumen;
- generación de la clase `.md`;
- actualización del Dashboard;
- actualización del estado;
- identificación de conexiones;
- creación de `[[wikilinks]]` cuando corresponda.

---

# 15. Archivos generados por `End-Session`

Como mínimo:

```text
Sessions/
└── Session XX - [Título].md
```

y:

```text
00 - Course Dashboard.md
```

Si existe una razón pedagógica real para crear una nota independiente, puede generarse adicionalmente.

No debe crearse una nota por cada palabra.

---

# 16. Comportamiento de `End-Session` en ChatGPT

ChatGPT normalmente no tiene acceso directo a:

```text
C:\Users\arobl\Desktop\Programacion estudio\English for Developers/
```

Por lo tanto, al ejecutar:

```text
End-Session
```

debe generar el contenido completo de:

```text
Session XX - [Título].md
```

y:

```text
00 - Course Dashboard.md
```

El alumno debe guardar o reemplazar manualmente estos archivos en Obsidian.

La IA no debe afirmar que ha guardado los archivos si no tiene acceso real al sistema de archivos.

---

# 17. Comportamiento de `End-Session` en un agente

Si el agente tiene acceso real al sistema de archivos, al ejecutar:

```text
End-Session
```

debe:

1. generar la sesión;
2. guardarla en `Sessions/`;
3. actualizar `00 - Course Dashboard.md`;
4. crear notas adicionales cuando corresponda;
5. comprobar los enlaces;
6. informar de los archivos creados o modificados.

---

# 18. Comandos exclusivos de agentes

Estos comandos requieren acceso al sistema de archivos:

```text
Find-LastClass
Load-CourseState
Sync-Course
```

No deben duplicarse para ChatGPT.

---

# 19. `Find-LastClass`

## Función

Buscar automáticamente la última clase existente.

## Uso

```text
Find-LastClass
```

## Ubicación principal

```text
C:\Users\arobl\Desktop\Programacion estudio\English for Developers\Sessions
```

## Resultado

Debe:

1. localizar las sesiones;
2. determinar la última;
3. abrirla;
4. identificarla;
5. informar al usuario.

No debe comenzar una clase automáticamente.

Para comenzar se utiliza:

```text
Run-Session
```

---

# 20. `Load-CourseState`

## Función

Cargar el estado actual completo del curso.

## Uso

```text
Load-CourseState
```

## Debe revisar

```text
00 - Course Dashboard.md
```

y, cuando sea necesario:

- última sesión;
- sesiones relacionadas;
- errores;
- objetivos;
- patrones;
- conocimientos relevantes.

Después puede informar brevemente del estado.

No debe modificar archivos simplemente por cargar el estado.

---

# 21. `Sync-Course`

## Función

Comprobar que el proyecto de inglés sea coherente.

## Uso

```text
Sync-Course
```

Debe comprobar:

- Dashboard;
- sesiones;
- enlaces;
- duplicados evidentes;
- coherencia del estado.

Puede corregir problemas menores y claramente seguros.

No debe:

- borrar información;
- reorganizar todo;
- renombrar archivos masivamente;
- modificar otras áreas del cerebro;
- reescribir sesiones antiguas innecesariamente.

---

# 22. Flujo recomendado: primera clase

## En cualquier entorno

Usar:

```text
Run-FirstClass
```

El profesor utiliza el diagnóstico existente.

Después de la clase:

```text
End-Session
```

Resultado:

```text
Session 01 - [Título].md
00 - Course Dashboard.md
```

---

# 23. Flujo recomendado: ChatGPT

Cuando ya existe historial:

1. adjuntar `00 - Course Dashboard.md`;
2. adjuntar la última sesión cuando sea necesario;
3. proporcionar el Prompt Maestro;
4. ejecutar:

```text
Run-Session
```

Al terminar:

```text
End-Session
```

Guardar los `.md` generados dentro del proyecto.

---

# 24. Flujo recomendado: agente

El agente puede trabajar directamente con los archivos.

Flujo recomendado:

```text
Find-LastClass
```

↓

```text
Load-CourseState
```

↓

```text
Run-Session
```

↓

sesión de estudio

↓

```text
End-Session
```

No es obligatorio ejecutar siempre `Find-LastClass` y `Load-CourseState` por separado si el agente ya dispone del estado correctamente cargado.

Son herramientas para facilitar la interacción.

---

# 25. Qué hacer al cambiar de entorno

El curso no pertenece a una conversación concreta.

Ejemplo:

### Día 1

ChatGPT.

Se genera:

```text
Session 01.md
00 - Course Dashboard.md
```

Se guardan en Obsidian.

### Día 2

OpenCode.

El agente lee:

```text
00 - Course Dashboard.md
Session 01.md
```

y continúa.

### Día 3

ChatGPT.

Se proporcionan nuevamente los archivos necesarios.

El curso continúa.

La memoria del curso está en Markdown, no en la conversación.

---

# 26. Regla de archivos obligatorios

El archivo más importante para continuar el curso es:

```text
00 - Course Dashboard.md
```

La última sesión proporciona contexto adicional.

Si solo se dispone del Dashboard, puede continuar.

Si el Dashboard no existe pero existen sesiones, el agente puede reconstruir el estado cuando tenga acceso a los archivos.

Si no existe ni Dashboard ni historial y el usuario quiere iniciar:

```text
Run-FirstClass
```

---

# 27. Red de conocimiento

La carpeta de inglés crea una red de conocimiento independiente mediante:

```text
[[wikilinks]]
```

Ejemplo:

```text
[[Talking About My Work]]
        ↓
[[Talking About Progress]]
        ↓
[[Explaining Problems]]
        ↓
[[Explaining Solutions]]
```

Las conexiones deben ser naturales y pedagógicas.

No deben crearse solamente para aumentar la cantidad de enlaces.

---

# 28. Regla de independencia

No crear:

```text
[[Debugging]]
[[Git]]
[[API]]
[[LeetCode]]
```

si esas notas pertenecen al cerebro de programación externo al proyecto de inglés.

Una clase puede hablar sobre debugging en inglés.

Eso no implica crear una conexión con la nota técnica de debugging.

El proyecto de inglés mantiene su propia red.

---

# 29. Filosofía de crecimiento

No crear estructuras ni notas antes de necesitarlas.

La red debe crecer orgánicamente.

Si un concepto aparece una vez y no tiene importancia independiente, puede permanecer dentro de la sesión.

Si se convierte en un conocimiento recurrente, útil o conectado con otros conocimientos, entonces puede convertirse en una nota propia.

---

# 30. Seguridad para agentes

Los agentes deben ser conservadores.

No eliminar archivos.

No modificar otras áreas de `Programacion estudio`.

No cambiar masivamente nombres.

No reorganizar el proyecto sin autorización.

No inventar información.

No afirmar que una operación fue realizada si no se ejecutó realmente.

---

# 31. Regla de honestidad

Si el entorno no tiene acceso a los archivos:

no debe afirmar que encontró una clase.

Si no puede escribir archivos:

no debe afirmar que actualizó el Dashboard.

En ese caso debe proporcionar el contenido completo para que el usuario lo guarde.

---

# 32. Ejemplos rápidos de uso

## Primera vez

```text
Run-FirstClass
```

## Nueva clase

```text
Run-Session
```

## Repasar

```text
Review
```

## Evaluar progreso

```text
Assess
```

## Terminar clase

```text
End-Session
```

## Buscar última clase desde un agente

```text
Find-LastClass
```

## Cargar estado desde un agente

```text
Load-CourseState
```

## Comprobar coherencia desde un agente

```text
Sync-Course
```

---

# 33. Comandos por entorno

## ChatGPT / Voz / Chat normal

Disponibles:

```text
Run-FirstClass
Run-Session
Review
Assess
End-Session
```

Necesita archivos adjuntos cuando el contexto anterior no está disponible.

---

## Agente con acceso a archivos

Disponibles:

```text
Run-FirstClass
Run-Session
Review
Assess
End-Session
Find-LastClass
Load-CourseState
Sync-Course
```

Puede leer y escribir directamente dentro del proyecto.

---

# 34. Regla principal del sistema

No debes memorizar el funcionamiento interno de este sistema.

Simplemente utiliza los comandos.

La IA debe interpretar el comando, comprobar el contexto disponible y ejecutar la operación correspondiente.

---

# 35. Flujo mental más sencillo

La forma más simple de recordar el sistema es:

```text
¿Primera clase?
→ Run-FirstClass

¿Quiero estudiar?
→ Run-Session

¿Quiero repasar?
→ Review

¿Quiero medir progreso?
→ Assess

¿Terminé?
→ End-Session

¿Soy un agente y necesito encontrar la última?
→ Find-LastClass

¿Soy un agente y necesito cargar el estado?
→ Load-CourseState

¿Soy un agente y quiero comprobar el proyecto?
→ Sync-Course
```

---

# 36. Resultado esperado

El sistema debe permitir que el alumno cambie de entorno sin perder continuidad.

El conocimiento permanece en:

```text
English for Developers//
```

La conversación es solo la interfaz.

La IA puede cambiar.

El modelo puede cambiar.

La herramienta puede cambiar.

El curso debe continuar.

---

# 37. Principio final

El sistema debe comportarse como un profesor con una memoria externa organizada, pero la memoria no pertenece al modelo de IA.

Pertenece al proyecto de Obsidian.

El objetivo es que el aprendizaje sea:

- continuo;
- acumulativo;
- conectado;
- portable;
- práctico;
- orientado a producción;
- independiente de la herramienta utilizada.