---
id: 20260616-font-self-hosting-stillpoint
date: '2026-06-16'
project: stillpointlabs-site
domain: infrastructure
tags:
- fonts
- performance
- privacy
- astro
- stillpoint-labs
alternatives: []
applies_to: []
confidence: 0.7
status: active
superseded_by: null
cost_to_produce: null
source_tool: null
source_model: null
source_session: null
created_at: '2026-06-16 10:27:02.387181+00:00'
updated_at: '2026-06-16 10:27:02.387181+00:00'
---

# 20260616-font-self-hosting-stillpoint

## decision

Self-hosted Cormorant Garamond + Inter from /public/fonts/ rather than loading from Google Fonts CDN

## context

Brand guide specified these typefaces; needed to choose delivery mechanism for a CF Pages static site

## reasoning

Follows the pattern already established in reveal-site; eliminates third-party DNS lookup latency, avoids GDPR/privacy concerns with Google Fonts tracking, and ensures fonts load even if Google CDN has issues
