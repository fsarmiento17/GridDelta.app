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

## Historical Snapshot Policy

- Bundled immutable snapshots for `2015...2025`.
- Current season stays live via Jolpica with rolling refresh cache suffixes.
- Repository routing:
  - Use bundled snapshot directly when present.
  - Fall back to API + cache if bundled data is unavailable.