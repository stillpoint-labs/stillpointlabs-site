---
id: 20260527-stash-before-fast-forward
date: '2026-06-11'
project: stillpointlabs-site
domain: infrastructure
tags:
- git
- devcontainer
- merge-safety
- workflow
alternatives: []
applies_to: []
confidence: 0.7
status: active
superseded_by: null
cost_to_produce: null
source_tool: null
source_model: null
source_session: null
created_at: '2026-06-11 20:31:25.234222+00:00'
updated_at: '2026-06-11 20:31:25.234224+00:00'
---

# 20260527-stash-before-fast-forward

## decision

Use `git stash -u` before fast-forwarding main, then verify byte-equivalence of local changes against HEAD before dropping stash

## context

Local .devcontainer/ files existed and appeared identical to origin/main, but there was risk of silent overwrite during fast-forward from 72780f0 to 926449f

## reasoning

Proves equivalence before destroying local state; if diff shows divergence, stash remains as recovery point. Zero cost when files are identical, high value when they're not.
