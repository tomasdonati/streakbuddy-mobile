# Project Planning (Shared)

This document holds **platform-agnostic, implementation-ready specifications** that apply to both Android and iOS. It is the source of truth for product scope, core flows, and data models that should remain consistent across platforms.

Use platform-specific planning files for implementation details:
- `ProjectPlanning.Android.md`
- `ProjectPlanning.iOS.md`

---

## How This Document Is Updated

- Content is promoted here only after it is refined in `ProjectBrainstorming.md`.
- Decisions here should be stable enough to guide implementation on any platform.
- If platform differences exist, capture the shared intent here and the specifics in the platform file.

---

## Core Product Scope (MVP)

> **Status:** Placeholder

Define the MVP feature set and non-negotiable constraints shared across platforms.

---

## Navigation & App Flow (Shared)

> **Status:** Placeholder

Define the main user flows and navigation intent (e.g., Home, Groups, Planner) without tying to platform UI frameworks.

---

## Data Model (Shared)

> **Status:** Placeholder

Define canonical entities and fields (e.g., User, Group, Membership, AttendanceEntry, Routine, Exercise) that both platforms must implement.

---

## Feature Specifications (Shared)

### Group Challenge

#### Scope (MVP)

- Group creation, invites/joining, attendance logging, group overview, basic notifications.
- Single admin per group (MVP).

#### Group Structure

- Name and optional description.
- Single admin.
- Rules text defined at creation.
- Challenge period:
  - Start date and end date set at creation.
  - Checkpoints defined by fixed interval (days/weeks/months).
  - Not editable in MVP.

#### Membership Rules

- Users can join multiple groups.
- Members can leave at any time; their past attendance remains.
- Admins cannot leave the group in MVP (role transfer later).
- Admins can remove members; removed members can be re-invited.

#### Invites & Joining (MVP)

- Single-use invite links.
- No expiry.
- Payload includes groupId, inviterId, and optional inviter display name.
- Security uses a random token; do not expose plain groupId alone.
- Invite generation is admin-only.
- No revoke/rotate in MVP.
- If an existing member opens an invite, skip confirmation and open Group Overview.

#### Attendance Logging (MVP)

- One attendance per user per group per day.
- Date/time auto-filled to now; backdating allowed within 1 day.
- If an entry already exists for the day, allow overwrite with confirmation.
- Editing is not supported (delete + re-log instead).
- Deletion is allowed for any log in MVP.
- Optional location is out of MVP scope.

#### Group Overview (MVP)

- Show member list with attendance counts.
- Toggle between:
  - Current checkpoint view.
  - Full challenge view.
- Default sort: current checkpoint count.
- Toggle switches sort order and displayed counts.
- Show time remaining based on toggle (checkpoint vs full challenge).
- Member list is fully shown and scrollable.
- Empty state: "No gym attendance yet" + CTA (e.g., "Register attendance").

#### Notifications (MVP)

- Opt-in per notification type.
- Attendance logged:
  - Notify all group members except the logger.
  - Message: "X logged a gym visit in Group Y".
- New member joined group.
- Multiple notifications per day are allowed.

### Fitness Planner

> **Status:** Placeholder

Implementation-ready, platform-agnostic behavior for routines and exercises.

---

## Onboarding & Authentication (Shared)

### Scope (0.1.0)

- Authentication uses email + password only.
- All users must be authenticated to create or join groups (no guest mode).
- Display name is required after first sign-up.
- Invite links are the primary join mechanism.

### User Flows (Implementation-Ready)

#### First App Launch (No Invite, New User)

1. User opens app with no invite context.
2. App shows a sign up / log in choice.
3. After successful auth and display name capture, user lands on Home with an empty state (no groups yet).

#### First App Launch via Invite Link (New User)

1. App receives invite link and extracts group identifier.
2. App fetches minimal group info (group name, optional inviter name) and shows an invite intro screen:
   - Title: "Join your friends in <Group Name>".
   - Short description of the app.
   - Primary: Continue.
   - Secondary: Not now (optional; can exit or continue to generic onboarding).
3. User selects "Create account" and signs up with email + password:
   - Validate email format.
   - Validate password + confirm password.
   - Show clear error messages on failure.
