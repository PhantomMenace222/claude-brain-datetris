---
name: Strict deploy order — never deploy from a fix branch
description: Prod deploys must always run from `development` after a fix branch has been merged; never from the fix branch directly
type: feedback
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
**Rule:** `bash deploy/deploy.sh` may only be run when the working tree is on `development` (or `main` for a tagged release), with the target fix branch already merged in and pushed. Never run deploy.sh while checked out on a `fix/*` branch.

Strict order:
1. Commit on `fix/<desc>`
2. `git checkout development`
3. `git merge --no-ff fix/<desc>`
4. `git push origin development`
5. (If migration needed) scp + run migration on prod
6. `bash deploy/deploy.sh`
7. Delete the local fix branch

**Why:** Established 2026-04-23 after a false-alarm scare about fix/blocked-users-api supposedly going to prod without a merge (investigation showed no violation had actually happened, but the rule is worth codifying pre-emptively). The risk: `deploy.sh` rsyncs whatever's in the *working tree* of the current branch — if you run it on a fix branch, prod gets un-reviewed, un-merged, potentially incomplete code, and the git history is silent about what went out. That's an audit and recovery nightmare.

**How to apply:**
- Before running `bash deploy/deploy.sh`, ALWAYS verify: `git branch --show-current` must print `development` (or `main`).
- If the user's instructions skip the merge-first step, pause and ask — don't infer it.
- If something is already on prod that isn't on `development` (caught by `scp ... && diff`), that's a recovery situation: merge the commits to `development` to bring git history in sync with prod before taking further action. Don't "fix" it by reverting prod — that discards live work.
- The `fix/<desc> → development → (on App Store submit) main + tag` workflow in `feedback_git_workflow.md` defines the branch flow. This rule adds: **deploys run from the target of that flow, never from an upstream fix branch.**
