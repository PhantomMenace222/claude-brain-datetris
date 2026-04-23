---
name: Session wrap 2026-04-23
description: What shipped 2026-04-23 (TestFlight build 3, deploy.sh fix, /api/version + VERSION bump live), App Store Connect rename pending
type: project
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
# Accomplished today (2026-04-23)

- Built iOS IPA 2026.4.22+3 with yesterday's fixes
- Uploaded to TestFlight, Apple processed, build 3 live
- Prod backend lacked face-slot patch — deployed via `bash deploy/deploy.sh`
- Initial deploy partially failed: photos rsync errored on `--chmod=F644` (mac rsync); `server_setup.sh` ALSO wiped HTTPS nginx config (overwrote certbot's 443 blocks)
- HTTPS down ~30 min; recovered by re-running:
  `certbot --nginx -d api.datetris.date --agree-tos -m [email] --non-interactive`
- Built debug IPA 2026.4.22+4 with login tracing on `fix/login-trace` — not needed after HTTPS recovery, IPA exists locally
- **FIXED both deploy bugs** (commit `27be564` on `development`, branch `fix/deploy-nginx-https-safe`):
  - `deploy/deploy.sh` — dropped `--chmod=F644`; chmod is now done server-side via `find ... -exec chmod 644/755` after the existing chown
  - `deploy/server_setup.sh` — guards the nginx-site install with `grep -q "listen 443"` so certbot's HTTPS rewrite is preserved on every redeploy
- End-to-end verified: re-ran `bash deploy/deploy.sh` clean, `https://api.datetris.date/docs` → 200, `listen 443 ssl` still in nginx config, photo perms `644 backa20:backa20`
- **Added `GET /api/version` endpoint** (commit `59faebb` on `development`, branch `fix/version-endpoint`):
  - `routes.py` — new public route returns `{"version": config.VERSION}` (config.py was already reading `os.getenv("VERSION", "V1.0")`)
  - Updated `/etc/backa20.env` on prod: `VERSION="2026-04-23(4.3)"` (must be quoted — see "Gotchas" below)
  - Restarted backa20 service
  - Verified live: `curl https://api.datetris.date/api/version` → `{"version":"2026-04-23(4.3)"}`

**Why:** Captures today's incident timeline + patches + live VERSION so the next session can pick up without re-deriving state.

**How to apply:** `bash deploy/deploy.sh` is now safe. To check what build is on prod, hit `/api/version`.

# Gotchas (worth remembering)

- **`/etc/backa20.env` values must be shell-quoted if they contain `()`, `*`, `?`, etc.** Step `[5/6]` of `deploy.sh` runs `set -a; . /etc/backa20.env; set +a` — bash sources the file, and unquoted parens caused a `syntax error near unexpected token (` that broke the init_db step. Solution adopted: quote VERSION as `"2026-04-23(4.3)"`. Apply the same to any future env values containing shell metachars.

# Bugs still open

3. **App Store / TestFlight shows app name "Pipeline Dating"** — this is App Store Connect *metadata*, not code. User must fix manually at:
   `appstoreconnect.apple.com → DATETRIS app → App Information → Name → change to "Datetris"`

# Pending high-priority

- Fix App Store Connect app name (manual, not code)
- Then plan: re-enable gallery slots, proper "pick your search photo" feature, app icon, Google Play upload key fingerprint email reply, real Dating Speed feature
