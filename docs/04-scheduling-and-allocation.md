## 4. Scheduling / Allocation Optimization

### 4.1 Problem Formulation

Model as a **bipartite graph** `G = (C ∪ J, E)`:
- `C` = candidates (or candidate-availability-slots), `J` = job roles (or role-slots within a team headcount).
- Edge `(c, j) ∈ E` exists iff candidate `c` satisfies all **hard constraints** of job `j` (skills, location, work-auth, availability window overlap).
- Edge weight `w(c, j)` = the fit score from Section 3.

Two variants, chosen per use case:

**(a) One-to-one optimal assignment (single hire per role)** — classic **assignment problem**, solved with the **Hungarian algorithm** (O(V³)) for exact optimality on moderate `|C|,|J|`, or **Hopcroft–Karp** (O(E√V)) for maximum-cardinality matching when a feasibility/coverage question ("can we fill all roles") matters more than weight-optimality.

**(b) Many-to-one / capacity-constrained assignment (team building, multiple hires per role, or one candidate eligible for multiple roles but takes only one)** — modeled as **min-cost max-flow**: source `s → each candidate (cap 1) → each eligible job (cap = 1, cost = -w(c,j)) → team sink node (cap = team headcount) → super-sink t`. Solve with **Successive Shortest Paths (SSP)** using Bellman-Ford/SPFA (handles negative costs from the negated weight) or **Dinic-based** min-cost-flow variants for larger graphs, matching the course's Module-4 network-flow curriculum directly.

### 4.2 Constraints

| Constraint type | Encoding |
|---|---|
| Hard skill requirements | Edge existence filter (no edge if unmet) |
| Team composition (e.g., "team needs ≥1 senior backend, ≥1 frontend, ≤3 total") | Per-team sub-capacity nodes in the flow graph, one node per (team, role-category) with its own capacity bound |
| Availability window | Edge existence filter — overlap test between candidate `availability` and job start window |
| Location / remote policy | Edge existence filter (hard) |
| Visa / work authorization | Edge existence filter (hard) — candidate `work_auth_status` must be in job's `work_auth_required` list |
| Soft preferences (e.g., preferred but not required skill) | Folded into edge weight `w(c,j)`, not a hard filter |

### 4.3 Objective Function

```
maximize  Σ_(c,j)∈M  w(c,j)
subject to:
  each candidate assigned to ≤ 1 job                     (candidate-side capacity)
  each job assigned ≤ headcount(j) candidates             (job-side capacity)
  per-team sub-role capacity constraints satisfied         (team composition)
  all edges in M satisfy hard constraints (already pre-filtered)
```

### 4.4 Stability Considerations

Where both sides have preferences and the marketplace needs a **stable** (not just optimal-weight) matching — i.e., no candidate–job pair would both prefer each other over their current assignment — use **Gale–Shapley deferred acceptance**, with candidates proposing to jobs ranked by their own fit-score-from-the-job's-perspective, and jobs holding the best offers up to their headcount. This trades a small amount of aggregate weight-optimality (vs. the pure max-weight assignment in 4.1) for a matching that is stable and strategy-proof for the proposing side, which matters in a marketplace where both employers and candidates can walk away. **Recommendation:** run max-weight assignment (4.1) as the default allocation for internal team-staffing use cases (Sec.5, where the platform is the sole decision-maker), and run Gale–Shapley for the open two-sided marketplace matching use case (candidates and employers both have agency to accept/decline).

---

