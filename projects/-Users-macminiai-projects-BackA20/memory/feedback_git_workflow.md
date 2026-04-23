---
name: Fix-branch git workflow
description: Mandatory branching/merge/tag workflow for every feature and fix — use fix/<desc> branches off development, merge only when green, promote to main on release
type: feedback
originSessionId: 6d6598a4-d473-4ac3-9697-347b555f996b
---
For every new feature or fix, follow this workflow start-to-finish:

1. Start from `development`: `git checkout -b fix/<short-description>`.
2. Work on the fix branch until code is error-free (builds, no lint/type errors, tests pass where applicable).
3. Commit with the title being **today's date + build increment**, matching the app-side version bump — e.g. `2026.4.22+4`. If multiple commits same day, increment the `+N` suffix. Detail/context goes in the commit body, not the title.
4. Test on the fix branch and confirm the behaviour is good before merging.
5. Only merge to `development` when the change is 100% verified working.
6. If a merge turns out to be broken, revert on `development`, go back to the fix branch, iterate, and re-merge — keep bouncing until it lands clean.
7. `development` must stay stable at all times.
8. Promote `development` → `main` **only** when the coupled frontend ships to the App Store (frontend drives the promotion cadence).
9. Tag `main` at promotion time: `git tag v<date>` (e.g. `v2026.4.22`), incrementing `+N` if multiple promotions same day.

**Why:** User wants `development` to be a stable integration branch and `main` to mark exactly what was in production when the paired frontend shipped. Workflow established 2026-04-22 and applies to both FrontA20 (Flutter) and BackA20 (this repo) in lockstep.

**How to apply:**
- Never commit directly to `development` for new work — always branch first.
- Never fast-forward `main` except on release; tag immediately after.
- When starting any task (feature, fix, refactor), the very first step is `git checkout -b fix/<desc>` off `development`, before any file edits.
- If the user skips this and asks you to edit on `development`, flag the workflow and ask before proceeding.
- The same workflow is live in the FrontA20 repo.

## HARD RULE — main branch requires double confirmation

Any operation that writes to `main` or `origin/main` requires **two separate explicit confirmations** from the user before execution. No exceptions, no inference, no "I assume you want…".

**Operations in scope (non-exhaustive):**
- `git push origin main` (any form, including `development:main`, `HEAD:main`, push-after-checkout)
- `git push origin --delete main`
- `git reset` on `main` (hard/soft/mixed — all of them)
- `git merge <anything> main` / `git merge main` while checked out on another ref that targets main
- `git tag` pointing at a `main` commit, and `git push origin <tag>` for tags on main
- `git rebase` onto main
- `git cherry-pick` onto main
- Any `git commit --amend` on a checked-out `main`
- Any force variant: `--force`, `--force-with-lease`, `+refs/...`

**Protocol — exactly these five steps, in order:**
1. Claude announces intent explicitly: "About to do X on main" (naming the exact command).
2. User confirms first time (e.g. "Yes, proceed").
3. Claude confirms AGAIN before executing: "Are you sure? Last chance."
4. User confirms second time (e.g. "Confirmed").
5. Only then execute.

**Why:** `main` is what ships — it's the live app. A mistake here affects real users, and most main-branch operations are irreversible at the mistake's blast radius (force-pushes overwrite history, tag pushes publish versions, merges publish code). The double-confirmation ritual costs a few seconds and makes it impossible to slip from "planning" to "execution" in a single turn of inattention. Established 2026-04-22 after a session where branch state got tangled and a `git reset --hard` on a shared branch was nearly proposed.

**Corollaries:**
- Never bundle a main-branch operation into a compound command (`&&`-chain) with other ops — isolate it so the two confirmations apply to that single command, not a batch.
- If the user pre-approves a multi-step task that happens to contain a main-branch operation, the main-branch step still gets its own double-confirm — pre-approval for the task does not waive this rule.
- If the user types only "Confirmed" without having confirmed first, treat it as the first confirmation only and ask again.
