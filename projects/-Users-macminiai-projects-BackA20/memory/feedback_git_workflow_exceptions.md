---
name: Git workflow — when to bend vs. rewrite
description: If a fix branch gets lost mid-session and a commit lands on development directly, push as-is; do not rewrite history to retrofit the workflow
type: feedback
originSessionId: 8d8f0e0a-555a-4e0f-b254-c9ee10448a11
---
Normal rule: every change goes `fix/<desc>` → `development` → (on FE App Store submit) `main` + tag.

Exception: if the fix branch gets deleted mid-session (e.g. a parallel session cleaned it up) and a commit ends up on `development` directly, **push the commit as-is**. Do not `git branch` + `reset --hard` + re-merge to retrofit the workflow.

**Why:** Git history rewriting (even on unpushed local commits) is riskier than bending the workflow once. A single commit that skipped the fix-branch step is a minor process blip; a botched reset is lost work. The user explicitly corrected me after I proposed rewinding `development` to redo a merge commit with --no-ff.

**How to apply:** When a commit lands on the wrong branch but the branch state is otherwise correct and unpushed, just push it. Don't offer `reset --hard` as a "cleanup." If in doubt, ask before rewriting history.
