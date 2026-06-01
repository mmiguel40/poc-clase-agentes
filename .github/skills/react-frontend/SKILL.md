---
name: react-frontend
description: >-
  Activa al crear o estructurar proyectos React con TypeScript y Vite.
  Usar cuando: nueva app, nueva feature, scaffolding, estructura de carpetas,
  componentes, hooks, services, organización de código frontend.
---

# Skill: Arquitectura Frontend React

Esta skill define la **estructura de carpetas, separación de responsabilidades y patrones de organización** para todo proyecto React en este repositorio.

> Cuando se crea una app React nueva desde cero, siempre se genera dentro de una subcarpeta nueva en la raíz del repositorio. No se debe crear la app en el directorio raíz ni sobrescribir los archivos existentes del proyecto principal.

## Estructura de Carpetas (Feature-Based)

La organización es **feature-based**, no type-based. Cada feature es autocontenida.

```
src/
├── features/
│   └── [feature-name]/                    # kebab-case
│       ├── [FeatureName]List.tsx          # componente lista/contenedor
│       ├── [FeatureName]Item.tsx          # componente ítem individual
│       ├── [FeatureName]Form.tsx          # formulario de creación/edición
│       ├── use[FeatureName].ts            # hook: toda la lógica de la feature
│       ├── [featureName].service.ts       # persistencia y operaciones de datos
│       └── [featureName].types.ts         # interfaces y tipos de la feature
├── shared/
│   ├── components/                        # componentes reutilizables entre features
│   ├── hooks/                             # hooks genéricos (useLocalStorage, useDebounce)
│   └── utils/                             # funciones puras utilitarias (cn, formatDate, etc.)
├── App.tsx
└── main.tsx
```

### Por qué feature-based

Agrupar por dominio (no por tipo de archivo) hace que:

- Borrar una feature sea trivial: borrar una carpeta.
- Cambios en una feature no requieran tocar 4 carpetas distintas.
- Sea evidente qué pertenece a qué feature.

## Separación de Responsabilidades

Cada capa tiene una responsabilidad única.

### Hook (`use[Feature].ts`)

- Estado de la feature (`useState`, `useReducer`).
- Lógica de negocio (filtros, validaciones, transformaciones).
- Llamadas al service para persistir o leer.
- Expone una API limpia al componente: datos + acciones.

### Componente (`.tsx`)

- JSX.
- Event handlers que delegan al hook.
- Estilos (Tailwind).
- **No conoce el service.** Solo conoce el hook.

### Service (`[feature].service.ts`)

- Lectura y escritura a `localStorage` (o futuro backend).
- Transformaciones serialización/deserialización (JSON parse/stringify).
- Manejo de errores de persistencia (try/catch alrededor de JSON.parse).
- **Funciones puras o cercanas a puras**, sin estado de React.

### Tipos (`[feature].types.ts`)

- Interfaces de las entidades del dominio.
- Enums y tipos derivados.
- Tipos compartidos por hook, service y componentes de la feature.

## Regla del localStorage

**Nunca acceder a `localStorage` directamente en componentes ni hooks.** Toda persistencia se encapsula en un service con funciones nombradas:

```ts
// src/features/todo-list/todo.service.ts
import type { TodoItem } from './todo.types'

const STORAGE_KEY = 'todos'

export const todoService = {
  getAll: (): TodoItem[] => {
    try {
      const raw = localStorage.getItem(STORAGE_KEY)
      return raw ? (JSON.parse(raw) as TodoItem[]) : []
    } catch {
      return []
    }
  },

  save: (todos: TodoItem[]): void => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  },

  clear: (): void => {
    localStorage.removeItem(STORAGE_KEY)
  },
}
```

## Estado Inicial desde el Service

El hook debe inicializar el estado **leyendo del service con una función lazy**, no en un `useEffect`. Esto evita un render inicial vacío seguido de un segundo render con los datos.

```ts
// src/features/todo-list/useTodos.ts
import { useState } from 'react'
import { todoService } from './todo.service'
import type { TodoItem } from './todo.types'

export function useTodos() {
  const [todos, setTodos] = useState<TodoItem[]>(() => todoService.getAll())
  // ...
}
```

## Persistencia Reactiva

Persistir el estado al service usando `useEffect` con el estado como dependencia:

```ts
import { useEffect } from 'react'

useEffect(() => {
  todoService.save(todos)
}, [todos])
```

Cualquier cambio en `todos` se persiste automáticamente. El componente no sabe nada de esto.

## Resumen del Flujo

```
Componente ── llama ──> Hook ── usa ──> Service ── lee/escribe ──> localStorage
     ▲                    │
     └── recibe datos ────┘
```

El componente nunca conoce al service. El service nunca conoce al componente.
