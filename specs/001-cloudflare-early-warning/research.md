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
- **Decision**: Expose a minimal HTTP API using Node's built-in `http` module.
- **Rationale**: Keeps dependencies and surface area small while supporting the
  required summary/insight/trend endpoints.
- **Alternatives considered**: Express or Fastify (more dependencies than needed).

## Decision 5: Warning delivery channel
- **Decision**: Deliver warnings via Telegram Bot API over HTTPS.
- **Rationale**: Direct HTTPS calls avoid extra dependencies and keep delivery
  transparent and auditable.
- **Alternatives considered**: `node-telegram-bot-api` library (additional dependency).

## Decision 6: Scheduling and retries
- **Decision**: Use an internal interval loop with backoff on failures.
- **Rationale**: Matches the 10-minute freshness target and supports rate-limit aware
  retries without external schedulers.
- **Alternatives considered**: System cron or queue workers (more ops overhead).

## Decision 7: Data retention
- **Decision**: Retain raw payloads for 90 days and derived aggregates for 1 year.
- **Rationale**: Balances auditability with storage growth for an MVP.
- **Alternatives considered**: Indefinite retention (unbounded growth), 30-day retention
  (insufficient for trend analysis).
