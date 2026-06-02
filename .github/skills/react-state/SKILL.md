---
name: react-state
description: >-
  Activa al diseñar gestión de estado en React. Usar cuando: Context API,
  estado global, estado compartido entre componentes, prop drilling,
  decisión entre useState vs useReducer vs Zustand, estado del servidor.
---

# Skill: Gestión de Estado en React

Esta skill guía la decisión sobre **qué herramienta de gestión de estado usar** según el caso, evitando complejidad innecesaria.

## Árbol de Decisión Obligatorio

Antes de introducir cualquier solución de estado, recorrer este árbol de arriba hacia abajo y detenerse en la primera respuesta afirmativa:

```
¿El estado es local a un componente?
  └── SÍ → useState

¿El estado tiene lógica compleja o múltiples sub-estados relacionados?
  └── SÍ → useReducer (en el mismo hook de la feature)

¿El estado se necesita en múltiples features sin prop drilling?
  └── SÍ (2-4 componentes) → Context API
  └── SÍ (5+ componentes o actualizaciones frecuentes) → Zustand

¿El estado viene de una API externa?
  └── SÍ → TanStack Query (en proyectos con backend)
  └── NO hay backend → localStorage via service
```

**Regla:** no saltar pasos. No introducir Zustand "porque escalará". Empezar con `useState` y subir un nivel solo cuando el problema concreto lo justifique.

## useState

Para estado local a un único componente o a un único hook de feature.

```ts
const [count, setCount] = useState(0)
```

## useReducer

Cuando el estado tiene varios campos relacionados que cambian juntos, o cuando las transiciones de estado son complejas (ej: formulario con múltiples pasos, máquina de estados).

```ts
type FormState = { name: string; email: string; status: 'idle' | 'submitting' | 'error' }
type FormAction = { type: 'set_name'; value: string } | { type: 'submit' } | { type: 'error' }

function reducer(state: FormState, action: FormAction): FormState {
  switch (action.type) {
    case 'set_name': return { ...state, name: action.value }
    case 'submit': return { ...state, status: 'submitting' }
    case 'error': return { ...state, status: 'error' }
  }
}

const [state, dispatch] = useReducer(reducer, { name: '', email: '', status: 'idle' })
```

## Context API

Cuando un estado debe compartirse entre 2 y 4 componentes hermanos o anidados sin prop drilling excesivo.

### Reglas

- **Un Context por dominio**, no un Context global con todo dentro.
- **Separar Context de valor y Context de dispatch** para evitar re-renders innecesarios:
  - `CotizacionStateContext` (datos)
  - `CotizacionDispatchContext` (acciones)
- Siempre proveer un custom hook para consumir el Context:

```ts
import { createContext, useContext } from 'react'

const CotizacionContext = createContext<CotizacionContextValue | null>(null)

export const useCotizacionContext = () => {
  const ctx = useContext(CotizacionContext)
  if (!ctx) throw new Error('useCotizacionContext debe usarse dentro de CotizacionProvider')
  return ctx
}
```

Esto garantiza que un mal uso del Context falle ruidosamente en desarrollo en vez de propagar `undefined`.

## Zustand

Cuando el estado se necesita en 5+ componentes, o cuando las actualizaciones son frecuentes y se necesita evitar el costo de re-render que Context impone.

### Reglas

- **Un store por dominio/feature**, no un store global monolítico.
- Las **acciones se definen dentro del store**, no fuera:

```ts
import { create } from 'zustand'

interface CotizacionStore {
  cotizaciones: Cotizacion[]
  addCotizacion: (cliente: string, monto: number) => void
  removeCotizacion: (id: string) => void
}

export const useCotizacionStore = create<CotizacionStore>((set) => ({
  cotizaciones: [],
  addCotizacion: (cliente, monto) => set((state) => ({
    cotizaciones: [
      ...state.cotizaciones,
      { id: crypto.randomUUID(), cliente, monto, estado: 'enviada', fechaEnvio: new Date().toISOString() },
    ],
  })),
  removeCotizacion: (id) => set((state) => ({ cotizaciones: state.cotizaciones.filter((c) => c.id !== id) })),
}))
```

- Usar `subscribeWithSelector` cuando se necesiten suscripciones selectivas a una parte del store.
- **Nunca mutar el estado directamente**; siempre usar el setter (`set`).

## TanStack Query

Cuando el estado viene de una API HTTP y se necesitan: caché, refetch, optimistic updates, deduplicación de requests.

No introducir TanStack Query si el proyecto no tiene backend.

## Recomendación para Esta POC (localStorage sin backend)

Para proyectos sin backend que persisten en `localStorage`:

- **Usar `useState` + `useEffect` + service pattern es suficiente.**
- **No introducir Zustand ni Context** a menos que se demuestre prop drilling real (más de 3 niveles de profundidad pasando los mismos props).
- La persistencia se maneja con el service de la feature, no con una librería de estado.

Mantener simple lo simple.