4. App requires a display name and confirms it before proceeding.
5. App fetches full group details and shows a join confirmation screen:
   - Group name, description, rules, member count (if available).
   - Primary: Join group.
   - Secondary: Cancel (skip joining and go to Home).
6. On join success:
   - Create membership record in backend.
   - Persist membership locally for offline access.
7. Show the walkthrough/education modal:
   - Multi-page, manual advance (swipe or Next).
   - Progress indicator.
   - Final action: Done / Get started.
   - On completion, record a local "seen walkthrough" flag.
8. Navigate to Group Overview for the newly joined group.

#### Open Invite Link (Already Logged In)

1. App receives invite link and fetches group details.
2. Show join confirmation screen (same as step 5 above).
3. On join success, navigate to Group Overview.

#### Normal App Open (Already Logged In)

1. App opens to Home (or last known destination if we add that later).

#### Log In (Email + Password)

1. User enters email + password.
2. Validate input and authenticate.
3. On success, land on Home.

#### Sign Up (Email + Password)

1. User enters email, password, confirm password.
2. Validate fields; create account.
3. Require display name, then land on Home.

#### Password Reset

1. User selects "Forgot password".
2. User enters email.
3. App triggers reset email and confirms success or failure.

### Error Handling & Edge Cases

- Invalid or expired invite:
  - Show a clear message and a "Go to Home" action.
- Network unavailable during sign-up or join:
  - Show error with a "Retry" action.
  - User cannot complete invite join flow offline.

### Future Enhancements (0.2.0+)

- Add Google sign-in.
- Improve password strength guidance.

---

## Walkthrough / Education System (Shared)

### Scope (0.1.0)

- A reusable, full-screen, multi-page walkthrough UI.
- Shown only once per user per walkthrough type.
- Each walkthrough has:
  - A `should_show_walkthrough` flag bound to the user.
  - A `skippable` flag to control whether Skip is shown.
  - A maximum of 5 pages.

### Walkthrough Types & Triggers

- **Invite Join Walkthrough (core, not skippable)**
  - Trigger: first time a user joins a group via invite.
- **First Group Creation Walkthrough (core, not skippable)**
  - Trigger: first time a user creates a group.
- **Group Overview Walkthrough (non-core, skippable)**
  - Trigger: first time a user opens Group Overview from Home/Groups.
- **Planner Walkthrough (non-core, skippable)**
  - Trigger: first time a user opens the Planner screen.

### Behavior (Implementation-Ready)

- Full-screen modal that blocks background interaction until finished or skipped (if skippable).
- Manual advance only (swipe or Next).
- Progress indicator (dots or step count).
- Final action closes the walkthrough and sets the per-user flag to false.
- If `skippable = true`, show a Skip action that also sets the flag to false.

### Content Constraints

- Each page contains:
  - Title.
  - Short description.
  - Optional illustration/icon.
- Each walkthrough is limited to 2-5 pages.

---

## Architecture & Data Sync (Shared)

### Core Principles

- Offline-first: local database is the primary read source for app UI.
- Backend-backed: Cloud Firestore is the system of record for shared data.
- Modularization follows the Now in Android pattern from the start.

### Data Sources

- Local persistence uses a relational store (Room on Android; equivalent on iOS).
- Remote persistence uses Cloud Firestore.

### Firestore Structure (MVP)

- Top-level collections for primary entities (e.g., `users`, `groups`).
- Use subcollections for group-specific data (e.g., `groups/{groupId}/attendance_entries`).

### Sync Rules (MVP)

- Sync on app start and manual pull-to-refresh.
- IDs are client-generated UUIDs; server timestamps are canonical.
- Attendance history is paged (do not load full history by default).
- User-owned data:
  - Local edits win and overwrite server on next sync.
  - Example: routines, exercises, personal attendance entries.
- Shared data:
  - Backend wins on conflicts; local edits are rejected/overwritten.
  - Example: group rules, group settings, membership roles.

### Access Control (MVP)

- Only group members can read group data.
- Only admins can edit group settings and rules.

### Layers (Shared Intent)

- Data layer: local + remote data sources, repositories.
- Domain layer: use cases and business rules.
- UI layer: screens and state holders.
