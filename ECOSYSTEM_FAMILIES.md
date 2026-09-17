# Ecosystem Families

Many of Dependabot's ecosystems are **not independent** — they are different tools for the **same language**. Tools in the same family target the same dependencies, resolve from the same registries, and follow largely the **same business logic**.

Because of that, a bug or improvement is often **general to the whole family**, not specific to the one ecosystem where it was reported — even when the code is duplicated rather than literally shared.

> [!IMPORTANT]
> When you investigate or change one ecosystem, **consider its siblings in the same family**. If the root cause is general (dependency resolution, registry handling, version comparison, requirement parsing, error handling, etc.), the siblings likely have the same issue and should be updated too — each with its own tests. Some logic is shared in code and some is only conceptually the same; either way, check whether the siblings need the same change.

## The families

| Family | Ecosystems |
| --- | --- |
| **JavaScript / TypeScript** | `npm_and_yarn` (npm, Yarn, pnpm), `bun`, `deno` |
| **Python** | `python` (pip, pip-compile, pipenv, Poetry), `uv`, `conda` |
| **JVM** | `maven`, `gradle`, `sbt` |
| **Terraform / IaC** | `terraform`, `opentofu` |
| **Containers** | `docker`, `docker_compose`, `helm` |

Single-tool ecosystems (e.g. `bundler`, `cargo`, `go_modules`, `hex`, `nuget`, `pub`, `composer`, `elm`, `swift`, `github_actions`, `git_submodules`) have no sibling to mirror, but changes in `common/` still affect every ecosystem.

## How the change reaches siblings

Within a family, siblings relate in one of two ways. Both require you to **think about the whole family**; they differ only in how the fix gets there.

- **Shared in code** — some siblings reuse another's classes (e.g. `gradle`/`sbt` build on `maven`; `uv`/`conda` build on `python`; `helm`/`docker_compose` build on `docker`). The fix reaches them at runtime, but you must still run **their** test suites.
- **Duplicated logic** — some siblings reimplement the same behavior separately (e.g. `bun` largely mirrors `npm_and_yarn`; `opentofu` mirrors `terraform`). Fix every affected sibling. **Prefer consolidating the shared behavior into a common base** (in `common/` or the family's base ecosystem) when it's practical, so the fix lives in one place and improves stability. If extracting a base is not practical, apply the fix to each duplicate — but don't introduce *new* duplication just to match a sibling.

If a sibling intentionally differs, note why in the code/PR instead of forcing parity.

## Workflow

1. Identify the ecosystem and the class involved.
2. Decide whether the root cause is **ecosystem-specific** or **general to the family**.
3. If general, fix every affected sibling. Prefer sharing the logic through a common base when practical; otherwise fix each duplicate. Add tests either way.
4. Validate each affected ecosystem's test suite, not just the one you started from.
