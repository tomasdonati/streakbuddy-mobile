---
name: android-implementation
description: Implement Android Kotlin/Compose code changes with strict project conventions. Use for any code change request. Enforce no non-null assertion operator (`!!`) outside tests, no business logic in composables, runCatching instead of try/catch, lifecycle-safe coroutines, and Hilt-friendly constructor injection.
---

# Android Implementation

Apply this skill for any code change in this repository.

Keep this skill implementation-focused. Do not expand architecture design here.
Do not rewrite planning documents unless explicitly requested.

## Non-Negotiable Rules

1. Forbid `!!` in non-test code.
- Allowed only when either condition is true:
  - The directory path contains `test` (case-insensitive).
  - The filename contains `test` (case-insensitive).
- Forbidden everywhere else.

2. Keep business logic out of Composables.
- Composables render UI state and emit user actions only.
- Place business rules in ViewModels/use-case/repository layers.
- This is mandatory because the project is expected to add iOS in the future.

3. Follow MVVM + UDF screen contract.
- Expose immutable `UiState` from ViewModel.
- Route user intent through a ViewModel action entry point (for example `onAction(...)`).
- Do not call repositories directly from composables.

4. Prefer `runCatching` over `try/catch`.
- When exception handling is required, use `runCatching { ... }`.
- Use `onFailure { ... }` and `getOrElse`, `getOrNull`, or `fold` as needed.
- Do not write raw `try/catch` unless explicitly requested.

5. Use structured concurrency.
- Use `viewModelScope` for screen-driven work.
- Do not use `GlobalScope`.
- Respect dispatcher intent (`Main`, `IO`, `Default`) when introducing coroutine work.

6. Keep dependency injection constructor-friendly.
- Prefer constructor injection patterns compatible with Hilt.
- Avoid service locator style access.

## Implementation Conventions

- Use Kotlin idioms and keep nullability explicit.
- Keep functions focused and small.
- Avoid hidden side effects in UI code.
- Keep naming clear and aligned with existing project conventions.

## Practical Workflow

1. Read the relevant files and nearby patterns.
2. Implement the smallest complete change.
3. Validate rules before finalizing:
- No forbidden `!!` usage.
- No raw `try/catch` introduced when `runCatching` is applicable.
- No business logic introduced inside composables.
4. Run quick checks when shell access is available:
- `rg \"!!\" app/src/main`
- `rg \"try\\s*\\{\" app/src/main`
5. Report what changed and how it was verified.

## Definition of Done

Default done criteria for this project:
- The user can run and manually verify the feature/behavior works as expected.

Automated tests are welcome but not required by default unless requested.
