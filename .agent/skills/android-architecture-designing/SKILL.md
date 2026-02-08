---
name: android-architecture-designing
description: Guide for scaffolding Android apps with Now in Android architecture and modularization, SOLID-driven layering, MVVM, Compose-first UI, and coroutine/dispatcher best practices. Use when defining project structure, module boundaries, data/domain/UI layers, navigation shells, DI setup, structured concurrency, or adapting architecture to app requirements.
---

# Android Architecture Designing

Use this skill to scaffold or refactor Android projects with a NIA-inspired architecture, deep SOLID principles, correct MVVM, Compose-first UI, and coroutine best practices.

## Workflow
1. Read requirements and constraints. Identify core features, data sources, offline rules, and navigation scope. Confirm any ambiguous requirements before structuring modules.
2. Choose modularization depth. For multiple features or long-lived projects, plan feature modules plus core modules. For small apps, start with app plus a minimal core set and leave seams to split later.
3. Draft the module graph and dependency rules. Use `references/nia-modularization.md` when you need a template or naming guidance.
4. Define layer contracts with SOLID. Put interfaces in stable layers, implementations in volatile layers. Keep dependencies pointing inward and inject via constructors.
5. Apply MVVM + UDF for each screen. Define `UiState`, `UiEvent` or `UiAction`, and ViewModel responsibilities. Use `references/compose-mvvm.md` for Compose-specific patterns.
6. Plan coroutine strategy. Specify scopes, cancellation, and dispatcher usage. Use `references/coroutines.md` for lifecycle safety and dispatcher guidance.
7. Plan data flow. Specify data sources, repository interfaces, mappers, caching, and sync constraints. Identify what must be online vs. cached.
8. Scaffold the project. Create modules, base packages, DI setup, navigation shell, and theme. Provide a minimal vertical slice for one feature to validate the structure.
9. Validate with tests. Add unit tests for use cases, repositories, and ViewModels using fakes.

## Output Expectations
- Provide module list with purpose and dependencies.
- Provide package layout per module.
- Provide key interfaces and classes to create first.
- Provide DI plan and navigation graph summary.
- Provide coroutine and dispatcher guidance tied to the app needs.
- Call out tradeoffs and decisions needing confirmation.

## Constraints
- Do not over-modularize for small scope.
- Preserve existing structure when refactoring; propose incremental steps.
- Prefer Compose-first UI with unidirectional data flow.
- Avoid `GlobalScope`; use structured concurrency and lifecycle-aware scopes.