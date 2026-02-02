# Cursor project context

Project-specific notes for AI assistance in this codebase.

## Stack

- **Framework:** Astro 5
- **UI:** Vue 3, Tailwind CSS 4
- **Content:** MDX
- **Deploy:** Vercel
- **Package manager:** Yarn

## Structure

- `src/pages/` — Astro/MDX pages; file names become routes
- `src/components/` — Astro, Vue, or other framework components
- `src/styles/` — Global and shared styles (e.g. `global.css`)
- `public/` — Static assets (favicon, images, etc.)

## Conventions

- Use **functional React** when adding React code (no class components).
- Prefer **Vue 3 Composition API** for Vue components.
- Stick to the task at hand; ask before changing unrelated code or fixing unrelated issues.

## Commands

| Command        | Action                          |
|----------------|---------------------------------|
| `yarn install` | Install dependencies            |
| `yarn dev`     | Dev server at `localhost:4321`  |
| `yarn build`   | Production build to `./dist/`   |
| `yarn preview` | Preview production build locally |

## Config

- `astro.config.mjs` — Astro and integrations
- `tsconfig.json` — TypeScript
- `.vscode/launch.json` — Debug config
