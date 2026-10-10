---
id: obs-20260616-porkbun-api-disabled-by-default
session_date: '2026-06-16'
project: stillpointlabs-site
tool: porkbun-api
tags:
- porkbun
- dns
- api
- domain-management
- cloudflare
category: gotcha
files_involved: []
confidence: 0.9
source_model: null
source_session: null
superseded_by: null
created_at: '2026-06-16 10:27:02.389075+00:00'
updated_at: '2026-07-24 03:52:21.933874+00:00'
---

# obs-20260616-porkbun-api-disabled-by-default

## content

Porkbun API access is disabled at the account level by default. Attempting to use the Porkbun API for programmatic NS record updates or domain management will fail with an auth/access error until the account owner explicitly enables API access in Porkbun account settings.

## resolution

Noah (account owner) enabled API access manually. Domain nanoprobe was then re-fired successfully. Always verify Porkbun API access is enabled before automating domain operations; include this as a preflight check in domain wiring nanoprobes.
