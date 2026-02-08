# Dependencies

This document defines the dependency plan for the Android project.

This file is the dependency source of truth for what is used in this project.

All dependency versions should be managed in `gradle/libs.versions.toml`.

## Current Baseline (Already Present)

- Kotlin Android + Compose plugin
- AndroidX Core KTX
- Lifecycle runtime KTX
- Activity Compose
- Compose BOM + UI + Material3
- JUnit, AndroidX JUnit, Espresso, Compose UI test artifacts

## Foundation Dependencies (Phase 1)

These support the modular app shell and implementation conventions.

### Navigation
- `androidx.navigation:navigation-compose`
- Why: app-level and feature-level navigation graph support.

### Lifecycle Compose
- `androidx.lifecycle:lifecycle-runtime-compose`
- Why: lifecycle-aware state collection (`collectAsStateWithLifecycle`).

### ViewModel Compose
- `androidx.lifecycle:lifecycle-viewmodel-compose`
- Why: Compose integration for screen state holders.

### Hilt (DI)
- Plugin: `com.google.dagger.hilt.android`
- `com.google.dagger:hilt-android`
- `com.google.dagger:hilt-compiler` (ksp)
- `androidx.hilt:hilt-navigation-compose`
- Why: constructor-friendly dependency injection and scalable module wiring.

### Coroutines
- `org.jetbrains.kotlinx:kotlinx-coroutines-android`
- Why: explicit coroutine runtime for structured async work.

## Data Backbone Dependencies (Phase 2)

These support repository and cache/remote implementation.

### REST Networking Stack (Chosen)
- Retrofit + OkHttp
- Why: fastest, most stable Android-first implementation path with clean coroutine-based APIs.

Locked libraries:
- `com.squareup.retrofit2:retrofit`
- `com.squareup.retrofit2:converter-moshi`
- `com.squareup.okhttp3:okhttp`
- `com.squareup.okhttp3:logging-interceptor` (debug-focused)

### Room (Local Cache)
- `androidx.room:room-runtime`
- `androidx.room:room-ktx`
- `androidx.room:room-compiler` (ksp)
- Why: relational local cache for groups, memberships, attendance, and related entities.

### DataStore
- `androidx.datastore:datastore-preferences`
- Why: lightweight key-value storage for user preferences and local flags.

### Firebase Platform + Services
- Firebase BOM (`com.google.firebase:firebase-bom`)
- `com.google.firebase:firebase-auth-ktx`
- `com.google.firebase:firebase-firestore-ktx`
- `com.google.firebase:firebase-messaging-ktx`
- Why: authentication, backend-authoritative shared data, and push notifications.

### Firebase Gradle Plugins
- `com.google.gms.google-services`
- Why: Firebase project configuration and service initialization.

## Media and Utility Dependencies (Phase 3+)

### Image Loading
- `io.coil-kt:coil-compose`
- Why: avatar and future media rendering.

### WorkManager (Phase 3)
- `androidx.work:work-runtime-ktx`
- Why: resilient background work and retry paths.

## Testing Dependencies (Phase 2+)

### Unit and Coroutine Testing
- `org.jetbrains.kotlinx:kotlinx-coroutines-test`
- Why: deterministic coroutine tests.

### Flow Testing
- `app.cash.turbine:turbine`
- Why: ergonomic Flow assertions.

### DI Test Support
- `com.google.dagger:hilt-android-testing`
- `com.google.dagger:hilt-compiler` (kspAndroidTest)
- Why: dependency replacement and integration-style tests.

## Dependency Decisions and Constraints

- Add dependencies according to the phase assignments in this file.
- Prefer AndroidX/Firebase official libraries for MVP.
- Keep module dependency surface minimal (do not add all dependencies to all modules).
- Keep versions centralized in `gradle/libs.versions.toml`.
- Update this file first before changing dependency decisions.
- The REST stack is locked to Retrofit + OkHttp + Moshi converter.

## Immediate Next Additions

For the next implementation step, add:
1. Navigation Compose
2. Lifecycle runtime compose + ViewModel compose
3. Hilt + hilt navigation compose

Then add Room/DataStore/Firebase in Phase 2 when data modules are scaffolded.
Then add Room/DataStore/Firebase in Phase 2.
