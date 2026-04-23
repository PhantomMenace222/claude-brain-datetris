---
name: Gallery slots / "pick your search photo" refactor
description: Plan and decision points for re-enabling gallery slots and letting users pick which slot is their search/display photo
type: project
originSessionId: 10253868-b4d2-48d0-b16b-b401946315ec
---
# Decision: Option A (single `display_slot` column on `User`)

Recommended over Option B (keep `user_photos` as a cache) because:
- Fewer moving parts; one source of truth (`user_media` table + `MEDIA_DIR`)
- ~20 seeded users → migration is trivial
- `photo_url` strings in 6+ response models keep pointing at `/api/users/{id}/photo` — only the resolver behind the URL changes, no FE changes needed until the "pick slot" UI is built

**Why:** Discussed 2026-04-23. Today the only way to change your search photo is to re-upload to the `face` slot, because `routes.py:1493-1507` hard-codes a face → `user_photos` bridge and `GET /api/users/{id}/photo` reads only `user_photos`.

**How to apply:** When implementing, do NOT add a second column on `User` for cached filename. Resolve at request time from `user_media` joined to `User.display_slot`. Drop the face-double bridge. Eventually delete `user_photos` table.

# Implementation steps (when greenlit)

1. Add `User.display_slot = Column(String, default="face")` + migration backfill
2. Rewrite `routes.py` `GET /users/{id}/photo` to look up `user.display_slot` then `UserMedia(user_id, slot=display_slot)` from `MEDIA_DIR`
3. Add `PUT /users/me/display-slot` endpoint with body `{slot: <one of VALID_GALLERY_SLOTS>}`
4. For each existing `UserPhoto` row, ensure a `UserMedia(slot='face')` row exists (copy `PHOTOS_DIR/photoface_<id>.jpg` → `MEDIA_DIR/gallery_<id>_face.jpg` if missing)
5. Remove the face-double block at `routes.py:1493-1507`
6. Update `seed_*.py` scripts to write `UserMedia` not `UserPhoto`
7. Eventually drop `user_photos` table

# Known traps before coding

- **`GET /api/gallery/{user_id}/{slot}` is IDOR-locked to own-user (`routes.py:1546-1550`).** "Pick your search photo" must work via the existing `/api/users/{id}/photo` endpoint, NOT by exposing arbitrary slots publicly. If you ever DO want public access to non-face slots, only allow the slot equal to `user.display_slot`.
- **Confirm `MEDIA_DIR` exists on prod before relying on it.** `server_setup.sh` creates `/var/lib/backa20/{photos,voice_notes}` but does NOT create `/var/lib/backa20/media`. If gallery uploads have been failing silently, the migration step that copies face → media will fail too.
- **`photo_url` is in 6+ response models** — all point at `/api/users/{id}/photo`. Don't change that URL or the FE's existing references break.
