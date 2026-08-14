## 13. Phasing: MVP → Enhancements

### Phase 0 — MVP (Weeks 1–4)

**Goal:** end-to-end single-machine pipeline proving the core algorithms, sized for course-project demo scale (hundreds–thousands of records), directly exercising DSA-3 Modules 2–5.

Deliverables:
- Hand-built inverted index + trie-based skill alias dictionary (Sec.2.4/6).
- Skill normalization tiers 1–2 (exact + edit-distance fuzzy); tier 3 (embeddings) stubbed/optional.
- Linear fit-scoring model (Sec.3.1) without historical-signal feature (no data yet to learn it from).
- One-to-one Hungarian-algorithm assignment (Sec.4.1a) for a single-role-per-hire demo.
- Bitmask-DP exact minimum-skill-set solver (Sec.5.1) for small teams.
- Minimal REST API (`POST /resumes`, `POST /jobs`, `GET /jobs/{id}/matches`).
- Unit tests for every hand-built algorithm against textbook known-answers.

### Phase 1 — Core Product (Weeks 5–8)

**Goal:** multi-role allocation, approximate algorithms for scale, first pass at fairness/audit.

Deliverables:
- Min-cost max-flow allocation engine with team-composition constraints (Sec.4.1b/4.2).
- Greedy set-cover approximation + partial-staffing fallback (Sec.5.2).
- Ontology DAG with propagation-credit scoring (Sec.2.3/3.2).
- Audit logging of every scored decision with feature vectors (Sec.7.4/8.1).
- Streaming ingestion path for near-real-time indexing (Sec.6.4).

### Phase 2 — Scale & Fairness Hardening (Weeks 9–12)

**Goal:** millions-of-record readiness and compliance maturity.

Deliverables:
- Embedding tier-3 normalization + ANN semantic search (Sec.2.2/6.4).
- Sharding/replication of indexes (Sec.6.5), caching layer with skill-based invalidation.
- MinHash/LSH deduplication at scale (Sec.6.3).
- Fairness-parity monitoring dashboard + calibrated-weight retraining pipeline (Sec.3.3/9.3).
- Gale–Shapley stable-matching mode for the open two-sided marketplace (Sec.4.4).
- Consent/retention automation and PII field-level access control (Sec.8).
- Full observability stack: tracing, SLO dashboards, canary deploys (Sec.7.4/9.3/10.1).

### Phase 3 — Continuous Enhancement (ongoing)

- Monthly scoring-model recalibration; quarterly ontology-curation review.
- A/B testing of scoring-weight changes behind the canary pipeline.
- Parallel-algorithm optimizations (Module-6: parallel prefix-sum for feature aggregation, parallel sort for large ranking batches) as data volume grows past single-node throughput.
- Migration tooling maturity (Sec.10.3) exercised on real ontology-evolution events.

---

*End of document.*
