# Standard: Observability — Error Tracking

*Cross-project standard for the Stillpoint Labs family (stillpointlabs-site, ingle, troth, reveal). Canonical
source for error-tracking conventions. Not auto-loaded — `@`-include or summarize it where a repo's CLAUDE.md or
a skill needs these rules.*

## Why this exists

Silent failures are the costliest class of production bug. An uncaught exception in a worker, a cascade of API
5xx responses, or a mis-handled edge case in the Next.js server runtime can silently degrade the user experience
for hours before anyone notices via support tickets. Centralised error tracking closes that gap: every runtime
project ships a DSN, captures the events that matter, and routes them to a single dashboard that a human can
actually review.

## Decision: GlitchTip as the standard error tracker

**Chosen in the June 2026 bake-off.** GlitchTip is Sentry-protocol-compatible, self-hosted, and has no per-event
pricing. Key decision points:

- **Sentry SDK compatibility.** Every SDK (`sentry-sdk`, `@sentry/nextjs`, `@sentry/node`) works against
  GlitchTip without modification — only `autoSessionTracking: false` is required (GlitchTip does not implement
  the Sentry sessions endpoint). Swapping back to managed Sentry later is a one-line DSN change.
- **arm64 native.** `glitchtip/glitchtip:latest` (v6.2.0+) publishes `linux/arm64` manifests — no Rosetta, no
  emulation on the drone host (Apple Silicon).
- **Self-hosted on the drone host.** Five services: `postgres:17`, `valkey/valkey:8` (Redis drop-in), `web`,
  `worker`, `migrate`. Minimal operational surface; no external dependency for event ingestion.
- **Reversible.** `GLITCHTIP_DSN` is the single env var all runtimes read. To switch to managed Sentry, update
  the secret and restart. No code change required.

## Infrastructure: running GlitchTip

The canonical `compose.yml` lives in the drone host's GlitchTip service directory. Key environment variables:

| Variable | Purpose |
|---|---|
| `SECRET_KEY` | Django secret — generate with `openssl rand -hex 32` |
| `DATABASE_URL` | Postgres DSN — `postgres://gt:gt@db:5432/glitchtip` |
| `REDIS_URL` | Valkey DSN — `redis://cache:6379/0` |
| `GLITCHTIP_DOMAIN` | Base URL for generated links — must match the scheme clients use |
| `DEFAULT_FROM_EMAIL` | Sender address for notification email |
| `EMAIL_BACKEND` | `django.core.mail.backends.console.EmailBackend` for local; real SMTP via `EMAIL_URL` |
| `ENABLE_USER_REGISTRATION` | `"true"` for initial setup; lock down after first user is created |

**Startup sequence (migrations are NOT automatic):**

```bash
docker compose up -d db cache
docker compose run --rm migrate
docker compose up -d web worker
```

Run `migrate` to completion before starting `web`. The `migrate` service exits with code 0 on success; `web` does
not wait for it and will serve 500s if the schema is not ready.

**Beat scheduler:** `./bin/run-celery-with-beat.sh` runs both the Celery worker and the periodic-task scheduler
in one process. Do not run multiple replicas with this command — duplicate beat tasks will result.

## What MUST be captured

Every runtime project MUST instrument the following event classes. These are non-negotiable; gaps here are
treated as bugs, not configuration choices:

- **Unhandled exceptions** — any exception that escapes the application's normal error boundary. Python: the
  default `sentry_sdk.init` call captures these automatically. Next.js: requires the `instrumentation.ts`
  registration (see SDK section below).
- **Worker / background-job failures** — Celery task failures, cron job panics, queue consumer crashes. Wire the
  `CeleryIntegration` for Python workers. Node workers must wrap their entrypoint in a top-level try/catch that
  calls `Sentry.captureException`.
- **API 5xx responses** — any HTTP 500–599 emitted by the server runtime. Framework integrations
  (`@sentry/nextjs`, `sentry-sdk[fastapi]`, `sentry-sdk[flask]`) capture these automatically when SDK is
  initialised at server startup.

Events that are OPTIONAL (instrument if useful, do not block on them):

- 4xx client errors (usually noise; capture 404s only when debugging routing gaps).
- Performance traces (`tracesSampleRate > 0`) — useful for diagnosing slow endpoints, but not required for
  baseline coverage. Default to `0.1` (10 % sample) to avoid volume overhead.

## SDK setup

