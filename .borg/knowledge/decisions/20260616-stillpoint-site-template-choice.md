---
id: 20260616-stillpoint-site-template-choice
date: '2026-06-16'
project: stillpointlabs-site
domain: architecture
tags:
- astro
- css
- cloudflare-pages
- templating
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
created_at: '2026-06-16 10:27:02.386189+00:00'
updated_at: '2026-06-16 10:27:02.386190+00:00'
---

# 20260616-stillpoint-site-template-choice

## decision

Used reveal-site (Astro/CSS-vars, CF Pages) as template for stillpointlabs-site rather than ingle-site (Astro/Tailwind, static)

## context

Two existing Astro sites available as scaffolding references when standing up a net-new marketing site

## reasoning

reveal-site's plain-CSS custom properties pattern matched the desired brand token approach better than Tailwind utility classes; also already targets CF Pages deployment which is the chosen host
