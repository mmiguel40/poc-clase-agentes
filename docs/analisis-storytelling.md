# Análisis de Storytelling — Presentación Agentes de IA

> Material generado para revisar el hilo conductor de la guía visual *"Agentes de IA · GitHub Copilot Framework"*. Contiene el contenido completo de los 14 slides en orden + análisis crítico + propuesta de mejora.

---

## Parte 1 — Contenido actual (orden secuencial)

### Slide 1 · Portada (`#s1`)

**Eyebrow:** Glosario IA · 2025–2026
**Título:** Agentes de IA
**Bajada:** Qué son, cómo funcionan y cómo se configuran. Bajo el framework de GitHub Copilot.
**CTA:** `github.com/msanchez-labs/poc-clase-agentes`

---

### Slide 2 · Sobre mí (`#s-autor`)

**Eyebrow:** sobre mí · quien les habla
**Título:** Miguel Sánchez — Arquitecto de Soluciones Cloud + IA
**Bio:** 16 años en IT — desarrollador → líder técnico → DevOps → arquitecto cloud. Diseña arquitectura cloud, automatiza procesos, ayuda a equipos a escalar sin fricción.
**Skills tags:** Azure · AWS · DevOps · CI/CD · Terraform · IA Aplicada · OpenAI · Gemini · GitHub Copilot · Claude Code · Microservicios · Docker · Kubernetes
**Ubicación:** Santiago, Chile · LATAM + Remote
**Link:** msanchez.tech

---

### Slide 3 · ¿Qué es un Agente de IA? (`#s2`)

**Eyebrow:** 01 — concepto base
**Título:** ¿Qué es un Agente de IA?
**Definición:** Un sistema que usa un LLM para tomar decisiones, ejecutar acciones con herramientas y operar en un loop autónomo hasta completar un objetivo — sin intervención humana en cada paso.
**4 componentes mostrados:**
- 🧠 Modelo LLM — motor de razonamiento y toma de decisiones
- 🔄 Loop autónomo — percibir → razonar → actuar → observar
- 🔧 Herramientas — terminal, filesystem, compilador, browser
- 📋 Instrucciones — objetivos, límites y comportamiento esperado

---

### Slide 4 · Capas del sistema (`#s3`)

**Eyebrow:** 02 — arquitectura
**Título:** Capas del sistema
**5 capas (de arriba hacia abajo):**
1. **Usuario** — da el objetivo, revisa el plan, confirma para ejecutar (input/output)
2. **Agente** — orquesta el loop: planifica, activa skills, ejecuta herramientas, evalúa y reintenta (Copilot · Claude Code · Cursor)
3. **Configuración** — Instructions base + Skills + Agents + Prompts + Commands, cargados según contexto (`.github/` · 5 artefactos)
4. **Herramientas** — terminal, filesystem, compilador — acciones reales en el proyecto
5. **Modelo LLM** — motor de razonamiento. El agente lo orquesta — no actúa solo (GPT · Claude · Gemini)

---

### Slide 5 · Evolución de Copilot (`#s-evolucion`)

**Eyebrow:** 03 — evolución
**Título:** Cómo llegó Copilot a ser un agente
**Bajada:** En 4 años pasó de autocompletar líneas a orquestar agentes con planificación, skills personalizadas y MCP.
**Timeline:**
- Jun 2021 — Autocompletado (sugerencias línea por línea)
- Mar 2023 — Chat (conversación contextual)
- Nov 2023 — Workspace (contexto multi-archivo)
- Oct 2024 — Edición multi-archivo (cambios coordinados)
- Feb 2025 — Modo Agente (loop autónomo · Code Review)
- May 2025 — Coding Agent · MCP (modernización · servidores MCP)
- **Oct 2025 — Modo Plan · Custom Agents (Code Quality · Agent HQ) ← AQUÍ ENTRA ESTA CLASE**
**Punchline:** Custom Agents y MCP son las piezas que tú puedes diseñar.

---

