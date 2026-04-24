---
name: Local test password convention
description: Use 111111111111 as the password for any locally-seeded test user; do NOT reintroduce older conventions
type: reference
originSessionId: 0fd2af8c-49e2-4f6d-87a1-6fb69cb21b93
---
The local test password convention is `111111111111` (twelve ones).

Use it for:
- any seeded test user created during local development
- any login when manually exercising auth flows in dev / simulator
- any test fixture that needs a password value

Do NOT use or suggest:
- `testpass123`
- `testpass12345`
- any other "testpass*" variant

These older conventions were retired and any reference to them should
be treated as stale — replace with `111111111111` if encountered.
