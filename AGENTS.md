# Repository Guidelines

## Project Structure & Module Organization
- `app/` is the only Gradle module; production code lives in `app/src/main/java/com/tomasdonati/streakfit`.
- UI resources are in `app/src/main/res` and themes live under `app/src/main/java/com/tomasdonati/streakfit/ui/theme`.
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
