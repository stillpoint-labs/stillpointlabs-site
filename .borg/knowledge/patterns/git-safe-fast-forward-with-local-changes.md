---
id: git-safe-fast-forward-with-local-changes
project: stillpointlabs-site
domain: infrastructure
tags:
- git
- workflow
- safety
- fast-forward
preconditions: []
steps:
- Check for stale lock files in .git/ before any git operations (zsh glob `ls .git/*.lock`
  — 'no matches found' means clean)
- Run `git stash -u` to stash all changes including untracked files
- Run `git pull --ff-only` (or equivalent fast-forward)
- For each locally-modified file, run `diff <local-version> HEAD:<path>` to verify
  byte-equivalence
- If all diffs are clean, run `git stash drop` — no work lost
- If any diff shows divergence, do NOT drop the stash; reconcile manually first
pitfalls:
- Skipping `git stash -u` (without -u) will leave untracked files unprotected — they
  can be overwritten by checkout operations
- Fast-forward only works if local branch has no divergent commits; if it fails, you
  need a merge/rebase decision before proceeding
- Byte-equivalence check must use `HEAD:<path>` (post-pull HEAD), not the pre-pull
  ref, or you're diffing against stale state
cost_estimate: null
times_applied: 0
last_applied: null
confidence: 0.7
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-11 20:31:25.236647+00:00'
updated_at: '2026-06-11 20:31:25.236648+00:00'
---

# git-safe-fast-forward-with-local-changes

## description

Safely fast-forward a branch when local working tree has uncommitted changes that may overlap with incoming commits
