# [1711 — Distinct Routes](https://cses.fi/problemset/task/1711)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, max flow, edge-disjoint paths  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A game consists of `n` rooms and `m` teleporters. At the beginning of each day, you start in room `1` and you have to reach room `n`.

You can use each teleporter at most once during the game. How many days can you play if you choose your routes optimally?

### Input

The first input line has two integers `n` and `m`: the number of rooms and teleporters. The rooms are numbered `1, 2, ..., n`.

After this, there are `m` lines describing the teleporters. Each line has two integers `a` and `b`: there is a teleporter from room `a` to room `b`.

There are no two teleporters whose starting and ending room are the same.

### Output

First print an integer `k`: the maximum number of days you can play the game. Then, print `k` route descriptions according to the example. You can print any valid solution.

### Constraints

- `2 <= n <= 500`
- `1 <= m <= 1000`
- `1 <= a, b <= n`

### Examples

**Input**
```
6 7
1 2
1 3
2 6
3 4
3 5
4 6
5 6
```

**Output**
```
2
3
1 2 6
4
1 3 4 6
```

---

## What this problem really is

Directed graph: max number of **edge-disjoint** paths from `1` to `n` (each edge used in at most one path across all days). Also output the paths.

## What to use

| Tool | Why |
|------|-----|
| Max flow, capacity `1` per edge | Unit capacities → flow = #edge-disjoint paths |
| Path decomposition | After max flow, extract `k` paths by following flow-`1` edges |
| Dinic / EK | `n<=500`, `m<=1000` |

## Approach

**Root idea:** each day needs a `1→n` route; teleporters are single-use over the whole game → paths must be edge-disjoint. That number equals max flow with every edge capacity `1`.

**Mental picture:** each teleporter is a one-shot ticket; pack as many `1→n` journeys as possible without reusing tickets.

**Steps**

1. Build flow network: each teleporter `a→b` capacity `1`.
2. `k = maxflow(1, n)`.
3. Print `k`.
4. Decomposition: while flow remains, DFS/BFS from `1` along edges with residual flow used (original forward flow `1`), record the path to `n`, decrease flow along it by `1`, print length and nodes.

## Walkthrough (on a sample)

Two edge-disjoint routes: `1-2-6` and `1-3-4-6` → **`k = 2`** (as in sample; the other branch `1-3-5-6` shares the start edge style but sample picks `4`).

## Pitfalls

- Vertex-disjoint vs edge-disjoint — here only edges are limited.
- Recovering paths from residual incorrectly (follow edges that carried flow).
- Printing flow value without routes.

## Complexity

- Time: max-flow + `O(k * (n+m))` decomposition  
- Space: `O(n + m)`

## Key takeaway

**Max days with one-use directed edges = unit-capacity max flow `1→n`, then decompose flow into paths.**