### Slide 6 · Los 5 artefactos de configuración (`#s4`)

**Eyebrow:** 04 — GitHub Copilot framework
**Título:** Los 5 artefactos de configuración
**Bajada:** GitHub Copilot lee la carpeta `.github/` y carga cada artefacto según su rol: siempre activo, auto-activado u on-demand.
**4 cards interactivas (click highlights tree):**
- **INSTRUCTIONS** — contexto base, siempre activo. Stack, idioma, prohibiciones globales
- **SKILLS** — capacidades especializadas, se auto-activan por contexto
- **AGENTS** — orquestan flujos complejos end-to-end multi-paso
- **PROMPTS** — punto de entrada del usuario, invocados con `/nombre`

**Árbol `.github/` mostrado:**
- `copilot-instructions.md` (siempre activo)
- `skills/` con react-frontend, react-state, react-ui (auto-activa)
- `agents/react-developer.agent.md` (on-demand)
- `prompts/new-react-app.prompt.md` y `add-feature.prompt.md`

---

### Slide 7 · Skill anatomy — `react-frontend` (`#s5`)

**Eyebrow:** 05 — skill anatomy
**Título:** Qué contiene `react-frontend`
**Bajada:** El frontmatter define cuándo activarse. El body son las reglas que el agente debe seguir.
**3 áreas del SKILL.md:**
- **DESCRIPCIÓN — el trigger:** frases que activan la skill ("crea componente", "estructura React", "nuevo hook")
- **ESTRUCTURA:** feature-based folders, un hook por feature, separación lógica/UI
- **REGLAS DURAS:** sin `any`, sin lógica en componentes UI, interfaces explícitas, PascalCase / use* / kebab-case

**Mención:** react-frontend · react-state · react-ui — 3 skills que se activan juntas según contexto.

**Bloque de código:** un fragmento del SKILL.md con frontmatter + reglas.

**Diagrama: Carga lazy en 3 pasos (interactivo)**
1. Lee solo el frontmatter (name + description de todas las skills)
2. Infiere relevancia (¿el contexto coincide con la description?)
3. Carga el body completo (solo si fue marcada como relevante)

**Footer:** Estándar abierto · compatible con Copilot, Claude Code, Cursor.

---

### Slide 8 · Plan mental del dev (`#s-setup`)

**Eyebrow:** 07 — antes de la demo · el plan mental
**Título:** Antes de configurar nada, Miguel se hace 5 preguntas
**5 preguntas interactivas:**
1. ¿Cómo voy a invocar al agente? → **2 prompts**
2. ¿Qué reglas son siempre verdad? → **1 instructions global**
3. ¿Qué reglas aplican solo a ciertos archivos? → **2 instructions con glob**
4. ¿Cuántos agentes necesito y qué hace cada uno? → **1 agente**
5. ¿Qué conocimiento técnico especializado debe cargar? → **3 skills**

**Árbol real `.github/` (9 archivos · 5 categorías):**
- `copilot-instructions.md` (global)
- `agents/react-developer.agent.md` (on-demand)
- `prompts/new-react-app.prompt.md`, `add-feature.prompt.md`
- `instructions/react-components.instructions.md`, `react-tests.instructions.md`
- `skills/react-frontend/`, `react-state/`, `react-ui/` (cada una con SKILL.md)

**Punchline:** 5 preguntas → 9 archivos → 1 inversión única.

---

### Slide 9 · Caso de uso · El reto (`#s-reto`)

**Eyebrow:** 08 — caso de uso · el reto
**Título:** Distribuidora ABC tiene un problema
**Cast:** Luisana (PM) → Miguel (Dev)
**Dolor:** El equipo comercial envía decenas de cotizaciones por semana. Las trackean en hoja de cálculo compartida — se pierden estados, se duplican filas, nadie sabe cuáles están vencidas.
**Lo que necesita Luisana:**
- ✓ Tablero interno para 6 personas (ver, agregar, editar, marcar estado)
- ✓ Filtrar por cliente, estado y fecha
- ✓ Lo necesita esta semana
- ✗ No tiene un dev libre en el equipo

