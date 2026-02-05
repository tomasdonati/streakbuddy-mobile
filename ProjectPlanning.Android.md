# Project Planning (Android)

This document captures **Android-specific implementation decisions**. It complements `ProjectPlanning.Shared.md` and should only include details that are specific to Android (Compose, Room, WorkManager, Firebase SDK usage, etc.).

---

## Architecture & Modules (Android)

> **Status:** Placeholder

Define module boundaries, Android Gradle modules, and dependencies aligned with the shared plan.

---

## Navigation (Android)

> **Status:** Placeholder

Describe the Compose navigation graph, deep links, and any Android-only entry points.

### Navigation Scaffold (MVP)

- Main activity hosts the bottom navigation scaffold with a prominent center action button.
- Use `BottomBarActivity` as the host activity name.
- Bottom bar layout mirrors a 5-slot visual layout:
  - 4 standard tabs: Home, Groups, Planner (WIP), Settings.
  - Center primary action button (elevated) for "Log attendance".
- Center button is visually distinct (larger, circular) and overlaps the bar.

### Tab Behavior

- Tapping a tab switches the main content area.
- Tapping the center button opens the Log Attendance flow (non-camera MVP).
- Planner tab shows a "Coming soon / Work in progress" screen.

### Top Bar (Android)

- Show profile area on the left.
- Show notifications and feedback actions on the right.
- Top bar is part of the scaffold and visible on all tabs (unless overridden later).

### TODO - Next Implementation Step

- Define the navigation scaffold implementation details (Compose structure, route map, and bottom bar behavior) before coding.

---

## Data Layer (Android)

> **Status:** Placeholder

Describe Room entities/DAOs, DataStore usage, and sync mechanics on Android.

---

## Feature Specs (Android)

### Group Challenge

> **Status:** Placeholder

Android-specific UI and implementation notes.

### Fitness Planner

> **Status:** Placeholder

Android-specific UI and implementation notes.
