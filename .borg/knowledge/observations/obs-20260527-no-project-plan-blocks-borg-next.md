---
id: obs-20260527-no-project-plan-blocks-borg-next
session_date: '2026-06-11'
project: stillpointlabs-site
tool: cursor
tags:
- borg
- PROJECT_PLAN.md
- orchestrator
- workflow-dependency
category: tool_behavior
files_involved: []
confidence: 0.7
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-11 20:31:25.238625+00:00'
updated_at: '2026-07-24 03:52:21.933874+00:00'
---

# obs-20260527-no-project-plan-blocks-borg-next

## content

The borg `borg-next` command returns nothing useful for a repo that has no PROJECT_PLAN.md. Without a declared plan, the orchestrator has no criteria to evaluate next actions against.

## resolution

Before expecting borg-next to be useful, ensure a PROJECT_PLAN.md exists with 3-6 verifiable criteria and is marked at minimum 'proposed, awaiting confirmation'. The handoff doc at docs/plans/handoff/ is a prerequisite input for that plan, not a substitute for it.