All runtimes read their DSN from a single environment variable: **`GLITCHTIP_DSN`**. Browser-exposed Next.js
clients use **`NEXT_PUBLIC_GLITCHTIP_DSN`** (same value, different prefix for Next's env exposure rules).

### Python service / worker

```python
import os
import sentry_sdk

sentry_sdk.init(
    dsn=os.environ["GLITCHTIP_DSN"],
    traces_sample_rate=0.1,
    auto_session_tracking=False,
    environment=os.environ.get("APP_ENV", "development"),
    release=os.environ.get("APP_RELEASE", "unknown"),
)
```

With Celery:

```python
from sentry_sdk.integrations.celery import CeleryIntegration

sentry_sdk.init(
    dsn=os.environ["GLITCHTIP_DSN"],
    integrations=[CeleryIntegration()],
    traces_sample_rate=0.1,
    auto_session_tracking=False,
    environment=os.environ.get("APP_ENV", "development"),
    release=os.environ.get("APP_RELEASE", "unknown"),
)
```

### Next.js app

`sentry.client.config.ts` (browser):

```typescript
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_GLITCHTIP_DSN,
  tracesSampleRate: 0.1,
  autoSessionTracking: false,
  environment: process.env.NODE_ENV,
  release: process.env.APP_RELEASE,
});
```

`sentry.server.config.ts` (Node runtime):

```typescript
import * as Sentry from "@sentry/nextjs";

Sentry.init({
  dsn: process.env.GLITCHTIP_DSN,
  tracesSampleRate: 0.1,
  autoSessionTracking: false,
  environment: process.env.NODE_ENV,
  release: process.env.APP_RELEASE,
});
```

`instrumentation.ts` (App Router, Next.js 13.4+) — required for server-side capture:

```typescript
export async function register() {
  if (process.env.NEXT_RUNTIME === "nodejs") {
    await import("./sentry.server.config");
  }
}
```

### Node / TypeScript service (MCP server, standalone API)

```typescript
import * as Sentry from "@sentry/node";

Sentry.init({
  dsn: process.env.GLITCHTIP_DSN,
  tracesSampleRate: 0.1,
  autoSessionTracking: false,
  environment: process.env.NODE_ENV ?? "development",
  release: process.env.APP_RELEASE,
});
```

Import this file before any other module in the entrypoint, or pass `--require ./instrumentation.js` to the
Node process.

## Release and environment tagging

Every event MUST carry two tags:

- **`environment`** — one of `development`, `staging`, `production`. Set via the `APP_ENV` / `NODE_ENV`
  environment variable at runtime. Never hard-code a string in source.
- **`release`** — a short identifier for the deployed version. The convention is `git rev-parse --short HEAD`
  baked into the container image at build time as `APP_RELEASE`. If a release tag is unavailable, fall back to
  `"unknown"` — do not omit the field.

Why this matters: without `environment`, a noisy development machine floods the production dashboard. Without
`release`, bisecting a regression requires manual timeline archaeology.

## PII scrubbing

GlitchTip inherits Sentry's `send_default_pii` behaviour. The default is `False` — do not change it.
Specifically:

- **Do not set `send_default_pii: True`** in any SDK init block. This flag causes the SDK to attach HTTP
  headers (including `Cookie`, `Authorization`) and the authenticated user object to every event.
- **Scrub before capture when building custom event payloads.** If you call `Sentry.captureException` or
  `sentry_sdk.capture_exception` with extra context, strip fields that may carry PII (`email`, `name`,
  `user_id` beyond an opaque internal ID, free-text search queries) before attaching them.
- **`beforeSend` hook as the last line of defence.** For Next.js and Node, add a `beforeSend` callback that
  redacts known-sensitive keys from `event.request.data` and `event.extra` if your codebase handles user-
  supplied text:

  ```typescript
  Sentry.init({
    // ...
    beforeSend(event) {
      if (event.request?.data) {
        delete (event.request.data as Record<string, unknown>).query;
        delete (event.request.data as Record<string, unknown>).email;
      }
      return event;
    },
  });
  ```

- **Household data (ingle / troth).** Item names, store names, and purchase amounts are not PII under this
  standard — they carry no identity. `user_id` as an opaque UUID is acceptable in event context; the user's
  name or email is not.

## Acceptance checklist (per project, per deploy)

Before declaring a new project or service "observable":

- [ ] `GLITCHTIP_DSN` is present in the runtime environment (not hard-coded, not committed to source).
- [ ] SDK `init` call is the first thing that runs in the process entrypoint.
- [ ] `environment` and `release` tags are set and non-empty.
- [ ] `send_default_pii` is absent or explicitly `False`.
- [ ] A deliberate `throw` / `raise` in a test route reaches the GlitchTip dashboard within 30 seconds.
- [ ] Worker failures are captured (smoke-test by raising inside a Celery task / background job).
- [ ] `autoSessionTracking: false` is set for all JavaScript SDK calls (Node and browser).
