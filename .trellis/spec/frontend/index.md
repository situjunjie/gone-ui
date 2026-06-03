# Frontend Development Guidelines

> Project-specific frontend conventions for this Vue 3 admin application.

---

## Overview

This project is a single Vue 3 + Vite frontend application. The spec files in
this directory describe the code patterns that already exist in `src/` and
should be preserved by future AI sessions and contributors.

Read this index first, then open the guides relevant to the work you are about
to do.

---

## Pre-Development Checklist

- Confirm the work belongs to this frontend package.
- Read [Directory Structure](./directory-structure.md) for placement and naming.
- Read [Component Guidelines](./component-guidelines.md) before editing Vue SFCs
  or shared UI.
- Read [Hook Guidelines](./hook-guidelines.md) before adding composables.
- Read [State Management](./state-management.md) before changing stores or
  promoting state.
- Read [Type Safety](./type-safety.md) before adding DTOs, declarations, or
  shared types.
- Read [Quality Guidelines](./quality-guidelines.md) before final verification.
- Read shared thinking guides from `../guides/` when changes span layers or
  suggest duplication.

---

## Quality Check

Before calling frontend work done:

- Run `pnpm ts:check`.
- Run `pnpm lint:eslint`.
- Run `pnpm lint:style` when styles changed.
- Manually verify changed screens or flows because this repo does not currently
  expose a standard frontend test suite.

---

## Guidelines Index

| Guide | Description | Status |
|-------|-------------|--------|
| [Directory Structure](./directory-structure.md) | Module organization and file layout | Ready |
| [Component Guidelines](./component-guidelines.md) | Component patterns, props, composition | Ready |
| [Hook Guidelines](./hook-guidelines.md) | Custom hooks, data fetching patterns | Ready |
| [State Management](./state-management.md) | Local state, global state, server state | Ready |
| [Quality Guidelines](./quality-guidelines.md) | Code standards, verification, review checks | Ready |
| [Type Safety](./type-safety.md) | Type placement, validation, pragmatic TS rules | Ready |

---

## Notes

These guidelines document current repository reality, including pragmatic areas
such as limited automated testing and relaxed eslint rules. Improve the codebase
in tasks that are explicitly about cleanup; otherwise keep new work consistent
with the patterns described here.
