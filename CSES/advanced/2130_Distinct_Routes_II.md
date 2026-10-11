# [2130 — Distinct Routes II](https://cses.fi/problemset/task/2130)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** min-cost flow, edge-disjoint paths, route reconstruction  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

A game consists of `n` rooms and `m` teleporters. At the beginning of each day, you start in room `1` and you have to reach room `n`.

You can use each teleporter at most once during the game. You want to play the game for exactly `k` days. Every time you use any teleporter you have to pay one coin. What is the minimum number of coins you have to pay during `k` days if you play optimally?

### Input

The first input line has three integers `n`, `m` and `k`: the number of rooms, the number of teleporters and the number of days you play the game. The rooms are numbered `1,2,...,n`.

After this, there are `m` lines describing the teleporters. Each line has two integers `a` and `b`: there is a teleporter from room `a` to room `b`.

There are no two teleporters whose starting and ending room are the same.

### Output

First print one integer: the minimum number of coins you have to pay if you play optimally. Then, print `k` route descriptions according to the example. You can print any valid solution.

If it is not possible to play the game for `k` days, print only `-1`.

### Constraints

- `2 <= n <= 500`
- `1 <= m <= 1000`
- `1 <= k <= n-1`
- `1 <= a, b <= n`

### Examples

**Input**
```
8 10 2
1 2
1 3
2 5
2 4
3 5
3 6
4 8
5 8
6 7
7 8
```

**Output**
```
6
4
1 2 4 8
4
1 3 5 8
```

---

## What this problem really is

Find `k` **edge-disjoint** paths from `1` to `n` minimizing the total number of edges used (each edge costs `1`), then print the paths. Impossible → `-1`.

## What to use

| Tool | Why |
|------|-----|
| Min-cost max-flow | Cap `1` per teleporter; cost `1` per unit; need flow `k` |
| Path decomposition | After flow, extract `k` paths from residual/flow edges |
| Unit capacities | Matches “each teleporter at most once” over the whole game |

## Approach

**Root idea:** one day = one path `1 ↝ n`. Teleporters are shared across days and each may be used on at most one day total ⇒ edge-disjoint paths. Coins = total edges over all days ⇒ minimize sum of lengths = min-cost flow with cost `1` per edge, value `k`.

**Mental picture:** `k` tokens travel from start to finish on separate days, never reusing a portal. Pay one coin per portal hop; minimize total hops.

**How that becomes the method**

1. Network: each teleporter `a→b` becomes an edge capacity `1`, cost `1`.
2. Compute min-cost flow of value `k` from `1` to `n`. If max flow `< k`, print `-1`.
3. Cost of the flow is the coin total — print it.
4. Decomposition: while you need another path, DFS/BFS from `1` along edges that still carry residual flow toward `n`, record the node list, decrease flow on used edges by `1`. Print `length` then the node sequence (as in the sample).

**Data structures:** residual MCMF graph; after solving, adjacency of edges with positive flow for decomposition.

## Walkthrough (on a sample)

Two disjoint routes `1-2-4-8` and `1-3-5-8` use `3+3=6` teleporters — optimal for `k=2`.

## Pitfalls

- Vertex-disjoint instead of edge-disjoint (wrong).  
- Minimizing days instead of coins.  
- Forgetting to print routes in the required format (`len` then nodes).  
- Only printing `-1` on failure (no extra lines).

## Complexity

- Time: MCMF feasible at `n<=500`, `m<=1000`, `k<=n-1`  
- Space: `O(n + m)`

## Key takeaway

**`k` edge-disjoint paths with min total length = min-cost flow of value `k` (cap 1, cost 1), then decompose.**
