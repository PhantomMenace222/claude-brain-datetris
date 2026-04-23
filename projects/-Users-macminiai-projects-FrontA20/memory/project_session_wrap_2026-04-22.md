---
name: Session wrap 2026-04-22
description: End-of-day snapshot covering production infra, today's shipped work, hardened workflow rules, and pending follow-ups for the next session
type: project
originSessionId: 6705248d-3cfd-4f41-9945-c12b6563a43f
---
## Production infrastructure (locked in today)

- **Backend host:** DigitalOcean droplet at `167.71.136.223`
- **Public API URL:** `https://api.datetris.date` (TLS)
- **Frontend release branch of config.dart** routes debug builds to `http://0.0.0.0:8000`, release builds to `https://api.datetris.date` (`kReleaseMode` switch in `lib/core/config.dart`).
- **iOS TestFlight last Apple-processed build:** `2026.4.22+2`. Current `pubspec.yaml` is `+3` but not yet built. Next IPA upload will be `+3`.
- **Stream Chat API key** rotated today to `67j8nyav75n9` (frontend `lib/screens/matches_screen.dart` constant).

## Versioning state (as of session end)

- `pubspec.yaml`: `2026.4.22+3`
- `lib/core/version.dart` banner: `2026-04-22(3.10)` on fix branch `fix/preview-my-profile` (uncommitted not merged)
- `development` branch banner: `2026-04-22(3)` clean form
- Apple-processed X: `+2`. Next target X: `3`. So the next fix branch still targets `(3.Y)`, not `(4.Y)`. Y resets to `1` only after Apple accepts a `+3` upload.
- See `feedback_versioning.md` for the full convention.

## Today's shipped work (what exists on `development`)

High-level — detailed commits are in `git log` on `development`:

- Prod API switch (kReleaseMode branch in config.dart), Stream key rotation, pubspec bump to +2
- Username validation: re-enabled server check; client-side "jason" substring block (case-insensitive), error "Username not allowed"
- Banner format migrated from `V10` to `YYYY-MM-DD(X)` / `(X.Y)` convention
- Matches list: new match auto-lands at bottom + auto-scroll on detection (depends on backend `ORDER BY Match.created_at ASC` — landed on BackA20)
- `lib/widgets/authed_image.dart`: new widget that fetches image bytes with JWT, renders via `Image.memory`, has a 50-entry static LRU cache keyed by URL. Workaround for Flutter web dropping custom headers on `Image.network`. Used app-wide.
- Photo display fix: replaced 7+ `Image.network` sites across Discover/Profile/WIL/WLM/Matches/Gallery with `AuthedImage`. Also standardized on client-side URL construction `'$_baseUrl/api/users/<id>/photo'` everywhere (dropped the `replaceFirst('http://0.0.0.0:8000', ...)` rewrite-hack in Discover and WLM).
- Gallery: web upload via `http.MultipartFile.fromBytes` (dart:io not available on web), null-guard on `userId` in URL construction, routed thumbnail + preview through `AuthedImage`.
- Profile preview: `ProfileScreen.isPreview` flag (default `false`) hides Like/Block action bar and shows a gold "Preview mode" pill at top. `EditProfileScreen` passes `isPreview: true` on its "Preview my profile" button.
- ProfileScreen `age` and `sex` became nullable; the age/sex display line renders only non-null parts joined by ` · `, else hidden. Eliminated "0 · Unknown" literals at all callers.
- Gallery reduced to single `face` slot (5 others commented out, `crossAxisCount: 1`).
- Dating Speed ListTile commented out in Settings (button only — state/methods remain live).

## System behaviors / temporary hacks (non-obvious, hard to derive from code)

- **Gallery face slot doubles as the user's profile photo** (quick patch). The "profile photo" endpoint `/api/users/<id>/photo` and the gallery `face` slot are tied together on the backend. Replacing the face slot changes the discovery/profile/matches avatar too. Proper "user picks search photo" flow is pending.
- **Discover excludes**: WLM (people who liked current user), outbound likes (people current user liked), matches, blocked users, and self. This is backend filter logic.
- **Test data**: Local dev DB has 22 users — `happysammy`, `jasoooooooooooo`, and 20 seeded. Reseeded clean today.
- **Username registration blocks `jason` substring** (case-insensitive). Also re-enabled server-side uniqueness check via `/api/check-username`.
- **`photo_url` returned by backend `/api/users/me`** is prefixed with the server's `config.BASE_URL` env var. The frontend now ignores this field entirely — builds photo URLs client-side from `BaseConfig.baseUrl` + user id.

## Workflow rules hardened today (re-confirmed)

All already live in dedicated memory files; re-stating for continuity:
- `feedback_git_workflow.md` — `fix/<desc>` branches off `development`, never commit direct to `development`, promote to `main` only at App Store submit, tag `v<date>`.
- `feedback_git_workflow.md` — **HARD RULE: double confirmation on any `main`-touching operation.** 5-step protocol, never skipped.
- `feedback_versioning.md` — banner `YYYY-MM-DD(X.Y)` on fix, `(X)` clean on dev, Y running across fix branches within one Apple cycle, pubspec pinned to X until Apple accepts.
- `feedback_reply_separator.md` — prepend `---` to every reply.

## Additional operational rules mentioned in wrap (newly-stated — may warrant own memory later)

- **No popups (`use_user_input_v0`)**: unclear reference — likely a Flutter/app feature flag or an internal rule about avoiding popup-based UX. Not encoded in repo as of session end. Clarify next session and save a dedicated memory if it's durable guidance.
- **Never paste secrets in chat.** Applies to API keys, JWTs, passwords, private infra. Keys get swapped in via user paste at the exact edit site, never echoed back.
- **One Claude Code instance per repo at a time** (avoids branch/merge collisions between parallel sessions). Critical when FrontA20 and BackA20 work is coordinated — the other repo has its own Claude session.

## Pending for next session

1. **Build IPA `+3`** with today's fixes bundled. Bump `pubspec.yaml +3 → +3` (already set) and banner to `(3)` on `development`, run `flutter build ipa --release`, upload via Transporter to TestFlight.
2. **Test all fixes on real iPhone** once TestFlight processes `+3`.
3. **Merge `fix/preview-my-profile`** to `development` first — it has 4 uncommitted-to-dev commits (preview mode, photo URL standardization, single-slot gallery, commented-out dating speed). Banner to be cleaned `(3.10) → (3)` in the merge commit per convention.
4. **Plan proper "user picks search photo" feature** to replace the current face-slot-doubles-as-profile-photo patch. Decide whether to expose a photo selector in Edit Profile or promote a specific gallery slot differently.
5. **Re-implement Dating Speed feature** (currently commented out in Settings). State/methods are still live — will generate 2 analyzer warnings until re-enabled or fully excised.
6. **Re-enable the other 5 gallery slots** when the proper gallery/profile photo flow is designed.
7. **App icon + launch screen** for App Store review. Flutter build log warned: default placeholder icons in place.
8. **Google Play upload key fingerprint mismatch** — email reply pending from user side.

## Branch state at wrap

- `development`: tip `4ce5023 Merge fix/settings-gallery-photos; banner (3)` (pushed to origin)
- `fix/preview-my-profile`: tip `12d7f14 gallery: single-slot layout (face only); banner (3.9)` (local only, not pushed), plus uncommitted Dating-Speed commenting-out (banner will become `3.10` when that lands). **4 commits ahead of `development`, not merged, not pushed.**
- `main`: stale (last promoted state). Don't touch without double-confirmation protocol.
