---
id: obs-20260527-fuse-mount-blocks-orchestrator-rm
session_date: '2026-06-11'
project: stillpointlabs-site
tool: cursor
tags:
- fuse-mount
- orchestrator
- borg
- checkpoints
- rm
category: gotcha
files_involved: []
confidence: 0.9
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-11 20:31:25.237735+00:00'
updated_at: '2026-07-24 03:52:21.933874+00:00'
---

# obs-20260527-fuse-mount-blocks-orchestrator-rm

## content

The Dispatch orchestrator failed to delete a stale checkpoint file when a FUSE mount was active, causing it to write a placeholder checkpoint instead of completing the cleanup. The placeholder persisted until manually superseded.

## resolution

Manually create the real checkpoint and delete the placeholder in the next human-driven session. Treat any orchestrator-written placeholder checkpoint as a signal that a FUSE or filesystem issue was active at write time.
