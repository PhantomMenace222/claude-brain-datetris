---
name: Always include full launch command when asking user to test
description: Every "please test this" handoff must bundle the complete ready-to-paste shell sequence — kill ports, checkout branch, start BE, verify, launch FE
type: feedback
originSessionId: 0fd2af8c-49e2-4f6d-87a1-6fb69cb21b93
---
When asking the user to test built work — whether the ask comes from
PM (Claude.ai chat) OR from me directly after shipping a fix — ALWAYS
include the full launch command in the same message. Never assume the
user remembers the sequence and never make them ask.

The user's only action should be: paste + wait + test.

**Why:** the user has 7+ branches stacked across two repos (FrontA20 +
BackA20), Flutter on a specific device, backend on a specific port,
and a PM persona that pings them between sessions. Asking them to
"test fix X" without the runbook forces them to context-switch back
into infra mode just to get to the actual test — friction that
compounds over a session of dozens of fix cycles.

**How to apply:** every test-ask message must include a fenced bash
block containing, in order:

1. **Kill existing processes** on the dev ports
   ```bash
   lsof -ti:8088 | xargs kill -9 2>/dev/null; lsof -ti:8000 | xargs kill -9 2>/dev/null
   ```

2. **Checkout the correct branch** in BOTH repos as needed
   ```bash
   cd /users/macminiai/projects/BackA20 && git checkout <backend-branch>
   cd /Users/macminiai/projects/FrontA20 && git checkout <frontend-branch>
   ```

3. **Start backend** if needed (in background) and **verify it responds**
   ```bash
   cd /users/macminiai/projects/BackA20 && uvicorn main:app --reload --port 8000 &
   sleep 3 && curl -sf http://localhost:8000/api/version || echo "BACKEND DOWN"
   ```

4. **Launch Flutter on a specific device** (always pass `-d <device-id>`,
   never let the user pick). Pick per `reference_default_devices.md`:
   default = iPhone 17 Sim `841E07BF-ABB0-4FB2-8D01-E7CC0648CDFC`;
   switch to iPhone 26 wireless `00008140-0018688C3602801C` when the
   test needs real mic / camera / push / App Store auth; use `chrome`
   for web smoke.
   ```bash
   cd /Users/macminiai/projects/FrontA20 && flutter run -d 841E07BF-ABB0-4FB2-8D01-E7CC0648CDFC
   ```

If any of those steps don't apply to a given test (e.g. backend already
running from previous test, no branch switch needed), still SAY SO
explicitly — "backend already up from M__, skip step 3" — rather than
silently omitting and leaving the user to guess.

When in doubt about which device or branch, ASK the user once at the
start of a session and remember for the rest. Never let "I assumed
you'd know" be the reason a test ask is incomplete.