**Contraste de flujos:**
- **Flujo tradicional ⏱ semanas:** reuniones → arquitectura → setup → implementar → code review + QA → deploy
- **Flujo agéntico ⏱ minutos:** Luisana → Miguel → agente → plan → confirma → construye → localhost

**Banner final:** Vamos a abordarlo con las capacidades agénticas de Copilot (Custom Agents + Skills + Prompts).

---

### Slide 10 · Caso de uso · El plan (`#s6`)

**Eyebrow:** 09 — caso de uso · el plan
**Título:** El dev es el traductor entre negocio y agente
**Panel 1 — Slack (Luisana ↔ Miguel):** "Necesito un tablero interno de cotizaciones — 6 personas. No tengo dev libre y lo necesito esta semana." → Miguel: "Te armo un MVP esta tarde 🚀"
**Panel 2 — VS Code · Copilot Chat (Miguel ↔ Agente):**
- Miguel: `/new-app "tablero de cotizaciones — entidad Cotizacion (id, cliente, monto, estado, fecha)"`
- Agente: razonando… (carga skills, identifica entidades, aplica reglas) → plan listo (7.8 seg)
- Miguel: "agrega exportar a CSV"
- Miguel: ✅ confirmado, ejecuta

**Plan generado (panel derecho):**
- Entidades: Cotizacion · EstadoCotizacion · Cliente
- Modelo: Cotizacion { id, cliente, monto, estado, fechaEnvio, notas }
- Componentes: CotizacionList · Item · Form · FilterBar · ExportButton
- Hooks/Services: useCotizaciones · useFilter · cotizacionService
- Capacidades: CRUD · filtros · persistencia local · exportar CSV
- Stack: Vite + React 18 + TypeScript + Tailwind

**Callout:** Miguel revisa el plan antes de ejecutar. Cero código todavía.

---

### Slide 11 · Caso de uso · Construcción (`#s7`)

**Eyebrow:** 10 — caso de uso · construcción
**Título:** Luisana confirma · el agente construye y entrega
**Fase 1 — Planificación:** Solicitud → Razona → Plan → Confirma (Miguel aprueba o pide ajustes)
**Fase 2 — Construcción (loop autónomo, Miguel solo supervisa):** Scaffold → Implementa → Compila → Listo
**Herramientas:** 📁 filesystem · 💻 terminal · 🔨 compiler · 🔄 retry loop
**Cierre:** Luisana tiene su MVP corriendo en localhost. Lo que tomaba semanas, se resolvió en minutos.

---

### Slide 12 · Dos semanas después… (reuso) (`#s-reuso`)

**Eyebrow:** 11 — dos semanas después…
**Título:** Mismo agente, próxima feature
**Slack (2 sem después):** Luisana: "¿Pueden agregar filtro por rango de fechas al tablero? Y un resumen mensual arriba." → Miguel: "Lo agrego antes del almuerzo ✌️"
**VS Code:** `/add-feature "filtro por rango de fechas + resumen mensual"`
**El agente ya conoce:** la arquitectura · el estilo visual · las entidades del dominio · las reglas del proyecto.
**Punchline:** Un agente bien configurado paga dividendos por feature, no por proyecto.

---

### Slide 13 · Fuentes oficiales (`#s8`)

**Eyebrow:** 12 — fuentes oficiales
**Título:** Lo dicen ellos mismos
**4 citas:**
- **OpenAI** — "Los agentes son sistemas que cumplen tareas en tu nombre con un alto grado de independencia."
- **Anthropic** — "Los agentes son sistemas donde los LLMs dirigen dinámicamente sus propios procesos y el uso de herramientas, manteniendo el control sobre cómo completan las tareas."
- **Google (Gemini)** — "Los agentes son sistemas que aprovechan los modelos Gemini, las herramientas y las capacidades de razonamiento para ejecutar tareas complejas de múltiples pasos."
- **GitHub (Agent Skills)** — "Las Agent Skills son carpetas de instrucciones, scripts y recursos que Copilot puede cargar cuando son relevantes para mejorar su desempeño."

