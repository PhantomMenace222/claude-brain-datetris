---
name: Detailed error display on POST failure
description: Every frontend POST flow must surface status code + error body + trace_id (if present) + endpoint to the user on failure — no generic messages
type: feedback
originSessionId: 0fd2af8c-49e2-4f6d-87a1-6fb69cb21b93
---
Any frontend feature that POSTs to the backend MUST, on failure, display
detailed error info to the user via toast/snackbar or inline error box:

- HTTP status code (e.g. "422")
- Error body (the backend's `detail` / `error` / `message` field, parsed
  from JSON when possible — fall back to raw text)
- `trace_id` if present in response (header or body)
- Which endpoint was called (e.g. `POST /api/like`)

NEVER show a generic "Error 500" or "Something went wrong" — those are
useless for triage. Network/timeout failures should still name the
endpoint and the failure mode.

Going forward, when adding ANY new POST feature, the detailed error
handler is part of the acceptance criteria — not a follow-up.

**Why:** silent / generic failures during real-world testing made M58
(voice-like UI staleness) untriageable from the user side. The backend
returned 201 successfully but the user couldn't tell the post even
landed; equally, when posts fail, "Error 500" gives nothing to act on
without server-side audit.

**How to apply:** on every `http.post` / `MultipartRequest.send` /
similar, on non-2xx OR exception:
1. Parse `resp.body` for `detail` / `error` / `message`.
2. Pull `trace_id` from response headers OR body.
3. Build a message of the shape:
   `POST /api/<endpoint> failed (HTTP <code>)\n<detail>\n[trace: <id>]`
4. Show via `ScaffoldMessenger.showSnackBar` (long duration) OR inline
   error widget — whichever fits the screen.
For new POST work, treat this as a checklist item alongside the happy path.
