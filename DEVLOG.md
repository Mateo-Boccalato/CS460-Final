# Development Log – The Torchbearer

**Student Name:** Mateo Boccalato
**Student ID:** 130328923

> Instructions: Write at least four dated entries. Required entry types are marked below.
> Two to five sentences per entry is sufficient. Write entries as you go, not all in one
> sitting. Graders check that entries reflect genuine work across multiple sessions.
> Delete all blockquotes before submitting.

---

## Entry 1 – 2026-05-13: Initial Plan

Read through all four files before touching any code. The core problem is a weighted directed graph where I need to visit every relic node in some order and then exit, minimizing total fuel. It's not just shortest path; the order of visits matters, so there's a combinatorial layer sitting on top of Dijkstra. My plan is to build up in pieces: get select_sources right first, then run_dijkstra in isolation with manual tests, then wire them into precompute_distances, and only after that start on the backtracking search. I'm going to write the search without pruning first just to make sure it's correct, then layer in best-so-far pruning. Two things feel risky: making sure Dijkstra handles nodes that only appear as edge targets (not as keys in the graph dict), and getting the pruning lower bound right so it never cuts the optimal path. I'll test each piece in the Python shell with small examples from the spec before moving forward.

---

## Entry 2 – 2026-05-14: Dijkstra missing-node bug

Ran into the exact issue I flagged in Entry 1. My first version of run_dijkstra only initialized dist for nodes that were keys in the graph dict, so nodes that only showed up as edge destinations never got added. When Dijkstra tried to relax an edge to one of those nodes it would throw a KeyError. Fixed it by scanning all neighbor lists during initialization and adding any missing nodes with dist = inf. Tested on a graph where T has no outgoing edges and only appears as a destination, and it works correctly now. precompute_distances was straightforward after that, just a loop over the sources from select_sources.

---

## Entry 3 – [Date]: [Short description]

_Your entry here._

---

## Entry 4 – [Date]: Post-Implementation Reflection

> Required. Written after your implementation is complete. Describe what you would
> change or improve given more time.

_Your entry here._

---

## Final Entry – [Date]: Time Estimate

> Required. Estimate minutes spent per part. Honesty is expected; accuracy is not graded.

| Part | Estimated Hours |
|---|---|
| Part 1: Problem Analysis | |
| Part 2: Precomputation Design | |
| Part 3: Algorithm Correctness | |
| Part 4: Search Design | |
| Part 5: State and Search Space | |
| Part 6: Pruning | |
| Part 7: Implementation | |
| README and DEVLOG writing | |
| **Total** | |
