---
name: Jason test user is a permanent dev fixture
description: Every BackA20 dev environment must have a "Jason" user; seed_jason.py ensures it idempotently. Registration blocks new jason-prefix signups.
type: project
originSessionId: c732c6f5-6b30-49a9-969c-571f3093382c
---
Every local/dev BackA20 database must contain a test user with `username="Jason"` (exact case). After M97 (2026-04-24) `seed_dev_clean.py` nuke+reseed, Jason is `id=1` (the root user; all 24 other seeded users have `referred_by='Jason'`). Email=`jason@logindemo.com`, sex=`male`, sexuality=`other`, position=`both`, dating_speed=`1day`, timezone=`Europe/London`. Password is `111111111111` (the BackA20 local-dev convention — see [Test account credentials](test_credentials.md)), hashed with argon2id.

Two seed scripts exist and serve different purposes:
- **`seed_jason.py`** — lightweight ensure-script. Creates Jason if missing, self-heals password to `111111111111` if hash doesn't verify. Does NOT wipe other data. Use this when you just want Jason present on an existing DevDB.
- **`seed_dev_clean.py`** (M97) — destructive full reseed. Wipes DevDB.sqlite + photos/ + voice_notes/ + media/ and rebuilds from scratch (25 users, 95 likes with voice, 40 media rows). Deterministic under RANDOM_SEED=97. Use when you want a pristine known-state dev environment.

The FastAPI `/register` handler at `routes.py:376` rejects any signup where `"jason" in username.lower()` — this reserves the namespace so only dev seeding (direct SQLAlchemy insert) can create a Jason-prefix user. Do NOT remove that block.

`seed_jason.py` at the repo root is the idempotent ensure-script: if `User.username == "Jason"` already exists it's a no-op; otherwise it inserts the row bypassing the /register block. Run `python seed_jason.py` (or `venv/bin/python seed_jason.py`) at the start of every fresh dev environment setup, before any testing that assumes Jason is present.

**Why:** M76 (2026-04-24) codified Jason as a permanent dev fixture. Several older seed scripts (seed_jason_correct.py, seed_jason_final.py, seed_jason_likes*.py) reference stale IDs like 201 from a prior environment — `seed_jason.py` is the current canonical ensure-script.

**How to apply:** Before running local backend smoke tests or anything that depends on a known user, run `python seed_jason.py`. If recommending setup steps for a new dev machine, include this after DB init/migrations. Never edit or delete Jason's existing row without the user's explicit say-so (M76 step 3: "if exists, leave alone").
