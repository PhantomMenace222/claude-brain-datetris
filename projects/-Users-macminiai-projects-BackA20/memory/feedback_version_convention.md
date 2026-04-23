---
name: Backend VERSION env var convention
description: Backend VERSION reflects the code currently deployed to prod, snapshotting the FE banner at deploy time. Never bumps for FE-only fix-branch work that hasn't shipped to prod.
type: feedback
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
**Rule (corrected 2026-04-23, M10):** Backend `/api/version` (read from `/etc/backa20.env` `VERSION` via `config.py:17`) reflects **the code currently deployed to prod**. It snapshots the FE banner *at the moment of an actual prod code deploy* and stays put until the next deploy.

**Critically: VERSION does NOT bump for FE banner movement during fix-branch work that hasn't shipped to prod.** If the FE bumps its banner from `(105)` to `(105.1)` while iterating on a fix branch, the backend stays on whatever VERSION reflects the last actual prod deploy. The FE banner can move freely; the backend banner only catches up when code actually ships.

Format: `YYYY-MM-DD(N)` for bare or `YYYY-MM-DD(N.M)`.

**When to bump VERSION:**
- ONLY at the moment of an actual prod code deploy (`bash deploy/deploy.sh` or `git push origin main` for a tagged release).
- Set the new value to whatever the FE banner is at deploy time (could be a bare `(N)` after a merge, or an `(N.M)` if you're shipping mid-fix-branch backend code that needs visibility).
- A backend-only deploy (e.g. shipping new endpoints for FE testing during a fix-branch cycle) DOES bump VERSION to the FE's then-current banner — that's still a real code deploy and prod observers should see what's running.

**When NOT to bump VERSION:**
- FE starts a fix branch and bumps its banner: backend stays as-is.
- FE merges a fix branch back to its `development` and drops the banner to bare `(N)`: backend stays as-is.
- IPA/AAB submitted, accepted, or rejected: backend stays as-is until the corresponding code lands on prod.
- Any session activity that doesn't end with `bash deploy/deploy.sh` succeeding.

**Example timeline:**
1. Body-photo deploy this morning → VERSION set to `(104)`. Code on prod = development tip including body-photo.
2. FE starts a fix branch, bumps its banner to `(105.1)`. **Backend stays on `(104)`** — no deploy happened.
3. FE iterates: `(105.1)` → `(105.2)` → `(105.4)`. **Backend stays on `(104)`.**
4. Backend deploys blocked-users endpoints for FE testing → VERSION set to whatever the FE banner is at THAT moment (e.g. `(105.2)`).
5. More FE iteration: `(105.4)`, `(105.5)`. Backend stays on `(105.2)` because no further deploy.
6. Eventually FE merges and IPA 105 ships. New backend deploy → VERSION updates to whatever the FE merged-banner is then.

**Why:** Prior session had this rule as "strict lockstep with FE banner". That was wrong — it caused VERSION to advertise code that wasn't actually on prod, defeating the debuggability the field exists for. The corrected rule (set 2026-04-23 in M10): VERSION must always be a truthful description of what's running, not what's being worked on.

**How to apply:**
- Before bumping VERSION, ask: "Did code just deploy to prod?" If no, do not bump. Push back if instructed to bump without a deploy.
- After running `bash deploy/deploy.sh`, ask the user what VERSION value to set (the current FE banner usually, but they choose).
- The SSH-sed mechanic is unchanged — `sed -i 's|^VERSION=.*|VERSION="YYYY-MM-DD(N.M)"|' /etc/backa20.env && systemctl restart backa20`. Always shell-quote the value (parens break bash sourcing in `deploy.sh` step [5/6]).
- `bash deploy/deploy.sh` does NOT itself update VERSION — it's a separate SSH step.
- Per `feedback_prod_double_approval.md`, the SSH write requires 2-step user approval like any other prod-modifying op.
