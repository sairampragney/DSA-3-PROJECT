## 1. System Overview

### 1.1 Objectives

| # | Objective | Metric |
|---|---|---|
| O1 | Match candidates to job postings with high relevance | Precision@10 ≥ 0.75, Recall@50 ≥ 0.85 |
| O2 | Rank candidates for a given role fairly and explainably | 100% of scores traceable to feature contributions |
| O3 | Assign candidates to open roles / teams optimally at scale | Stable, envy-minimizing assignment on ≥10⁵ candidate–role pairs in <60s batch |
| O4 | Compute the minimum viable skill set to staff a team | Exact for small teams (≤20 roles), ≥95%-optimal approximation for large teams |
| O5 | Operate at millions-of-records scale with sub-200ms query latency | p95 query latency < 200ms, p99 < 500ms |
| O6 | Satisfy fairness, auditability, and data-protection requirements | Every score reproducible from a stored feature vector + model version |

### 1.2 Stakeholders

- **Candidates** — submit résumés, consent to processing, receive match explanations.
- **Employers / Hiring Managers** — post jobs, define team-composition constraints, receive ranked shortlists.
- **Talent-Ops / Marketplace Admins** — manage the skill ontology, run allocation batches, monitor fairness dashboards.
- **Compliance/Legal** — audit scoring decisions, enforce retention and consent policy.
- **Platform Engineering (this team)** — build, operate, and scale the matching engine.

### 1.3 Success Metrics (product-level)

- Time-to-shortlist (job posted → ranked candidate list): < 5 minutes end-to-end (async pipeline) / < 200ms for an already-indexed query.
- Match acceptance rate (employer marks a suggested candidate as "interview-worthy"): tracked as the primary offline/online eval signal for the scoring model.
- Fairness parity: selection-rate ratio across protected-adjacent proxy groups (only where legally permitted to measure) stays within a configured band, monitored continuously.
- Coverage: % of postings that receive at least K qualified candidates within N hours of posting.

### 1.4 High-Level Architecture

```
                                ┌─────────────────────────┐
                                │      Client Layer        │
                                │ (Web/App/Recruiter UI)   │
                                └────────────┬─────────────┘
                                             │ REST/GraphQL (Section 7)
                                ┌────────────▼─────────────┐
                                │       API Gateway         │
                                │ AuthN/AuthZ, rate limit,  │
                                │ request routing           │
                                └────────────┬─────────────┘
             ┌───────────────────────────────┼───────────────────────────────┐
             │                               │                               │
   ┌─────────▼─────────┐         ┌───────────▼───────────┐        ┌─────────▼─────────┐
   │  Ingestion Service │         │   Matching/Scoring     │        │ Allocation/Optim.  │
   │  (résumé & JD       │         │   Service               │        │ Service (bipartite │
   │   parsing, ETL)     │         │   (ranking API)         │        │ matching / flow)   │
   └─────────┬─────────┘         └───────────┬───────────┘        └─────────┬─────────┘
             │                               │                               │
   ┌─────────▼─────────────────────────────────────────────────────────────▼─────────┐
   │                          Core Data & Index Layer                                 │
   │  - Résumé Store (document DB)      - Job Store (document DB)                     │
   │  - Skill Ontology Graph (graph DB) - Inverted Index (hand-built)                  │
   │  - Embedding Index (ANN, hand-built or vetted lib per Sec 6.5)                    │
   │  - Feature Store (scoring features, versioned)                                    │
   └─────────┬─────────────────────────────────────────────────────────────┬─────────┘
             │                                                             │
   ┌─────────▼─────────┐                                         ┌─────────▼─────────┐
   │  Batch Pipeline    │                                         │  Streaming Pipeline │
   │  (nightly ETL,     │                                         │  (near-real-time    │
   │   re-embedding,    │                                         │   new-posting /     │
   │   dedup)           │                                         │   new-résumé index) │
   └────────────────────┘                                         └────────────────────┘
             │
   ┌─────────▼─────────┐
   │ Observability      │
   │ (logs/metrics/trace│
   │  + fairness audit) │
   └────────────────────┘
```

### 1.5 Module List

| Module | Responsibility | Primary algorithms (Sec.) |
|---|---|---|
| Ingestion Service | Parse résumé/JD text (PDF/DOCX/plain), extract structured fields, enrich | String matching (KMP/Z), DP edit-distance for field normalization |
| Skill Normalizer | Map raw skill tokens → canonical ontology nodes | Trie + Aho-Corasick, edit-distance fuzzy match, embedding fallback |
| Indexer | Build inverted index + embedding index | Hand-built inverted index, hashing (rolling hash for shingles) |
| Matching/Scoring Service | Compute candidate↔job fit score | Weighted feature model, DP for skill-set alignment, cosine sim |
| Allocation/Optimization Service | Assign candidates to roles under constraints | Bipartite matching (Hungarian/Hopcroft–Karp), max-flow/min-cost-flow |
| Minimum-Skill-Set Service | Derive minimal skill set to staff a team | Weighted set-cover approximation, bitmask DP for small n |
| Fairness/Audit Service | Log every scoring decision with feature attribution | Deterministic replay, no ML black box without logged features |

---

