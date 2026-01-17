## Initial Overview & Planning Workflow

This document defines the high-level vision for the app and the rules for how we plan and document the project. It explains how the different planning files relate to each other and how ideas move from rough brainstorming to implementation-ready specifications.

---

## Project Purpose & Vision

The app is inspired by a real-world "fitness/gym challenge" between friends: everyone competes over a long period of time (e.g., a year, with 3-month checkpoints), and the person who goes to the gym the least by the end incurs a penalty.

**Core vision**:

- Help **groups of friends** (roughly ages 16–40) build consistent fitness habits together.
- Use **social accountability and light competition** as the main motivators.
- Make it easy and fun to track gym attendance and basic workout routines.

**MVP focus**:

- A **group fitness challenge / attendance tracker** for multi-device friend groups.
- A **basic fitness planner** for creating routines and exercises.

Future iterations can add richer gamification, deeper analytics, and more advanced social features.

---

## Documentation Files & Their Roles

We use three main documentation files to manage ideas and plans:

### `InitialOverviewPlanning.md` (this file)

- **Role**: Meta-document describing:
  - Overall project vision and goals.
  - How planning and documentation are organized.
  - Rules for promoting ideas from brainstorming to implementation.
- **Change policy**:
  - Updated when high-level goals, workflow, or constraints change.
  - Does **not** contain detailed, implementation-level specs.

### `ProjectBrainstorming.md`

- **Role**: Main hub for **ideas**.
  - Collects raw thoughts, options, alternatives, and early designs.
  - Can contain conflicting or evolving ideas.
  - Includes product ideas, architectural sketches, dependency considerations, and open questions.
- **Change policy**:
  - Can be updated freely as new ideas appear.
  - Old ideas are usually kept and annotated rather than deleted, to preserve context.

### `ProjectPlanning.md`

- **Role**: **Implementation-ready** specification.
  - Contains decisions that are considered stable enough to guide coding.
  - Acts as the single source of truth when implementing the app.
- **Change policy**:
  - Only updated when an idea in `ProjectBrainstorming.md` is consciously **promoted** to "ready for implementation".
  - Should remain consistent, clear, and as free of open questions as possible.

---

## Workflow & Decision Rules

The planning workflow is designed to separate **exploration** from **execution**:

1. **Ideation in `ProjectBrainstorming.md`**
   - New ideas, variations, and questions are first recorded here.
   - It is acceptable to:
     - Propose alternative approaches.
     - Leave open questions and trade-offs explicitly noted.
     - Capture partial or tentative designs.

2. **Refinement**
   - Over time, we refine ideas in `ProjectBrainstorming.md`:
     - Clarify goals and scope.
     - Identify dependencies and risks.
     - Resolve major open questions.

3. **Promotion to `ProjectPlanning.md`**
   - An idea is considered **ready for implementation** when:
     - The problem it solves is clear.
     - The scope is reasonably well-bounded for an iteration/MVP.
     - Key architectural and UX decisions have been made (or at least constrained).
     - There are no blocking open questions on core aspects (data model, navigation entry points, etc.).
   - When promoting:
     - Copy/transform the relevant parts into `ProjectPlanning.md`, rewriting them in a precise, implementation-oriented way.
     - In `ProjectBrainstorming.md`, add a note (e.g., "Promoted to `ProjectPlanning.md` – v0.x") rather than deleting the original idea.

4. **Handling deprecated or rejected ideas**
   - When an idea is rejected or superseded:
     - Keep it in `ProjectBrainstorming.md` under an appropriate section (e.g., "Deprecated ideas").
     - Add a short rationale for why it was not chosen.

