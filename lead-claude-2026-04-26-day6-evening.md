# CLAUDE BRAIN DUMP — DATETRIS SESSION
**Saved:** 2026-04-26 evening (Day 6 → Day 7 transition)
**Lead Claude state at time of save**

## My role
- Lead Claude, took over from previous session via DATETRIS-HANDOFF__3_.md
- Grandfather CAI on veto duty (only ping for big decisions, surprises, prod deploys, irreversible actions)
- Jason wants short replies, no buttons/popups in chat, plain text only

## Critical state corrections vs handoff doc
1. FE branch is fix/126-frontend, NOT fix/127-frontend. Doc was wrong, FE CC was right with git evidence. The branches are siblings off a21fee1, not parent-child:
   - fix/126-frontend = M285 build convention + M291 push fix → THIS ships Build 126 STAG
   - fix/127-frontend = M286 batch (frozen UI, feedback, DI) → parked, depends on cycle 128 BE not on staging
2. "Build 126 in flight" was actually stuck on keychain ACL all session, not network/upload
3. Prod state confirmed by Jason: BE cycle 123 + FE Build 120 live, locked

## What we just fixed (the keychain saga)
The 4 prior session attempts to build Build 126 STAG IPA failed with errSecInternalComponent on codesign. Diagnosed late as per-key partition tag missing — GUI ACL "Always Allow" works for Terminal-with-TTY but NOT for headless CC subprocess. Only `security set-key-partition-list` writes the partition tag.

Resolution path that worked:
1. Jason ran `security unlock-keychain ~/Library/Keychains/login.keychain-db` (interactive password prompt)
2. Then `security set-key-partition-list -S apple-tool:,apple: -s ~/Library/Keychains/login.keychain-db` (NO -k, prompts via stdin invisibly — safe)
3. Verified with codesign smoke test → SUCCESS

Important security lesson: Jason rightly pushed back when I initially relayed CC's password-on-CLI suggestion. I should have led with the stdin-prompt form (no -k). That's the safe pattern for any future `security` invocations. Don't recommend `-k <password>` ever.

## Live state at save time

- BE: Idle, healthy. fix/127-backend, 5 commits ahead of dev, all pushed. M287/b/c/d/M289 done. M287e/f/g/h queued.
- FE: Build re-firing now. Just sent confirmation of partition tag fix. Expecting clean→build→upload, ~7-10 min
- CHAT: V2 plan running. Push M254chat1 → backfill 5 log entries → M255-M262chat1 batch (8 tasks). ~30-60 min total
- ADMIN: V3 plan running. Full M254admin1 (TOTP feature, ~45-60 min) → backfill → M255-M262admin1 (8 tasks) → docs. ~90-130 min total

## Cycle 129 batch contents

CHAT M255-M262chat1 (8 remaining):
- M255: Group room invitation links
- M256: Voice notes transcription (placeholder)
- M257: Read indicators per recipient
- M258: Message edit history
- M259: Chat archive per-user
- M260: Forwarded messages
- M261: Chat polls
- M262: Mention notifications

ADMIN M255-M262admin1 (8 remaining):
- M255: Login session management
- M256: SSO prep (OAuth scaffolding)
- M257: Notification dispatch refinement
- M258: Reports filtering
- M259: Impersonation cleanup
- M260: Metrics caching
- M261: Scheduled tasks v2
- M262: Chat moderation v2

## Known gaps from network drop
All 4 sessions had log.jsonl write gaps when internet dropped. Backfills are part of CHAT V2 + ADMIN V3 plans. BE didn't have any cycle 129 work in flight so no backfill needed there.

## What's next after FE build succeeds
1. CC reports build + upload success → Build 126 STAG on TestFlight (~5 min App Store processing)
2. Jason installs Build 126 STAG on iPhone via TestFlight app
3. Run Gate 3 verification checklist (in handoff doc section 6.6)
4. If all green → Gate 3 sign-off, plan path to prod deploy of cycle 124+125+126
5. Prod deploy path: merge fix/127-backend → development → main, run deploy-prod.sh (Gate 2 enforces SHA match), build PROD IPA, upload to TestFlight prod track

## Workflow norms with Jason
- Section labels (***** BACK END ***** etc.) on every CC prompt
- M-numbered prompts: [M<number>]
- Next M-numbers: BE/FE = M292, CHAT = M263chat1, ADMIN = M263admin1
- AUTO-APPROVE on routine work, double-approval on prod deploys
- Wrap CC prompts in code blocks for easy copy-paste
- Numbered options for choices (plain text, NOT tappable buttons — Jason disabled those)
- Calibrated CC time estimates (CC's natural estimates are ~50x too high)
- Don't be verbose. Jason scans.

## Calibrated CC time estimates
- Single small task: 5-10 min real
- Single medium task: 15-25 min real
- 6-task batch: 22-45 min real
- 10-task batch: 30-60 min real
- BE+FE pair-merge cycle: 10-20 min real

## Things Jason's tired of and I should remember
- The keychain saga ate ~30+ min of session time
- He's running on momentum at end of Day 6, evening hours
- Don't ever recommend a security risk
- Don't keep him waiting on grandfather for things I can lead
- Short replies. No big paragraphs unless absolutely needed.

## Outstanding non-cycle-129 work (from devlist snapshot)
Bugs: B40 (5-like cap), B41 (rank 6+ broken), B37 (rerank race) — all NOT REPRODUCIBLE, awaiting sim repro
Features queued: Cloudflare Tunnel for chat.datetris.date, real Firebase E2E test, AdminA20 prod deploy, multi-photo profile, encryption v2 (deferred ~4 weeks)
