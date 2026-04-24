---
name: Test account credentials
description: Every local DevDB user has password 111111111111 (twelve 1s). Prod passwords are separate and untouched.
type: project
originSessionId: c732c6f5-6b30-49a9-969c-571f3093382c
---
**Local BackA20 convention (M78, 2026-04-24): every user in `DevDB.sqlite` has password `111111111111` (twelve 1s).** Unified across all seeded accounts so local testing never has to guess which password applies to which user.

Canonical logins for quick smoke tests (post-M97 reseed, 2026-04-24):
- `Jason / 111111111111` — id=1, root dev fixture (see [Jason test user](project_jason_test_user.md))
- `happysammy / 111111111111` — id=2 after M97 reseed (was id=1 before; `seed_dev_clean.py` puts Jason first)
- Other 23 seeded users all at ids 3..25 (coolheron, mintdusk, brightfox412, …) — all `referred_by='Jason'`

Pre-M97 the DB had `jasoooooooooooo` and other historical users. M97's `seed_dev_clean.py` wipes those on every run; if you need them back, restore from `DevDB.sqlite.backup-*` or modify the seed username pool.

But any seeded user (26 rows as of M78: `coolheron836`, `abcdefg`, the `*falcon306`/`*oak687` style username-generator rows, etc.) logs in with the same password.

`seed_jason.py` self-heals against this password: if a user row exists but the stored hash doesn't verify `111111111111`, it rehashes and commits. Run it after any DB restore/reseed to re-unify. Covers Jason and happysammy by default; other users are reset ad-hoc via a small loop over `User` (see M78 command in session history).

**Prod** passwords are **separate** and untouched by any dev seeding. Never run `seed_jason.py` or a mass password-reset loop against prod, and never reset a prod password without the double-approval rule.

**How to apply:** When the user says "test login" or similar, use `111111111111` for any username. If login fails unexpectedly on a fresh DevDB, run `python seed_jason.py` to heal the fixture accounts.
