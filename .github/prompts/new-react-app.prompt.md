---
name: new-react-app
description: Inicia el flujo completo para crear una nueva aplicación React desde cero
agent: react-developer
---

# Prompt: Nueva Aplicación React

Actúa como el agente `react-developer` definido en `.github/agents/react-developer.agent.md`.

El usuario quiere crear una nueva aplicación React. Su descripción es:

"{{description}}"

⚠️ **Regla crítica de ubicación:** La aplicación React debe crearse SIEMPRE dentro de una subcarpeta nueva en la raíz del repositorio. **NUNCA** uses `npm create vite@latest . ...` (con punto). **NUNCA** modifiques o sobrescribas el `index.html`, `README.md`, `.github/` ni ningún otro archivo o carpeta preexistente en la raíz del repo — esos pertenecen al repo padre. El nombre del subfolder debe aparecer explícitamente como "Folder de la app" en el plan de la Fase 1, y el usuario debe poder confirmarlo o ajustarlo antes de la construcción.

Sigue el protocolo de dos fases definido en el agente:

1. **Fase 1 — Planificación:** Genera el plan de arquitectura completo (entidades, modelo de datos, páginas/rutas, componentes, hooks, services, capacidades) en el formato exacto que define el agente.
2. **Confirmación:** Espera la confirmación explícita del usuario antes de escribir cualquier archivo o ejecutar comandos.
3. **Fase 2 — Construcción:** Al confirmar, construye el proyecto completo siguiendo el orden de construcción definido en el agente (scaffold → Tailwind → utility cn → tipos → service → hook → componentes → App.tsx → build → dev server → reporte).

Si el usuario no proporcionó descripción (es decir, `{{description}}` está vacío o es genérico), pregúntale primero:

> ¿Qué app quieres construir? Descríbela brevemente: qué hace, qué entidades maneja, y cualquier capacidad clave que deba tener.

No avances a Fase 2 sin el plan confirmado.
