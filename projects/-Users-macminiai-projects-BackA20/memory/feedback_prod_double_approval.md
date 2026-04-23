---
name: Prod operations require 2-step manual approval
description: Every prod-modifying action needs two explicit user approvals, even when the session has blanket AUTO-APPROVE — this rule overrides.
type: feedback
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
**Hard rule:** Any operation that modifies prod Droplet state (`167.71.136.223` / `api.datetris.date`) must pause for **two explicit user approvals** before execution. This rule overrides any blanket "AUTO-APPROVE" instruction in the same session — AUTO-APPROVE applies to local/git/read-only operations only.

## What counts as prod-modifying (requires double approval)

- `bash deploy/deploy.sh` — rsyncs code + restarts service
- `git push origin main` — release tag side (the App Store–submit path)
- SSH commands that write prod state:
  - `sed -i ... /etc/backa20.env` (VERSION bumps, secret edits, config changes)
  - `systemctl restart backa20` (or stop/start/reload)
  - Running migration scripts (`python migrate_*.py` against prod DB)
  - Any `sqlite3 /var/lib/backa20/db.sqlite "UPDATE..."` / `"DELETE..."` / `"INSERT..."`
  - `chown`, `chmod`, `rm`, `mv` under `/srv/backa20/` or `/var/lib/backa20/`
- `scp` uploading files onto the Droplet (staging a script to be run)
- Prod HTTP writes (curl POST/PUT/DELETE against `api.datetris.date`) that mutate DB state, even as a "test"

## What does NOT require double approval (still AUTO-approvable per normal rules)

- `curl GET https://api.datetris.date/...` — read-only
- `ssh root@... "grep ... /etc/backa20.env"` / `"sqlite3 ... SELECT ..."` / `"systemctl status"` — read-only
- `git push origin development` — pushes to the remote `development` branch; does not touch prod
- Local dev operations (local uvicorn, DevDB, fix branches)

## Approval protocol

Ask in two discrete turns; do not collapse them into one message.

**Turn 1 (ask approval 1/2):**
> Ready to `<specific command>` on prod. This will `<one-line impact>`. Approve 1/2?

Wait for the user's explicit "yes" / "approve" / "go" response. Ambiguous answers ("ok", "sure") still count as yes — but a silent or deflected response does not.

**Turn 2 (ask approval 2/2):**
> Confirm: `<exact command that will run>` on prod will `<more specific impact including any irreversibility>`. Approve 2/2?

Wait for the user's second explicit approval.

**Turn 3 (execute):** run the command, report result.

If the user revokes or expresses doubt at any turn, stop. Do not re-ask without changed context.

## Why

Established 2026-04-23 after a false-alarm workflow scare (the user believed `fix/blocked-users-api` had been deployed unmerged; diff showed it hadn't, but the incident motivated tightening). The underlying concern: prod touches from a CLI agent are hard to undo, the Droplet is shared infra, and hooks/auto-approve make the single-keystroke-to-prod distance too short. Two deliberate approvals force the user into the loop twice per prod action, which is cheap insurance against rogue deploys, wrong-env VERSION edits, or accidental destructive SQL.

## Composition with other rules

- **Overrides:** `feedback_auto_approve.md` ("proceed without asking on ... git/SSH/curl"). For PROD SSH/curl-write, this stricter rule wins.
- **Reinforces:** `feedback_deploy_workflow.md` (deploy must run from `development`). The double-approval asks happen ON TOP of the branch-check — first verify you're on development, then ask 1/2, then ask 2/2, then `deploy.sh`.
- **Does not block:** `feedback_version_convention.md` mechanical VERSION bumps on the user's say-so. The user giving the banner value IS the first approval; the second approval is still required before the SSH write.