---

### Slide 14 · Resumen / Cierre (`#s9`)

**Eyebrow:** 13 — resumen
**Título:** El modelo razona. El agente construye.
**Subtagline:** Un agente, N features · configurás una vez, reusás para siempre.
**4 cards de síntesis:**
- 📋 Instructions — stack, idioma, prohibiciones globales (siempre activo)
- 📦 Skills + Agents — reglas especializadas React + agente orquesta end-to-end
- 💬 Prompts + Commands — `/new-app` · `/add-feature`
- 🔄 Loop autónomo — planifica → confirmás → construye → compila → itera

**Link al código:** github.com/msanchez-labs/poc-clase-agentes

---

## Parte 2 — Análisis del hilo conductor actual

### Arco narrativo declarado

> Conceptos generales → framework → caso específico (POC) → cierre

### Lo que funciona ✅

1. **Slides 1-4 (intro):** flujo limpio. Portada → presenter → qué es un agente → cómo se estructura. La progresión es coherente: definición → componentes → arquitectura.
2. **Slides 9-12 (caso de uso):** unidad narrativa fuerte. Reto → plan → construcción → reuso son una historia continua con personajes y conflicto.
3. **Cast de personajes:** Luisana + Miguel + agente queda claro como tres roles distintos.

### Lo que rompe el hilo ⚠️

#### Problema 1 — Slide 7 (skill anatomy) está fuera de lugar

El slide profundiza en UN solo archivo (`SKILL.md` de react-frontend) **antes** de que la audiencia sepa por qué hay 3 skills, cuántos otros artefactos hay para esta POC, o qué decidió el dev. Es zoom-in sin contexto.

> **Antes del slide 7, sabemos:** *"hay 5 tipos de artefactos en general"*.
> **Después del slide 7, sabemos:** *"así se ve por dentro UN archivo en particular"*.
> **Lo que falta:** *"y para ESTA POC, decidimos usar X archivos de cada tipo"*.

El slide 8 (plan mental) responde justo esa pregunta intermedia — pero llega **después** del zoom-in técnico. La secuencia natural es: framework → MI plan → ZOOM a UN archivo de MI plan.

#### Problema 2 — Slide 13 (citas oficiales) está descolgado

Las 4 citas de OpenAI/Anthropic/Google/GitHub son **evidencia para la definición** del slide 3. Mostrarlas justo antes del cierre las desconecta de su función natural (refuerzo conceptual) y rompe el momentum emocional del caso de uso → reuso.

Después del slide 12 (reuso), la audiencia está en clímax narrativo. El slide 13 los devuelve a un registro académico de citas, y el slide 14 (cierre) tiene que volver a levantar el tono. Es una caída de energía evitable.

#### Problema 3 — Slide 5 (evolución) interrumpe el flujo conceptual → técnico

Después del slide 4 (arquitectura por capas), la mente está lista para entrar al framework. El slide 5 mete una historia de 4 años de Copilot que es **interesante pero tangencial**. No es malo — solo está mal ubicado.

Mejor ubicación: como introducción **antes** del concepto base (situar el momento histórico) o como **bridge** entre concepto y framework (ya entendiste qué es un agente → así llegó Copilot a serlo → ahora veamos cómo se configura).

#### Problema 4 — Redundancia controlada slide 6 vs slide 8

Ambos muestran un árbol `.github/`:
- Slide 6 muestra el árbol **genérico** (4 tipos de artefactos, con instructions globales pero sin instructions específicas).
- Slide 8 muestra el árbol **del POC** (5 categorías, 9 archivos, con instructions específicas).

Esto **no es un bug, es una feature** — la repetición refuerza. Pero hay que asegurarse de que la audiencia perciba la progresión "genérico → específico", no "lo mismo dos veces".

