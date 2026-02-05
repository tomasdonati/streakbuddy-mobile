## Planning Guide (Read First)

This file explains how the planning documents are organized and how to move ideas from brainstorming to implementation-ready specs. It should stay short and instructional.

---

## File Hierarchy & Roles

1. `ProjectBrainstorming.md`
   - Idea sandbox. Can contain conflicting or exploratory notes.
   - Use this file to iterate on decisions and capture open questions.
2. `ProjectPlanning.Shared.md`
   - Source of truth for implementation-ready, platform-agnostic specs.
   - Anything in this file is considered stable enough to build.
3. `ProjectPlanning.Android.md`
   - Android-specific implementation details only.
4. `ProjectPlanning.iOS.md`
   - Reserved for iOS-specific details (future use).

---

## Promotion Rules

Promote ideas only when they are ready to implement.

An idea is ready when:
- The scope is clearly bounded.
- Key UX and data-model decisions are resolved.
- No blocking open questions remain.

When promoting:
- Rewrite the idea in `ProjectPlanning.Shared.md` as a clear, implementation-ready spec.
- Add a short note in `ProjectBrainstorming.md` indicating it was promoted (do not delete the original).

---

## Update Rules

- Keep `ProjectBrainstorming.md` flexible and exploratory.
- Keep `ProjectPlanning.Shared.md` concise, stable, and internally consistent.
- Put platform-specific details only in the platform files.

---

## Scope Notes

- If MVP scope changes, update `ProjectPlanning.Shared.md` first.
- If a conflict exists between brainstorming and planning, planning wins.
