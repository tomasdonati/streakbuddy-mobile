## Project Brainstorming

This document is the main hub for ideas about the app. It is intentionally exploratory and may contain conflicting or evolving concepts. Once ideas are refined and considered "ready for implementation", they will be promoted and captured in a more formal way in `ProjectPlanning.md`.

---

## Product & Users

- **Target users**
  - Groups of young people (roughly **16–40** years old) who:
    - Want to build or maintain a gym/fitness habit.
    - Are motivated by friendly competition and accountability.
    - Prefer a lightweight, social experience instead of complex training analytics.

- **Primary use case**
  - A group of friends sets up an ongoing challenge (e.g., one year) with intermediate checkpoints (e.g., every 3 months).
  - Each friend logs each gym visit, and at the end of a period:
    - Everyone sees who has gone to the gym the least.
    - A penalty or "punishment" is applied to the person with the lowest count, according to group rules.

- **Secondary use case**
  - Individual users want to:
    - Plan simple routines (e.g., "Push Day", "Pull Day", "Leg Day").
    - Track what exercises they plan to do on a given day, with sets and reps.

- **Core value proposition**
  - Make it easy and fun to stay accountable with friends.
  - Lower the friction of tracking gym attendance and basic workouts.
  - Encourage habit formation through peer pressure, visibility, and light competition.

---

## Features

### Group Fitness Challenge / Attendance Tracker

High-level idea: users create and join "fitness groups" where each member has their own attendance counter and shared rules.

Promoted to `ProjectPlanning.Shared.md` (group challenge MVP spec).

- **Group structure**
  - A group has:
    - Name and optional description.
    - One or more **admins**.
    - A main **challenge period** with start/end dates.
    - **Checkpoints** defined at creation by a fixed interval (days/weeks/months).
    - A free-text **rules section** where admins can define:
      - Penalties (e.g., loser buys dinner).
      - Expected behavior (e.g., what counts as a "gym visit").
      - Any special conditions or agreement between group members.
  - Groups are **continuous**:
    - For MVP, time frames are not editable after creation.

- **Membership**
  - Users can belong to **multiple groups** at the same time.
  - Each group membership tracks:
    - The user’s attendance count for that group.
    - Any relevant per-member metadata (e.g., join date, maybe role).
  - **Membership rules (MVP)**:
    - Roles: admin/member only, with a single admin per group.
    - Members can leave at any time; their past attendance remains.
    - Admins cannot leave the group in MVP (role transfer later).
    - Admins can remove members; removed members can be re-invited.

- **Invites & joining**
  - MVP approach:
    - **Invite links** that encode enough information to:
      - Identify the target group.
      - Route the user through:
        - Install / open the app.
        - Authentication (if needed).
        - Join confirmation screen.
    - **Invite semantics (MVP)**:
      - Single-use invite links.
      - No expiry.
      - Payload includes groupId, inviterId, and optional inviter display name.
      - Security uses a random token (do not expose plain groupId alone).
      - Invite generation is admin-only.
      - No revoke/rotate in MVP.
      - Admins can remove members to handle accidental joins.
      - If invite is opened by an existing member, skip confirmation and open Group Overview.
  - Future possibilities:
    - Group codes for manual entry.
    - QR codes that represent the same invite links.

- **Attendance logging**
  - For each "gym visit", a user can log an **attendance entry** that includes:
    - **Date/time** (auto-filled to "now" with option to adjust within 1 day).
    - **Group** (if the user belongs to multiple groups; default group can be preselected).
    - **Optional location** removed from MVP.
  - Rules:
    - One attendance per user per group per day.
    - If an entry already exists for the day, allow overwrite with a confirmation prompt.
    - Editing is not supported in MVP (delete + re-log instead).
    - Deletion is allowed for any log in MVP.
  - **Evidence photos**:
    - **Not part of MVP** (requires cloud storage).
    - Future behavior when implemented:
      - Optional photo per attendance.
      - Visible to all group members (evidence, motivational feed).
      - Data retention:
        - Possibly limited to ~3 days of photos per user per day.
        - Automatic deletion of older photos to save space.
      - Photos should be:
        - Compressed and limited in quality (basic selfie-level quality is sufficient).

