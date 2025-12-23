# Implementation Plan: Cloudflare Early Warning System

**Branch**: `001-cloudflare-early-warning` | **Date**: 2025-12-22 | **Spec**: `/Users/paulpham157/Downloads/test-spec-kit/specs/001-cloudflare-early-warning/spec.md`
**Input**: Feature specification from `/Users/paulpham157/Downloads/test-spec-kit/specs/001-cloudflare-early-warning/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Build a minimal service that polls Cloudflare RSS feeds and public status APIs, records
raw payloads with fetch metadata, normalizes downtime events (no minimum duration
threshold), computes 24-hour and 7-day counts (primary window: 24h), and triggers
warnings when fixed thresholds are met (24h >= 2, 7d >= 5). Expose summary, insights,
and trend data via a small HTTP API (internal, read-only, no auth) and send warning
messages via a Telegram bot with retry/backoff (no extra failure alert). Capture
auditability metrics (ingest success/failure counts, last success time, dedupe count,
freshness lag). Ensure data freshness is visible and ingestion is resilient with retries
and backoff, with retention/backfill/purge rules enforced.

## Technical Context

**Language/Version**: Node.js 20 (ESM)
**Primary Dependencies**: rss-parser, better-sqlite3
**Storage**: SQLite file (append-only raw payloads + derived tables)
**Testing**: Node.js built-in `node:test`
**Target Platform**: Linux server / container
**Project Type**: single
**Performance Goals**: Ingestion reflects source updates within 10 minutes; warning
messages delivered within 10 minutes of threshold detection
**Constraints**: Cloudflare rate limits respected; thresholds fixed in spec; raw
payloads preserved for auditability; retention: raw payloads 90 days, derived
aggregates 1 year; backfill limited to 30 days; weekly purge; HTTP API is
internal read-only with no authentication; deterministic downtime event logic
(no duration threshold); auditability metrics captured (ingest success/failure
counts, last success time, dedupe count, freshness lag)
**Scale/Scope**: Single-tenant MVP; low event volume (dozens/day)

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- [x] Simplicity First: scope limited to ingestion, counts, insights, suggestions.
- [x] Cloudflare-Only Source: RSS + public API only; raw payloads and fetch metadata stored.
- [x] Deterministic Downtime Logic: explicit incident lifecycle, windows, dedupe rules, and no duration threshold.
- [x] Explainable Insights: insights/suggestions traceable to events and labeled heuristic.
- [x] Reliable Ingestion: retries, backoff, logging, metrics, and freshness surfaced.

Post-design re-check (2025-12-22): PASS

## Project Structure

### Documentation (this feature)

```text
/Users/paulpham157/Downloads/test-spec-kit/specs/001-cloudflare-early-warning/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
└── tasks.md
```

### Source Code (repository root)

```text
/Users/paulpham157/Downloads/test-spec-kit/codex_gpt-5.2-codex/
├── src/
│   ├── ingest/
│   ├── normalize/
│   ├── storage/
│   ├── services/
│   ├── alerts/
│   ├── api/
│   └── cli/
└── tests/
    ├── contract/
    ├── integration/
    └── unit/
```

**Structure Decision**: Single project with a small HTTP API, ingestion workers,
alert delivery, and shared storage modules.

## Complexity Tracking

No constitution violations identified; no complexity tracking entries.
