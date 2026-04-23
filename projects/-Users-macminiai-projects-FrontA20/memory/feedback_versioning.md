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

X = the build number on the *next* release we'll upload (iOS TestFlight or Android Play Store — unified series, not per-platform). As of 2026-04-23 end-of-day, X = 105 (IPAs 101, 102, 104 have been uploaded; next release is 105). It stays constant across:
- multiple fix branches within the same release cycle
- all merges to `development`
- all iterations of any one fix branch

X changes only when a release has been accepted by the store and we start the next cycle. Then `pubspec +X → +X+1`, and subsequent work targets the new X.

**History note:** Prior to 2026-04-23, X tracked the iOS TestFlight build number only (values 2, 3, 4). On 2026-04-23 the scheme unified to a single shared series continuing from the Android AAB build 100 → next X = 101. The jump from 4 → 101 is intentional and one-time.

**Flow example** (Y is a counter that only advances, across all fix branches in the cycle):

1. `development` stable at banner `2026-04-23(101)`. Next release target = 101.
2. `fix/a` off `development` → banner `2026-04-23(101.1)`. pubspec unchanged.
3. Second commit on `fix/a` → banner `2026-04-23(101.2)`.
4. Merge `fix/a` to `development`, clean banner to `2026-04-23(101)`.
5. New work → `fix/b` off `development`. First commit uses banner `2026-04-23(101.3)` (continues the counter from the last fix-branch iteration, does **not** reset to `.1`).
6. Merge `fix/b` clean → `(101)` on `development`.
7. Ready to ship → `flutter build ipa --release --build-number=101 --build-name=2026.4.23` (or via pubspec `+101`). Upload.
8. Store accepts → bump X to 102 and banner `(101) → (102)` on `development` in one commit. Y counter resets. Next fix branch starts at `(102.1)`.

**Why:** User wants the banner to carry two pieces of information: (a) which Apple build a runtime corresponds to, and (b) whether that runtime is a stable dev-branch snapshot or a WIP fix-branch iteration. Keeping pubspec pinned until Apple confirms avoids burning build numbers on unshipped work and matches Apple's strictly-monotonic build-number rule.

**How to apply:**
- Every fix-branch commit increments `Y` in `lib/core/version.dart`. The first commit on a new fix branch picks up from the last `Y` used anywhere in the current Apple cycle, not `1`. Check the banner on the latest merged-to-`development` fix branch (via `git log`) if unsure.
- `Y` resets to `1` only after Apple accepts the `+X` upload and we advance to `+X+1`.
- Never bump `pubspec.yaml` on a fix branch or at merge-to-`development` time.
- When merging a fix branch to `development`, the very next commit (or the merge commit itself) changes banner from `(X.Y)` → clean `(X)`.
- Bump `pubspec.yaml` and banner `(X) → (X+1)` only **after** Apple has accepted the previous upload.
- Before running `flutter build ipa --release`, verify: banner is `(X)` clean-form (not `(X.Y)`) and pubspec matches that `X`. If banner still has a dot, the fix isn't merged to `development` yet — stop and fix that first.
- Date portion `YYYY-MM-DD` reflects the work day. Update it on new-day commits if the branch or cycle spans days.