- **Progress & status views**
  - For each group:
    - Overview of members and their attendance counts.
    - Indication of:
      - Current challenge period and checkpoint.
      - Time remaining until the end of the current checkpoint or full period (toggle).
    - Leaderboard:
      - Default sort: current checkpoint count.
      - Optional toggle to sort by full challenge total.
      - Toggle also switches member counts to checkpoint vs full challenge.
      - Member list is fully shown and scrollable if needed.
  - For each user:
    - Personal view of:
      - Their attendance across all groups.
      - History of logged visits.
  - Empty state:
    - If no attendance yet: show "No gym attendance yet" and a CTA like "Register attendance".

- **Notifications (MVP-level)**
  - Basic **push notifications** (opt-in per type):
    - Attendance logged (notify all group members except the logger).
    - New member joined group.
  - Content (MVP):
    - "X logged a gym visit in Group Y".
  - Multiple notifications per day are allowed in MVP.
  - Future:
    - Custom reminders near checkpoint or challenge end.
  - More complex gamification (streaks, badges, etc.) is a later enhancement.

#### User Flows – Group Challenge

This subsection outlines the main user flows related to the group challenge feature. Detailed step-by-step behavior will be refined later.

- **Create Group (existing logged-in user)**
- **Join Group via Invite Link (new user via first-time app open)**
- **Join Group via Invite Link (already logged-in user)**
- **View Group Overview**
- **Log Attendance**
- **View Personal Attendance History**
- **Edit Group Settings (Admin)**
- **Leave Group**

### Fitness Planner

High-level idea: a simple, flexible planner for routines and exercises, that can grow over time.

- **MVP scope**
  - Planner is out of MVP; show a simple "Work in progress" placeholder screen.

- **Routines**
  - Users can create named routines, e.g.:
    - "Push Day", "Pull Day", "Leg Day".
    - "Full Body", "Cardio Day".
  - Each routine contains a list of **exercises**, each with:
    - Name.
    - Number of sets.
    - Number of reps (or rep range).
    - (Future) Optional rest time, notes, or intensity indicators.

- **Exercises**
  - MVP:
    - Support **user-defined exercises**:
      - Custom name and simple configuration (sets, reps).
    - Basic local catalog we define (e.g., common gym movements) can be added later or in a minimal form.
  - Future:
    - Integrate with a **fitness/exercise API** to:
      - Provide standardized exercise entries.
      - Show descriptions, tips, and (possibly) linked videos.
    - Allow users to:
      - Browse a catalog.
      - Import exercises into their routines.

- **Usage during workouts**
  - MVP idea (subject to refinement):
    - A simple view that:
      - Shows today's chosen routine.
      - Lets users see the list of exercises, sets, and reps.
    - Logging actual completion is optional in MVP; focus is on **planning** rather than detailed workout tracking.

### Future / Nice-to-Have Features

Brainstormed ideas that are **not** part of the MVP but may be considered later:

- **Advanced gamification**
  - Streak tracking and visualizations.
  - Badges or achievements.
  - Seasonal or themed challenges.

- **Social & sharing**
  - Sharing group results or milestones to social networks.
  - Community-created routines and template sharing.

- **Coaching and guidance**
  - Coach mode (where a more advanced user or trainer can design plans for others).
  - Recommended plans based on goals.

- **Analytics**
  - Trend charts for attendance over weeks/months.
  - Routine usage statistics.

---

### Cross-App Features

This section captures features that are reusable across multiple parts of the app.

#### Walkthrough / Education System [target: 0.1.0]

High-level idea: a reusable, full-screen, multi-page walkthrough to explain how the app and its main features work.

Promoted to `ProjectPlanning.Shared.md` (walkthrough system spec for 0.1.0).

- **Purpose**
  - Help new users quickly understand:
    - What the app does (high-level value).
    - How the main features (e.g., Group Challenge, Routine Tracker) work.
  - Provide light, visual education instead of dense text blocks.

- **Behavior (MVP)**
  - Appears as a **full-screen modal**:
    - Consists of multiple pages/screens.
    - User advances manually (e.g., swipe or tap "Next").
    - No automatic per-page timers or story-like auto-advance in the MVP.
  - Each page can include:
    - A title.
    - A short description.
    - An illustration/icon (optional; can start simple or text-only).
  - A simple progress indicator (e.g., dots or step count) shows how many pages are left.
  - A clear final action (e.g., "Done" or "Get started") closes the walkthrough.

