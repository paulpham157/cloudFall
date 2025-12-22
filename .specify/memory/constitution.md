<!--
Sync Impact Report
- Version change: N/A (template) -> 1.0.0
- Modified principles:
  - N/A -> I. Simplicity First
  - N/A -> II. Cloudflare-Only Source of Truth
  - N/A -> III. Deterministic Downtime Event Logic
  - N/A -> IV. Explainable Insights & Suggestions
  - N/A -> V. Reliable Ingestion & Traceability
- Added sections: Scope & Data Governance; Development Workflow & Quality Gates
- Removed sections: None
- Templates requiring updates:
  - [updated] .specify/templates/plan-template.md
  - [updated] .specify/templates/spec-template.md
  - [updated] .specify/templates/tasks-template.md
- Follow-up TODOs: None
-->
# Cloudflare Status Early Warning System Constitution

## Core Principles

### I. Simplicity First
We MUST ship the smallest useful feature set: ingest Cloudflare status data,
count downtime events, compute frequency, surface insights, and suggest
solutions. Anything beyond this baseline requires explicit justification and a
measurable benefit.
Rationale: Tight scope enables fast, low-risk iteration.

### II. Cloudflare-Only Source of Truth
We MUST only ingest from Cloudflare RSS feeds and Cloudflare public APIs. No
scraping, private endpoints, or third-party aggregators without a constitution
amendment. Raw payloads and fetch metadata (timestamp, source, status) MUST be
stored before normalization.
Rationale: Provenance and repeatability depend on stable, published interfaces.

### III. Deterministic Downtime Event Logic
Downtime events MUST have an explicit, documented definition (state transitions,
duration thresholds, dedupe rules, and counting windows). Event counts and
frequencies MUST be reproducible from recorded data. Changes to event logic MUST
be versioned with migration notes.
Rationale: Ambiguous metrics erode trust in early warnings.

### IV. Explainable Insights & Suggestions
Insights and suggested solutions MUST be traceable to specific events or
aggregates and labeled as heuristic. Avoid opaque or ML-only conclusions unless
the underlying evidence is documented and reviewable.
Rationale: Users need explanations they can validate and act on.

### V. Reliable Ingestion & Traceability
Ingestion MUST be resilient: rate-limit aware, retry with backoff, and record
failures. The system MUST expose last successful fetch time and data freshness.
Logs and metrics MUST be sufficient to audit missed or duplicated events.
Rationale: Early warning depends on fresh, trustworthy data.

## Scope & Data Governance

- Scope is limited to Cloudflare status monitoring and early warning signals.
- Raw source payloads are append-only; derived aggregates MUST be reproducible.
- Timestamps MUST be stored in UTC with source identifiers.
- Retention, backfill, and purge rules MUST be documented in the plan before
  release.
- Requests MUST respect published rate limits and API usage policies.

## Development Workflow & Quality Gates

- Every plan MUST pass the Constitution Check in plan.md before Phase 0.
- Any change to parsing, downtime logic, or counting MUST include tests or
  fixtures that prove expected outcomes.
- New dependencies require explicit justification and must preserve simplicity.
- Specs MUST document data sources, polling cadence, and event definitions.
- Releases MUST note any event-logic version changes and migration steps.

## Governance

- This constitution supersedes other project practices; conflicts require an
  amendment.
- Amendments require a PR with rationale, impact analysis, and updates to any
  affected templates or guidance.
- Versioning follows semantic rules: MAJOR for breaking governance/principles,
  MINOR for new principles/sections, PATCH for clarifications.
- Reviewers MUST verify compliance with each principle before merge.
- Event-logic changes MUST include migration notes and a backfill plan when
  historical metrics are affected.

**Version**: 1.0.0 | **Ratified**: 2025-12-22 | **Last Amended**: 2025-12-22
