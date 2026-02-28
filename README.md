# GridDelta

GridDelta is a native SwiftUI iOS app for Formula 1 analytics focused on explainable performance insights rather than basic standings-only views.

## Repo Documentation Rules

- Use repo-relative links in docs (for example, `docs/ANALYTICS_CONTRACTS.md`), not machine-local absolute paths.
- Active docs live in `docs/`.
- Archived docs live in `docs/archive/` and must use the `ARCHIVED_` filename prefix.

## Current Product State

### Core tabs
- `Insights` (first tab, default)
- `Drivers`
- `Constructors`
- `Calendar`

### Insights (current)
- Shared `Race Context` panel:
  - `Season`
  - `Race`
  - `Session` (`Race` / `Qualifying`)
- Separate `Mode` panel:
  - `Advanced Analytics`
  - `Raw Stats`

### Insights Raw Stats
- Race session:
  - `Top Three`
  - `Fastest Lap` (includes extra-point eligibility note)
  - `Race Points` (per selected race, all drivers)
- Qualifying session (core slice complete):
  - `Top Three (Qualifying)`
  - `Pole Lap`
  - `Qualifying Order`

### Insights Advanced
- Race session:
  - Insight cards
  - Clean Air Pace
  - Overperformance Index
  - Strategy Simulator
  - Driver DNA
- Qualifying session:
  - Delta to Pole (top-10 chart + ranked rows)
  - Qualifying Consistency (Q3 rate + Top-10 rate)
  - Qualifying Conversion Preview (grid expectation hints)

### Driver Comparison
- Reworked to avoid mixed-scale chart distortion:
  - `Normalized` mode (readable paired metric bars)
  - `Raw Values` mode (table with explicit units + delta)

## Architecture

- Pattern: `MVVM`
- Async networking: `async/await`
- Data/API abstraction: `F1Repository`
- Error model:
  - Typed network errors
  - Structured partial-data repository errors
- Caching:
  - API responses: `ResponseCacheStore`
  - Computed analytics: `AnalyticsCacheStore`
- Shared season state: `AppSeasonStore`
- Historical dataset support: `HistoricalSnapshotStore`

## Architecture Guardrails

- Analytics formulas must live in domain engines only (`GridDelta/Domain/Analytics`).
- `Views` and `ViewModels` must not implement analytics math.
- Services are orchestration-only: repository calls, caching, mapping, and task coordination.
- Transport/API models must be mapped into canonical domain contracts before formula execution.
- Every user-facing analytics metric must expose a stable `AnalyticsMetricID` and explainability payload.
- Test naming must be functional (no project-phase naming like `PhaseX...`).

## Canonical Analytics Sources

- Contracts:
  - `docs/ANALYTICS_CONTRACTS.md`
- Formulas:
  - `docs/ANALYTICS_FORMULA_HANDBOOK.md`
- Fixture schema:
  - `docs/ANALYTICS_DOMAIN_FIXTURE_SCHEMA.json`

When there is any conflict, these docs take precedence over legacy notes.

### Refactor milestones
- `Domain foundation`: complete.
- `Insights formula extraction`: complete (snapshot loading, selected-race summary, qualifying analytics assembly, partial-data loading, and season/race policy are extracted from `InsightsViewModel` into reusable analytics services/policies).
- `Explainability and deterministic fixtures`: complete (domain explainability payloads are surfaced in Insights, and deterministic golden fixtures cover complete, partial, and future-race schedule scenarios).

### Layer map
- `GridDelta/App`
- `GridDelta/Models`
- `GridDelta/Networking`
- `GridDelta/Services`
- `GridDelta/Analytics`
- `GridDelta/ViewModels`
- `GridDelta/Views`
- `GridDelta/Utils`

## Data Sources

Primary source: Jolpica Ergast-compatible API.

Used endpoints include:
- `/{season}/driverStandings.json`
- `/{season}/constructorStandings.json`
- `/{season}.json`
- `/{season}/results.json`
- `/{season}/qualifying.json`
- `/{season}/pitstops.json`
- `/{season}/{round}/laps.json`

The repository layer is intentionally designed to support richer providers later.

## Historical Snapshot Policy

- Bundled immutable snapshots for `2015...2025`.
- Current season stays live via Jolpica with rolling refresh cache suffixes.
- Repository routing:
  - Use bundled snapshot directly when present.
  - Fall back to API + cache if bundled data is unavailable.

