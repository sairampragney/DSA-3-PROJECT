# Résumé–Job Matching & Talent-Marketplace Engine

Full engineering documentation for a Résumé–Job Matching & Talent-Marketplace Engine, built as a DSA-3 (25CS2103E, KLBCH, Odd Sem 2026-27) semester project. The core algorithmic engine follows the course's hand-built-data-structures constraint (no `java.util.*`-equivalent standard collections/algorithms inside the engine) and maps directly onto the course's five advanced-algorithm modules — string algorithms, advanced DP, network flow, NP-completeness/approximation, and randomized/parallel algorithms.

## Documentation

| # | Section | File |
|---|---|---|
| 1 | System Overview | [`docs/01-system-overview.md`](docs/01-system-overview.md) |
| 2 | Data Model & Normalization | [`docs/02-data-model-and-normalization.md`](docs/02-data-model-and-normalization.md) |
| 3 | Matching and Scoring | [`docs/03-matching-and-scoring.md`](docs/03-matching-and-scoring.md) |
| 4 | Scheduling / Allocation Optimization | [`docs/04-scheduling-and-allocation.md`](docs/04-scheduling-and-allocation.md) |
| 5 | Minimum Skill Set Calculation | [`docs/05-minimum-skill-set.md`](docs/05-minimum-skill-set.md) |
| 6 | Data Pipeline and Indexing | [`docs/06-data-pipeline-and-indexing.md`](docs/06-data-pipeline-and-indexing.md) |
| 7 | API and Service Contracts | [`docs/07-api-and-service-contracts.md`](docs/07-api-and-service-contracts.md) |
| 8 | Security, Privacy, and Compliance | [`docs/08-security-privacy-compliance.md`](docs/08-security-privacy-compliance.md) |
| 9 | Deployment Architecture | [`docs/09-deployment-architecture.md`](docs/09-deployment-architecture.md) |
| 10 | Operational Considerations | [`docs/10-operational-considerations.md`](docs/10-operational-considerations.md) |
| 11 | Example Schemas, Workflows & Pseudocode | [`docs/11-schemas-workflows-pseudocode.md`](docs/11-schemas-workflows-pseudocode.md) |
| 12 | Documentation Artifacts (API ref, data dictionary, glossary) | [`docs/12-documentation-artifacts.md`](docs/12-documentation-artifacts.md) |
| 13 | Phasing: MVP → Enhancements | [`docs/13-phasing-roadmap.md`](docs/13-phasing-roadmap.md) |

The complete, single-file version of the documentation (all sections concatenated) is also kept at [`FULL-DOCUMENTATION.md`](FULL-DOCUMENTATION.md) for convenience.

## DSA-3 Module Mapping

| DSA-3 Module | Where it's used |
|---|---|
| Module-2 (String Algorithms) | Skill/field extraction — KMP, Z-function, Rabin-Karp, Aho-Corasick |
| Module-3 (Advanced DP) | Wagner–Fischer edit distance (skill normalization), bitmask DP (minimum skill set) |
| Module-4 (Network Flow) | Min-cost max-flow candidate↔job allocation |
| Module-5 (NP-Completeness & Approximation) | Minimum skill set as set cover, greedy approximation |
| Module-6 (Randomized & Parallel) | MinHash/LSH dedup, reservoir sampling, parallel prefix-sum |

See [`docs/08-security-privacy-compliance.md`](docs/08-security-privacy-compliance.md#85-dsa-3-syllabus-alignment-course-specific-note) for the full alignment table.

## Status

Documentation v1.0 — engineering-lead sign-off draft. No implementation code yet; see [`docs/13-phasing-roadmap.md`](docs/13-phasing-roadmap.md) for the MVP build plan.
