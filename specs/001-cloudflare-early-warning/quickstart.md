# Quickstart: Cloudflare Early Warning System

## Prerequisites

- Node.js 20
- Access to Cloudflare status RSS feeds and public status endpoints
- Telegram bot token and target chat ID

## Configuration

Set environment variables:

- `CLOUDFLARE_RSS_URLS` (comma-separated)
- `CLOUDFLARE_API_BASE_URL`
- `TELEGRAM_BOT_TOKEN`
- `TELEGRAM_CHAT_ID`
- `DB_PATH` (default: `./data/status.db`)
- `POLL_INTERVAL_SECONDS` (default: 300)
- `PORT` (default: 3000)

Thresholds and time windows are fixed in the specification (24h >= 2, 7d >= 5) and are
not user-configurable. No minimum duration threshold is applied. The HTTP API is
internal read-only with no authentication. Auditability metrics are captured via
ingest logging (success/failure counts, last success time, dedupe count, freshness lag).

## Run Locally

```bash
npm install
node src/cli/run-server.js
```

## Verify

- Open `GET /v1/status/summary` to confirm current status, counts, and freshness.
- Trigger a test ingestion cycle and confirm a Telegram warning message is delivered
  when thresholds are met.

## Tests

```bash
node --test
```
