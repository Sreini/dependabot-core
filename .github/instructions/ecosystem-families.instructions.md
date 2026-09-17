---
applyTo: "*/lib/dependabot/**,*/helpers/**,updater/**"
---

# Cross-Ecosystem Fix Propagation

Many ecosystems are different tools for the **same language**, so they target the same dependencies, resolve from the same registries, and follow largely the **same business logic** — a bug or improvement is often **general to the whole family**, even when the code is duplicated rather than literally shared.

**When you change one ecosystem, consider its siblings.** If the root cause is general, they likely have the same issue and should be updated too — each with its own tests.

## Families

- **JavaScript / TypeScript**: `npm_and_yarn` (npm, Yarn, pnpm), `bun`, `deno`
- **Python**: `python` (pip, pip-compile, pipenv, Poetry), `uv`, `conda`
- **JVM**: `maven`, `gradle`, `sbt`
- **Terraform / IaC**: `terraform`, `opentofu`
- **Containers**: `docker`, `docker_compose`, `helm`

A change to `common/` affects **every** ecosystem.

## How the fix reaches siblings

- **Shared in code** (e.g. `gradle`/`sbt` → `maven`, `uv`/`conda` → `python`, `helm`/`docker_compose` → `docker`): the fix reaches them at runtime, but still run their test suites.
- **Duplicated logic** (e.g. `bun` ↔ `npm_and_yarn`, `opentofu` ↔ `terraform`): fix every affected sibling. Prefer consolidating the shared behavior into a common base when practical (so the fix lives in one place and improves stability); otherwise fix each duplicate. Don't introduce new duplication just to match a sibling.

If a sibling intentionally differs, note why in the code/PR instead of forcing parity.

See [`ECOSYSTEM_FAMILIES.md`](../../ECOSYSTEM_FAMILIES.md) for the full map and workflow.
