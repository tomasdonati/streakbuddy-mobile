# Repository Guidelines

# ExecPlans
When writing complex features or significant refactors, use an ExecPlan (as described in .agent/PLANS.md) from design to implementation.

## Planning Workflow & Documentation
- Planning lives in `.planning/`.
- `.planning/ANDROID_ARCHITECTURE.md` is the source of truth for project architecture, module boundaries, layering, DI strategy, and implementation phases.
- `.planning/FEATURES.md` lists the main product features and their scope status (MVP vs deferred).
- `.planning/USER_NAVIGATION.md` defines platform-agnostic user navigation flows for both Android and iOS.
- When asked to help with planning or continue planning work, the LLM must read all files in `.planning/` before proposing or applying planning changes.

## Session Memory
- `SESSION.md` is the living memory for the current session.
- At the start of every new session, the LLM must read `SESSION.md` first.
- During the session, the LLM must update `SESSION.md` after every change and every decision.
- Keep session notes short and factual.
- At the end of the session, the LLM must replace detailed notes with a brief summary of completed work and pending items, then remove the rest.

## Project Structure & Module Organization
- `app/` is the only Gradle module; production code lives in `app/src/main/java/com/tomasdonati/streakbuddy`.
- UI resources are in `app/src/main/res` and themes live under `app/src/main/java/com/tomasdonati/streakbuddy/ui/theme`.
- Unit tests are in `app/src/test`, and instrumented tests are in `app/src/androidTest`.

## Build, Test, and Development Commands
Use the Gradle wrapper from the repo root (`gradlew.bat` on Windows):
- `./gradlew assembleDebug`: build the debug APK.
- `./gradlew installDebug`: install the debug build to a device or emulator.
- `./gradlew test`: run JVM unit tests in `app/src/test`.
- `./gradlew connectedAndroidTest`: run instrumented tests on a connected device.
- `./gradlew lint`: run Android lint checks.

## Coding Style & Naming Conventions
- Kotlin + Jetpack Compose; follow standard Android/Kotlin style.
- Indentation: 4 spaces, no tabs.
- Classes and composables use PascalCase; functions and variables use camelCase.
- Packages are lowercase; resources use `snake_case` (for example, `activity_main.xml`).
- No formatter is configured; keep changes consistent with nearby code.

## Testing Guidelines
- Unit tests use JUnit (see dependencies in `app/build.gradle.kts`).
- Instrumented tests use AndroidX/JUnit, Espresso, and Compose testing.
- Name test classes `*Test` and mirror the production package structure.

## Commit & Pull Request Guidelines
- No Git history is present in this repo, so use a clear, consistent convention such as `type(scope): summary` (for example, `feat(ui): add streak counter`).
- PRs should include: a brief description, testing steps, and screenshots for UI changes.
- Link related issues or planning docs when applicable.

## Configuration Notes
- `local.properties` is for local SDK paths; avoid committing machine-specific values.
- Keep API keys or secrets out of the repo; use environment-specific config instead.

## Skills
- Use the local `android-architecture-designing` skill whenever creating new files, directories, or modules.
- Skill path: `.agent/skills/android-architecture-designing/SKILL.md`
- This skill defines the NIA-inspired architecture, SOLID layering, MVVM/UDF conventions, and coroutine best practices for this project.
- Use the local `android-implementation` skill whenever implementing new Android/Kotlin code or updating existing Android/Kotlin code.
- Skill path: `.agent/skills/android-implementation/SKILL.md`
- This skill defines implementation conventions such as null-safety rules, composable boundaries, coroutine patterns, and error-handling style.