- **Where we use it (initial ideas)**
  - **Onboarding from invite link**:
    - After a new user signs up and joins a group for the first time.
    - Show:
      - 1–2 pages about the app overall.
      - 1–2 pages specifically about the Group Challenge concept.
  - **First-time entry to specific features**:
    - Group Challenge:
      - First time user opens the Group Overview from Home or Groups list.
    - Routine Tracker:
      - First time user opens the Planner screen.

- **Future enhancements (beyond MVP)**
  - Timed pages or auto-advance.
  - Richer media (e.g., short animations).
  - Tracking completion/skip rates via analytics to improve content over time.

---

### Onboarding & Authentication

This section covers how users first experience the app, create accounts, and sign in, with the requirement that all group members must be authenticated (no guest mode in the MVP).

#### Goals

- Ensure every user has an account before creating or joining groups.
- Make first-time setup fast and simple, especially when coming from an invite link.
- MVP (`0.1.0`): use **email + password** authentication only.
- Plan a later update (`0.2.0`) to add **Google sign-in** on top of email/password.
- Keep the initial implementation focused on email/password auth, while leaving room for future providers (e.g., Google Sign-In).

#### User Flows – Onboarding & Auth

High-level list of onboarding/auth flows. Some are still high-level; others (like the invite-based first launch) are described in more detail.

- **First App Launch (no invite link, new user)**
  - User opens the app directly for the first time and is guided to sign up or log in, then lands on an empty Home state.

- **First App Launch via Invite Link (new user) [target: 0.1.0]**
  1. **Invite link tap**
     - User receives a group invite link (e.g., in a chat app).
     - User taps the link.
     - If the app is not installed:
       - The system opens the store page; user installs the app and then taps "Open".
       - The deep link parameters associated with the invite are preserved so the app can resume the flow after installation.
     - If the app is already installed:
       - The system opens the app directly, passing along the invite deep link.
  2. **Invite detection & intro screen**
     - App parses the deep link and detects that it corresponds to a group invite.
     - App fetches minimal group info from backend (if possible), such as:
       - Group name.
       - Inviter name or identifier (if encoded/available).
     - App shows a lightweight intro screen:
       - Example content:
         - Title: "Join your friends in \<Group Name>"
         - Subtitle: 1–2 lines describing what the app does (e.g., "Track gym visits with your friends and stay motivated together.")
       - Primary action: "Continue" (moves to sign up / log in).
       - Secondary action: "Not now" (optional; could dismiss and go to a generic onboarding path or app exit).
  3. **Sign up / log in choice**
     - Screen offers:
       - Primary CTA: "Create account" (email + password).
       - Secondary: "Already have an account? Log in".
     - For `0.1.0`:
       - Only email/password options are shown (no Google sign-in button yet).
  4. **Sign Up (email + password + confirm password)**
     - User chooses "Create account".
     - Fields:
       - Email.
       - Password.
       - Confirm password.
     - Behavior:
       - Validate email format.
       - Validate that password and confirm password match.
       - For `0.1.0`, enforce a minimal set of password rules (to be defined), but without full strength/complexity suggestions yet.
       - On error, show clear messages and let user correct inputs.
       - On success, create the user via Firebase Auth.
     - Future enhancement `[target: 0.2.0]`:
       - Add clearer password strength guidance (e.g., minimum length, uppercase, symbol suggestions) and more explicit rules.
  5. **Minimal profile setup (required display name)**
     - After successful sign up, user is taken to a simple profile step:
       - Required field:
         - Display name / username (e.g., what friends will see in groups and leaderboards).
       - Avatar:
         - Not configured in `0.1.0`; app uses a placeholder (e.g., initial-based icon or simple default vector).
     - User confirms the display name and continues.
  6. **Resume invite: group preview and join confirmation**
     - App still holds the invite context and fetches full group details:
       - Group name.
       - Description.
       - Rules text (scrollable).
       - Approximate member count (if available).
     - Screen content:
       - Title: "Join \<Group Name>?"
       - Body: high-level description + rules preview.
       - Actions:
         - Primary: "Join group".
         - Secondary: "Cancel" (skip joining and go to Home without any group).
     - On "Join group":
       - App creates a membership record associating the new user with the group.
       - Handles any backend errors (e.g., invite expired, group full, etc.) with clear messages.
  7. **Walkthrough / education modal (cross-app feature)**
     - Immediately after joining the group for the first time:
       - App shows the **Walkthrough / Education System** modal described in the Cross-App Features section.
       - Content example:
         - Page 1–2: General app overview.
           - E.g., "Compete with friends on gym visits" / "Plan your routines".
         - Page 3–4: Group Challenge basics.
           - E.g., "Each visit increases your counter" / "Checkpoints and penalties are defined by your group rules".
       - User manually swipes or taps "Next" between pages.
       - Final page has a "Done" or "Get started" action.
     - Once the user finishes or dismisses the walkthrough:
       - The app records that the user has seen this first-time walkthrough (e.g., via a flag in local storage).
  8. **First in-app destination: Group Overview**
     - After the walkthrough is completed:
       - User is taken directly to the **Group Overview** screen for the group they just joined.
       - Group Overview shows:
         - Members and their attendance counts (initially, counts may be low/zero).
         - Current challenge period / checkpoint status.
         - A visible "Log attendance" entry point for future use.
  9. **Edge and error conditions**
     - Invite expired or invalid:
       - At any point where the invite is validated, if it's invalid/expired:
         - Show a friendly message like:
           - "This invite link is no longer valid. Ask the group admin for a new link."
         - Provide an action to "Go to Home".
     - Network unavailable:
       - Sign-up and joining the group both require connectivity.
       - If the network is unavailable:
         - Show an error and a "Retry" action.
         - User cannot complete this specific flow offline.

