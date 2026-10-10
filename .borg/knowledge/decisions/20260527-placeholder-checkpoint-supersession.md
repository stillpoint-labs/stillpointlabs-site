---
id: 20260527-placeholder-checkpoint-supersession
date: '2026-06-11'
project: stillpointlabs-site
domain: infrastructure
tags:
- borg
- checkpoints
- orchestrator
- fuse-mount
alternatives: []
applies_to: []
confidence: 0.7
status: active
superseded_by: null
cost_to_produce: null
source_tool: null
source_model: null
source_session: null
created_at: '2026-06-11 20:31:25.235520+00:00'
updated_at: '2026-06-11 20:31:25.235521+00:00'
---

# 20260527-placeholder-checkpoint-supersession

## decision

Supersede orchestrator-written placeholder checkpoint with a real one in the same session rather than amending it

## context

The Dispatch orchestrator created a placeholder checkpoint (2026-05-26-2203) when a FUSE mount blocked its `rm` command. The placeholder contained no real session content.

## reasoning

Creating a fresh checkpoint with the actual session content and deleting the placeholder preserves checkpoint integrity without polluting history with an empty record. The placeholder's only purpose was to unblock the orchestrator's lock-file concern.
