---
id: 20260527-defer-unverified-citations
date: '2026-06-11'
project: stillpointlabs-site
domain: code-quality
tags:
- documentation
- citations
- color-science
- source-of-truth
alternatives: []
applies_to: []
confidence: 0.7
status: active
superseded_by: null
cost_to_produce: null
source_tool: null
source_model: null
source_session: null
created_at: '2026-06-11 20:31:25.236004+00:00'
updated_at: '2026-06-11 20:31:25.236005+00:00'
---

# 20260527-defer-unverified-citations

## decision

Do not promote color-science principles doc to a shared source-of-truth until citation URLs are click-verified

## context

docs/principles/2026-05-26-color-science-stillpoint.md was drafted from prior model knowledge in the May 26 session, not from live-fetched sources. Two other project design systems (ingle, troth) already reference it.

## reasoning

Promoting an unverified doc as a cross-project source-of-truth risks propagating bad citations into multiple downstream documents. The verification cost is low; the correction cost after promotion is high.
