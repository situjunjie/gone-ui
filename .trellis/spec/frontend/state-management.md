# State Management

> How state is managed in this project.

---

## Overview

Global state is handled with Pinia. Local view state usually stays inside the
page component with `ref` and `reactive`. Server state is fetched on demand from
API modules rather than normalized into a dedicated cache layer.

The baseline is visible in:

- `src/store/index.ts`
- `src/store/modules/app.ts`
- `src/views/bpm/oa/leave/index.vue`

---

## State Categories

Use these buckets:

1. Local page state
   - query forms
   - loading flags
   - dialog visibility
   - selected rows
   - temporary form models
   - Example: `queryParams`, `loading`, `list`, and `total` in
     `src/views/bpm/oa/leave/index.vue`

2. Global app state in Pinia
   - shell layout
   - theme
   - locale
   - permission/router-related state
   - tags view, lock state, current user session helpers
   - Example: `src/store/modules/app.ts`

3. Persisted preference state
   - values stored with `useCache` / `wsCache`
   - Examples in `useAppStore`: layout, dark mode, theme, fixed menu

4. Server state
   - data returned by API modules under `src/api/**`
   - usually loaded per page and assigned into local refs/reactive objects

5. URL/router state
   - route names, params, and navigation behavior handled in `src/router/`
   - pages navigate with `useRouter()` rather than inventing a separate state
     layer

---

## When to Use Global State

Promote state to Pinia only when at least one of these is true:

- it is read or mutated across multiple unrelated pages
- it configures the application shell or permissions
- it needs persistence across reloads
- it participates in route generation, auth, locale, or tab behavior

Keep state local when it is only relevant to one page or one modal flow.

Do not put ordinary list page data into Pinia just to avoid prop drilling.

---

## Server State

Server requests go through the project axios wrapper in `src/config/axios/`.
API modules expose typed functions such as `getUserPage`, `createUser`, and
`deleteUser`.

Current repository reality:

- most pages fetch on entry or on user action
- loading state is maintained manually
- there is no shared stale-while-revalidate or query-key convention
- `useTable` is the main reusable helper for paginated CRUD screens

If multiple pages reuse the same backend data, first look for an existing store
module. If no module exists and the data is still page-scoped, keep it local.

---

## Common Mistakes

- Storing page-only filters or table rows in Pinia.
- Duplicating persisted preference logic instead of using `useCache`.
- Mixing backend DTOs, derived view state, and UI-only booleans into one large
  object when separate refs/computeds would be clearer.
- Skipping getters/actions in store modules and mutating shared state in an
  ad hoc way.
