# Data Model: Cloudflare Early Warning System

## Entities

### Source
- **Fields**: id, type (rss|api), url, etag, lastModified, lastFetchedAt
- **Notes**: Stores per-source caching headers and fetch metadata.

### SourceRecord
- **Fields**: id, sourceId, fetchedAt, publishedAt, payloadType (xml|json), rawPayload,
  rawHash, incidentId, status, title, link
- **Notes**: Append-only records of raw payloads plus extracted identifiers.

### DowntimeEvent
- **Fields**: id, incidentId, firstSeenAt, lastSeenAt, severity (degraded|outage),
  status (open|resolved), latestSummary
- **Notes**: Normalized incidents deduped by incidentId.

### FrequencyWindow
- **Fields**: key (24h|7d), durationSeconds
- **Notes**: Fixed windows per spec.

### WindowCount
- **Fields**: id, windowKey, windowStart, windowEnd, count, computedAt
- **Notes**: Derived counts per window for reporting and trends.

### Warning
- **Fields**: id, windowKey, threshold, count, triggeredAt, deliveryChannel (telegram),
  deliveryStatus (pending|sent|failed), deliveredAt, message
- **Notes**: Tracks warning lifecycle and delivery outcomes.

### Insight
- **Fields**: id, windowKey, summary, eventIds, createdAt
- **Notes**: Heuristic explanation tied to specific events.

### Suggestion
- **Fields**: id, insightId, text
- **Notes**: User-facing recommended actions linked to insights.

## Relationships

- **Source 1..N SourceRecord**: each source produces many records.
- **SourceRecord N..1 DowntimeEvent**: records map to a single incident via incidentId.
- **DowntimeEvent 1..N Insight**: insights reference one or more events.
- **Insight 1..N Suggestion**: suggestions belong to a specific insight.
- **FrequencyWindow 1..N WindowCount**: counts are computed per window.
- **Warning 1..1 FrequencyWindow**: warnings are issued for a specific window.

## Validation Rules

- All timestamps stored in UTC ISO-8601.
- `incidentId` required for dedupe; records without an incidentId are discarded.
- `rawHash` used to prevent duplicate SourceRecord ingestion per source.
- `severity` and `status` must match defined enums.

## State Transitions

- **DowntimeEvent**: open → resolved (based on source status updates).
- **Warning**: pending → sent | failed.
