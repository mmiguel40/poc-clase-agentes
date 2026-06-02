---
applyTo: "src/**/*.test.ts,src/**/*.test.tsx,src/**/*.spec.ts"
description: "Convenciones de testing para hooks y componentes React"
---

# Convenciones de Testing

Estas reglas aplican automáticamente a todo archivo de test (`.test.ts`, `.test.tsx`, `.spec.ts`).

## Stack

- **Test runner:** Vitest
- **Testing de componentes:** React Testing Library (`@testing-library/react`)
- **Aserciones DOM:** `@testing-library/jest-dom`
- **User events:** `@testing-library/user-event`

## Filosofía

Testear **desde la perspectiva del usuario final**: qué ve en pantalla, qué puede hacer, qué resultado obtiene. No testear detalles de implementación.

### Prohibido

- Testear nombres de variables internas, métodos privados, o el estado interno de un componente.
- Asertar sobre la forma exacta del estado de un hook si no es lo que el usuario percibe.
- Usar `container.querySelector` salvo último recurso.

### Preferido

- `screen.getByRole('button', { name: /agregar/i })`
- `screen.getByText(/sin tareas pendientes/i)`
- `screen.getByLabelText(/título de la tarea/i)`

Usar `getByTestId` **solo** cuando no exista un rol, texto o label semántico apropiado.

## Mocks de Persistencia

Todos los tests de hooks o componentes que usen persistencia (services con `localStorage`) deben mockear `localStorage` antes de cada test.

Ejemplo de setup en `vitest.setup.ts`:

```ts
beforeEach(() => {
  localStorage.clear()
})
```

Si el service tiene una capa propia (recomendado), mockear el service directamente con `vi.mock()`.

## Estructura de Cada Test

Cada bloque de test debe seguir **Arrange → Act → Assert** con comentarios explícitos:

```ts
it('debería marcar una cotización como aceptada al cambiar el estado', async () => {
  // Arrange: render del componente y datos iniciales
  const user = userEvent.setup()
  render(<CotizacionList />)

  // Act: el usuario interactúa
  await user.selectOptions(screen.getByLabelText(/estado/i), 'aceptada')

  // Assert: el resultado visible
  expect(screen.getByText(/aceptada/i)).toBeInTheDocument()
})
```

## Naming de Tests

Los nombres de los `describe` y `it` van **en español**, redactados como una frase descriptiva del comportamiento esperado:

- `describe('CotizacionList', ...)`
- `it('debería mostrar un mensaje cuando no hay cotizaciones', ...)`
- `it('no debería permitir guardar una cotización con monto cero o negativo', ...)`

## Cobertura Mínima

Por cada función pública (hook, service, componente con lógica), cubrir al menos:

1. **Happy path:** el escenario principal exitoso.
2. **Al menos 1 edge case:** entrada vacía, datos corruptos, error de red, lista sin elementos, valor límite, etc.

Cobertura total objetivo: razonable, no obsesiva. No perseguir 100% a costa de tests frágiles.
