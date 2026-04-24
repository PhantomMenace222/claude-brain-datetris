---
name: Always include the full restart/verify command when asking the user to test
description: When PM (Claude.ai chat / this Claude Code session) asks the user to test or verify something, include the exact shell command(s) to run — never a bare "please verify".
type: feedback
originSessionId: c732c6f5-6b30-49a9-969c-571f3093382c
---
When asking the user to test or verify *anything* backend-related — after a code change, a config tweak, a password reset, a migration, whatever — include the **full, ready-to-paste command** in the same message. That means the backend restart incantation (kill old PID → start uvicorn → tail log, or whatever the current convention is) plus the verify step (curl, sqlite query, etc.). No bare "please test this" or "please verify login works" without the command literal right there.

**Why:** The user is running the dev loop. Saying "please verify" without the command forces them to context-switch — reconstruct the kill/restart/log/curl sequence themselves, which is exactly the kind of manual toil this session exists to eliminate. Established M82 (2026-04-24).

**How to apply:** Any message to the user that contains a verify/test/confirm ask must also contain the shell command(s) in a code block. If the backend needs restarting, include that too (process kill, venv activation, uvicorn launch, log tail). If a curl is needed, include it with the exact port, path, headers, and expected status/response. The rule applies whether or not the user already has the backend running — don't assume state, spell it out. If the exact restart sequence for this repo is uncertain, check `run.sh` / `backend.pid` / recent session notes before composing the verify ask rather than punting.
