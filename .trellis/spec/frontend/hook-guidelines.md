# Hook Guidelines

> How hooks are used in this project.

---

## Overview

This repository uses Vue composables, not React-style data layers. Hooks are
primarily small utilities that wrap repeated UI behavior, cache helpers, and
table/form workflows.

Representative examples:

- `src/hooks/web/useTable.ts`
- `src/hooks/web/useDesign.ts`
- `src/hooks/event/useScrollTo.ts`
- `src/views/Login/components/useLogin.ts`

---

## Custom Hook Patterns

Use hooks when logic is reused across pages or when a page needs a clean
stateful helper that would otherwise bloat the SFC.

Patterns already in use:

- File name starts with `use` and exports one or more named functions.
- Hooks return plain objects of refs, computeds, and methods.
- Hooks keep registration APIs explicit when they coordinate with components.
  `useTable` returns `register`, `tableObject`, `methods`, and `tableMethods`.
- Hooks may define feature-local enums or interfaces next to the implementation,
  as `useLogin.ts` does.

Keep hooks side-effect free on import. Any DOM watchers, API calls, or event
listeners should happen only when the hook function is invoked.

---

## Data Fetching

There is no repository-wide query cache abstraction such as Vue Query. Current
server data is usually fetched directly inside pages or page-specific methods.

Current pattern:

- page SFC owns `loading`, `list`, `total`, and query params
- page calls a typed API module from `src/api/**`
- repeated table CRUD flow can be wrapped by `useTable`

Examples:

- `src/views/bpm/oa/leave/index.vue` performs `getList()` directly in the page
- `src/hooks/web/useTable.ts` centralizes pagination, delete, and export flows

Do not add a new data-fetching framework for a single feature. Follow direct
API calls unless a larger architectural migration is intentional.

---

## Naming Conventions

- Shared hooks go under `src/hooks/web/` or `src/hooks/event/`.
- Feature-local hooks live next to the feature when they are not reused outside
  that area, e.g. `src/views/Login/components/useLogin.ts`.
- Hook names should describe the behavior they expose: `useTable`,
  `useDesign`, `useMessage`, `useValidator`.
- Avoid default exports; named exports are the dominant style in current code.

When a hook wraps a component contract, keep the method names aligned with the
component vocabulary (`register`, `setProps`, `getSelections`, `validForm`).

---

## Common Mistakes

- Hiding one-off page logic in a shared hook too early.
- Putting backend request functions into hooks instead of `src/api/**`.
- Returning a large unstructured bag of values from a hook when a smaller API
  would be clearer.
- Introducing global mutable state in a hook when the state actually belongs in
  Pinia or in a single page component.
