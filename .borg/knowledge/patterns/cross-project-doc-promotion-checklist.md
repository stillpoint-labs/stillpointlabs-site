---
id: cross-project-doc-promotion-checklist
project: stillpointlabs-site
domain: architecture
tags:
- documentation
- cross-project
- source-of-truth
- promotion
preconditions: []
steps:
- Identify all projects that already reference or will reference the doc
- Verify all external citations/URLs are live and point to the claimed content
- Confirm doc content was derived from primary sources, not from model prior knowledge
  alone
- Commit the verified doc with a clear message indicating it is now a source-of-truth
- Update or create references in downstream projects only after the source doc is
  committed
pitfalls:
- Model-generated citations can look plausible but point to nonexistent or wrong URLs
  — always click-check
- Downstream projects may reference a doc before it's verified if promotion is done
  incrementally; keep the doc in a non-public path until verification is complete
- When multiple projects reference the same doc, a post-promotion correction requires
  coordinated updates across all referencing repos
cost_estimate: null
times_applied: 0
last_applied: null
confidence: 0.7
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-11 20:31:25.237183+00:00'
updated_at: '2026-06-11 20:31:25.237184+00:00'
---

# cross-project-doc-promotion-checklist

## description

Gate process before promoting a document to a shared cross-project source-of-truth
