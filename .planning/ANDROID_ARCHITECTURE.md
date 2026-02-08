# Streakbuddy Architecture Source of Truth

This document defines the foundational Android architecture for `streakbuddy` and is the implementation reference for the project structure.

It complements product planning files:
- `ProjectPlanning.Shared.md` for platform-agnostic behavior.
- `ProjectPlanning.Android.md` for Android-specific feature details.

If there is a conflict on architecture implementation details, this file is the source of truth.

## Why This Architecture

The MVP (`0.1.0`) needs fast iteration without creating future migration pain. We will use a NIA-inspired modular architecture with strict dependency direction, MVVM + UDF for UI state, and backend-authoritative data flow with local cache.

Goals:
- Keep feature code isolated and easy to reason about.
- Keep shared concerns in reusable core modules.
- Enable incremental delivery (vertical slices) while preserving clean boundaries.

## Core Principles

1. Dependency direction is always inward.
- App and features depend on core modules.
- Core modules never depend on feature modules.

2. UI is Compose-first and unidirectional.
- Screens render immutable `UiState`.
- User actions are represented as events/actions handled by ViewModels.

3. Business rules live outside Composables.
- Composables do not call repositories directly.
- ViewModels orchestrate use cases/repositories.

4. Backend is authoritative.
- Writes require connectivity in MVP.
- Room cache is for offline read and fast startup.

5. Structured concurrency only.
- Use `viewModelScope`, lifecycle-aware collection, and injected dispatchers.
- Do not use `GlobalScope`.

## Target Module Graph

Initial foundational module set:
- `:app`
- `:core:model`
- `:core:common`
- `:core:designsystem`
- `:core:ui`
- `:core:data`
- `:core:database`
- `:core:datastore`
- `:core:network`
- `:core:testing`
- `:feature:home`
- `:feature:groups`
- `:feature:planner`
- `:feature:settings`
- `:feature:auth`

Dependency rules:
- `:app` depends on `:feature:*`, `:core:designsystem`, `:core:ui`, and DI wiring.
- `:feature:*` depends on `:core:model`, `:core:ui`, `:core:designsystem`, `:core:common`, and repository/use-case APIs.
- `:core:data` depends on `:core:model`, `:core:network`, `:core:database`, `:core:datastore`, `:core:common`.
- `:core:database`, `:core:network`, `:core:datastore` do not depend on feature modules.
- `:core:testing` can depend on any API layer needed for fakes, but production modules do not depend on `:core:testing`.

## Module Responsibilities

### `:app`
- Application class, root DI setup, and top-level navigation host.
- No feature business logic.

### `:core:model`
- Platform-agnostic models and enums (e.g., `User`, `ChallengeGroup`, `Membership`, `AttendanceEntry`, `Invite`).
- No Android framework dependencies.

### `:core:common`
- Shared utilities: result wrappers, dispatcher qualifiers, constants, time helpers.
- Contains abstractions used across layers.

### `:core:designsystem`
- Theme, typography, colors, reusable visual tokens/components.
- No feature-specific UI behavior.

### `:core:ui`
- Reusable UI primitives and cross-feature UI state models.
- Navigation-independent shared composables.

### `:core:data`
- Repository interfaces and implementations.
- Mapping between network/database models and domain models.
- Sync orchestration according to backend-authoritative rules.

### `:core:database`
- Room database, entities, DAOs, type converters.
- Local cache only; no business decisions.

### `:core:datastore`
- Preferences storage (`UserPreferences` and local flags).
- Strongly typed read/write APIs.

### `:core:network`
- Firebase/Firestore/Auth/FCM datasource wrappers.
- DTOs and remote mappers.

### `:core:testing`
- Shared test dispatchers, fake repositories, fixture builders.

### `:feature:*`
- Screen-level UI, ViewModels, feature-specific UI models and flows.
- Features never depend on other feature modules.

## Package Conventions

Base package: `com.tomasdonati.streakbuddy`

Module package examples:
- `com.tomasdonati.streakbuddy.core.model`
- `com.tomasdonati.streakbuddy.core.data`
- `com.tomasdonati.streakbuddy.feature.groups`

Per feature package shape:
- `ui/` composables and route entrypoints
- `presentation/` ViewModels, UiState, UiAction, effect streams
- `domain/` optional feature-scoped use cases when needed
- `data/` feature-specific adapters only if they cannot live in `:core:data`

## Layer Contracts

Data contract:
- Repositories expose suspend/Flow APIs in terms of `:core:model` models.
- Network and DB models are internal to their modules.

