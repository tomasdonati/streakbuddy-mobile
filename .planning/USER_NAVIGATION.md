# User Navigation (Platform-Agnostic)

This document defines how users navigate through the application, independent of platform-specific implementation details.

It applies to both Android and iOS and should be used as the shared navigation source of truth.

## Purpose

The navigation system must help users move quickly between the app's main areas while preserving context and minimizing friction for core actions like joining a group and logging attendance.

## Navigation Model

The app has two root states:
- Unauthenticated state
- Authenticated state

Users move between these root states based on authentication status.

## Root State: Unauthenticated

Visible experience:
- Authentication and onboarding flow only.
- Main app tabs are not accessible.

Supported entry points:
- Normal app open (no invite)
- App open via invite link

Main outcomes:
- User signs up or logs in.
- If invite context exists, user can continue into invite-join flow.
- On successful authentication, user moves to Authenticated state.

## Root State: Authenticated

Visible experience:
- Main application shell with top-level navigation.

Top-level destinations:
- Home
- Groups
- Planner (WIP in MVP)
- Settings

Primary global action:
- Log attendance
- This is a global action, not a tab destination.

Behavior rules:
- On cold start after authentication, default destination is Home.
- On resume from background, restore last visited destination when possible.

## Deep Link Navigation

### Invite Link

If unauthenticated:
- Open invite intro flow.
- Continue through auth.
- Resume invite confirmation.
- On join success, navigate to target Group Overview.

If authenticated:
- Open invite confirmation flow directly.
- On join success, navigate to target Group Overview.

If user is already a member:
- Skip join confirmation and open Group Overview.

If invite is invalid/expired:
- Show recovery state with action to go to Home.

## Main User Navigation Flows

### Flow 1: First Launch (No Invite)
1. Open app.
2. Sign up or log in.
3. Land on Home.

### Flow 2: First Launch via Invite
1. Open app from invite link.
2. Complete sign up or log in.
3. Confirm join.
4. Land on Group Overview for the invited group.

### Flow 3: Daily Return
1. Open app.
2. Land on Home or restored destination.
3. Navigate to Groups/Home and log attendance.

### Flow 4: Group-Centric Usage
1. Open Groups tab.
2. Select a group.
3. View Group Overview.
4. Trigger Log attendance action.

### Flow 5: Account & Preferences
1. Open Settings tab.
2. Manage notification and account actions.
3. Return to previous destination or Home.

## Destination Definitions

### Home
- Dashboard entry point for authenticated users.
- Shows key summaries and shortcuts.

### Groups
- Full list of joined groups.
- Entry point to each Group Overview.

### Group Overview
- Group-specific detail view.
- Shows members, counts, and leaderboard modes.
- Supports attendance logging entry.

### Planner (WIP)
- Placeholder destination in MVP.
- Reserved for future planner workflows.

### Settings
- User preferences and account management entry.

### Auth Screens
- Sign up, log in, password reset, and required display name step.

## Navigation Rules

1. Keep top-level navigation stable.
- Main destinations remain accessible from anywhere in authenticated shell.

2. Keep feature-specific depth inside feature flows.
- Details (like Group Overview) are entered from relevant top-level destination.

3. Preserve context after actions.
- After completing a sub-flow, return user to the most relevant destination.

4. Avoid dead ends.
- Every non-terminal screen has a clear forward or back path.

5. Handle errors with recovery actions.
- Invalid invite, network failures, and auth failures must provide clear retry/exit navigation.

## MVP Scope Notes

In MVP (`0.1.0`):
- Planner is a placeholder destination.
- Walkthrough system is deferred.
- Camera/photo evidence navigation is deferred.

## Ownership and Updates

- This file defines shared navigation intent for all platforms.
- Platform-specific implementation details belong in platform planning docs.
- If navigation behavior changes, update this file first, then sync platform-specific files.
