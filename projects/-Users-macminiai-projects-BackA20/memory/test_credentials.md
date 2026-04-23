---
name: Test account credentials
description: Default test account for backend smoke tests (login, API probes, etc.)
type: project
originSessionId: 6d6598a4-d473-4ac3-9697-347b555f996b
---
Use `happysammy` / `111111111111` as the default test account when curl-testing endpoints or otherwise exercising the backend. Confirmed working via `POST /api/login` against the local dev backend.

**Why:** User corrected me after I invented `bctest1 / testpass123`, which doesn't exist in DevDB.sqlite. `happysammy` is a real registered account the user keeps around for exactly this purpose.

**How to apply:** When the user says "test login" or similar without specifying an account, default to happysammy/111111111111. Don't invent test usernames.
