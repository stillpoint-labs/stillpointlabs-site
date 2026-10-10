---
id: obs-20260611-porkbun-api-disabled-by-default
session_date: '2026-06-11'
project: stillpointlabs-site
tool: cursor
tags:
- porkbun
- dns
- api
- domain
- cloudflare
category: gotcha
files_involved: []
confidence: 0.9
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-11 22:41:19.438002+00:00'
updated_at: '2026-07-24 03:52:21.933874+00:00'
---

# obs-20260611-porkbun-api-disabled-by-default

## content

Porkbun API access is disabled at the account level by default. Attempting to use the Porkbun API to update nameservers will fail silently or with an auth error until the account owner explicitly enables API access in the Porkbun dashboard. This blocked the domain nanoprobe mid-flight.

## resolution

Account owner (Noah) had to log into Porkbun dashboard and enable API access at the account level before the nanoprobe could proceed. Always verify Porkbun API access is enabled before queuing any automated DNS operation against a Porkbun-registered domain.
