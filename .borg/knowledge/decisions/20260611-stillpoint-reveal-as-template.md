---
id: 20260611-stillpoint-reveal-as-template
date: '2026-06-11'
project: stillpointlabs-site
domain: architecture
tags:
- astro
- css
- cloudflare-pages
- templating
- static-site
alternatives: []
applies_to: []
confidence: 0.7
status: active
superseded_by: null
cost_to_produce: null
source_tool: null
source_model: null
source_session: null
created_at: '2026-06-11 22:41:19.435776+00:00'
updated_at: '2026-06-11 22:41:19.435776+00:00'
---

# 20260611-stillpoint-reveal-as-template

## decision

Use reveal-site (Astro + plain CSS custom properties) as the template for stillpointlabs-site rather than ingle-site

## context

Two existing Astro sites available as scaffolding references when standing up a net-new marketing site from zero

## reasoning

reveal-site uses plain CSS custom properties (no Tailwind) which matched the desired brand-token pattern for stillpoint. ingle-site uses Astro/Tailwind which would have added a dependency not needed for a simple static marketing site.
