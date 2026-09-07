# Crear Tema en Modo Explicativo - Cerebro Java

Usa este prompt cuando quieras crear un nuevo tema de estudio en el vault "Cerebro" en formato **Explicativo** (curso narrativo sin niveles).

---

## Rutas

- Vault: `C:\Users\arobl\Desktop\Programacion estudio`
- Repositorio: `https://github.com/andres-robles-dev/Obsidian-Cerebro-Estudio.git` (branch `main`)
- Editar archivos con las herramientas de edicion. Commits/push con PowerShell (git).

---

## Que hacer

### 1. Investigar antes de crear

- Buscar en el vault si el concepto ya esta cubierto (grep en temas 01-47 y plantillas)
- Si existe: informar donde y proponer mejorar/expandir ese tema en vez de duplicar
- Si no existe: proceder a crear

### 2. Crear el archivo del tema

Nombre: `NN - Titulo.md` (sin tildes, ASCII). El numero sigue al ultimo tema existente.

**Estructura obligatoria del Modo Explicativo:**

```markdown
---
tags: [java, ...]
---

# NN - Titubre del Tema

Intro breve: que es y para que sirve, en 2-3 lineas.

---

# METODO EXPLICATIVO

## 1. Concepto base

Explicacion fluida del concepto. Ejemplo SOLO si es estrictamente necesario.

## 2. Siguiente capa de profundidad

...

## N. Ultimo concepto o resumen practico

---

## Errores Comunes

> **Error tipico**. Correccion o explicacion de por que esta mal.

---

## Conexiones

- [[NN - Tema relacionado]] - Descripcion breve

---

## Tags
`#java #...`
```

### 3. Reglas del formato Explicativo

- **Sin NIVEL 1/2/3**: la progresion va integrada en la narrativa
- **Secciones numeradas** `## 1.` a `## N.` (tantas como necesite, no fijas a 10)
- **Ejemplos solo cuando necesarios**: no por defecto. Si el concepto se entiende con texto, no poner codigo
- **Texto fluido**: como un curso que se lee, no como una referencia tecnica
- **Intro breve** al inicio (2-3 lineas)
- **Erros Comunes** siempre al final (blockquotes con `> `)
- **Conexiones** con wiki-links a temas relacionados
- **Tags** al final

### 4. Actualizar indice

Editar `00 - INDICE MAPA MENTAL.md`:

1. **Mindmap**: añadir nodo/rama en el bloque ` ```mermaid mindmap `
2. **Orden de Lectura**: añadir entrada numerada en Fase 6 (despues del ultimo tema)
3. **Tabla de Tags**: añadir fila con el tag principal

### 5. Anadir puentes

En los temas relacionados existentes, añadir en la seccion `## Conexiones` un wiki-link al nuevo tema:

```markdown
- [[NN - Nuevo Tema]] - Descripcion breve
```

### 6. Verificar y subir

```powershell
git status
git add -A
git commit -m "Añadir tema NN (descripcion breve) y actualizar indice"
git push origin main
```

---

## Estado actual

- Temas: 01 a 47. Ultimo: `47 - List.copyOf() (inmutabilidad).md`
- Plantillas: 01 a 29. Ultima: `29 - Clase Generica.md`
- Formato Explicativo disponible desde tema 47

---

## Ejemplo de tema creado con este prompt

Ver `47 - List.copyOf() (inmutabilidad).md` como referencia del formato.