- **Open App via Invite Link (already logged-in user)**
  - User taps an invite link, app opens, and they are shown a group preview and a confirmation to join.

- **Normal App Open (already logged-in user)**
  - User opens the app and is taken directly to Home (or the most relevant starting screen).

- **Sign Up Flow (email/password)**
  - User chooses to create a new account with email/password and minimal required profile data.

- **Log In Flow (email/password)**
  - User signs in with existing credentials to regain access to their data and groups.

- **Password Reset Flow**
  - User requests a password reset from the login screen via email.

- **Log Out & Re-login**
  - User logs out from profile/settings, then can log back in later through the usual login flow.

#### Open Questions – Onboarding & Auth

Early uncertainties to resolve before promoting these flows to `ProjectPlanning.md`:

- Google sign-in is tentatively targeted for `0.2.0`; exact UX, error handling, and rollout details are still open.
- What minimum profile information should be required at signup (e.g., display name, avatar) versus optional or deferred.
- How strongly we should encourage users to immediately create or join a group after onboarding (e.g., guided flow vs. letting them explore Home first).

---

## Architecture

This section captures high-level architectural ideas. These are not final and may change as we refine the design.

### Architecture Decisions (Draft for Promotion)

- **Backend choice**
  - Use **Cloud Firestore** for 0.1.0.
- **Modularization**
  - Adopt Now in Android style modularization from the start (avoid later refactor).
- **Firestore structure**
  - Use top-level collections with subcollections where needed.
  - Example: `groups/{groupId}` with subcollections for attendance entries and group-specific data.
- **Sync cadence**
  - Sync on app start and via manual pull-to-refresh.
- **Write handling**
  - MVP: failed writes can be dropped after user feedback (retry queue can be added later).
- **Conflict policy**
  - User-owned data: local edits win and overwrite server on next sync.
  - Shared data: backend wins; local edits are rejected/overwritten.
- **IDs and timestamps**
  - IDs: client-generated UUIDs for offline creation.
  - Timestamps: server timestamps are canonical; use local provisional timestamps for UI until synced.
- **Access control**
  - Only group members can read group data.
  - Only admins can edit group settings and rules.
- **Paging**
  - Attendance history is paged (do not load full history by default).

### Offline-first with Backend Sync

- **Local storage (Room)**
  - Use Room as the main local persistence mechanism for:
    - User profile basics.
    - Groups and memberships.
    - Attendance entries.
    - Routines and exercises.
  - The local database should be treated as the source of truth for most app logic.

- **Backend**
  - Use a backend service for:
    - **User authentication**.
    - **Multi-device group data** (shared across users).
    - **Push notifications**.
  - Likely approach:
    - **Firebase Auth**:
      - Email/password sign-up and login.
      - Google sign-in as an option.
    - **Firebase database**:
      - Preference for **Cloud Firestore** due to familiarity.
      - Alternative: Realtime Database (note as an option, but not preferred for now).
    - **Firebase Cloud Messaging (FCM)**:
      - For push notifications (e.g., group attendance updates).

