---
name: react-ui
description: >-
  Activa al crear interfaces de usuario, estilos, layouts o componentes visuales
  en React. Usar cuando: Tailwind CSS, diseño responsivo, dark mode, accesibilidad,
  formularios, botones, modales, listas visuales, temas de color.
---

# Skill: UI, Estilos y Accesibilidad

Esta skill define el **stack visual, las convenciones de estilo, dark mode, accesibilidad y responsividad** para todos los componentes.

## Stack de UI

- **Tailwind CSS v3+** para todos los estilos.
  - No usar CSS modules.
  - No usar styled-components ni Emotion.
  - No usar archivos `.css` salvo `src/index.css` con las directivas `@tailwind`.
- **Sin librerías de componentes externas** por defecto (no MUI, no Chakra, no Ant Design).
  - Excepción: si el usuario lo pide explícitamente.
- **Iconos:** `lucide-react`.
- **Utilidad de clases:** `cn()` basada en `clsx` + `tailwind-merge`.

## Convenciones Tailwind

### Utility `cn()`

Crear `src/shared/utils/cn.ts`:

```ts
import { clsx, type ClassValue } from 'clsx'
import { twMerge } from 'tailwind-merge'

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```

Usar `cn()` para clases condicionales y para combinar clases base con overrides:

```tsx
<button
  className={cn(
    'rounded px-4 py-2 font-medium',
    variant === 'primary' && 'bg-blue-600 text-white hover:bg-blue-700',
    variant === 'danger' && 'bg-red-600 text-white hover:bg-red-700',
    disabled && 'cursor-not-allowed opacity-50',
  )}
>
  {label}
</button>
```

### Extracción de Clases Repetidas

Si una combinación de clases se repite dentro de un componente, extraerla a una constante local. No crear archivos CSS externos.

```tsx
const cardClasses = 'rounded-lg border border-gray-200 bg-white p-4 shadow-sm dark:border-gray-700 dark:bg-gray-800'

return (
  <>
    <div className={cardClasses}>...</div>
    <div className={cardClasses}>...</div>
  </>
)
```

### Orden de Clases Tailwind

Para legibilidad, mantener este orden dentro de cada `className`:

1. **Layout:** `flex`, `grid`, `block`, `hidden`, `relative`, `absolute`
2. **Spacing:** `p-*`, `m-*`, `gap-*`, `space-*`
3. **Sizing:** `w-*`, `h-*`, `min-*`, `max-*`
4. **Colors:** `bg-*`, `text-*`, `border-*`
5. **Typography:** `font-*`, `text-*` (tamaño), `leading-*`, `tracking-*`
6. **Effects:** `shadow-*`, `opacity-*`, `transition-*`, `hover:*`, `focus:*`

## Dark Mode

### Configuración

En `tailwind.config.ts`:

```ts
import type { Config } from 'tailwindcss'

export default {
  darkMode: 'class',
  content: ['./index.html', './src/**/*.{ts,tsx}'],
  theme: { extend: {} },
  plugins: [],
} satisfies Config
```

### Hook `useTheme`

Gestionar el tema con un hook que:

1. Lee la preferencia del sistema con `window.matchMedia('(prefers-color-scheme: dark)')`.
2. Persiste la preferencia manual del usuario en `localStorage` vía un `themeService`.
3. Aplica/remueve la clase `dark` en `document.documentElement`.

```ts
// src/shared/hooks/useTheme.ts
import { useEffect, useState } from 'react'
import { themeService } from '@/shared/services/theme.service'

type Theme = 'light' | 'dark' | 'system'

export function useTheme() {
  const [theme, setTheme] = useState<Theme>(() => themeService.get())

  useEffect(() => {
    const resolved =
      theme === 'system'
        ? window.matchMedia('(prefers-color-scheme: dark)').matches
          ? 'dark'
          : 'light'
        : theme

    document.documentElement.classList.toggle('dark', resolved === 'dark')
    themeService.save(theme)
  }, [theme])

  return { theme, setTheme }
}
```

**Nunca usar clases `dark:` en un componente sin haber configurado el provider/hook de tema.**

## Accesibilidad (Obligatoria)

### Elementos Interactivos

- Todo elemento interactivo sin texto visible debe tener `aria-label`:
  ```tsx
  <button aria-label="Eliminar tarea" onClick={onDelete}>
    <Trash2 className="h-4 w-4" />
  </button>
  ```

### HTML Semántico

- Usar elementos nativos: `<button>`, `<input>`, `<ul>/<li>`, `<nav>`, `<main>`, `<header>`, `<footer>`.
- **Nunca** usar `<div onClick>` para acciones — usar `<button>`.

### Contraste

- Contraste mínimo **WCAG AA**: 4.5:1 para texto normal, 3:1 para texto grande.
- Evitar combinaciones de gris-sobre-gris en modo claro.

### Formularios

- Todo `<input>` debe tener un `<label>` asociado vía `htmlFor`/`id`:
  ```tsx
  <label htmlFor="todo-title" className="block text-sm font-medium">
    Título de la tarea
  </label>
  <input id="todo-title" type="text" className="..." />
  ```
- Inputs de búsqueda o filtros: envolver el contenedor con `role="search"`.

### Foco

- Mantener el outline de foco visible. Si se sobrescribe, proveer un estilo `focus-visible:` alternativo claro.

## Responsividad

### Mobile-First

Las clases base son para móvil. Los breakpoints (`sm:`, `md:`, `lg:`) se aplican hacia arriba.

```tsx
<div className="grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
  {items.map((item) => <Card key={item.id} item={item} />)}
</div>
```

### Breakpoints Estándar

| Prefijo | Min-width | Uso típico |
|---|---|---|
| (base) | 0px | Móvil vertical |
| `sm:` | 640px | Móvil horizontal / phablet |
| `md:` | 768px | Tablet |
| `lg:` | 1024px | Desktop |
| `xl:` | 1280px | Desktop grande |

### Patrones Comunes

- Grids responsivos: `grid-cols-1 md:grid-cols-2 lg:grid-cols-3`
- Sidebars: `hidden md:block` para ocultar en móvil.
- Padding del contenedor: `p-4 md:p-6 lg:p-8`.
- Tipografía: `text-base md:text-lg` para titulares.
