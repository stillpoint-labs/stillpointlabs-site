---
id: new-site-zero-to-pages-dev
project: stillpointlabs-site
domain: infrastructure
tags:
- cloudflare-pages
- astro
- scaffold
- deployment
- static-site
preconditions: []
steps:
- Recon existing similar sites in the org to select best scaffold template (compare
  dependency profiles, CSS approach, CF Pages config)
- Read brand assets from authoritative source (brand architecture doc + brand guide
  HTML) — paraphrase copy, do not invent positioning
- Wire brand tokens as CSS custom properties in a :root block; self-host fonts from
  /public/fonts/ to avoid GDPR/performance issues with external CDN
- Create public GitHub repo under the correct org (e.g., stillpoint-labs/stillpointlabs-site)
- Scaffold pages (Home/Products/About/Contact minimum), push to main
- Create Cloudflare Pages project connected to the repo; verify first deploy → HTTP
  200 on .pages.dev
- Capture rollback NS records from current registrar BEFORE initiating zone transfer
- 'Run domain nanoprobe: create CF zone → update registrar NS to CF → bind apex +
  www CNAME → poll for cert issuance'
pitfalls:
- Porkbun API access for NS updates may be disabled at the account level — verify
  API access is enabled before firing domain nanoprobe or the whole flow stalls silently
- Self-hosted fonts require correct MIME types and Cache-Control headers in CF Pages
  _headers file; missing this causes browser warnings
- og-default.png (1200×630) must be created separately — scaffold does not generate
  it automatically
cost_estimate: null
times_applied: 0
last_applied: null
confidence: 0.7
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-11 22:41:19.436961+00:00'
updated_at: '2026-06-11 22:41:19.436961+00:00'
---

# new-site-zero-to-pages-dev

## description

Stand up a new Astro static marketing site from zero to a live .pages.dev URL with custom-domain wiring queued