- **Sync model (draft)**
  - Data modelled in Firestore (or similar) for:
    - Users.
    - Groups.
    - Group memberships.
    - Attendance entries.
  - Syncing strategy:
    - Fetch server state into local Room tables.
    - Write operations:
      - Update local DB first and enqueue sync to backend.
      - Handle eventual consistency and conflict resolution (details to be refined later).

Promoted to `ProjectPlanning.Shared.md` (architecture and data sync spec for 0.1.0).

### Layers & Modules (inspired by `nowinandroid`)

Draft module boundaries and layers:

- **Core modules**
  - `core-model`:
    - Shared data classes and domain entities.
  - `core-data`:
    - Repository interfaces and common data patterns.
  - `core-database`:
    - Room database, DAOs, local data sources.
  - `core-network` (or similar):
    - Firebase/remote data sources, API clients.
  - `core-designsystem`:
    - Shared UI components, theming, typography, colors.

- **Feature modules**
  - `feature-group`:
    - Group creation, membership, attendance tracking screens and logic.
  - `feature-planner`:
    - Routine and exercise management screens and logic.
  - `feature-home`:
    - Home/dashboard that brings together group status and planner highlights.
  - `feature-auth`:
    - Login, registration, account management flows.

- **App module**
  - Composes feature modules.
  - Hosts the root navigation graph.
  - Sets up DI and app-wide configuration.

These module names and responsibilities are tentative and will be refined before being promoted to `ProjectPlanning.md`.

---

## Dependencies

### Internal (Android / Google / Jetpack)

Ideas for internal dependencies to consider:

- **Language & base**
  - Kotlin.
  - Coroutines and Flows.

- **UI**
  - Jetpack Compose.
  - Material 3.
  - Navigation-Compose.

- **Architecture & DI**
  - Hilt for dependency injection (aligned with `nowinandroid`).

- **Storage**
  - Room for local relational storage.
  - DataStore for key-value preferences (e.g., theme, notification settings, onboarding state).

- **Background work**
  - WorkManager for background jobs (e.g., sync retries).

### External (Third-party)

Ideas for third-party dependencies:

- **Firebase**
  - Firebase Auth (email/password + Google).
  - Cloud Firestore (preferred) for backend data storage.
  - Firebase Cloud Messaging (FCM) for push notifications.
  - (Later) Firebase Storage for evidence photo uploads.

- **Image loading**
  - Coil for image loading (e.g., avatars and, later, photos).

- **Fitness / exercise APIs (future)**
  - To be evaluated later when we design the exercise catalog integration.

---

## Non-Functional Requirements

Brainstormed non-functional considerations:

- **Offline behavior**
  - Core flows (view routines, log attendance) must work without network.
  - Changes should sync automatically when connectivity returns.

- **Performance**
  - Fast app startup on mid-range devices.
  - Smooth scrolling and interactions in lists and feeds.

- **Reliability**
  - Sync operations should:
    - Be resilient to temporary network failures.
    - Retry intelligently.

- **Privacy & data handling**
  - For MVP:
    - Target audience is small, trusted friend groups.
    - Evidence photos (once implemented) are visible to all members in a group by default.
  - Future considerations:
    - Better privacy controls (e.g., who can see what).
    - Account deletion and data export.
    - Clearer user-facing explanations of how data is stored and used.

- **Permissions**
  - Internet permission (manifest only).
  - Camera and photo library access:
    - Only requested when evidence photos are implemented.
    - Must include a clear justification (e.g., "to attach optional photo evidence to your gym visits").
  - Location access:
    - Only requested if location is used (e.g., to tag gym visits).
    - Optional; users should be able to log attendance without sharing their location.

---

## Design & UX Notes

These are early, high-level design and UX ideas that can be refined over time.

### Home Screen

Possible sections on the main Home screen:

- **Today’s status**
  - Quick view of:
    - Whether the user has logged a gym visit today.
    - Shortcut to "Log attendance".

- **Group highlights**
  - Summary of:
    - Recent activity in the user’s groups.
    - Who has logged visits recently.

- **Upcoming or active routines**
  - Today’s planned workout (if any).
  - Shortcut to open the routine in the planner.

The Home screen should function as a **dashboard** that connects the group challenge and planner features.

**Home layout (MVP)**
- Home is a scrollable list of sections.
- Each section can define:
  - Title.
  - Optional subtitle (tappable, e.g., "View all").
  - Tap behavior (section-level CTA or button).

