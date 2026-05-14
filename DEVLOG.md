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

## Entry 3 – 2026-05-14: Pruning design

The trickiest part of the search was getting the lower bound right. My first instinct was to just prune on cost_so_far >= best[0], which works but cuts almost nothing early on when best[0] is still infinity. Adding a lower bound that accounts for both the cheapest remaining relic hop and the cheapest relic-to-exit hop made a real difference on larger inputs. I spent a while convincing myself it was actually safe: the key is that both legs have to exist in any valid completion, so taking their minimums separately still gives you something the real cost can't go below. Once I was sure about that the comment basically wrote itself.

---

## Entry 4 – 2026-05-14: Post-Implementation Reflection

If I had more time I'd tighten the lower bound. Right now it takes two independent minimums, which is valid but loose when there are several relics left since it doesn't account for the cost of visiting the ones in between. A better bound would sum the minimum incoming edge for each remaining relic, similar to a minimum spanning tree bound used in TSP. I'd also add a check that skips branches the moment any required relic becomes provably unreachable, rather than waiting until we try to traverse an inf-cost edge. Both changes would cut more branches without affecting correctness.

---

## Final Entry – 2026-05-14: Time Estimate

| Part | Estimated Hours |
|---|---|
| Part 1: Problem Analysis | 0.25 |
| Part 2: Precomputation Design | 0.5 |
| Part 3: Algorithm Correctness | 1.0 |
| Part 4: Search Design | 0.75 |
| Part 5: State and Search Space | 0.5 |
| Part 6: Pruning | 0.75 |
| Part 7: Implementation | 3.0 |
| README and DEVLOG writing | 1.0 |
| **Total** | **7.75** |