Presentation contract:
- Each screen exposes a single immutable `UiState` stream.
- User intent enters ViewModel via `onAction(action: ...)`.
- One-off events use `SharedFlow` or `Channel` and are consumed with lifecycle-aware effects.

Mapping contract:
- Domain model -> UI model mapping happens at ViewModel boundary.

## DI Plan

Preferred DI: Hilt (aligned with NIA-style Android architecture).

DI boundaries:
- `:app` owns Hilt application + root graph.
- Core modules provide bindings for data sources and repositories.
- Feature modules request dependencies via constructor injection.

Migration strategy:
- Introduce Hilt as part of foundation before feature implementation.
- Keep module APIs constructor-friendly to simplify test doubles.

## Coroutines & Dispatchers

Dispatcher rules:
- Main: UI state updates.
- IO: database + network operations.
- Default: CPU-heavy transforms if needed.

Scope rules:
- ViewModels launch work in `viewModelScope`.
- Long-lived sync/prefetch jobs use an injected application scope.

Flow rules:
- Use `stateIn` for ViewModel state exposure.
- Collect in Compose with `collectAsStateWithLifecycle`.
- Avoid duplicate collectors and non-lifecycle-aware collection.

## Navigation Foundation

Foundation nav model:
- Single-activity app.
- Root graph branches:
  - `auth` graph (unauthenticated)
  - `main` graph (authenticated)
- Main graph contains tabs defined in product planning: Home, Groups, Planner (WIP), Settings.
- “Log attendance” center action is a top-level route action, not a tab destination.

## Testing Foundation

Before feature scale-up, establish:
- Unit tests for repository implementations with fake data sources.
- ViewModel tests validating `UiState` transitions and action handling.
- Basic integration smoke test for app startup + root navigation state.

Testing tooling baseline:
- JUnit + coroutine test APIs.
- `:core:testing` reusable fakes and test dispatchers.

## Incremental Implementation Roadmap

### Phase 1: Foundation Bootstrapping
- Add core/feature modules to Gradle settings.
- Add shared build conventions (or minimal repeated config first, then build-logic module when module count grows).
- Introduce Hilt, base navigation shell, and empty feature entrypoints.

Exit criteria:
- App builds with modular structure.
- App can launch and switch between placeholder tab destinations provided by feature modules.

### Phase 2: Data Backbone
- Implement `:core:model` MVP entities.
- Add Room schema in `:core:database`.
- Add DataStore preferences in `:core:datastore`.
- Add remote abstractions in `:core:network`.
- Wire repository interfaces + initial implementations in `:core:data`.

Exit criteria:
- Repositories compile and are injectable.
- A simple read path works through repository -> ViewModel -> UI.

### Phase 3: First Vertical Slice
- Implement one end-to-end slice for `Groups` (e.g., list groups + empty state).
- Add tests for repository + ViewModel.

Exit criteria:
- User can open Groups tab and see deterministic state from repository.
- Unit tests pass for the slice.

## Architecture Guardrails

Do:
- Keep module APIs small and explicit.
- Prefer interface-first for volatile dependencies (network, storage).
- Keep feature logic inside feature modules.

Do not:
- Put Firebase/Room types in feature or UI modules.
- Share code by creating feature-to-feature dependencies.
- Let composables orchestrate side effects directly.

## Open Decisions To Confirm Early

1. DI rollout timing:
- Decided: add Hilt in Phase 1.
- Rationale: this project is moving to multi-module architecture; early DI avoids costly rewiring across `:app`, `:core:*`, and `:feature:*` modules.

2. Build convention plugins:
- Decided: create `build-logic` after the first 4 to 5 modules are in place.
- Rationale: this keeps early setup simpler while module boundaries are being validated, then introduces shared Gradle conventions once repeated patterns are concrete.

3. Domain/use-case module split:
- Decided: keep use cases inside feature modules and `:core:data` initially; introduce dedicated domain modules later only when growth justifies extraction.
- Rationale: avoids premature module complexity while preserving clean boundaries and a clear extraction path.

## Change Management

When architecture changes:
- Update this file first.
- If behavior scope changes, update `ProjectPlanning.Shared.md` first, then sync `ProjectPlanning.Android.md` and this file.
- Keep architecture decisions additive and migration-safe.


## Confirmed Decisions

1. Hilt is part of Phase 1 foundation work and must be in place before feature implementation begins.
2. Build convention plugins are deferred until after the first 4 to 5 modules are created.
3. Domain/use-case logic starts in `:feature:*` and `:core:data`, with dedicated domain modules deferred until needed.