**Groups section (MVP)**
- Shows only the first group (full list lives in Groups tab).
- Content:
  - Group name.
  - User’s attendance count (current checkpoint).
  - User’s position in leaderboard (current checkpoint).
  - Time remaining in current checkpoint.
- Tap behavior:
  - Tapping the section opens Group Overview.

### Navigation

- **Single-activity, Jetpack Compose**.
- **Bottom navigation bar**:
  - MVP destinations: **Home**, **Groups**, **Planner (WIP)**, **Settings**.
  - Planner tab opens a "Coming soon / Work in progress" screen.
  - Home is the start destination after cold start.
  - When app resumes from background, restore the last visited screen.
  - If user is not authenticated, show only the auth flow (no tabs).
  - Primary CTA: "Log attendance" opens a non-camera log flow in MVP.
  - Future: primary CTA can open camera viewfinder when photo evidence is added.

### Top Bar

- **Left side**:
  - Tappable **profile area** (avatar + greeting).
  - Opens profile UI (details TBD).

- **Right side actions**:
  - **Notifications** button (placeholder for future use).
  - **Feedback** button (placeholder for future use).

### Visual Style

- Modern, clean, and playful style, consistent with **Material 3** guidelines.
- Emphasis on:
  - Readable typography.
  - Clear status states (e.g., "you’re on track", "you haven’t logged this week").
  - Friendly, non-intimidating visuals suitable for beginners.

---

## Open Questions / Risks

This section tracks uncertainties and potential risks that we should revisit before promoting ideas to `ProjectPlanning.md`.

- **Backend technology choice**
  - Firestore vs Realtime Database:
    - Data modeling trade-offs.
    - Offline support behavior differences.
    - Familiarity vs suitability.

- **Sync complexity**
  - Handling:
    - Conflicting updates (e.g., duplicated or out-of-order attendance logs).
    - Partial offline periods where multiple users log entries before syncing.

- **Abuse and moderation**
  - Potential for:
    - Fake evidence or misleading photos.
    - Offensive content in photos or group rules.
  - MVP may ignore this for trusted groups, but it is a long-term risk.

- **Photo storage costs**
  - Storing even low-quality compressed photos can accumulate costs over time.
  - Need a clear retention and deletion policy when evidence photos are implemented.

- **Navigation complexity**
  - As more features are added, ensuring:
    - Simple, intuitive navigation.
    - Avoiding overwhelming the Home screen and bottom nav.
  - Deciding when (or if) to introduce more advanced navigation patterns (e.g., deep links for many screens).

These questions should be revisited and answered before we move toward locking down implementation details in `ProjectPlanning.md`.

---

## Naming & Branding

This section is for brainstorming potential app names, taglines, and overall brand direction. Nothing here is final.

- **Working name ideas (examples, exploratory)**
  - Names that evoke:
    - Group effort (e.g., "Squad", "Crew", "Team").
    - Habit/consistency (e.g., "Streak", "Routine").
    - Motivation/accountability (e.g., "Push", "Commit").
  - We can combine these themes into candidate names later (e.g., "Gym Squad", "Habit Crew", "CommitFit", etc.).

- **Brand tone**
  - Friendly, supportive, and playful rather than harsh or overly competitive.
  - Should feel:
    - Approachable to beginners.
    - Still motivating for more experienced users.

- **Visual identity (high-level)**
  - Likely aligned with Material 3, but with:
    - Bold accent colors for active/“you did it” states.
    - Softer neutrals for rest states.
  - Iconography:
    - Simple, recognizable shapes (e.g., group silhouettes, checkmarks, subtle fitness references).

- **Tagline themes (exploratory)**
  - "Keep each other moving."
  - "Stay accountable, together."
  - "Make the gym a group habit."

---

## Monetization & Business Model

This section explores potential monetization approaches. For now, the focus is on a friend-group MVP with no pressure to monetize early.

- **MVP monetization stance**
  - Initial versions can be:
    - **Free** and focused on value for small friend groups (including the initial real-world group).
    - No ads in early versions to keep the experience clean and focused.

- **Future monetization options (brainstorming)**
  - **Subscription model**:
    - Free tier:
      - Core features (groups, basic planner).
    - Premium tier:
      - Advanced analytics.
      - More groups or larger group sizes.
      - Enhanced planner features (e.g., templates, coach plans).
  - **One-time purchases / upgrades**:
    - Unlocking certain advanced features permanently.
  - **Coach / trainer features**:
    - Paid plans where trainers can manage groups or design routines for clients.

