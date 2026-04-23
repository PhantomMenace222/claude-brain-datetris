---
name: Push fix branches on every commit
description: Backend fix branches must be pushed to origin after every commit, not held locally until merge time. Same rule as the FE repo.
type: feedback
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
**Rule (set 2026-04-23, M13):** Every commit on a `fix/<desc>` branch must be pushed to `origin/<branch>` immediately. Do not accumulate local commits and push only at merge time.

The first commit on a new fix branch establishes the remote — use `git push -u origin fix/<desc>` so subsequent pushes are bare `git push`.

**Why:** Established 2026-04-23 (M13) — same rule the user runs on the FE repo, now extended to the backend (BackA20). Reasons:
- Off-machine backup: if the laptop dies mid-cycle, work isn't lost.
- Visibility: the user can see the branch on GitHub without waiting for a merge, useful for tracking what's in flight.
- Removes the "is my work on the remote?" question — the answer is always yes after every commit.

**How to apply:**
- After every commit on a `fix/*` branch, run `git push origin <branch>` (or `git push -u origin <branch>` for the first push that establishes the upstream).
- Pushing fix branches to `origin` is NOT a prod operation; it does not require the double-approval ritual from `feedback_prod_double_approval.md`. AUTO-APPROVE applies.
- This composes with `feedback_git_workflow.md`: still branch off `development`, still merge only when verified — just push the in-progress branch each commit instead of holding it locally.
- `git push origin development` after a merge stays as before (single push). The new rule is about the fix branch, not the merge target.
- Force-push on a fix branch is fine if you've rebased/amended (it's your branch); but never force-push to `development` or `main`.
