# Agentes de IA — GitHub Copilot Framework

> Material de clase y POC práctica del glosario IA 2025–2026.
> Aprende qué es un agente de IA, cómo se compone, y cómo configurar uno real con GitHub Copilot Agent Mode usando los 5 artefactos del framework.

**🔗 Repo del ejercicio:** https://github.com/mmiguel40/poc-clase-agentes

Este repositorio acompaña la sesión **"Agentes de IA — GitHub Copilot Framework"** y contiene tanto el material teórico (slides) como una **POC funcional**: la configuración completa de un agente que construye aplicaciones React frontend desde una descripción en lenguaje natural.

---

## Índice

- [¿Qué es un agente de IA?](#qué-es-un-agente-de-ia)
- [Arquitectura por capas](#arquitectura-por-capas)
- [Los 5 artefactos del framework](#los-5-artefactos-del-framework)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Prerrequisitos](#prerrequisitos)
- [Quick Start](#quick-start)
- [Cómo usar la POC](#cómo-usar-la-poc)
- [Recorrido por la configuración](#recorrido-por-la-configuración)
- [Adaptar el framework a otros stacks](#adaptar-el-framework-a-otros-stacks)
- [Material de la clase](#material-de-la-clase)
- [Compatibilidad con otras herramientas](#compatibilidad-con-otras-herramientas)
- [Créditos](#créditos)

---

## ¿Qué es un agente de IA?

Un **agente de IA** es un sistema que usa un LLM (Large Language Model) para tomar decisiones, ejecutar acciones con herramientas, y operar en un loop autónomo hasta completar un objetivo — sin intervención humana en cada paso.

Tiene cuatro piezas:

| Pieza | Rol |
|---|---|
| 🧠 **Modelo LLM** | Motor de razonamiento y toma de decisiones |
| 🔄 **Loop autónomo** | Percibir → Razonar → Actuar → Observar |
| 🔧 **Herramientas** | Terminal, filesystem, compilador, browser |
| 📋 **Instrucciones** | Objetivos, límites y comportamiento esperado |

El **LLM solo no es un agente**: el agente es la orquestación que conecta el LLM con herramientas reales y un loop iterativo.

---

## Arquitectura por capas

```
┌───────────────────────────────────────────────────────┐
│  Usuario                                              │
│  Da el objetivo. Revisa el plan. Confirma para ejecutar │
└───────────────────────────────────────────────────────┘
                          ▲ ▼
┌───────────────────────────────────────────────────────┐
│  Agente                                               │
│  Orquesta el loop: planifica, activa skills,          │
│  ejecuta herramientas, evalúa y reintenta             │
│  (Copilot · Claude Code · Cursor)                     │
└───────────────────────────────────────────────────────┘
                          ▲ ▼
┌───────────────────────────────────────────────────────┐
│  Configuración (.github/)                             │
│  Instructions base + Skills + Agents +                │
│  Prompts + Commands — cargados según contexto         │
└───────────────────────────────────────────────────────┘
                          ▲ ▼
┌───────────────────────────────────────────────────────┐
│  Herramientas                                         │
│  Terminal · Filesystem · Compilador                   │
└───────────────────────────────────────────────────────┘
                          ▲ ▼
┌───────────────────────────────────────────────────────┐
│  Modelo LLM                                           │
│  GPT · Claude · Gemini                                │
└───────────────────────────────────────────────────────┘
```

La capa que **tú diseñas** como ingeniero es la de **Configuración**. Es donde defines cómo se comporta el agente, qué reglas sigue, y qué patrones aplica.

---

## Los 5 artefactos del framework

GitHub Copilot Agent Mode lee la carpeta `.github/` y compone el contexto del agente a partir de **5 tipos de artefactos**. Cada uno cumple un rol distinto y se activa de forma diferente.

| # | Artefacto | Carpeta | Cuándo se carga | Para qué sirve |
|---|---|---|---|---|
| 1 | **Instructions globales** | `.github/copilot-instructions.md` | Siempre | Contexto base: stack, naming, prohibiciones |
| 2 | **Instructions por archivo** | `.github/instructions/*.instructions.md` | Por glob (`applyTo`) | Reglas que aplican solo a ciertos tipos de archivo |
| 3 | **Skills** | `.github/skills/<nombre>/SKILL.md` | Auto-activación por contexto (description) | Conocimiento de dominio: patrones, arquitectura, decisiones |
| 4 | **Agents** | `.github/agents/<nombre>.agent.md` | Invocados por nombre desde prompts (atributo `agent:`) | Orquestadores con protocolo propio (planificar → confirmar → construir) |
| 5 | **Prompts** | `.github/prompts/*.prompt.md` | Invocados con `/nombre` por el usuario | Puntos de entrada parametrizables. **Reemplazan el concepto de "commands"** en Copilot — los slash-commands SON prompt files. |

### Diferencia clave: Skills vs Instructions

- **Instructions** describen **reglas duras y obligatorias** (qué nunca hacer, qué nomenclatura usar). Son cortas y categóricas.
- **Skills** describen **conocimiento aplicable** (cómo organizar carpetas, cuándo elegir Context API vs Zustand). Son más largas y explicativas.

### Diferencia clave: Agents vs Prompts

- **Agent**: define un protocolo de ejecución reutilizable (ej. siempre planificar antes de construir, validar con `npm run build`, iterar al fallar).
- **Prompt/Command**: punto de entrada que el usuario invoca con `/comando`. Suele activar un agent y pasarle parámetros.

---

## Estructura del repositorio

```
.
├── README.md                                     # Este archivo
├── index.html                                    # Slides de la clase (abrir en navegador o GitHub Pages)
└── .github/
    ├── copilot-instructions.md                   # ① Contexto base global
    ├── instructions/
    │   ├── react-components.instructions.md      # ② Reglas para src/**/*.tsx
    │   └── react-tests.instructions.md           # ② Reglas para src/**/*.test.{ts,tsx}
    ├── skills/
    │   ├── react-frontend/SKILL.md               # ③ Arquitectura feature-based
    │   ├── react-state/SKILL.md                  # ③ Decisión de gestión de estado
    │   └── react-ui/SKILL.md                     # ③ Tailwind, dark mode, a11y
    ├── agents/
    │   └── react-developer.agent.md              # ④ Agente con protocolo de 2 fases
    └── prompts/
        ├── new-react-app.prompt.md               # ⑤ /new-react-app — crear app desde cero
        └── add-feature.prompt.md                 # ⑤ /add-feature — agregar feature
```

---

## Prerrequisitos

Para usar la POC necesitas:

1. **VS Code** versión 1.10+
2. **Extensión GitHub Copilot** instalada y activa
3. **Suscripción a GitHub Copilot** con Agent Mode disponible (Copilot Pro, Business o Enterprise)
4. **Node.js 20+** y `npm` instalados (los necesita el agente para hacer scaffold y `npm run build`)
5. **Git** (recomendado para iterar con seguridad)

### Configuración recomendada de VS Code

Verifica en `settings.json` (la mayoría vienen por defecto desde VS Code 1.10+):

```json
{
  "github.copilot.chat.codeGeneration.useInstructionFiles": true,
  "chat.promptFiles": true,
  "chat.agent.enabled": true
}
```

---

## Quick Start

**Repo del ejercicio:** [github.com/mmiguel40/poc-clase-agentes](https://github.com/mmiguel40/poc-clase-agentes)

### 1. Clonar el repo

```bash
git clone https://github.com/mmiguel40/poc-clase-agentes.git
cd poc-clase-agentes
```

### 2. Abrir en VS Code

```bash
code .
```

VS Code detectará automáticamente `.github/copilot-instructions.md` y los archivos de la carpeta `.github/`.

### 3. Abrir el chat de Copilot en modo Agent

- Atajo: `Ctrl+Alt+I` (Windows/Linux) o `Cmd+Ctrl+I` (Mac).
- En el selector superior del chat, elige **Agent**.

### 4. Invocar el prompt

En el chat escribe:

```
/new-react-app una todo list con prioridades y dark mode
```

El agente generará el **plan de arquitectura** y esperará tu confirmación antes de construir.

### 5. Confirmar y dejar trabajar

Responde `sí`. El agente:

1. Hace scaffold con Vite + React + TypeScript.
2. Configura Tailwind CSS y dark mode.
3. Crea tipos, services, hooks y componentes en el orden correcto.
4. Ejecuta `npm run build` y corrige errores hasta que pase.
5. Levanta el dev server y reporta la URL local.

---

## Cómo usar la POC

### Crear una app nueva

```
/new-react-app <descripción de la app>
```

Ejemplos:

- `/new-react-app un blog estático con búsqueda y categorías`
- `/new-react-app un CRM simple para gestionar contactos con etiquetas`
- `/new-react-app un dashboard de gastos personales con gráficos`
- `/new-react-app una landing page para una agencia con formulario de contacto`

El agente seguirá el **protocolo de dos fases**:

1. **Fase 1 — Planificación:** muestra entidades, modelo de datos, componentes, hooks y services. **Espera tu "sí"**.
2. **Fase 2 — Construcción:** ejecuta el scaffold, crea archivos en orden, valida con `npm run build`, levanta `npm run dev`.

### Agregar una feature a un proyecto existente

Dentro de un proyecto generado por el agente (o cualquier proyecto React que siga la convención):

```
/add-feature <descripción de la feature>
```

Ejemplos:

- `/add-feature filtros por etiqueta y búsqueda por texto`
- `/add-feature exportar tareas como JSON`
- `/add-feature autenticación con localStorage`

El agente:

1. Lee `src/features/` para entender el patrón actual.
2. Genera un mini-plan de la nueva feature.
3. Espera tu confirmación.
4. Implementa con el mismo patrón que las features existentes.
5. Valida con `npm run build`.

---

## Recorrido por la configuración

Esta sección explica qué hace cada archivo y por qué está donde está. Útil si quieres **adaptar la POC a tu stack** o **entender el patrón** para construir tu propio agente.

### ① `copilot-instructions.md` — Contexto base global

> **Se carga en TODAS las peticiones al agente.**

Define lo que nunca cambia en el proyecto:

- Stack (React 18 + TypeScript + Vite + Tailwind CSS).
- Naming convencions (PascalCase, camelCase, kebab-case).
- Prohibiciones duras (`any`, `var`, lógica en JSX, `localStorage` directo).
- Reglas operativas (verificar antes de sobrescribir, validar con `npm run build`).

**Regla de oro:** este archivo debe ser **corto y categórico**. No repitas aquí lo que ya está en una Skill.

### ② `instructions/*.instructions.md` — Reglas por glob

> **Se cargan automáticamente cuando el agente toca archivos que matchean `applyTo`.**

Cada archivo tiene un frontmatter con `applyTo: "glob"`:

- `react-components.instructions.md` → aplica a `src/**/*.tsx`
- `react-tests.instructions.md` → aplica a `src/**/*.test.{ts,tsx}`

Útil para reglas que **solo tienen sentido en cierto tipo de archivo** (ej. testing solo aplica a tests, no a componentes).

### ③ `skills/<nombre>/SKILL.md` — Conocimiento de dominio

> **Se auto-activan cuando la `description` matchea el contexto de la petición.**

Las skills son la pieza más importante del framework. Cada una agrupa conocimiento profundo sobre un dominio:

- **`react-frontend`**: estructura de carpetas feature-based, separación hook/service/componente, patrón de `localStorage` via service.
- **`react-state`**: árbol de decisión para elegir entre `useState`, `useReducer`, Context, Zustand, TanStack Query.
- **`react-ui`**: Tailwind, dark mode, accesibilidad WCAG AA, responsividad mobile-first.

**Frontmatter clave:**

```yaml
---
name: react-frontend                      # lowercase con guiones
description: >-                           # crítico: el LLM lee esto para decidir si activarla
  Activa al crear o estructurar proyectos React con TypeScript y Vite.
  Usar cuando: nueva app, nueva feature, scaffolding, ...
---
```

La `description` actúa como un **clasificador**: cuanto más explícita sobre **cuándo activarla**, mejor activa el agente automáticamente.

### ④ `agents/react-developer.agent.md` — Orquestador

> **Es invocado por nombre desde un prompt (atributo `agent: react-developer` en el frontmatter del `.prompt.md`).**

Define un protocolo de ejecución reutilizable. En este caso, el **protocolo de dos fases**:

1. **Planificación:** generar plan completo (entidades, tipos, componentes, hooks, services) y **esperar confirmación del usuario**.
2. **Construcción:** scaffold → Tailwind → tipos → services → hooks → componentes → `npm run build` → `npm run dev` → reporte.

Incluye **reglas de iteración**: qué hacer si el build falla, cuándo dividir componentes, qué nunca hardcodear.

### ⑤ `prompts/*.prompt.md` — Puntos de entrada

> **Invocados por el usuario con `/nombre` desde el chat.**

Son la "API pública" del agente. Frontmatter:

```yaml
---
name: new-react-app
description: Inicia el flujo completo para crear una nueva aplicación React desde cero
agent: react-developer    # ← apunta al agente que ejecutará el prompt
---
```

El body del prompt es el mensaje que recibirá el agente, incluyendo placeholders como `{{description}}` que el usuario rellena al invocar el prompt con `/nombre`.

> **Nota sobre convenciones de GitHub Copilot:** los slash-commands (`/algo`) SON archivos `*.prompt.md`. No existe una carpeta `commands/` separada en el spec oficial — eso es una convención de Claude Code, no de Copilot. Para máxima compatibilidad con Copilot, todo lo invocable con `/` vive en `.github/prompts/` con extensión `.prompt.md`.

---

## Adaptar el framework a otros stacks

La estructura `.github/` es **agnóstica al stack**. Para adaptarla a Vue, Svelte, Next.js, Astro, una API en NestJS o un proyecto Python, replica el patrón:

### 1. Reescribe `copilot-instructions.md`

Cambia el stack declarado y las prohibiciones duras a las que apliquen en tu ecosistema. Mantenlo corto.

### 2. Crea skills por dominio

Una skill por preocupación independiente. Ejemplos:

- **Backend Node:** `node-api-architecture`, `node-database`, `node-auth`.
- **Vue:** `vue-composition`, `vue-pinia`, `vue-ui`.
- **Python:** `python-fastapi`, `python-orm`, `python-testing`.

**Regla:** una skill se justifica si su contenido es **largo, autocontenido, y solo aplica en cierto contexto**. Si tu regla es de una línea, va en `copilot-instructions.md`.

### 3. Define instructions por glob

Para reglas que solo aplican a ciertos archivos:

- `applyTo: "src/**/*.test.py"`
- `applyTo: "apps/**/route.ts"`
- `applyTo: "**/*.bicep"`

### 4. Define un agente con protocolo

Casi siempre vale la pena un protocolo de **planificar → confirmar → ejecutar → validar**. Adapta los pasos de construcción a tu stack:

- Para una API: scaffold → DB schema → migrations → endpoints → tests → `pytest`/`npm test`.
- Para infraestructura: plan → `terraform plan` / `bicep what-if` → confirmación → `apply`.

### 5. Exponé prompts amigables

Nombres cortos, descripción clara, un placeholder bien nombrado.

---

## Material de la clase

### Slides

**En línea (GitHub Pages):** https://mmiguel40.github.io/poc-clase-agentes/

**Localmente:** abre [`index.html`](index.html) en cualquier navegador moderno. Es una presentación HTML de 9 slides autocontenida (sin dependencias externas).

Navegación: flechas `←` `→` del teclado.

Contenido:

1. Portada
2. ¿Qué es un agente de IA?
3. Arquitectura por capas
4. Los 5 artefactos de configuración
5. Qué contiene `react-frontend` (deep dive de una skill)
6. El agente planifica antes de construir (Fase 1)
7. El agente construye e itera (Fase 2)
8. Lo dicen ellos mismos (citas y referencias)
9. Cierre

---

## Compatibilidad con otras herramientas

La carpeta `.github/` con esta estructura es **portable** a varios agentes de codificación:

| Herramienta | Soporte |
|---|---|
| **GitHub Copilot (VS Code)** | Soporte nativo de `copilot-instructions.md`, `instructions/`, `prompts/` y agents custom |
| **GitHub Copilot CLI** | Lee `copilot-instructions.md` y skills |
| **Claude Code (Anthropic)** | Lee skills y agents siguiendo el mismo spec de frontmatter |
| **Cursor** | Lee `.github/copilot-instructions.md` como contexto base |

> Notas:
> - El comportamiento exacto de auto-activación de skills depende de cada herramienta.
> - Algunas funcionalidades (placeholders `{{var}}` en prompts, Agent Mode con plan/confirm) son más maduras en VS Code + Copilot.

---

## Roadmap / Ideas para extender

- [ ] Skills para testing E2E (Playwright)
- [ ] Agent para code review automatizado del diff
- [ ] Command `/refactor-feature` que aplique los patrones a una feature legacy
- [ ] Skill `react-routing` con React Router v6 + lazy loading
- [ ] Skill `react-forms` con React Hook Form + Zod

Si extiendes el repo, envía un PR. La idea es que se vuelva un **catálogo de skills reutilizables** para distintos stacks.

---

## Estructura para tu propio repo

Si quieres usar esto como **plantilla**:

```bash
# Clona solo la carpeta .github
git clone https://github.com/mmiguel40/poc-clase-agentes.git temp
cp -r temp/.github tu-proyecto/
rm -rf temp

# Edita .github/copilot-instructions.md con tu stack
# Edita las skills/ con tu dominio
# Listo
```

---

## Créditos

- **Autor:** Miguel Sánchez — Arquitecto de Soluciones Cloud
- **Web:** [msanchez.tech](https://msanchez.tech)
- **Clase:** Glosario IA 2025–2026 · Sesión "Agentes de IA"

### Inspiración / Referencias

- [GitHub Copilot Agent Mode docs](https://docs.github.com/copilot)
- [Anthropic Claude Code docs](https://docs.anthropic.com/claude-code)
- Spec de Agent Skills (frontmatter `name` + `description` con auto-activación por contexto)

---

## Licencia

MIT. Úsalo, fórmalo, modifícalo, llévatelo a tu equipo.

Si te resulta útil, una estrella en el repo y compartirlo con tu red es la mejor forma de retribuir.