- **Non-goals / constraints (early thinking)**
  - Avoid:
    - Pay-to-win mechanics that give unfair advantages in group challenges.
    - Intrusive ads that interrupt core flows (e.g., logging attendance, viewing routines).

---

## Analytics & Success Metrics

This section captures ideas for what we might eventually measure to understand whether the app is successful and how people use it.

- **High-level success metrics**
  - **Engagement & retention**
    - Daily/weekly/monthly active users.
    - Retention over 7/30/90 days.
  - **Group health**
    - Number of active groups per user.
    - Percentage of group members who log at least one attendance per week.
    - Percentage of groups that reach the end of a challenge period.
  - **Attendance behavior**
    - Average gym visits per user per week.
    - Distribution of attendance across days of the week.

- **Feature-level metrics**
  - Group challenge:
    - How often users create or join new groups.
    - How frequently notifications lead to app opens and subsequent logging.
  - Planner:
    - How many users create routines.
    - How often routines are viewed on workout days.

- **Instrumentation ideas (later)**
  - Potential use of:
    - Firebase Analytics (or similar) for basic event tracking.
    - Custom events for:
      - Group creation/join events.
      - Attendance log events.
      - Routine create/edit/open events.

- **Privacy considerations**
  - Analytics should:
    - Focus on aggregated behavior rather than individual identities.
    - Avoid storing sensitive personal information in analytics events.

---

## Versioning & Release Strategy

This section explores how we want to name and structure app versions over time.

- **Proposed versioning scheme**
  - Use a **semantic-style versioning** format: `MAJOR.MINOR.PATCH` (e.g., `1.3.2`).
  - Meaning:
    - **MAJOR**:
      - Incremented when we introduce big, breaking changes (e.g., major redesigns, significant data model changes that require migrations that might affect compatibility).
    - **MINOR**:
      - Incremented when we add or significantly enhance features in a backward-compatible way.
      - Example: introducing a new feature module, or substantially expanding the planner.
    - **PATCH**:
      - Incremented for bug fixes and small improvements that do not change core behavior or contracts.

- **Pre-1.0 behavior**
  - While in early development (e.g., versions `0.x.y`):
    - Expect faster iteration and potentially more breaking changes.
    - MAJOR is `0` and:
      - MINOR increments can still represent substantial changes.
      - PATCH focuses on smaller fixes on top of a given minor iteration.

- **Planned version roadmap (tentative)**
  - These targets are exploratory and may change as we learn more.
  - `0.1.0` – MVP:
    - Email + password authentication for onboarding and login.
    - Core group challenge functionality (create/join groups, basic attendance logging and viewing).
    - Basic planner (create routines and exercises, no advanced integrations).
    - Minimal notifications (e.g., simple group update notifications).
  - `0.2.0` – First feature update:
    - Add **Google sign-in** on top of email/password.
    - Small UX and onboarding refinements identified from early usage, including clearer password strength guidance and rules in the signup flow.

- **Android-specific considerations**
  - **versionName** (user-facing) can follow the `MAJOR.MINOR.PATCH` scheme directly.
  - **versionCode** (integer) must:
    - Increase with every release.
    - Potential strategy (to be refined later):
      - Encode version as `MAJOR * 10000 + MINOR * 100 + PATCH`, or
      - Use a simple monotonically increasing integer tracked independently of versionName.

- **Release strategy (high-level)**
  - Early stages:
    - Internal builds for the initial friend group (e.g., `0.1.0`, `0.2.0`).
    - Fast patch releases (`0.1.1`, `0.1.2`, etc.) to fix issues discovered in real usage.
  - Later:
    - Move toward a more stable `1.0.0` once:
      - Core flows (group challenge, planner, basic navigation) feel robust.
      - We have some confidence in the data model and sync behavior.

- **Tagging and documentation**
  - For each significant release:
    - Maintain a short changelog entry (even if only in documentation) describing:
      - New features.
      - Fixes.
      - Any breaking changes or required user actions (if applicable).
  - Within this brainstorming document, we can append simple annotations like `[target: 0.1.0]`, `[target: 0.2.0]`, etc. to bullets once we have a tentative idea of which version should deliver a given feature or change.
