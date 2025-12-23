# Research: Cloudflare Early Warning System

## Decision 1: Runtime and language
- **Decision**: Use Node.js 20 (ESM) for the service runtime.
- **Rationale**: Node 20 provides built-in `fetch` and `AbortController` support,
  simplifying RSS/API ingestion and HTTP calls without extra dependencies.
- **Alternatives considered**: Python 3.12, Go 1.22.

## Decision 2: RSS and API ingestion approach
- **Decision**: Use `rss-parser` for RSS/Atom feeds and native `fetch` for REST API calls.
- **Rationale**: `rss-parser` handles standard feeds with minimal setup; native `fetch`
  supports timeouts and retry wrappers. Use ETag/Last-Modified for conditional requests
  and skip processing on 304 responses.
- **Alternatives considered**: `fast-xml-parser` for custom XML parsing; third-party
  feed aggregators (rejected by constitution).

## Decision 3: Storage and persistence
- **Decision**: Use a local SQLite database for raw payloads and derived entities.
- **Rationale**: SQLite is simple, fast for low volume, and supports append-only tables
  and straightforward migrations for an MVP.
- **Alternatives considered**: Postgres (more operational overhead), DynamoDB (cloud
  dependency, more complex setup).

## Decision 4: HTTP API delivery for summaries
- **Decision**: Expose a minimal HTTP API using Node's built-in `http` module, with no
  authentication (internal read-only use).
- **Rationale**: Keeps dependencies and surface area small while supporting the required
  summary/insight/trend endpoints.
- **Alternatives considered**: Express or Fastify (more dependencies than needed);
  adding auth in MVP (unneeded for internal read-only use).

## Decision 5: Warning delivery channel
- **Decision**: Deliver warnings via Telegram Bot API over HTTPS, with retry/backoff and
  failure state recorded (no extra failure alert).
- **Rationale**: Direct HTTPS calls avoid extra dependencies and keep delivery
  transparent and auditable while preventing alert noise on retries.
- **Alternatives considered**: `node-telegram-bot-api` library (additional dependency);
  sending failure alerts (risk of alert fatigue).

## Decision 6: Downtime event logic
- **Decision**: Count any non-operational incident status (including maintenance) as a
  downtime event. No minimum duration threshold applies. An event is resolved when the
  incident status is "resolved" in the source data. Dedupe by incident ID across updates.
- **Rationale**: Aligns with Cloudflare incident lifecycle while keeping counting
  deterministic and reproducible.
- **Alternatives considered**: Excluding maintenance; applying a duration threshold;
  using "operational" as resolution.

## Decision 7: Warning thresholds
- **Decision**: Fixed thresholds of 24h >= 2 incidents and 7d >= 5 incidents.
- **Rationale**: Provides early warning sensitivity while avoiding constant alerts for
  low-volume events.
- **Alternatives considered**: Lower thresholds (noisy) or higher thresholds (late signal).

## Decision 8: Auditability metrics
- **Decision**: Capture ingest success/failure counts, last successful fetch time, dedupe
  count, and freshness lag.
- **Rationale**: Satisfies constitution traceability requirements without adding heavy
  observability overhead.
- **Alternatives considered**: Full tracing or per-source latency histograms (more
  complexity than needed for MVP).

## Decision 9: Retention, backfill, and purge
- **Decision**: Retain raw payloads for 90 days and derived aggregates for 1 year; limit
  backfill to 30 days; run weekly purge.
- **Rationale**: Balances auditability with storage growth for an MVP.
- **Alternatives considered**: Indefinite retention (unbounded growth), 30-day retention
  (insufficient for trend analysis).
