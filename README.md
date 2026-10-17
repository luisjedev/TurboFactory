# App Factory

![Turborepo](https://img.shields.io/badge/Turborepo-2.x-black)
![pnpm](https://img.shields.io/badge/pnpm-11-orange)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-blue)
![React](https://img.shields.io/badge/React-19-61DAFB)
![StyleX](https://img.shields.io/badge/StyleX-design%20system-ff69b4)
![License: MIT](https://img.shields.io/badge/License-MIT-green)

**Una fábrica de aplicaciones en un solo monorepo.** App Factory es un [Turborepo](https://turborepo.dev) para crear y hacer evolucionar varias apps a la vez sin reinventar la base en cada una: paquetes comunes, un sistema de diseño compartido y un flujo de trabajo interno de specs, issues y tareas gobernado por skills para agentes de IA.

La idea es sencilla: cada aplicación nueva nace sobre la misma cimentación (TypeScript estricto, React, StyleX, lint y tipos compartidos) y el trabajo se planifica, implementa, revisa y cierra dentro del propio repo, con las issues en Markdown como fuente de verdad.

## Cómo funciona

```text
Spec / plan ──> Issues en Markdown ──> Skills de IA ──> Código
 (plans/)      (backlog → in-progress   (.agents/skills)  (apps/)
                → in-review → done)          │
                                             └── consume ──> packages/*
                                                           (UI, StyleX, configs)
```

1. **Se planea.** Las specs y planes viven en `apps/issues-tracker/plans/` y se trocean en issues.
2. **Se trabaja con issues versionadas.** Cada issue es un archivo Markdown en `apps/issues-tracker/issues/` que se mueve entre `backlog`, `in-progress`, `in-review` y `done`.
3. **Las skills gobiernan el flujo.** En `.agents/skills/` hay skills para crear issues, planificar, implementar, revisar código y cerrar trabajo. Cualquier agente compatible las descubre y las aplica.
4. **Las apps consumen paquetes, no se copian entre sí.** Todo lo reutilizable vive en `packages/` con API pública; ninguna app importa archivos internos de otra.

## Aplicaciones

| App | Stack | Puerto | Qué es |
|---|---|---|---|
| [`dashboard`](apps/dashboard) | Next.js (App Router) | 3000 | Puerta de entrada local a la fábrica y a sus aplicaciones |
| [`ui-catalog`](apps/ui-catalog) | Vite + React | 3001 | Catálogo visual del sistema de diseño `@repo/ui` |
| [`issues-tracker`](apps/issues-tracker) | Vite + React | 3002 | Tablero que lee las issues y planes en Markdown del repo |

Cada aplicación se puede desarrollar, comprobar, compilar y desplegar de forma independiente.

## Sistema de diseño y paquetes compartidos

| Paquete | Contenido |
|---|---|
| [`@repo/ui`](packages/ui) | Componentes (Button, Card, Dialog, Input, Badge, Alert…), tokens, temas y reset con StyleX |
| [`@repo/stylex-config`](packages/stylex-config) | Adaptadores de compilación StyleX para Next.js (Babel/PostCSS) y Vite |
| [`@repo/eslint-config`](packages/eslint-config) | Presets comunes de ESLint para cada tipo de workspace |
| [`@repo/typescript-config`](packages/typescript-config) | Presets estrictos de TypeScript (base, Next.js, Vite, librerías React) |

Las apps no duplican opciones del compilador ni rutas internas de los paquetes: consumen solo sus interfaces públicas.

## Requisitos

- Node.js `>=24.11.0 <25` (ver [`.nvmrc`](.nvmrc))
- pnpm `11.24.0` (el repo usa exclusivamente pnpm)

```bash
pnpm install
```

## Desarrollo

Todo el monorepo a la vez:

```bash
pnpm dev
```

O una sola aplicación:

```bash
pnpm --filter dashboard dev       # http://localhost:3000
pnpm --filter ui-catalog dev      # http://localhost:3001
pnpm --filter issues-tracker dev  # http://localhost:3002
```

## Verificación

```bash
pnpm lint
pnpm check-types
pnpm build
```

Turbo ejecuta cada tarea solo en los workspaces que la declaran, respetando las dependencias entre paquetes y con caché de tareas.

## Añadir una aplicación nueva

1. Crea la carpeta en `apps/<nombre>` consumiendo `@repo/ui`, `@repo/stylex-config`, `@repo/eslint-config` y `@repo/typescript-config`.
2. Escribe su spec en `apps/issues-tracker/plans/` y deriva las issues.
3. Deja que las skills de `.agents/skills/` gobiernen implementación, revisión y cierre.

Las reglas completas de trabajo del monorepo (arquitectura, límites entre apps, calidad) están en [`AGENTS.md`](AGENTS.md).

## Licencia

[MIT](LICENSE) — Copyright (c) 2026 luisjedev
