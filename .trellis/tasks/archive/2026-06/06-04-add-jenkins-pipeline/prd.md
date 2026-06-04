# Add Jenkins pipeline for frontend deploy

## Goal

Add a Jenkins pipeline file for this frontend project so Jenkins can install
dependencies, build the selected frontend artifact, and deploy the build output
to a server directory that is already mounted or served by an existing
containerized runtime on the target server.

## What I already know

- The user will deploy frontend build artifacts to a specific server path.
- The user will reuse an existing server-side container and does not want a new
  nginx container in this project.
- The repository already exposes build scripts:
  - `pnpm build:test`
  - `pnpm build:stage`
  - `pnpm build:prod`
- The corresponding output directories are configured via env files:
  - `dist-test`
  - `dist-stage`
  - `dist-prod`
- There is currently no `Jenkinsfile` in the repository.

## Assumptions (temporary)

- Jenkins runs on a node that already has Node.js and can run `pnpm`.
- The deployment model is local copy/sync from Jenkins workspace to a target
  directory on the same machine or a mounted filesystem path.
- The exact deploy path should be parameterized instead of hard-coded in the
  repository.

## Open Questions

- None blocking for the initial Jenkinsfile because deploy path and optional
  post-deploy command can be parameters.

## Requirements (evolving)

- Add a root-level `Jenkinsfile`.
- Allow choosing build environment (`test`, `stage`, `prod`).
- Install dependencies and run the matching build command.
- Deploy built artifacts to a configurable target directory.
- Support an optional post-deploy shell command for restarting or reloading the
  existing server/container integration if needed.
- Do not define or require an nginx container in the pipeline.

## Acceptance Criteria (evolving)

- [ ] Repository contains a declarative Jenkins pipeline file.
- [ ] Pipeline supports environment selection and deploy path parameterization.
- [ ] Pipeline builds the correct output directory for the selected mode.
- [ ] Pipeline can copy/sync artifacts into the specified deployment directory.
- [ ] Pipeline exposes a safe place for an optional post-deploy command.

## Definition of Done (team quality bar)

- Jenkinsfile matches existing repo scripts and output directories
- Deployment path is parameterized, not hard-coded
- Assumptions and operational knobs are clear from the pipeline itself

## Out of Scope (explicit)

- Provisioning Jenkins itself
- Provisioning Node.js or pnpm on the Jenkins agent
- Creating docker-compose, nginx, or container runtime definitions
- SSH-based multi-host release orchestration

## Technical Notes

- Build scripts and output directories were inspected from `package.json`,
  `.env.prod`, `.env.stage`, and `.env.test`.
- The repo currently has no deployment automation files to extend, so this task
  will add a new root-level `Jenkinsfile`.