This workflow echoes disciplined planning in projects like [`nowinandroid`](https://github.com/android/nowinandroid), where architecture and modularization are intentionally designed and documented before and alongside implementation.

---

## High-Level Scope & Constraints (MVP)

### Core Features (MVP)

- **Group Fitness Challenge / Attendance Tracker**
  - Users can create "fitness groups" (name TBD) where friends participate in a shared challenge.
  - Each group:
    - Has members, one or more admins.
    - Has a **main goal period** (e.g., 1 year) and **checkpoints** (e.g., every 3 months), editable by admins.
    - Has free-text **group rules** (penalties, expectations, etc.), editable by admins.
  - Each member tracks their **gym attendance count** within the group.
  - Users can belong to **multiple groups**.
  - Users join groups primarily via **invite links** (MVP).

- **Basic Fitness Planner**
  - Users can:
    - Create **routines** (e.g., "Push Day", "Leg Day").
    - Add **exercises** with sets and reps.
    - Define **custom exercises** (MVP).
  - Future (beyond MVP): integrate with a fitness/exercise API to enrich exercises with tips, videos, and guidance, while still allowing custom entries.

### Target Users

- Small friend groups (approximately ages **16–40**) who:
  - Want to build or maintain a gym/fitness habit.
  - Need motivation and accountability from peers.
  - Prefer a lightweight, social, and fun approach over highly technical training analytics.

### Non-Functional Constraints (MVP)

- **Offline-first behavior**
  - The app should support key flows (e.g., viewing routines, logging attendance) without an active network connection.
  - Data should be persisted locally (e.g., via Room) and synced with a backend when connectivity is available.

- **Backend-backed multi-device groups**
  - Since groups are shared across different users and devices, group data must be stored in a **backend** from the start.
  - A likely approach is **Firebase** (Auth + database), with **Room** as a local cache.

- **Minimal gamification initially**
  - MVP focuses on:
    - Accurate tracking of attendance and basic routines.
    - Simple **push notifications** for important group events (e.g., a member logs a new attendance).
  - Advanced features (streaks, badges, rich leaderboards) can be introduced iteratively.

- **Privacy and security**
  - Early target audience is a small, trusted group of friends.
  - Evidence photos, when introduced, are visible to all group members by default.
  - More granular privacy controls and formal security/privacy policies will be considered after the MVP.

- **Permissions**
  - For MVP:
    - **Internet**: standard app-level permission in the manifest.
    - **Camera/Photos**: explicitly requested with a clear explanation when photo evidence is implemented.
    - **Location**: explicitly requested (if used) with justification (e.g., tagging attendance with gym location).

---

## Architectural Inspiration & Direction

The app’s architecture will be inspired by the **Now in Android** project ([`nowinandroid`](https://github.com/android/nowinandroid)):

- **Modularization**
  - Clear separation between:
    - Core modules (e.g., shared models, data access, design system).
    - Feature modules (e.g., group challenge, planner, home, auth).
  - This enables:
    - Better build times.
    - Clear ownership and boundaries between features.
    - Easier scaling and testing.

- **Layered architecture**
  - Separation of concerns between:
    - Data layer (persistence, network, repositories).
    - Domain layer (business logic, use cases).
    - UI layer (screens, state holders like ViewModels or equivalent).

- **Offline-first with sync**
  - Local DB (e.g., Room) as the single source of truth for most data.
  - Backend (e.g., Firebase) as a synchronization partner for multi-device and multi-user data.

The precise module and layer breakdown will be defined and refined in `ProjectBrainstorming.md` and later formalized in `ProjectPlanning.md`.

---

## Navigation & UX Principles (High-Level)

### Overall Navigation

- **Single-activity architecture** using **Jetpack Compose**.
- **Bottom navigation bar**:
  - Designed to support up to ~5 destinations.
  - MVP may start with primarily a **Home** destination, but the nav structure should be scalable.
  - A potential **center button** can be reserved for a special action (e.g., quick "log attendance" or similar) in the future.

### Top Bar

- A **top app bar** that:
  - Shows a tappable **profile area** on the left (avatar + greeting + username).
    - Tapping opens profile/account/settings UI (possibly a bottom sheet or a dedicated screen; TBD).
  - Includes roughly two action buttons on the right, e.g.:
    - A **"Create"** button (plus icon) for quick creation flows (e.g., new attendance, new routine).
    - A **"Help/Contact"** action as a placeholder, which can evolve later.

### Deep Links and Intents

- For the MVP, we can start with straightforward navigation within the app.
- There is an opportunity to:
  - Use **deep links** for certain flows (e.g., invite links to join groups).
  - Consider a scalable navigation approach that plays well with deep links, especially if we later adopt patterns similar to more complex apps or SDUI-style architectures.
- These concerns will be explored first in `ProjectBrainstorming.md` and only locked in once we are confident they are needed.

---

## Relationship Between This Document and the Other Files

- **`InitialOverviewPlanning.md`**:
  - Describes the big picture, planning workflow, and key non-technical constraints.
  - Updated when we adjust high-level direction, documentation rules, or core assumptions.

- **`ProjectBrainstorming.md`**:
  - The main evolving source of ideas for:
    - Product features and UX.
    - Architecture and dependencies.
    - Non-functional requirements and open questions.
  - Can frequently reference this file for context.

- **`ProjectPlanning.md`**:
  - The implementation blueprint.
  - Should contain only decisions that have passed through the brainstorming and refinement process.
  - Is considered the canonical guide once development starts.

This separation should keep the project organized, make reasoning explicit, and mirror the kind of structured planning seen in well-documented sample apps like [`nowinandroid`](https://github.com/android/nowinandroid).


