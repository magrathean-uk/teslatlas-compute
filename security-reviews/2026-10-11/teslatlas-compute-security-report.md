# Security Review: teslatlas-compute

## Scope

Standard offline scan of frozen Compute Rust kernel at current HEAD.

- Scan mode: repository
- Target kind: git_revision
- Target ID: target_sha256_2a7e109612971b428ebb072ef139f6145f5299662fb7ad119f04d1e1d6909d3c
- Revision: b2af5e28ad2fe29fce81ec572dca90ef73ec8eda
- Inventory strategy: repository
- Included paths: .
- Excluded paths: none
- Artifacts reviewed: src/lib.rs, src/cleaner.rs, src/raster.rs, Cargo.toml, README.md, tests

Limitations and exclusions:
- No builds or runtime tests.
- Frozen checkout is no longer authoritative; Hub/compute is current integration.
- App and Hub consumers were contextual checks, not exhaustive in this scan.

### Scan Summary

| Field | Value |
| --- | --- |
| Scan outcome | completed |
| Reportable findings | 0 |
| Severity mix | none |
| Confidence mix | none |
| Coverage | complete |
| Validation mode | static_source_trace |

Canonical artifacts: `scan-manifest.json`, `findings.json`, and `coverage.json`. This report is a deterministic projection of those files.

## Threat Model

Teslatlas Compute is a dependency-free Rust map kernel, frozen in this checkout and now authoritative under Hub/compute. It parses bounded position-page bytes and converts typed drives into deterministic cleaned and rasterized tile payloads for App and Hub callers.

### Assets

- Availability of App/Hub map computation
- Canonical position-page and tile-payload bytes
- Deterministic algorithm/version identity used by caches and published artifacts

### Trust Boundaries

- Caller-supplied bounded bytes and typed Drive slices to parser/cleaner/generator
- Page visitor controlled by App/Hub caller to repeated computation and output
- Compute output to signed/cached artifact publication, which is owned by callers

### Attacker Capabilities

- A malicious/corrupt upstream history source may influence positions later supplied by a caller
- A caller may provide arbitrary number of pages unless it enforces an aggregate bound; this crate does not itself open a network endpoint

### Security Objectives

- Reject malformed and oversized pages without panic or excessive allocation
- Bound tile/weight output and per-drive cleaner work
- Honor cancellation and optional RDP work budget
- Preserve canonical output and fail without partial publication

### Assumptions

- Offline read-only source review; no cargo builds/tests, app integration or runtime attack execution.
- This checkout is frozen; fixes belong in Hub/compute after owner authorization.
- Callers own scheduling, global source paging, cancellation and publication per README.

## Findings

### No findings

No reportable findings survived the canonical discovery, validation, and reportability gates.

## Reviewed Surfaces

| Surface | Risk Area | Outcome | Notes |
| --- | --- | --- | --- |
| Repeated valid pages can consume unbounded compute without aggregate work cap | Conditional caller misuse | Rejected | No standalone lower-trust entrypoint in the library; Hub caller caps scratch positions and uses finite RDP budget, while App integration needs separate assessment. |
| Binary position-page parser | Malformed bytes to allocation and panic | No issue found | 8 MiB and 128-drive bounds, checked point bytes, truncation/trailing checks and cancellation reviewed. |
| Typed drive cleaner | Untrusted position count and numeric edge cases | No issue found | 65,536 raw-position cap and numeric widening/checks reviewed. |
| Raster generator and page visitor | Algorithmic work and output bounds | No issue found | Tile/unique-weight/payload caps and optional RDP budget reviewed; total streaming work is caller-owned by contract. |
| Version and publication contract | Canonical output/cache reuse | No issue found | Algorithm and geometry identity stated; caller owns publication/signature eligibility. |

## Open Questions And Follow Up

- App caller uses unlimited streaming API; upstream import budgets and runtime liveness should be assessed in App integration scope, not inferred as this frozen library's standalone vulnerability.
