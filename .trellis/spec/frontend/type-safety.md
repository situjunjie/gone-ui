# Type Safety

> Type safety patterns in this project.

---

## Overview

TypeScript is used across the app, but the repository favors pragmatic typing
over strict type maximalism. Many eslint restrictions on `any` and explicit
return types are intentionally disabled, so type safety comes mostly from local
discipline and keeping types close to the code that owns them.

Use the patterns already present in:

- `src/api/system/user/index.ts`
- `src/types/table.d.ts`
- `src/store/modules/app.ts`
- `src/views/Login/components/useLogin.ts`

---

## Type Organization

Keep types where they are consumed most often.

Current conventions:

- API request/response interfaces usually live next to the API module that owns
  them, as in `src/api/system/user/index.ts`.
- Shared UI and framework types live under `src/types/*.d.ts`.
- Store-specific state interfaces are declared inside the store module when they
  are not reused elsewhere (`AppState` in `src/store/modules/app.ts`).
- Hook-local interfaces and generics stay in the hook file (`ResponseType<T>`,
  `UseTableConfig<T>`, `TableObject<T>` in `src/hooks/web/useTable.ts`).

Prefer exporting named `interface` or `type` definitions over anonymous object
types when the shape crosses file boundaries.

---

## Validation

There is no runtime schema library such as Zod or Yup in the current frontend
stack.

Current validation mechanisms are:

- Element Plus form validation through component refs and `form.validate()`
- backend response codes handled in the axios wrapper
- targeted utility guards such as `isNumber` from `@/utils/is`
- Vue runtime prop validation via `vue-types` wrappers in reusable components

Do not introduce a new validation library for routine forms without an explicit
architecture decision.

---

## Common Patterns

Patterns already used successfully:

- Generic hooks for reusable workflows: `useTable<T = any>(...)`
- Typed store state interfaces plus typed getter return values
- Ambient shared helper types like `TableSetPropsType` in `src/types/table.d.ts`
- Union prop declarations through `propTypes.oneOfType([String, Number])`
- Enums for small finite UI states, e.g. `LoginStateEnum`

Prefer inference when Vue/TS already gives it to you, but annotate public
interfaces, API boundaries, and store state explicitly.

---

## Forbidden Patterns

- Duplicating the same DTO shape in pages, stores, and API files.
- Adding new untyped globals when a declaration file under `src/types/` should
  exist instead.
- Blanket `as any` casts to suppress errors that could be fixed by tightening
  the local type.
- Moving feature-specific types into global declaration files too early.

`any` is already present in the codebase and not globally banned, but new code
should use narrower types at module boundaries whenever the shape is known.
