---
applyTo: "src/**/*.tsx"
description: "Reglas de arquitectura para componentes React con TypeScript"
---

# Reglas para Componentes React

Estas reglas aplican automáticamente a todo archivo `.tsx` dentro de `src/`.

## Pureza del Componente

Todo componente React debe ser una **función pura desde la perspectiva del consumidor**: recibe props, renderiza UI, y delega cualquier lógica a hooks o services.

Un componente NO debe:

- Acceder a `localStorage`, `sessionStorage` ni a APIs del navegador directamente.
- Hacer fetch a APIs externas directamente. Usar un hook.
- Contener lógica de negocio (validaciones complejas, transformaciones, cálculos de dominio).

## Props

- Props siempre tipadas con una `interface` explícita declarada en el mismo archivo (o en `.types.ts` si se reutiliza).
- Naming de la interface: `[NombreComponente]Props`. Ejemplo: `TodoListProps`.
- **Nunca pasar más de 5 props a un componente.** Si se necesitan más, agrupar en un objeto de configuración (`config: TodoListConfig`).
- Evitar props booleanas múltiples que representen estados mutuamente excluyentes — usar una unión discriminada.

## Listas

- Toda lista renderizada con `.map()` debe usar `key` con un identificador **estable y único** proveniente del dato (`item.id`).
- **Nunca usar el índice del array como `key`** salvo que la lista sea estática y nunca se reordene.

## Estados de Carga y Error

Todo componente que consume datos asincrónicos o derivados de un hook debe manejar explícitamente:

1. Estado de carga (loading)
2. Estado de error
3. Estado vacío (lista sin elementos)
4. Estado con datos

No asumir que los datos siempre están disponibles.

## Memoización

- Usar `React.memo` **solo cuando el profiler de React lo justifique**, no por defecto.
- Lo mismo aplica a `useMemo` y `useCallback`: usarlos cuando exista un costo real (cálculos pesados, dependencias estables para hijos memoizados), no preventivamente.

## Exports

- **Componentes de página o vista principal:** `export default`.
- **Componentes reutilizables o auxiliares:** `export` named.

Esto permite distinguir rápidamente la "entrada" de una feature de sus piezas internas.

## Estructura Interna del Archivo

Cada archivo `.tsx` debe seguir este orden estricto:

```tsx
// 1. Imports — primero librerías externas, luego internos (separados por línea en blanco)
import { useState } from 'react'
import { Pencil } from 'lucide-react'

import { useTodos } from './useTodos'
import type { TodoItem } from './todo.types'

// 2. Interface de Props
interface TodoListProps {
  filter: 'all' | 'active' | 'completed'
}

// 3. Componente
export default function TodoList({ filter }: TodoListProps) {
  // ...
}

// 4. Export (default ya está arriba; named exports al final si los hay)
```
