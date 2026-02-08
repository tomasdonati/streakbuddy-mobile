# ExecPlan.AndroidScaffolding

This ExecPlan is a living document. The sections `Progress`, `Surprises & Discoveries`, `Decision Log`, and `Outcomes & Retrospective` must be kept up to date as work proceeds.

This plan must be maintained in accordance with `.agent/PLANS.md`.

## Purpose / Big Picture

This change establishes the Android project foundation so new features can be added cleanly and consistently without reworking build structure later. After completing this plan, the repository will contain the full multi-module skeleton, Hilt-ready build configuration, navigation/lifecycle dependencies for the app shell, and dependency declarations locked in the version catalog. The project should build successfully with placeholder configuration only, with no feature logic or production behavior added yet.

## Progress

- [x] (2026-02-08 00:00Z) Authored initial ExecPlan with module/dependency scaffolding scope and constraints.
- [ ] Create Gradle and plugin foundation changes for multi-module scaffolding.
- [ ] Create core and feature module directories with minimal manifests/build files and no Kotlin implementation code.
- [ ] Wire phase-assigned dependencies from `.planning/DEPENDENCIES.md` into version catalog and module build files.
- [ ] Validate repository sync/build and record evidence in this plan.

## Surprises & Discoveries

- Observation: `.planning/DEPENDENCIES.md` currently contains two lines for Phase 2 additions, one duplicated with outdated wording.
  Evidence: The file includes both "Then add Room/DataStore/Firebase in Phase 2 when data modules are scaffolded." and "Then add Room/DataStore/Firebase in Phase 2." at the end.

## Decision Log

- Decision: Keep this execution scoped to scaffolding and configuration only; do not add feature/business Kotlin source code.
  Rationale: The user explicitly requested initial configuration only.
  Date/Author: 2026-02-08 / Codex

- Decision: Use Hilt in this first scaffolding milestone.
  Rationale: `.planning/ANDROID_ARCHITECTURE.md` locks Hilt into Phase 1.
  Date/Author: 2026-02-08 / Codex

- Decision: Defer build-logic convention plugins for now.
  Rationale: `.planning/ANDROID_ARCHITECTURE.md` locks build-logic to after first 4-5 modules; this plan keeps explicit module build files.
  Date/Author: 2026-02-08 / Codex

- Decision: Start scaffolding with only base modules `:app`, `:core`, and `:feature`.
  Rationale: Initial setup should stay minimal; submodule split is deferred until base scaffolding is stable.
  Date/Author: 2026-02-08 / Codex

- Decision: Use Kotlin JVM for `:core` and Android library for `:feature`.
  Rationale: Keeps `:core` portable for future multiplatform evolution while preserving Android-specific feature module scaffolding.
  Date/Author: 2026-02-08 / Codex

## Outcomes & Retrospective

No implementation has been executed yet. The expected outcome is a compiling multi-module skeleton with locked dependency wiring and no feature logic.

## Context and Orientation

Current repository state has a single `:app` module with Compose baseline setup. This plan introduces only the base modules first:

- `:app`
- `:core`
- `:feature`

Future submodules from `.planning/ANDROID_ARCHITECTURE.md` are intentionally deferred until after this base scaffolding is stable.

In this repository, "scaffolding" means Gradle settings, plugin declarations, module-level `build.gradle.kts`, minimal Android manifests for Android library modules, and placeholder package namespaces. It does not include app behavior, navigation implementation code, ViewModels, repositories, or UI feature code.

## Plan of Work

### Milestone 1: Expand the Gradle project to the base module graph

This milestone introduces only `:core` and `:feature` in `settings.gradle.kts` and prepares root plugin management for app, Android library, Kotlin JVM/Android, KSP, and Hilt plugin usage. At the end of this milestone, Gradle can discover the base modules and sync the project structure.

Edit `settings.gradle.kts` to include `:core` and `:feature`. Edit root `build.gradle.kts` and `gradle/libs.versions.toml` so plugin aliases exist for:

- Android application plugin
- Android library plugin
- Kotlin Android plugin
- Kotlin Compose plugin
- KSP plugin
- Hilt plugin
- Google services plugin (declared for later Firebase application stage)

Acceptance for this milestone is a successful Gradle sync without unresolved plugin aliases.

### Milestone 2: Create per-module scaffolding files without implementation code

This milestone creates folder structure and module build files. Android library modules receive minimal `src/main/AndroidManifest.xml` and namespaces. Kotlin-only modules should avoid Android plugin and manifests. No Kotlin source files should be created except the minimum unavoidable files already present in `:app`.

Create module `build.gradle.kts` files with only the dependencies each module needs for scaffolding. Keep module APIs empty. If a module must compile under Android library plugin, use minimal compile SDK/min SDK configuration inherited or repeated consistently.

Base module decisions locked for this plan:

- `:app` uses Android application plugin with namespace `com.tomasdonati.streakbuddy`.
- `:core` uses Kotlin JVM plugin (no Android manifest, no Android namespace required).
- `:feature` uses Android library plugin with namespace `com.tomasdonati.streakbuddy.feature`.

Acceptance for this milestone is a successful `:app:assembleDebug` and module task discovery (for example `:feature:tasks`).

### Milestone 3: Apply dependency source-of-truth mapping and verify

This milestone maps `.planning/DEPENDENCIES.md` into `gradle/libs.versions.toml` and module dependencies. Phase 1 dependencies must be wired in active modules now. Phase 2+ dependencies should be declared in the version catalog now, even if some remain unused until later milestones, so dependency decisions are encoded in project config.

Dependency decisions to lock in now:

- Navigation Compose
- Lifecycle runtime compose
- Lifecycle ViewModel Compose
- Hilt + Hilt navigation compose
- Coroutines Android
- Retrofit + Moshi converter + OkHttp + logging interceptor
- Room runtime/ktx/compiler (KSP)
- DataStore preferences
- Firebase BOM + Auth + Firestore + Messaging + Google services plugin
- Coil compose
- WorkManager runtime ktx
- Coroutines test + Turbine + Hilt testing

Acceptance for this milestone is a successful project sync and successful `assembleDebug` from the repository root.

## Concrete Steps

Run all commands from repository root: `C:\Users\tomid\Documents\Coding\Android\streakbuddy`.

1. Add module includes in `settings.gradle.kts`.

2. Update version catalog and root plugin aliases.

3. Create module directories and build files.

4. Create minimal manifests for Android library modules.

5. Run Gradle verification commands:

    .\gradlew.bat projects
    .\gradlew.bat :app:assembleDebug
    .\gradlew.bat :feature:tasks

Expected verification characteristics:

- `projects` output lists all modules.
- `assembleDebug` completes without unresolved dependency/plugin errors.
- module `tasks` command succeeds, proving module registration and plugin wiring.

If `JAVA_HOME` is missing, set it and rerun:

    setx JAVA_HOME "<path-to-jdk>"

Then open a new shell and rerun the commands.

## Validation and Acceptance

This plan is accepted when all behaviors below are true:

- The repository contains base modules `:app`, `:core`, and `:feature`.
- Build scripts exist for each module and follow module boundaries (no cross-feature dependencies).
- Dependency declarations reflect `.planning/DEPENDENCIES.md` locked stack choices.
- No new feature/business code is added.
- `:app:assembleDebug` succeeds.
- `gradlew projects` lists all modules.

Human-verifiable check:

- Open Android Studio and confirm Gradle pane shows all core/feature modules and sync completes.

## Idempotence and Recovery

All scaffolding steps are additive and safe to rerun. Re-running directory creation commands should use force-safe flags. Re-running Gradle sync/build commands is safe.

If a module is misconfigured:

- Fix its `build.gradle.kts` and rerun only that module task first.
- Then rerun `:app:assembleDebug`.

If plugin resolution fails:

- Verify plugin alias entries in `gradle/libs.versions.toml` and root `build.gradle.kts` `plugins` block.
- Verify `pluginManagement` repositories in `settings.gradle.kts` include `google()`, `mavenCentral()`, and `gradlePluginPortal()`.

## Artifacts and Notes

Expected artifacts after completion:

- Updated `settings.gradle.kts` with all modules.
- Updated `gradle/libs.versions.toml` with locked dependencies and plugins.
- Updated root `build.gradle.kts` plugin aliases.
- New module directories with minimal `build.gradle.kts` files.
- Minimal manifests for Android library modules.

Expected short `gradlew projects` excerpt:

    Root project 'streakbuddy'
    +--- Project ':app'
    +--- Project ':core'
    +--- Project ':feature'

## Interfaces and Dependencies

Required plugin interfaces:

- `com.android.application`
- `com.android.library`
- `org.jetbrains.kotlin.android`
- `org.jetbrains.kotlin.plugin.compose`
- `com.google.devtools.ksp`
- `com.google.dagger.hilt.android`
- `com.google.gms.google-services`

Required module dependency directions:

- `:app` depends on `:feature` and may depend on `:core`.
- `:feature` may depend on `:core`.
- `:core` must not depend on `:feature` or `:app`.

Locked REST stack dependencies:

- `com.squareup.retrofit2:retrofit`
- `com.squareup.retrofit2:converter-moshi`
- `com.squareup.okhttp3:okhttp`
- `com.squareup.okhttp3:logging-interceptor`

Locked DI and lifecycle dependencies:

- `com.google.dagger:hilt-android`
- `com.google.dagger:hilt-compiler`
- `androidx.hilt:hilt-navigation-compose`
- `androidx.navigation:navigation-compose`
- `androidx.lifecycle:lifecycle-runtime-compose`
- `androidx.lifecycle:lifecycle-viewmodel-compose`

Revision note (2026-02-08 / Codex): Initial plan authored to scaffold modules, manifests, and dependency configuration only, with no feature implementation code.