Snapshot assets:
- `GridDelta/Resources/HistoricalSnapshots`

Generator script:
- `scripts/generate_historical_snapshots.rb`
- Run: `ruby scripts/generate_historical_snapshots.rb`

## Build and Test

Project:
- `GridDelta.xcodeproj`

Schemes:
- `GridDelta`
- `GridDeltaUI`

Recommended iOS 26 simulator:
- `platform=iOS Simulator,name=iPhone 17,OS=26.2`

Commands:
- Build:
  - `xcodebuild -project GridDelta.xcodeproj -scheme GridDelta -destination 'platform=iOS Simulator,name=iPhone 17,OS=26.2' build`
- Unit tests:
  - `xcodebuild -project GridDelta.xcodeproj -scheme GridDelta -destination 'platform=iOS Simulator,name=iPhone 17,OS=26.2' test`
- UI smoke:
  - `xcodebuild -project GridDelta.xcodeproj -scheme GridDeltaUI -destination 'platform=iOS Simulator,name=iPhone 17,OS=26.2' test`
- Analytics parity sign-off gate (pre-merge):
  - `scripts/analytics_parity_gate.sh`

## Analytics Change Gate

Any change that can affect analytics outputs must pass all of the following before merge:

- `bash scripts/analytics_parity_gate.sh` passes.
- Metric IDs remain stable and mapped in spec/contracts.
- Explainability coverage/rationale stays present for surfaced metrics.
- New or changed behavior is covered by unit/golden fixtures.

Definition of done for analytics changes:

- Code path follows architecture guardrails.
- Contracts/formulas docs updated when behavior or assumptions change.
- Tests added/updated for edge cases and partial-data semantics.

## Documentation Update Triggers

When changing analytics behavior, update docs in the same PR:

- Formula changes:
  - `docs/ANALYTICS_FORMULA_HANDBOOK.md`
- Metric IDs or output contracts:
  - `docs/ANALYTICS_CONTRACTS.md`
- Fixture format/schema:
  - `docs/ANALYTICS_FIXTURE_SCHEMA.md`
  - `docs/ANALYTICS_DOMAIN_FIXTURE_SCHEMA.json`
- Parity gate/check policy:
  - `scripts/analytics_parity_gate.sh`
  - `docs/ANDROID_PARITY_CHECKLIST.md`
  - `docs/BRANCH_PROTECTION_SETUP.md`

## Git and Remote

- Remote: `origin git@github.com:fsarmiento17/GridDelta.git`
- Branch naming convention: `codex/<scope>`

## Planning Docs (Updated)

- Documentation index (owners, purpose, update triggers):
  - `docs/README.md`
- Active product spec with status addendum:
  - `docs/PRODUCT_SPEC.md`
- Active cross-platform analytics refactor plan (V1.3):
  - `docs/CROSS_PLATFORM_ANALYTICS_PLAN.md`
- Active portability tracker (maintenance and reopen criteria):
  - `docs/PORTABILITY_REFACTOR_BACKLOG.md`
- Stable fixture schema:
  - `docs/ANALYTICS_FIXTURE_SCHEMA.md`
- Formal fixture JSON schema:
  - `docs/ANALYTICS_DOMAIN_FIXTURE_SCHEMA.json`
- Android parity execution checklist:
  - `docs/ANDROID_PARITY_CHECKLIST.md`
- Android implementation manifest:
  - `docs/ANDROID_IMPLEMENTATION_MANIFEST.md`
- Kotlin migration notes for Android module structure:
  - `docs/KOTLIN_MIGRATION_NOTES.md`
- Archived execution checklist (historical reference, not active source of truth):
  - `docs/archive/ARCHIVED_PHASE3_EXECUTION_CHECKLIST.md`

## Operations Docs

- Release checklist:
  - `docs/RELEASE_READINESS_CHECKLIST.md`
- Xcode Cloud setup:
  - `docs/XCODE_CLOUD_SETUP.md`
- App Store metadata pack:
  - `docs/APP_STORE_METADATA.md`
- Public privacy policy:
  - `docs/PRIVACY_POLICY.md`
- Incident/debug playbook:
  - `docs/INCIDENT_DEBUG_PLAYBOOK.md`
- Cache invalidation/freshness policy:
  - `docs/CACHE_INVALIDATION_POLICY.md`

Active planning source is this README + current branch PR descriptions.
