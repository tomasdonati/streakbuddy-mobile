# NIA-Inspired Modularization Template

Use as a starting point, not a hard rule.

## Typical module set
- :app - app entry, navigation host, DI wiring.
- :core:common - shared utilities, result wrappers, coroutine helpers.
- :core:designsystem - Compose theming, typography, components.
- :core:model - domain models used across layers.
- :core:data - repository interfaces and orchestration of sources.
- :core:database - Room entities, DAOs, mappers.
- :core:datastore - DataStore preferences and serializers.
- :core:network - Retrofit/OkHttp, DTOs, API services.
- :core:ui - shared UI components and UI models.
- :core:testing - test fakes and rules.
- :feature:<name> - feature UI, ViewModel, and feature-specific use cases.

## Dependency rules
- :app depends on :feature:* and UI-facing core modules.
- :feature:* depends on :core:model, :core:ui, and domain/use-case modules if separated.
- :core:data depends on :core:network, :core:database, :core:datastore, :core:model.
- :core:* modules do not depend on :feature:*.
- Avoid cross-feature dependencies; share via core modules.

## Build logic
- Add a build-logic included build when you have 3+ modules or need consistent configuration.
- Create convention plugins for Compose, Kotlin Android, Hilt, Room, and testing.

## Packaging
- Use package names aligned to module purpose, for example `com.company.app.core.data`.