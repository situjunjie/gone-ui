# Component Guidelines

> How components are built in this project.

---

## Overview

This codebase is built around Vue Single File Components with
`<script setup lang="ts">`. Most business pages compose Element Plus widgets,
project wrapper components, and a small amount of page-local logic.

Prefer the existing patterns already visible in:

- `src/views/Login/Login.vue`
- `src/components/Dialog/src/Dialog.vue`
- `src/views/bpm/oa/leave/index.vue`

---

## Component Structure

Default structure for new components:

1. `<template>` first
2. `<script lang="ts" setup>` with typed imports and `defineOptions`
3. scoped `<style lang="scss">` for local styles
4. optional non-scoped style block only when overriding Element Plus or another
   global class contract

Conventions already in use:

- Add `defineOptions({ name: 'ComponentName' })` for devtools/debugging.
- Keep page-local state near the component with `ref`, `reactive`, and
  `computed`.
- Extract repeated stateful logic into a composable instead of hiding it in a
  very large SFC. `src/views/Login/components/useLogin.ts` is the local example.
- For reusable components, expose slots rather than hard-coding every variant.
  `src/components/Dialog/src/Dialog.vue` uses `title` and `footer` slots.

---

## Props Conventions

There are two accepted prop styles in this repository:

1. `defineProps` / `defineEmits` inside SFCs for ordinary page or feature
   components
2. `propTypes` from `@/utils/propTypes` for reusable infrastructure components
   that need Vue-style runtime prop declarations

Current examples:

- `src/components/Dialog/src/Dialog.vue` uses `propTypes.bool.def(false)`,
  `propTypes.string.def('Dialog')`, and `defineEmits(['update:modelValue'])`
- business pages like `src/views/Login/Login.vue` often take no explicit props
  and instead compose store/hook state

Rules:

- Type every prop and emit contract.
- Use `update:modelValue` for two-way bindings when wrapping Element Plus style
  controls.
- Prefer explicit default values for reusable components.
- Avoid broad `Record<string, any>` props unless the component is intentionally
  a pass-through wrapper.

---

## Styling Patterns

This project mixes UnoCSS utility classes in templates with SCSS for structural
or theme-aware styling.

Use this split:

- Use UnoCSS utility classes for layout, spacing, sizing, flex/grid, and simple
  visual adjustments. `src/views/Login/Login.vue` is representative.
- Use `lang="scss" scoped` for component-specific selectors and nested states.
- Use a non-scoped style block only when targeting global library selectors,
  such as Element Plus internals in `src/components/Dialog/src/Dialog.vue`.
- When a component needs namespaced class names, derive them from
  `useDesign().getPrefixCls(...)` instead of inventing ad hoc prefixes.

Do not introduce a second styling system for new work.

---

## Accessibility

Accessibility standards are lightweight in the current codebase, but new
components should preserve the semantics already present:

- Keep native or Element Plus form labels wired through `el-form-item`.
- Provide `alt` text for meaningful images; decorative images can use empty
  `alt=""`, as seen in the login page.
- Prefer project wrapper components and Element Plus primitives over raw clickable
  `div`s.
- Preserve keyboard submit behavior where forms already use
  `@keyup.enter="handleQuery"` or equivalent.
- For dialogs and overlays, prefer `ElDialog` and `ElScrollbar` wrappers instead
  of custom focus management from scratch.

---

## Common Mistakes

- Rebuilding existing wrappers instead of using `ContentWrap`, `Pagination`,
  `Dialog`, `Icon`, or other shared components.
- Mixing page business logic into generic components until they stop being
  reusable.
- Writing global CSS when a scoped block or utility classes would have been
  enough.
- Skipping `defineOptions({ name })` on non-trivial SFCs, which makes debugging
  route trees and keep-alive behavior harder.
