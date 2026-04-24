---
name: Banner and pubspec versioning convention
description: Banner is YYYY-MM-DD(X) on development and YYYY-MM-DD(X.Y) on fix branches, where X = next release build number (unified across iOS + Android); pubspec bumps to X at IPA build time
type: feedback
originSessionId: 6705248d-3cfd-4f41-9945-c12b6563a43f
---
Two version surfaces:

1. **`pubspec.yaml` build number** — integer suffix, format `YYYY.M.D+X` (e.g. `2026.4.23+101`). Holds the build number of the **next** release we'll upload. For Android AABs, the build number can also be passed explicitly via `flutter build appbundle --build-number=N --build-name=V` overriding pubspec for one-off builds. **Never bumped on fix branches or on development merges** between uploads.
2. **In-app banner** (`lib/core/version.dart`) — uses `YYYY-MM-DD(...)` format:
   - **On `development`** (stable, post-merge): `YYYY-MM-DD(X)` — clean, no dot. `X` matches the next release build.
   - **On a `fix/<desc>` branch**: `YYYY-MM-DD(X.Y)` — `X` same as next release target, `Y` is a running iteration counter *across all fix branches within the current release cycle* (not reset per branch). So if `fix/a` used `(101.1)` and merged clean to `(101)`, the next `fix/b` starts at `(101.2)`, not `(101.1)`.

**Definition of X (the number that matters):**

X = the **dev-cycle number** the work is part of. The dev cycle is the
integer that names the M107 branch trio (`fix/<X>-backend`,
`fix/<X>-frontend`, `fix/<X>-admin-panel`). The pubspec `+X` and the
banner `(X)` always equal that integer; you don't track them
independently.

X advances when a new dev cycle starts (i.e. when the first
`fix/<X+1>-...` branch is created). It stays constant across:
- multiple fix branches within the same cycle
- all merges to `development`
- all iterations of any one fix branch
- IPA builds — every IPA in cycle X carries `+X`, regardless of how
  many TestFlight uploads happened in that cycle (Apple's
  monotonic-build-number rule still applies; if you need a 2nd upload
  in the same cycle, bump pubspec to `+X.1` or skip ahead by mutual
  consent — but the default is one IPA per cycle)

**History note:** Three schemes have existed:
- Pre-2026-04-23: iOS TestFlight count only (values 2, 3, 4).
- 2026-04-23 to 2026-04-24: shared X across iOS+Android, advanced on
  Apple acceptance (101, 102, 104, 105).
- **From 2026-04-24 onward (M128): X = dev-cycle number.** Build 114
  IPA carries `+114` (was briefly `+4` from M127, bumped to `+114` to
  align). Next IPA in cycle 115 will carry `+115`.

**Flow example** (post-M128, Y is a counter within one cycle):

1. `development` stable at banner `2026-04-24(114)`. Cycle = 114.
   pubspec at `+114`.
2. `fix/115-frontend` opens off `development` → banner becomes
   `2026-04-24(115.1)` on first commit. Cycle has advanced to 115.
3. Second commit on `fix/115-frontend` → banner `2026-04-24(115.2)`.
4. Merge `fix/115-frontend` to `development` → banner cleans to
   `2026-04-24(115)` (either in the merge commit or a follow-up).
5. New same-cycle work → `fix/115-frontend` is the only frontend
   branch per the M107 convention, so further frontend work in cycle
   115 is impossible without violating the rule. New frontend work =
   new cycle = `fix/116-frontend` and banner `(116.1)`.
6. Ready to ship cycle 115 → `pubspec` at `+115` (bump in one commit
   on `development` if it's still at `+114`), `flutter build ipa
   --release`. Upload.
7. **No bump on Apple acceptance.** The next pubspec change is when
   cycle 116 starts.

**Why:** User wants the banner to carry two pieces of information: (a) which Apple build a runtime corresponds to, and (b) whether that runtime is a stable dev-branch snapshot or a WIP fix-branch iteration. Keeping pubspec pinned until Apple confirms avoids burning build numbers on unshipped work and matches Apple's strictly-monotonic build-number rule.

**How to apply (post-M128):**
- Every fix-branch commit increments `Y` in `lib/core/version.dart`,
  starting at `.1` for the first commit on a new cycle's fix branch.
- `Y` resets to `1` when a new dev cycle starts (new X). It does NOT
  carry across cycles.
- `pubspec` `+X` always equals the current dev cycle. Bump it when you
  start cycle X+1 — typically on the first commit of `fix/<X+1>-...`,
  OR on the `development` branch as a one-line commit before
  `flutter build ipa`. If you forget and pubspec is stale at IPA
  build time, the build will succeed with the stale value; you'll
  notice when the App Store Connect upload shows the wrong build
  number. Better to bump proactively.
- When merging a fix branch to `development`, the very next commit
  (or the merge commit itself) changes banner from `(X.Y)` → clean
  `(X)`.
- Apple acceptance is no longer a versioning trigger. The trigger is
  "new fix-branch cycle starts" → bump everything in lockstep.
- Before running `flutter build ipa --release`, verify: banner is
  `(X)` clean-form (not `(X.Y)`) AND pubspec `+X` matches the same X.
  If banner still has a dot, the fix isn't merged to `development`
  yet — stop and fix that first. If pubspec is stale, bump it.
- Date portion `YYYY-MM-DD` reflects the work day. Update it on
  new-day commits if the branch or cycle spans days.
