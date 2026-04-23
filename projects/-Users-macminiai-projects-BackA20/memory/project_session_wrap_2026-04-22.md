---
name: Session wrap 2026-04-22
description: State of BackA20 + FrontA20 at end of 2026-04-22 session — what shipped, where things live, what's pending for tomorrow
type: project
originSessionId: 6d6598a4-d473-4ac3-9697-347b555f996b
---
## Where things live now

- **Backend**: Droplet `167.71.136.223`, HTTPS live at `https://api.datetris.date`. TLS via Let's Encrypt (auto-renew). systemd unit `backa20.service`, env at `/etc/backa20.env`, data at `/var/lib/backa20/{db.sqlite,photos,voice_notes}`.
- **iOS TestFlight**: build `2026.4.22+2` uploaded, installed on user's phone.
- **Local dev DB**: fresh reseed via `reseed_20_users.py` — 22 users (`happysammy` id=1, `jasoooooooooooo` id=2, 20 adjective-noun seeded users ids 3-22, all password `111111111111`). 10 of 20 like `jasoooooooooooo` (populates WLM), no matches, no outbound likes from him. User may have added manual test state on top since reseed (check before relying on pristine counts).
- **Prod DB**: 201 users (`happysammy` + 200 from `seed_200_extra_users.py`).

## Shipped today (BackA20 development, pushed)

- Production deploy scaffolding: `deploy/{backa20.service,backa20.nginx,server_setup.sh,deploy.sh}` + `uvicorn[standard]` for WebSockets (`551174c`)
- Stream Chat keys → env vars (old pair burned in git history, rotated in Stream console) (`551174c`)
- `seed_200_extra_users.py` (`63026fa`)
- Matches ordering: `order_by(Match.created_at.asc())` in `/api/matches` — oldest-first so newest appears at bottom (`e0c375d`)
- `gallery face slot` → `user_photos` upsert — face uploads now double as profile photo (`e0c375d`)
- `.gitignore` for `photos/`, `voice_notes/`, `media/` + untrack (`8bc9a63`)
- `reseed_20_users.py` (`35e812e`)
- Discover: exclude users who liked me (`ea12011`)
- Discover: exclude matched users (both directions) (`30749f6`)

## Pending for next session (priority order)

1. **Build IPA `2026.4.22+3`** (pubspec is at +3, not yet built) with all today's FE+BE fixes, upload to TestFlight.
2. Test all fixes on real iPhone.
3. Plan proper **"user picks search photo"** feature — replaces the quick `gallery face slot → user_photos` patch from today.
4. Add real **Dating Speed** feature (currently commented out in code).
5. Re-enable the other 5 gallery slots when ready.
6. App icon + launch screen (blocker for App Store submission).
7. **Google Play upload key fingerprint mismatch** — still need to reply to Google's email with the PEM (`FrontA20/android/keystores/upload-certificate.pem`, fingerprint `05:A4:3A:...`). Expected fingerprint from Google was `C4:D1:5B:...`.

## Workflow clarifications from today (not yet in feedback_git_workflow.md)

- **Banner format**: `YYYY-MM-DD(X.Y)` on fix branches, `(X)` clean on `development`. `X` = next Apple build number. Currently `X=3` because Apple has `+2` processed and `+3` is pending build (not yet uploaded).
- **No popups**: use the `use_user_input_v0` pattern (avoid modal dialogs in the Flutter UI — they disrupt the flow).
- **One Claude Code per repo at a time**: multiple sessions editing the same repo caused branch-switch race conditions today (the `fix/photos-not-displaying` vs `fix/matches-order-by-created` collision). Stick to one session per repo.

## See also
- `feedback_git_workflow.md` — mandatory fix-branch workflow + main double-confirm rule
- `test_credentials.md` — backend smoke-test creds (`happysammy` / `111111111111`)
- `reply_separator.md` — prepend `---` to every reply
