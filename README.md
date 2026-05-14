# The Torchbearer

**Student Name:** Mateo Boccalato
**Student ID:** 130328923
**Course:** CS 460 – Algorithms | Spring 2026

---

## Part 1: Problem Analysis

- **Why a single shortest-path run from S is not enough:**
  Dijkstra from S gives the cheapest way to reach each node, but it can't decide which relic to visit first. That ordering choice is exactly what changes the total fuel cost.

- **What decision remains after all inter-location costs are known:**
  You still have to pick the sequence to visit the relic chambers. Knowing every pairwise travel cost doesn't tell you which permutation of relics is cheapest end-to-end.

- **Why this requires a search over orders (one sentence):**
  The total cost depends on the order relics are visited, so the algorithm has to explore possible orderings and pick the one with the lowest total fuel.

---

## Part 2: Precomputation Design

### Part 2a: Source Selection

| Source Node Type | Why it is a source |
|---|---|
| Spawn (S) | Every route starts here; need cheapest paths from S to each relic and to T |
| Each relic node | After visiting a relic, the next leg starts from there; need costs to remaining relics and to T |

### Part 2b: Distance Storage

| Property | Your answer |
|---|---|
| Data structure name | Nested dictionary (dict of dicts) |
| What the keys represent | Source nodes (spawn + each relic) |
| What the values represent | A dict mapping every reachable node to its minimum fuel cost from that source |
| Lookup time complexity | O(1) |
| Why O(1) lookup is possible | Python dicts are hash tables, so both the outer and inner lookups are constant time |

### Part 2c: Precomputation Complexity

- **Number of Dijkstra runs:** k+1 (one for spawn, one per relic; deduped if spawn is also a relic)
- **Cost per run:** O(m log n)
- **Total complexity:** O((k+1) * m log n)
- **Justification (one line):** Each source in select_sources gets one full Dijkstra pass over all n nodes and m edges

---

## Part 3: Algorithm Correctness

### Part 3a: What the Invariant Means

- **For nodes already finalized (in S):**
  dist[v] is the true shortest-path distance from the source to v, not just a running estimate. It will not change for the rest of the algorithm.

- **For nodes not yet finalized (not in S):**
  dist[u] is the cheapest path found so far from the source to u, but only counting paths whose intermediate stops are all in S. A shorter path might still exist once more nodes get finalized.

### Part 3b: Why Each Phase Holds

- **Initialization:** Before the first iteration, S is empty and only the source has dist = 0. That's correct since the shortest path from a node to itself costs 0. All other estimates start at infinity, which is a valid upper bound.

- **Maintenance:** When we extract the minimum-dist node u, any alternative path to u would have to pass through some unfinalized node v with dist[v] >= dist[u]. Since edge weights are nonnegative, extending any such path can only add cost, so no shorter path to u can exist. Finalizing u is safe.

- **Termination:** When the queue empties, every reachable node has been finalized and holds its true shortest-path distance. Nodes that were never reached keep dist = inf.

### Part 3c: Why This Matters for the Route Planner

If dist_table contains any wrong value, the search will evaluate routes using incorrect fuel costs and could select a suboptimal ordering or miss the true minimum entirely.

---

## Part 4: Search Design

### Why Greedy Fails

- **The failure mode:** Greedy always picks the nearest unvisited relic, but a cheap first step can force an expensive path to the remaining relics or the exit.
- **Counter-example setup:** Relics are A and B, exit is T. dist[S][A]=1, dist[S][B]=2, dist[A][B]=100, dist[A][T]=1, dist[B][A]=1, dist[B][T]=1.
- **What greedy picks:** S -> A (cost 1, nearest), then A -> B (cost 100), then B -> T (cost 1). Total fuel = 102.
- **What optimal picks:** S -> B (cost 2), then B -> A (cost 1), then A -> T (cost 1). Total fuel = 4.
- **Why greedy loses:** A looks better from S because it costs 1 vs 2, but committing to A first makes the A -> B leg cost 100. Going to B first is slightly more expensive upfront but keeps all the remaining paths cheap.

### What the Algorithm Must Explore

- The algorithm must try every possible order of visiting relics, tracking the total fuel cost for each complete order, and return the order with the lowest cost.

---

## Part 5: State and Search Space

### Part 5a: State Representation

| Component | Variable name in code | Data type | Description |
|---|---|---|---|
| Current location | `current_loc` | any hashable node | The chamber the Torchbearer is currently in |
| Relics not yet collected | `relics_remaining` | set | Relics still to be visited; shrinks as the search goes deeper |
| Fuel cost so far | `cost_so_far` | float | Total fuel burned to reach the current state |

### Part 5b: Data Structure for Visited Relics

| Property | Your answer |
|---|---|
| Data structure chosen | set |
| Operation: check if relic already collected | O(1) - check if node not in relics_remaining |
| Operation: mark a relic as collected | O(1) - remove from relics_remaining |
| Operation: unmark a relic (backtrack) | O(1) - add back to relics_remaining |
| Why this structure fits | All three operations are O(1) with a hash set, and add/remove makes backtracking straightforward |

### Part 5c: Worst-Case Search Space

- **Worst-case number of orders considered:** k! (k factorial)
- **Why:** At each step there are k choices for the next relic, then k-1, and so on, giving k * (k-1) * ... * 1 paths in the worst case before pruning kicks in.

---

## Part 6: Pruning

### Part 6a: Best-So-Far Tracking

- **What is tracked:** `best` is a two-element list `[cost, order]` holding the lowest total fuel found so far and the relic ordering that produced it.
- **When it is used:** At the base case, once all relics are collected, the completed route's cost is compared to `best[0]` and replaces it if strictly lower.
- **What it allows the algorithm to skip:** Any branch where `cost_so_far` plus the lower bound is already >= `best[0]` gets cut immediately, since it can't produce a strictly better result.

### Part 6b: Lower Bound Estimation

- **What information is available at the current state:** `cost_so_far`, the set of remaining relics, `dist_table[current_loc][r]` for every remaining relic r, and `dist_table[r][exit_node]` for every remaining relic r.
- **What the lower bound accounts for:** The cheapest possible hop from `current_loc` to any remaining relic, plus the cheapest possible hop from any remaining relic to `exit_node`. Both legs must appear in every valid completion of this branch.
- **Why it never overestimates:** Each minimum is taken independently over the remaining relics, so the actual completion cost (which must include both legs) is always >= their sum.

### Part 6c: Pruning Correctness

- The optimal solution has some total cost C*. If `cost_so_far + lb >= best[0]` and `best[0] <= C*`, then no completion of this branch achieves C*, so cutting it is safe.
- `best[0]` is only updated when a strictly better complete route is found, so it always holds a valid upper bound on the true optimum. Any branch that can't beat it is provably no better than what we already have.

---

## References

- Lecture notes only.
