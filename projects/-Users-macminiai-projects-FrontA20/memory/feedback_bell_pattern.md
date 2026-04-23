---
name: Bell pattern for this session
description: This FrontA20 session rings the terminal bell TWICE at end of every response (Stop) and on Notification, so the user can identify which of multiple concurrent Claude sessions is waiting for input
type: feedback
originSessionId: 2f51a5b5-fa5a-4230-b6bc-8c1970eca761
---
Terminal bell fires **2×** (two `\a` chars with a short pause) at end of every response and on notifications.

**Why:** User runs multiple concurrent Claude Code sessions (FrontA20, BackA20, etc.). The double-bell is this session's audio fingerprint so they know which terminal needs attention without looking at each one. A single bell was already configured by default on Notification; this session picked two to distinguish itself.

**How to apply:** Configured in `~/.claude/settings.json` via two hooks — `Notification` and `Stop` — each running `printf '\a\a' > /dev/tty 2>/dev/null || true`. Don't revert to single-bell and don't drop the Stop hook (Notification alone misses some idle end-of-response cases). If a future session wants a different pattern (3 beeps, different cadence), update the hook command here AND this memory.
