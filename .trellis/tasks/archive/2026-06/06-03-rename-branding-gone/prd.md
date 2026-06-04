# Rename branding to GONE platform

## Goal

Replace user-visible branding that currently shows "芋道管理系统" or related
default tenant branding with the new product naming for this project:
short name `GONE`, full name `GONE一站式产研管理平台`.

## What I already know

- The user wants page-visible branding changed first.
- Browser title and loading title come from `VITE_APP_TITLE` in `.env` and
  `index.html`.
- Login-related defaults currently include `tenantName: '芋道源码'` in multiple
  components.
- There are many references to `yudao`, `iocoder`, and `doc.iocoder.cn` in
  README, comments, and helper links, but those are not all page branding.

## Assumptions (temporary)

- This task should prioritize user-visible page text over repository metadata or
  third-party reference links.

## Open Questions

- Confirm whether this change should be limited to page-visible branding, or if
  README/project metadata/external links should also be renamed in this task.

## Requirements (evolving)

- Replace visible app title branding with `GONE一站式产研管理平台`.
- Replace visible short-name branding where appropriate with `GONE`.
- Update login-related default tenant branding if it is visible in the UI.

## Acceptance Criteria (evolving)

- [ ] Browser title and loading title show the new product branding.
- [ ] Login-related visible branding no longer shows `芋道管理系统` or `芋道源码`.
- [ ] No unintended changes are made to unrelated docs or external links unless
      explicitly included in scope.

## Definition of Done (team quality bar)

- Relevant UI text updated consistently
- Typecheck / lint status preserved where practical for a copy-only change
- Scope kept limited to requested branding replacement

## Out of Scope (explicit)

- Renaming package metadata, repository URLs, or upstream documentation links
  unless explicitly requested
- Refactoring external vendor/demo references that are not part of visible page
  branding

## Technical Notes

- Inspected `.env`, `index.html`, `src/views/Login/components/LoginForm.vue`,
  `src/views/Login/components/MobileForm.vue`, and `src/views/Login/SocialLogin.vue`.
- `src/store/modules/app.ts` uses `import.meta.env.VITE_APP_TITLE`, so updating
  the env title propagates through app shell display.
