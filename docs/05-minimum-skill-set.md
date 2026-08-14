## 5. Minimum Skill Set Calculation

### 5.1 Problem

Given a team-staffing goal — a set of required skills `S = {s_1 … s_k}` and a pool of candidates each covering a subset of `S` — find the **minimum number of candidates (or minimum-cost candidate set)** whose combined skills cover `S`, or, dually, the **minimum skill set** a single candidate/role must carry to close an existing team's coverage gap.

This is exactly the classic **Set Cover** problem (NP-hard — Module-5 of the course syllabus), so:

- **Exact solution (small instances, k ≤ ~20 skills or bounded team size)**: **bitmask DP** over the skill universe, `dp[mask] = min candidates to cover skill-subset `mask``, transitioning by trying each candidate's skill-subset as a bitmask OR — directly the course's Module-3 Bitmask-DP technique (`O(2^k · n)`).
- **Approximation (large instances)**: classic **greedy set-cover** — repeatedly pick the candidate whose *uncovered*-skill contribution is largest, weighted by cost if candidates have different cost (salary, urgency) — gives the standard `H(k) = O(ln k)` approximation guarantee (Module-5's approximation-algorithms content).
- **Weighted variant**: if minimizing headcount or payroll cost rather than just candidate count, use **weighted set cover**, same greedy structure with cost-effectiveness ratio `Δcoverage / cost` as the selection key.

### 5.2 Partial Staffing & Cascading Requirements

- **Partial staffing**: if full coverage of `S` is infeasible with the current pool, the algorithm falls back to **maximum-coverage-under-a-budget** (a matroid/greedy submodular-maximization variant of set cover — same greedy loop, but terminate at `budget` candidates instead of "cover everything"), reporting the residual uncovered skill set explicitly so Talent-Ops can source externally or retrain existing staff.
- **Cascading requirements**: some skills have prerequisite chains in the ontology (e.g., "Kubernetes" implicitly benefits from "Docker" + "Networking basics"). When computing minimum coverage, the DP/greedy uses the ontology-propagated skill credit from Section 2.3/3.2 — so a candidate with the parent skill contributes partial (discounted) coverage toward a child requirement, and the algorithm prefers combinations that fully cover cascading chains over combinations that leave a chain half-satisfied (implemented as a coverage-completeness bonus term in the greedy's selection key, not a hard rule, to avoid infeasibility).

---

