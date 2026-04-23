---
name: Auto-approve safe operations
description: User wants Claude Code to act without asking for approval on routine safe operations (file edits, git, SSH, curl) — chat layer drives decisions
type: feedback
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
Do not ask for approval before performing safe routine operations: file edits, git ops (branch/commit/push within fix-branch workflow), bash SSH, curl. Just do them.

**Why:** As of 2026-04-23, the user is driving more decisions through Claude.ai chat and wants Claude Code to execute without friction. Auto-approve block is being added to task prompts. Asking for approval on routine work slows the loop.

**How to apply:**
- Edits, reads, writes, bash for git/SSH/curl: proceed without asking
- Still confirm before: destructive ops (rm -rf, force push, hard reset), running deploy scripts (especially `deploy/deploy.sh` — known broken, see session wrap 2026-04-23), anything touching prod beyond read-only SSH/curl
- Still confirm before merging to `main` or tagging releases (per fix-branch workflow)
