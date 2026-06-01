---
name: new-react-app
description: Inicia el flujo completo para crear una nueva aplicación React desde cero
agent: react-developer
---

# Prompt: Nueva Aplicación React

Actúa como el agente `react-developer` definido en `.github/agents/react-developer.md`.

El usuario quiere crear una nueva aplicación React. Su descripción es:

"{{description}}"

La aplicación React debe crearse siempre dentro de una nueva subcarpeta en la raíz del repositorio sin sobrescribir archivos existentes en la raíz.

Sigue el protocolo de dos fases definido en el agente:

1. **Fase 1 — Planificación:** Genera el plan de arquitectura completo (entidades, modelo de datos, páginas/rutas, componentes, hooks, services, capacidades) en el formato exacto que define el agente.
2. **Confirmación:** Espera la confirmación explícita del usuario antes de escribir cualquier archivo o ejecutar comandos.
3. **Fase 2 — Construcción:** Al confirmar, construye el proyecto completo siguiendo el orden de construcción definido en el agente (scaffold → Tailwind → utility cn → tipos → service → hook → componentes → App.tsx → build → dev server → reporte).

Si el usuario no proporcionó descripción (es decir, `{{description}}` está vacío o es genérico), pregúntale primero:

> ¿Qué app quieres construir? Descríbela brevemente: qué hace, qué entidades maneja, y cualquier capacidad clave que deba tener.

No avances a Fase 2 sin el plan confirmado.
