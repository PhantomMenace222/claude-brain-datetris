---
name: One-cycle-one-pair branch naming convention
description: Each feature cycle gets a single number; branches use fix/<N>-backend / fix/<N>-frontend / fix/<N>-admin-panel — never split a number across multiple BE/FE branches, never use freeform descriptors
type: feedback
originSessionId: 0fd2af8c-49e2-4f6d-87a1-6fb69cb21b93
---
Every feature cycle is a single integer `N`. The repo branches that
implement that cycle MUST use these names verbatim:

| Repo | Branch name |
|---|---|
| BackA20 | `fix/<N>-backend` |
| FrontA20 | `fix/<N>-frontend` |
| Admin panel | `fix/<N>-admin-panel` |

**Exception:** if a cycle has no work in one of those repos, that
counterpart branch is simply not created. The number is still consumed —
it is never reused later for a different feature, even if no branch was
ever made on that side.

**NEVER:**

- Split one cycle's backend into two branches like `fix/<N>-env-config`
  + `fix/<N>-api-referrals-me`. Either bundle both into `fix/<N>-backend`,
  OR they are two cycles (`fix/<N>-backend` for one, `fix/<N+1>-backend`
  for the other).
- Use a freeform descriptor in place of the role suffix.
  `fix/113-rank-one-grace` is wrong; the correct name is
  `fix/113-backend` (the descriptor lives in commits + memory + the PM,
  not the branch name).
- Branch off a random tip. New cycle work branches off the tip of the
  matching repo's `development` (or whatever the cycle's stated base is)
  unless the user explicitly says "stack on top of fix/<X>-frontend".

**Why:** numbered cycles are the only durable join key between repos.
Past violations — cycle 112 split into `fix/112-env-config` +
`fix/112-api-referrals-me`, cycle 113 named `fix/113-rank-one-grace` —
forced manual cross-referencing every time we needed to know "did the
backend half of cycle N ship yet?" and made `git branch -a | grep <N>`
unreliable. With the strict convention, that grep is the one-line answer
to the cross-repo coordination question.

**How to apply:**

- Before running `git checkout -b`, sanity-check:
  1. Does `fix/<N>-backend` (or `-frontend`, etc.) already exist locally
     OR on origin in either repo? If yes, the cycle is in flight — branch
     onto a `fix/<N+1>-...` instead, or stack only with explicit user
     consent.
  2. Is the branch name exactly `fix/<N>-<role>` where role ∈
     {`backend`, `frontend`, `admin-panel`}? If not, fix the name
     before pushing.
- When the user gives you a freeform branch name in a request (e.g.
  "branch fix/113-rank-one-grace") that violates this convention, ASK
  before executing — don't just rename silently, but flag the conflict
  with the rule and propose `fix/<N>-frontend` as the correct form.
- The descriptor for what the cycle does lives in: PM context, the first
  commit message on the branch, and any `project_*` memory. NOT in the
  branch name.
