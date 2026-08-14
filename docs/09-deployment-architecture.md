## 9. Deployment Architecture

### 9.1 Tech Stack Recommendation

- **Core engine language**: a systems/general-purpose language with manual data-structure control (e.g., Java or C++/Go) to honor the "hand-build every structure" constraint cleanly — no reliance on `java.util.*`/`std::` containers inside the engine module; a thin, separately-audited infra layer may use the standard library.
- **Document/graph storage**: a document DB for `Resume`/`JobPosting` (e.g., MongoDB-class), a graph DB for the Skill Ontology (e.g., Neo4j-class) — infra layer, not the algorithmic core.
- **Message queue**: for the streaming ingestion path (Kafka-class).
- **API layer**: REST (Section 7) behind an API gateway; internal services communicate over gRPC for low-latency scoring calls.
- **Frontend**: standard SPA framework for recruiter/candidate dashboards (out of scope for the algorithmic engine).

### 9.2 Deployment Model

- Containerized microservices (one container image per module in Sec.1.5), orchestrated via Kubernetes.
- Environments: `dev → staging → prod`, with staging running against a sampled/anonymized production-shaped dataset for realistic load testing.

### 9.3 CI/CD

- Every merge: unit tests (algorithm correctness — e.g., known-answer tests for KMP/edit-distance/Hungarian/set-cover against textbook examples) + integration tests (end-to-end ingest→match→allocate on a fixture dataset) + fairness-regression tests (Sec.3.3 parity check on a fixed eval set) must pass before merge.
- Canary deploys for the Scoring service (any model-weight change) with automatic rollback if fairness or latency SLOs regress.

### 9.4 Scaling Plan

- Horizontal scaling of stateless services (API gateway, Matching/Scoring) behind a load balancer.
- Index shards (Sec.6.5) scale horizontally with data volume; allocation-optimization batch jobs scale via partitioning by team/region (the flow/assignment problems are solved independently per non-overlapping candidate pool where the business logic allows it, to keep each solve tractable).

---

