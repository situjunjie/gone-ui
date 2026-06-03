# Directory Structure

> How frontend code is organized in this project.

---

## Overview

This repository is a single frontend app built with Vue 3, Vite, Element Plus,
Pinia, UnoCSS, and TypeScript. The top-level rule is:

- business pages live under `src/views/<domain>/`
- request wrappers and DTO types live under `src/api/<domain>/`
- reusable UI and behavior live under `src/components/`, `src/hooks/`, and
  `src/utils/`
- framework bootstrap stays in `src/plugins/`, `src/router/`, `src/store/`,
  and `src/config/`

Keep new code inside the existing domain tree instead of creating parallel
architectures.

---

## Directory Layout

```text
src/
├── api/                # backend API wrappers, grouped by business domain
├── assets/             # static images, audio, svg, map data
├── components/         # reusable cross-page components
├── config/             # axios and runtime config wrappers
├── directives/         # permission and other Vue directives
├── hooks/              # reusable composables; split by usage style
├── layout/             # application shell and layout-only components
├── locales/            # i18n messages
├── plugins/            # Vue plugin/bootstrap adapters
├── router/             # base router and static route modules
├── store/              # Pinia root and store modules
├── styles/             # global styles, SCSS variables, theme tokens
├── types/              # ambient and shared TS declarations
├── utils/              # framework-agnostic helpers and adapters
└── views/              # route pages, grouped by business domain
```

---

## Module Organization

Use the existing domain mirroring pattern.

- A page under `src/views/<domain>/...` should normally call APIs from
  `src/api/<domain>/...`.
- Shared page scaffolding belongs in `src/components/`, not copied into each
  domain. Examples already in use include `ContentWrap`, `Pagination`,
  `Dialog`, and `DictTag`.
- Layout-only widgets stay under `src/layout/components/`, even when they are
  components. They are tied to the shell, not to feature pages.
- Runtime integrations belong in `src/plugins/`. `src/main.ts` wires them in
  one place through `setupI18n`, `setupStore`, `setupElementPlus`,
  `setupFormCreate`, and `setupRouter`.
- Cross-cutting browser or UI helpers belong in `src/hooks/web/` and
  `src/hooks/event/`, not inside page SFCs, when they are reused.

When adding a new business area, follow the same split:

1. `src/api/<domain>/` for request functions and request/response interfaces
2. `src/views/<domain>/` for route pages and feature-local subcomponents
3. `src/store/modules/` only if the state is cross-page or app-level
4. `src/components/` only if the UI is reused beyond one feature

Do not create a separate `services/`, `composables/`, or `features/` root
unless the project is intentionally being restructured.

---

## Naming Conventions

- Vue SFC files are usually PascalCase for generic components
  (`src/views/Login/Login.vue`, `src/components/Dialog/src/Dialog.vue`) and
  lower-case feature paths for business pages
  (`src/views/bpm/oa/leave/index.vue`).
- Hook files use `useX.ts` naming (`src/hooks/web/useTable.ts`,
  `src/hooks/web/useDesign.ts`, `src/views/Login/components/useLogin.ts`).
- Store modules use lower-case file names that reflect the store name
  (`src/store/modules/app.ts`, `src/store/modules/permission.ts`).
- Types use `.d.ts` in `src/types/` for shared ambient declarations and inline
  `interface`/`type` exports in API or hook files when the type is feature
  specific.
- Route page entry files commonly use `index.vue`, `create.vue`, and
  `detail.vue` for CRUD-like flows.
- Use the `@/` alias for imports from `src/`; avoid long relative import
  chains across domains.

---

## Examples

- App bootstrap: `src/main.ts`
- Domain page + matching API pattern:
  `src/views/bpm/oa/leave/index.vue` and `src/api/system/user/index.ts`
- Reusable component package with implementation under `src/`:
  `src/components/Dialog/src/Dialog.vue`
- Hook organization: `src/hooks/web/useTable.ts`, `src/hooks/event/useScrollTo.ts`
- Shell-only components: `src/layout/components/`
