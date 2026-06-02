# Instrucciones Globales del Repositorio

Estas instrucciones se aplican a todas las interacciones del agente con este repositorio. Son el contexto base que se inyecta en cada petición. Las skills, instructions específicas y agents complementan estas reglas con detalle adicional.

## Stack Técnico

- **Framework:** React 18
- **Lenguaje:** TypeScript (modo estricto)
- **Bundler/Dev Server:** Vite
- **Estilos:** Tailwind CSS v3+
- **Iconos:** Lucide React
- **Utilidades de clases:** `clsx` + `tailwind-merge`
- **Node.js target:** 20 o superior
- **Gestor de paquetes:** `npm` (no usar yarn, pnpm ni bun salvo petición explícita)

## Idioma

- **Código (identificadores, nombres de funciones, variables, archivos):** inglés
- **Comentarios en el código:** español
- **Mensajes para el usuario y documentación:** español

## ⚠️ REGLA CRÍTICA — Ubicación del Código Generado

**Esta regla tiene prioridad sobre el resto del documento. Aplica a CUALQUIER generación de código (apps nuevas, features, componentes, services, etc.) y debe verificarse antes de cada `Write` o `Edit`.**

### Cuando creas una app nueva (ej. `/new-react-app`)

- **SIEMPRE** dentro de una subcarpeta nueva en la raíz: `npm create vite@latest [app-folder] -- --template react-ts`.
- **NUNCA** scaffoldees en la raíz (`.`). Prohibido `npm create vite@latest . ...`.
- **NUNCA** modifiques, sobrescribas ni borres archivos preexistentes en la raíz del repositorio. Específicamente: el `index.html` de la raíz, `README.md`, `LICENSE`, `.github/`, `.git/`, ni cualquier otra carpeta o archivo existente. Esos archivos pertenecen al repo padre (esta guía/clase) y son intocables.

### Cuando agregas una feature a una app existente (ej. `/add-feature`)

- La feature vive **únicamente** dentro de `[app-folder]/src/features/[nombre-feature]/`.
- Está permitido modificar `[app-folder]/src/App.tsx` para integrar la nueva feature.
- Si necesitas tocar cualquier archivo **fuera de `[app-folder]/`**, **detente y pregunta al usuario antes de proceder**.

### Verificación obligatoria antes de cada Write/Edit

Responde mentalmente: *"¿esta ruta empieza con `[app-folder]/`?"*

- ✅ Sí → adelante.
- ❌ No → **detente**. Pregunta al usuario o reconsidera el plan.

Si todavía no decidiste el nombre del `[app-folder]`, debes elegirlo y declararlo **antes** de cualquier comando de generación.

## Prohibiciones Globales (Reglas Duras)

Las siguientes prácticas están prohibidas en todo el repositorio sin excepción:

1. **Prohibido usar `any`** en TypeScript. Si un tipo es desconocido, usar `unknown` y estrechar con type guards.
2. **Prohibido usar `var`**. Usar `const` por defecto; `let` solo cuando se requiera reasignación.
3. **Prohibido escribir lógica de negocio dentro de componentes JSX/TSX.** Toda lógica vive en hooks o services.
4. **Prohibido acceder a `localStorage` directamente desde componentes ni desde hooks.** Toda persistencia pasa por un `service` de la feature.
5. **Prohibido usar `useEffect` para derivar estado.** Para valores derivados usar `useMemo` o cálculo en render.

## Convenciones de Naming

| Elemento | Convención | Ejemplo |
|---|---|---|
| Componentes | PascalCase | `CotizacionList` |
| Hooks | `use` + PascalCase | `useCotizaciones` |
| Archivos de componentes | PascalCase.tsx | `CotizacionList.tsx` |
| Archivos de hooks | camelCase.ts | `useCotizaciones.ts` |
| Archivos de services | camelCase.service.ts | `cotizacion.service.ts` |
| Archivos de tipos | camelCase.types.ts | `cotizacion.types.ts` |
| Carpetas de features | kebab-case | `cotizaciones/` |
| Interfaces / Types | PascalCase descriptivo | `Cotizacion` (NO `ICotizacion`) |
| Enums | PascalCase | `EstadoCotizacion` |
| Constantes globales | UPPER_SNAKE_CASE | `MAX_ITEMS` |

## Reglas Operativas para el Agente

- **Antes de crear cualquier archivo**, verificar si ya existe. Si existe, leerlo antes de modificarlo. Nunca sobrescribir sin confirmación.
- **Antes de modificar un archivo existente**, leerlo completo para entender el contexto.
- **Al finalizar cualquier cambio significativo**, ejecutar `npm run build` para validar que no hay errores de TypeScript. Si falla, leer el error completo y corregir antes de reportar éxito.
- **No ignorar errores de TypeScript "por ahora".** Si aparece un error, corregirlo.
- **No introducir dependencias nuevas** sin justificarlas explícitamente al usuario.
