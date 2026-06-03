# Quality Guidelines

> Code quality standards for frontend development.

---

## Overview

Quality in this repository is enforced mostly through linting, TypeScript
checking, shared wrappers, and manual verification. The project currently has
no visible frontend unit-test workflow in `package.json`, so do not write spec
that assumes an existing test harness.

Primary quality levers in this repo:

- ESLint flat config in `eslint.config.mjs`
- formatting and line-length rules in `.editorconfig`
- style checks via `stylelint.config.js`
- type checks via `pnpm ts:check`
- application conventions encoded in shared wrappers and stores

---

## Forbidden Patterns

- Bypassing the axios wrapper in `src/config/axios/` for normal backend calls.
- Reimplementing common wrappers such as dialogs, pagination, icons, cache, or
  table CRUD behavior when an existing project helper already fits.
- Creating new global state for page-local concerns.
- Adding a new framework or architectural subsystem for a one-off page.
- Large cross-domain relative imports when `@/` alias should be used.

The repo already carries some technical debt. Follow the existing architecture
closely unless the task is explicitly a cleanup/refactor.

---

## Required Patterns

For normal frontend changes:

1. Use TypeScript and `script setup` for new Vue code.
2. Use the existing import alias and module boundaries.
3. Route backend access through `src/api/**` and `src/config/axios/`.
4. Keep business pages under `src/views/**` and shared UI under
   `src/components/**`.
5. Reuse Pinia stores, hooks, and wrappers before adding new abstractions.
6. Match existing naming and route/page file conventions.

---

## Testing Requirements

Repository reality as of June 3, 2026:

- there is no established frontend unit/integration test command in
  `package.json`
- the practical default check set is:
  - `pnpm lint:eslint`
  - `pnpm ts:check`
  - `pnpm lint:style` when styles were changed
  - manual verification for affected views and workflows

When a change is logic-heavy and isolated enough to benefit from tests, note the
gap explicitly. Do not claim automated coverage that the repo does not have.

---

## Code Review Checklist

Reviewers should check:

- Does the file live in the right domain or shared layer?
- Does the page use existing wrappers/components instead of duplicating them?
- Are API types and page usage aligned?
- Is state kept local unless it truly belongs in Pinia?
- Were i18n, permissions, and route names handled consistently where applicable?
- For style changes, are UnoCSS and SCSS used in the same way as nearby code?
- For infrastructure changes, did the author preserve current Vite/axios/store
  bootstrap patterns?