### Resumen del diagnóstico

| Slide | Problema | Severidad |
|-------|----------|-----------|
| 5 (evolución) | Interrumpe transición concepto → framework | media |
| 7 (skill anatomy) | Zoom-in antes de tener contexto del POC | **alta** |
| 13 (citas) | Descolgada del cierre · debería reforzar definición | **alta** |

---

## Parte 3 — Propuesta de mejora del hilo conductor

### Principio rector

> **Macro → meso → micro, en flujo unidireccional sin retrocesos.**
> Conceptos universales → framework genérico → decisiones del dev para esta POC → zoom a un archivo concreto → demo → cierre.

### Orden propuesto (14 slides)

| # | Slide actual | Slide propuesto | Cambio |
|---|--------------|-----------------|--------|
| 1 | Portada | Portada | — |
| 2 | Sobre mí | Sobre mí | — |
| 3 | ¿Qué es un agente? | ¿Qué es un agente? | — |
| **4** | Capas del sistema | **Citas oficiales** *(mover desde #13)* | 🔄 |
| **5** | Evolución Copilot | **Capas del sistema** | 🔄 |
| **6** | 5 artefactos | **Evolución Copilot** | 🔄 |
| 7 | Skill anatomy | 5 artefactos | sin cambio funcional |
| **8** | Plan mental | **Plan mental del dev** | 🔄 |
| **9** | Reto | **Skill anatomy** *(zoom in al archivo)* | 🔄 |
| 10 | Plan (chat) | Reto | — |
| 11 | Construcción | Plan (chat) | — |
| 12 | Reuso | Construcción | — |
| 13 | Citas oficiales | Reuso | — |
| 14 | Cierre | Cierre | — |

### Hilo conductor resultante

```
INTRO
1. Portada
2. Sobre mí (presenter)

CONCEPTO + VALIDACIÓN
3. ¿Qué es un Agente de IA?          ← define
4. Lo dicen ellos mismos             ← valida la definición (citas)
                                       ↓ "OK, ya entendemos qué es"
ARQUITECTURA + CONTEXTO
5. Capas del sistema                 ← cómo se compone
6. Evolución de Copilot              ← cómo llegó Copilot acá
                                       ↓ "Custom Agents son lo que vamos a ver"
FRAMEWORK (genérico → específico)
7. Los 5 artefactos                  ← framework genérico
8. Plan mental del dev               ← MI POC: 5 preguntas + 9 archivos
9. Skill anatomy (react-frontend)    ← zoom a UN archivo de MI POC
                                       ↓ "ya entendiste el setup — veamos la demo"
CASO DE USO
10. El reto (Luisana)
11. El plan (Slack + VS Code)
12. Construcción (loop end-to-end)
13. Dos semanas después (reuso)
                                       ↓ "esto es lo que aprendiste"
CIERRE
14. Resumen
```

### Justificación de los 4 movimientos

**Movimiento 1 — Citas (de #13 a #4):**
- Refuerza la definición del concepto cuando aún es fresca.
- Libera el final del caso de uso para mantener el momentum hacia el cierre.
- Las citas trabajan como *"no es mi opinión, así lo dicen los grandes"* — es validación, función ideal para principio del arco.

**Movimiento 2 — Capas + Evolución (intercambio 4↔5):**
- "Capas del sistema" sigue siendo arquitectura genérica del concepto.
- "Evolución" pasa a ser **el puente** entre concepto + arquitectura genérica → framework específico de Copilot. Cierra con "Custom Agents son la pieza que tú diseñas — eso vemos ahora", que conecta directamente con el slide 7 (5 artefactos).

**Movimiento 3 — Plan mental antes de Skill anatomy (intercambio 8↔9 conceptualmente):**
- **Este es el cambio más importante.**
- Secuencia natural: 5 artefactos (qué tipos hay) → MI plan mental (qué elegí y por qué) → zoom a UNO de los archivos de MI plan (cómo se ve por dentro).
- La anatomía del slide actual ya enseña que hay 3 skills — pero la audiencia llega ahí sin saber por qué son 3 ni para qué. Con el plan mental antes, ya entendieron la decisión y la anatomía se siente como "y así se ve uno de esos 3 archivos".

**Movimiento 4 — Reuso justo antes del cierre:**
- Naturalmente queda en posición clímax antes del resumen.
- Refuerza el "configurás una vez, reusás para siempre" que aparece en el cierre.

### Cambios de copy menores que sugiero

| Slide | Cambio en copy |
|-------|----------------|
| 4 (citas, nueva pos) | Cambiar eyebrow a "01b — fuentes oficiales" o "validación del concepto" para anclarlo al slide 3 |
| 6 (evolución, nueva pos) | El punchline actual ya conecta con custom agents — refuerza con *"…y los próximos slides muestran cómo se diseñan"* |
| 8 (plan mental) | Agregar un mini-transición al pie: *"ya sabemos qué archivos vamos a usar — antes de la demo, veamos cómo se ve uno por dentro"* |
| 9 (skill anatomy, nueva pos) | Cambiar el eyebrow a "08 — zoom · cómo se ve un SKILL.md" para que se sienta consecuencia del plan mental |

### Renumeración de eyebrows resultante

```
01 — concepto base                    (#3)
01b — fuentes oficiales · validación  (#4) ← NUEVO orden
02 — arquitectura                     (#5)
03 — evolución                        (#6)
04 — framework: 5 artefactos          (#7)
05 — plan mental del dev              (#8)
06 — skill anatomy                    (#9) ← zoom in
07 — caso de uso · el reto            (#10)
08 — caso de uso · el plan            (#11)
09 — caso de uso · construcción       (#12)
10 — dos semanas después              (#13)
11 — resumen                          (#14)
```

### Lo que NO toco

- El contenido interno de cada slide (excepto los pequeños ajustes de copy mencionados).
- Los efectos interactivos (clicks en cards → highlights en árbol/código).
- El cast de personajes (Luisana / Miguel / agente).
- El diseño visual, paleta, animaciones.

### Beneficios esperados

1. **Direccionalidad clara:** zero retrocesos conceptuales — siempre vamos de lo más general a lo más específico.
2. **Cada slide prepara al siguiente:** las citas validan la definición; la arquitectura abre paso a la evolución; la evolución abre paso al framework; el framework abre paso al plan mental; el plan mental abre paso al zoom; el zoom abre paso a la demo.
3. **Sin caídas de energía:** el momentum sube desde el slide 9 (reto) hasta el cierre sin pasar por slides "académicos" en el medio.
4. **Justifica la profundidad técnica del slide 9 (skill anatomy en nueva pos):** la audiencia ya entendió que esa skill es uno de los 9 archivos que Miguel eligió — el zoom-in tiene contexto.

---

## Parte 4 — Plan de implementación

Si querés que aplique estos cambios, el trabajo es **bajo riesgo** y se reduce a reordenar bloques HTML en `index.html`:

1. **Mover `#s8` (citas oficiales)** desde su posición actual (~líneas 1390-1420) hacia justo después del slide `#s2` (¿Qué es un agente?, ~línea 410).
2. **Intercambiar `#s3` (capas) y `#s-evolucion`** — el evolución pasa después de capas.
3. **Mover `#s5` (skill anatomy)** desde su posición actual (~línea 645) hacia después de `#s-setup` (plan mental).
4. **Actualizar los eyebrows** según el cuadro de renumeración.
5. **Aplicar los 4 cambios de copy** sugeridos.

**Verificación:** el JS de paginación lee `document.querySelectorAll('.slide').length` y los handlers de los interactivos (cards de artefactos, skill steps, plan mental) son self-contained por slide — el reordenamiento no rompe ningún handler.

**Estimación:** 4-6 edits dirigidos, ~15 minutos. Sin riesgo de regresiones en estilos ni en JS.
