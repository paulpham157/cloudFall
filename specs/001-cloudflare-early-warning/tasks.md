---

description: "Task list template for feature implementation"
---

# Tasks: Cloudflare Early Warning System

**Input**: Design documents from `/Users/paulpham157/Downloads/test-spec-kit/specs/001-cloudflare-early-warning/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Tests are REQUIRED for downtime event logic, parsing, and counting changes. For all other work, include
 tests only if explicitly requested in the feature specification.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Single project**: `src/`, `tests/` at repository root
- Paths shown below assume single project - adjust based on plan.md structure

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [ ] T001 Create project structure in `src/ingest/`, `src/normalize/`, `src/storage/`, `src/services/`, `src/alerts/`, `src/api/`, `src/cli/`, `tests/contract/`, `tests/integration/`, `tests/unit/`
- [ ] T002 Initialize Node.js 20 ESM project and dependencies in `package.json`
- [ ] T003 [P] Add environment template in `.env.example` with required variables
- [ ] T004 [P] Update `.gitignore` to exclude `node_modules/`, `.env`, and `data/`

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T005 Create env loader with defaults in `src/config/env.js`
- [ ] T006 [P] Implement structured logger in `src/services/logger.js`
- [ ] T007 [P] Implement metrics recorder for ingest auditability (log-only) in `src/services/metricsService.js`
- [ ] T008 Define SQLite schema and window seeds in `src/storage/schema.sql`
- [ ] T009 Implement DB connection wrapper in `src/storage/db.js`
- [ ] T010 Implement migration/bootstrap runner in `src/storage/migrate.js`
- [ ] T011 [P] Implement Source store in `src/storage/sourceStore.js`
- [ ] T012 [P] Implement SourceRecord store in `src/storage/sourceRecordStore.js`
- [ ] T013 [P] Implement DowntimeEvent store in `src/storage/downtimeEventStore.js`
- [ ] T014 [P] Implement WindowCount store in `src/storage/windowCountStore.js`
- [ ] T015 Implement HTTP fetch wrapper with Retry-After handling and timeout/retry/backoff in `src/ingest/httpClient.js`
- [ ] T016 [P] Add HTTP client retry/Retry-After tests in `tests/unit/ingest/httpClient.test.js`
- [ ] T017 [P] Add RSS parsing tests in `tests/unit/ingest/rssClient.test.js`
- [ ] T018 [P] Add record mapping tests in `tests/unit/ingest/recordMapper.test.js`
- [ ] T019 Implement RSS client with conditional requests in `src/ingest/rssClient.js`
- [ ] T020 [P] Implement API client with retry/backoff in `src/ingest/apiClient.js`
- [ ] T021 [P] Implement record mapping in `src/ingest/recordMapper.js`
- [ ] T022 Implement source config parsing in `src/ingest/sources.js`
- [ ] T023 Implement ingest runner in `src/ingest/ingestRunner.js`
- [ ] T024 [P] Add downtime normalization tests (core rules) in `tests/unit/normalize/downtimeNormalizer.test.js`
- [ ] T025 [P] Add downtime edge-case tests (out-of-order, duplicates, rapid alternation) in `tests/unit/normalize/downtimeEdgeCases.test.js`
- [ ] T026 Implement downtime normalizer (core + edge cases) in `src/normalize/downtimeNormalizer.js`
- [ ] T027 [P] Add window count tests in `tests/unit/services/windowCountService.test.js`
- [ ] T028 Implement frequency window definitions in `src/services/windowService.js`
- [ ] T029 Implement rolling count computation in `src/services/windowCountService.js`
- [ ] T030 Implement freshness calculation in `src/services/freshnessService.js`
- [ ] T031 Implement ingest audit summary logging in `src/ingest/ingestAudit.js`
- [ ] T032 Integrate auditability metrics capture in `src/ingest/ingestAudit.js`
- [ ] T033 Implement ingestion scheduler loop with backoff in `src/ingest/scheduler.js`
- [ ] T034 [P] Implement JSON response helpers in `src/api/httpUtils.js`
- [ ] T035 Implement HTTP server/router (internal no-auth) in `src/api/server.js`
- [ ] T036 Implement CLI entrypoint in `src/cli/run-server.js`

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - Monitor Current Status (Priority: P1) 🎯 MVP

**Goal**: Provide a clear summary of current Cloudflare status with 24-hour and 7-day downtime counts and freshness.

**Independent Test**: Verify `/v1/status/summary` returns latest status, correct counts, and last update time after an ingestion cycle.

### Implementation for User Story 1

- [ ] T037 [P] [US1] Implement overall status derivation in `src/services/statusService.js`
- [ ] T038 [P] [US1] Implement status summary builder in `src/services/statusSummaryService.js`
- [ ] T039 [US1] Implement no-data summary fallback (overallStatus="unknown", windowCounts=0, warningActive=false) in `src/services/statusSummaryService.js`
- [ ] T040 [US1] Implement status summary handler in `src/api/handlers/getStatusSummary.js`
- [ ] T041 [US1] Wire `/v1/status/summary` route in `src/api/server.js`

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently

---

## Phase 4: User Story 2 - Receive Explainable Early Warnings (Priority: P2)

**Goal**: Deliver Telegram warnings with explanations and suggested actions when fixed thresholds are met.

**Independent Test**: Simulate counts hitting thresholds and confirm a Telegram message is sent with an explanation tied to incidents.

### Implementation for User Story 2

- [ ] T042 [US2] Add Warning/Insight/Suggestion tables in `src/storage/schema.sql`
- [ ] T043 [US2] Update migration bootstrap in `src/storage/migrate.js`
- [ ] T044 [P] [US2] Implement Warning store in `src/storage/warningStore.js`
- [ ] T045 [P] [US2] Implement Insight store in `src/storage/insightStore.js`
- [ ] T046 [P] [US2] Implement Suggestion store in `src/storage/suggestionStore.js`
- [ ] T047 [P] [US2] Implement insight generation in `src/services/insightService.js`
- [ ] T048 [P] [US2] Implement fixed thresholds in `src/services/thresholds.js`
- [ ] T049 [P] [US2] Implement warning evaluator in `src/alerts/warningEvaluator.js`
- [ ] T050 [P] [US2] Implement Telegram client with retry/backoff in `src/alerts/telegramClient.js`
- [ ] T051 [US2] Implement warning delivery in `src/alerts/warningService.js`
- [ ] T052 [US2] Wire warning evaluation into `src/ingest/scheduler.js`
- [ ] T053 [P] [US2] Implement insights handler in `src/api/handlers/getStatusInsights.js`
- [ ] T054 [US2] Wire `/v1/status/insights` route in `src/api/server.js`
- [ ] T055 [US2] Update warningActive in `src/services/statusSummaryService.js`

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently

---

## Phase 5: User Story 3 - Review Historical Trends (Priority: P3)

**Goal**: Compare downtime counts across the 24-hour and 7-day windows to surface trends.

**Independent Test**: Call `/v1/status/trends` and verify counts plus change values for 24-hour vs 7-day windows.

### Implementation for User Story 3

- [ ] T056 [P] [US3] Implement trend computation in `src/services/trendService.js`
- [ ] T057 [US3] Implement trends handler in `src/api/handlers/getStatusTrends.js`
- [ ] T058 [US3] Wire `/v1/status/trends` route in `src/api/server.js`

**Checkpoint**: All user stories should now be independently functional

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T059 Implement retention purge job in `src/storage/retention.js` and schedule weekly in `src/ingest/scheduler.js`
- [ ] T060 Implement backfill limit enforcement (30 days) in `src/ingest/ingestRunner.js`
- [ ] T061 Implement SLA evaluation helper in `src/services/slaService.js`
- [ ] T062 Integrate SLA checks into ingest logging in `src/ingest/ingestAudit.js`
- [ ] T063 [P] Add SLA validation checks for freshness and warning delivery in `tests/integration/slaTargets.test.js`
- [ ] T064 [P] Update run/test/docs guidance in `README.md`
- [ ] T065 [P] Validate and update quickstart in `specs/001-cloudflare-early-warning/quickstart.md`

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Polish (Phase 6)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Depends on Foundational only; no dependency on other stories
- **User Story 2 (P2)**: Depends on Foundational only; integrates warningActive into summary if US1 is present
- **User Story 3 (P3)**: Depends on Foundational only; no dependency on other stories

### Within Each User Story

- Tests (if included) before implementation
- Models/stores before services
- Services before endpoints
- Core implementation before integration
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel
- All tasks within a story marked [P] can run in parallel

---

## Parallel Example: User Story 1

```bash
Task: "Implement overall status derivation in src/services/statusService.js"
Task: "Implement status summary builder in src/services/statusSummaryService.js"
```

---

## Parallel Example: User Story 2

```bash
Task: "Implement Warning store in src/storage/warningStore.js"
Task: "Implement Insight store in src/storage/insightStore.js"
Task: "Implement Suggestion store in src/storage/suggestionStore.js"
Task: "Implement warning evaluator in src/alerts/warningEvaluator.js"
```

---

## Parallel Example: User Story 3

```bash
Task: "Implement trend computation in src/services/trendService.js"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Test User Story 1 independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable
- Avoid vague tasks and cross-story dependencies that break independence
