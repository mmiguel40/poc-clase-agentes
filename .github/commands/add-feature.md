---
name: add-feature
description: Agrega una nueva feature completa a un proyecto React existente siguiendo la arquitectura del proyecto
agent: react-developer
---

# Comando: Agregar Feature

Actúa como el agente `react-developer` definido en `.github/agents/react-developer.md`. El usuario quiere **agregar una nueva feature a un proyecto React ya existente**, respetando la arquitectura y convenciones del proyecto actual.

Feature solicitada: "{{feature}}"

## Protocolo

### 1. Reconocimiento del Proyecto Existente

Antes de planificar, **lee `src/features/`** para entender:

- El patrón de carpetas usado (feature-based, naming).
- Las convenciones de naming concretas aplicadas (PascalCase, camelCase, sufijos).
- Cómo están estructurados los hooks y services existentes.
- Si hay shared/utils que ya existen y se pueden reusar (`cn`, hooks genéricos, etc.).

Si el proyecto no tiene `src/features/`, infórmaselo al usuario y pregunta si debe inicializar la estructura.

### 2. Plan de la Feature

Genera un **mini-plan** de la nueva feature:

```markdown
📋 PLAN DE LA FEATURE: [nombre]

**Nuevas entidades / tipos:**
[interfaces TypeScript necesarias]

**Archivos a crear:**
- src/features/[feature]/[feature].types.ts
- src/features/[feature]/[feature].service.ts
- src/features/[feature]/use[Feature].ts
- src/features/[feature]/[Componentes].tsx

**Integración con features existentes:**
[describir si la nueva feature consume datos de otras, si comparte tipos, etc.; o "ninguna" si es autocontenida]

**Cambios necesarios en archivos existentes:**
[lista explícita de cualquier archivo existente que se deba tocar, ej: App.tsx para integrar la nueva ruta/componente]

**Capacidades nuevas:**
[qué podrá hacer el usuario en la app tras esta feature]

¿Confirmas este plan para proceder con la implementación?
```

### 3. Confirmación

Espera la confirmación explícita del usuario. Si sugiere cambios, actualiza el plan y vuelve a presentarlo.

### 4. Implementación

Sigue el **mismo patrón de las features existentes** del proyecto. Consistencia con lo que ya existe es prioridad sobre cualquier preferencia.

Orden de creación:

1. `[feature].types.ts`
2. `[feature].service.ts`
3. `use[Feature].ts`
4. Componentes (de más atómico a más contenedor)
5. Integración en `App.tsx` u otros archivos existentes que el plan haya marcado.

### 5. Validación

Ejecuta `npm run build`. Si hay errores TypeScript:

- Lee el error completo.
- Corrige.
- Vuelve a ejecutar.
- Repite hasta que pase.

Luego ejecuta `npm run dev` (en background) y verifica que la app sigue levantando.

### 6. Reporte

Reporta al usuario:

- Lista de archivos **creados**.
- Lista de archivos existentes **modificados** (con descripción breve del cambio).
- Resumen de las nuevas capacidades disponibles en la app.

## Restricciones

- **No cambies archivos de features existentes** salvo que sea estrictamente necesario para la integración de la nueva feature. Si tienes que tocar uno, **infórmalo al usuario en el plan antes de hacerlo**.
- **No introduzcas patrones nuevos** (Zustand, Context, etc.) si el proyecto no los usa ya. Si crees que se justifica, propónlo al usuario antes de implementarlo.
- **No actualices dependencias del `package.json` existentes**. Si necesitas una nueva dependencia, justifícala al usuario antes de instalarla.
