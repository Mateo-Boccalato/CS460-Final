# The Torchbearer

**Student Name:** Mateo Boccalato
**Student ID:** 130328923
**Course:** CS 460 – Algorithms | Spring 2026

> This README is your project documentation. Write it the way a developer would document
> their design decisions , bullet points, brief justifications, and concrete examples where
> required. You are not writing an essay. You are explaining what you built and why you built
> it that way. Delete all blockquotes like this one before submitting.

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

> Document your understanding of why Dijkstra produces correct distances.
> Bullet points and short sentences throughout. No paragraphs.

### Part 3a: What the Invariant Means

> Two bullets: one for finalized nodes, one for non-finalized nodes.
> Do not copy the invariant text from the spec.

- **For nodes already finalized (in S):**
  _Your answer here._

- **For nodes not yet finalized (not in S):**
  _Your answer here._

### Part 3b: Why Each Phase Holds

> One to two bullets per phase. Maintenance must mention nonnegative edge weights.

- **Initialization : why the invariant holds before iteration 1:**
  _Your answer here._

- **Maintenance : why finalizing the min-dist node is always correct:**
  _Your answer here._

- **Termination : what the invariant guarantees when the algorithm ends:**
  _Your answer here._

### Part 3c: Why This Matters for the Route Planner

> One sentence connecting correct distances to correct routing decisions.

_Your answer here._

---

## Part 4: Search Design

### Why Greedy Fails

> State the failure mode. Then give a concrete counter-example using specific node names
> or costs (you may use the illustration example from the spec). Three to five bullets.

- **The failure mode:** _Your answer here._
- **Counter-example setup:** _Your answer here._
- **What greedy picks:** _Your answer here._
- **What optimal picks:** _Your answer here._
- **Why greedy loses:** _Your answer here._

### What the Algorithm Must Explore

> One bullet. Must use the word "order."

- _Your answer here._

---

## Part 5: State and Search Space

### Part 5a: State Representation

> Document the three components of your search state as a table.
> Variable names here must match exactly what you use in torchbearer.py.

| Component | Variable name in code | Data type | Description |
|---|---|---|---|
| Current location | | | |
| Relics already collected | | | |
| Fuel cost so far | | | |

### Part 5b: Data Structure for Visited Relics

> Fill in the table.

| Property | Your answer |
|---|---|
| Data structure chosen | |
| Operation: check if relic already collected | Time complexity: |
| Operation: mark a relic as collected | Time complexity: |
| Operation: unmark a relic (backtrack) | Time complexity: |
| Why this structure fits | |

### Part 5c: Worst-Case Search Space

> Two bullets.

- **Worst-case number of orders considered:** _Your answer (in terms of k)._
- **Why:** _One-line justification._

---

## Part 6: Pruning

### Part 6a: Best-So-Far Tracking

> Three bullets.

- **What is tracked:** _Your answer here._
- **When it is used:** _Your answer here._
- **What it allows the algorithm to skip:** _Your answer here._

### Part 6b: Lower Bound Estimation

> Three bullets.

- **What information is available at the current state:** _Your answer here._
- **What the lower bound accounts for:** _Your answer here._
- **Why it never overestimates:** _Your answer here._

### Part 6c: Pruning Correctness

> One to two bullets. Explain why pruning is safe.

- _Your answer here._

---

## References

> Bullet list. If none beyond lecture notes, write that.

- _Your references here._
