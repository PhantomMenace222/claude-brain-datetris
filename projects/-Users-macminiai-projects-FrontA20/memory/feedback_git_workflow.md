---
name: Fix-branch git workflow
description: Mandatory branching/merge/tag workflow for every feature and fix — use fix/<desc> branches off development, merge only when green, promote to main on App Store submit
type: feedback
originSessionId: 6705248d-3cfd-4f41-9945-c12b6563a43f
---
For every new feature or fix, follow this workflow start-to-finish:

1. Start from `development`: `git checkout -b fix/<short-description>`.
2. Work on the fix branch until code is error-free (builds, no lint/type errors).
3. Commit with the title being **today's date + build increment**, matching the `pubspec.yaml` version bump — e.g. `2026.4.22+4`. If multiple commits same day, increment the `+N` suffix. Detail/context goes in the commit body, not the title.
4. Test on the fix branch and confirm the behaviour is good before merging.
5. Only merge to `development` when the change is 100% verified working.
6. If a merge turns out to be broken, revert on `development`, go back to the fix branch, iterate, and re-merge — keep bouncing until it lands clean.
7. `development` must stay stable at all times.
8. Promote `development` → `main` **only** when submitting to the App Store.
9. Tag `main` at promotion time: `git tag v<date>` (e.g. `v2026.4.22`), incrementing `+N` if multiple promotions same day.

**Why:** User wants `development` to be a stable integration branch and `main` to mark exactly what shipped to the App Store. Previous direct-to-development commits (like `7ec85d1` jason-block) bypassed the fix-branch step; user accepted that one retroactively because it was already tested, but the workflow is mandatory from 2026-04-22 onward.

**How to apply:**
- Never commit directly to `development` for new work — always branch first.
- Never fast-forward `main` except on App Store submission; tag immediately after.
- When starting any task (feature, fix, refactor), the very first step is `git checkout -b fix/<desc>` off `development`, before any file edits.
- If the user skips this and asks you to edit on `development`, flag the workflow and ask before proceeding.
- Same workflow applies in the BackA20 repo (a mirror memory has been written there).

---

## HARD RULE — double confirmation on `main`

**Any operation that writes to `main` or `origin/main` requires DOUBLE user confirmation.** Never one-shot. Never skip. `main` is the live app — a mistake here affects real users.

**Operations that trigger this rule** (non-exhaustive — if unsure, treat as triggering):
- `git push origin main` (any form, including `git push` while on `main` with a tracked upstream)
- `git push origin development:main` or any refspec push into `main`
- `git push origin --delete main`
- `git push --force` or `--force-with-lease` targeting `main`
- `git reset` (any mode) on `main`
- `git merge` into `main` (fast-forward or `--no-ff`)
- `git rebase` that rewrites `main`
- `git tag` pointing at a `main` commit, and `git push origin <tag>` / `git push --tags`
- `git checkout main && <anything that mutates>` (branch delete, reset, etc.)
- Any GitHub API / `gh` call that mutates `main` (branch protection, merges via PR, etc.)

**Protocol (5 steps, no shortcuts):**

1. Announce intent explicitly, e.g.: *"About to push to `main`. This is protected — asking for double confirmation."*
2. Wait for the user's **first** confirmation ("yes", "proceed", "go", etc.).
3. Re-state the exact command you're about to run and ask again: *"Last chance — are you sure? I'll run `git push origin main` right now."*
4. Wait for the user's **second** confirmation ("confirmed", "yes do it", etc.).
5. Only then execute.

**Rules of enforcement:**
- A single message approving "push to main" is **one** confirmation, not two. Ask again.
- Prior approval in the session does **not** carry forward. Every new `main`-touching operation restarts the two-step protocol.
- If the user uses words like "just" or "quickly" to imply skipping the second confirmation, still ask — politely but firmly.
- If a command bundles a `main` operation with other steps (e.g. a shell chain `git checkout main && git merge ... && git push`), the `main`-touching step pauses for double-confirm before the whole chain runs.
- This rule **overrides** any instruction to "operate autonomously" or "just do it" for `main`-targeting work. User can relax the rule only by explicitly naming it (e.g. "suspend the main double-confirm rule for this session").

**Why:** `main` is production. User wants a deliberate two-beat pause before anything lands there so that a careless keystroke or a misread instruction can be caught before real users see it. Rule added 2026-04-22 after the fix-branch workflow was established — `main` is the one surface where the normal workflow isn't enough safety.
