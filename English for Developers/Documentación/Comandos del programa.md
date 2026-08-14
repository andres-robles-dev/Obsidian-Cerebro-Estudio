
| Comando            | Función                                                                                                        |
| ------------------ | -------------------------------------------------------------------------------------------------------------- |
| `Run-FirstClass`   | Inicia la primera clase real utilizando el diagnóstico inicial ya realizado. No repite el test.                |
| `Run-Session`      | Inicia una nueva sesión y continúa desde el estado actual del curso.                                           |
| `Review`           | Realiza una sesión de recuperación activa y repaso de conocimientos anteriores.                                |
| `Assess`           | Evalúa el progreso actual del alumno y detecta fortalezas, dificultades y evolución.                           |
| `End-Session`      | Finaliza la sesión, genera el archivo `.md` de la clase y actualiza el estado del curso.                       |
| `Find-LastClass`   | Busca y abre la última clase disponible dentro de `Sessions/`. Solo para agentes.                              |
| `Load-CourseState` | Carga el estado actual del curso leyendo el Dashboard y el contexto necesario. Solo para agentes.              |
| `Sync-Course`      | Comprueba la coherencia del proyecto, incluyendo Dashboard, sesiones, enlaces y estructura. Solo para agentes. |
