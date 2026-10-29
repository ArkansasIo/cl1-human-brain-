# CL1 Neural Labs — Repository Review

**Review date:** 2026-10-28  
**Reviewed branch:** `main`  
**Repository:** `ArkansasIo/cl1-human-brain-`

## Current architecture observed

The repository contains a React/TypeScript + Vite frontend, a TypeScript Express/WebSocket simulator, a Python software-only simulator and optional virtual cluster server, a Rust backend area, MySQL-compatible schemas/datasets, research documentation, and automated tests. The latest recorded tree had 290 entries.

The root package exposes `dev`, `dev:vite`, `build`, `preview`, `clean`, `lint`, `test`, and `deploy:pages` scripts. The existing release-gates workflow runs npm installation, the repository static audit, repository contract tests, TypeScript checking, a Vite production build, Python tests, a simulator CLI smoke test, and sandbox guard tests.

## Latest CI evidence inspected

The Pages workflow run for commit `7bbd7d237614570aa4fd0433fd0a37ebd2cc7d7a` completed with failure on 2026-10-24. The logged job shows:

- `npm ci`: passed
- TypeScript validation (`npm run lint`): passed
- repository tests: **16 passed, 0 failed**
- production artifact build: passed
- Pages setup (`actions/configure-pages@v5`): failed with `Get Pages site failed ... Not Found`
- upload/deploy: skipped because the setup step failed

The build emitted a bundle-size warning for the JavaScript chunk (~1.27 MB minified); this was a warning, not the cause of the deployment failure.

## Immediate Pages resolution

The existing workflow already declares `pages: write` and `id-token: write`. The recorded failure indicates that a Pages site was not enabled/configured for the repository using GitHub Actions. In repository Settings → Pages, set **Build and deployment** to **GitHub Actions**, then rerun `.github/workflows/pages.yml` or trigger it with `workflow_dispatch`.

Tracking issue: https://github.com/ArkansasIo/cl1-human-brain-/issues/1

## Engineering gaps to address next

1. Add focused TypeScript unit tests for deterministic simulator behavior, stimulation channel validation, seed/reproducibility metadata, and reset/pause behavior.
2. Add live HTTP endpoint tests for `/_/config`, `/api/build`, `/api/status`, and invalid/valid `/api/stim` inputs using an ephemeral port and isolated server lifecycle.
3. Add WebSocket tests for reset payload, binary frame length and endianness, reconnect, pause/resume, backpressure, and malformed inbound messages.
4. Add frontend component/browser checks for boot-state labeling, module navigation, settings persistence, accessible controls, responsive layout, and failed API states.
5. Add import/export fixtures for malformed rows, non-finite signal values, timestamps, units, duplicate events, and provenance labels.
6. Split large UI bundles using route/component dynamic imports and measure before/after production artifact sizes.
7. Reconcile Python cross-platform CI: `python/pyproject.toml` configures pytest and the primary release gate uses pytest, while `.github/workflows/python-cross-platform.yml` still calls `unittest discover` despite pytest-style tests existing. Verify the platform workflow actually discovers and runs the intended tests.
8. Add proper shutdown hooks and injectable port/host settings to the TypeScript server to make integration testing reliable and avoid fixed-port collisions.

## Safety and scientific-integrity boundary

This is software for simulating and studying neural-culture computation, not a validated complete human-brain model. Synthetic activity must remain distinguishable from imported and verified biological recordings. A simulator endpoint does not provide physical CL1 device connectivity. Keep physical stimulation disabled unless official vendor interfaces, authorization, device-specific limits, independent hardware interlocks, audit logging, and appropriate research approvals have been validated separately.

## Change log

- Created GitHub issue #1 to track the Pages configuration prerequisite and document the successful build/test stages versus the failing deployment setup step.
- Added this review as a tracking document. No application source files or existing working implementation were modified during the review.
