# [3308 — Graph Coloring](https://cses.fi/problemset/task/3308)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** chromatic number, bitmask DP, independent sets  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given a simple graph with `n` nodes and `m` edges. Your task is to use the minimum possible number of colors to color each node such that no edge connects two nodes of the same color.

### Input

The first line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1, 2, ..., n`.

Then there are `m` lines describing the edges. Each line has two integers `a` and `b`: there is an edge connecting nodes `a` and `b`.

### Output

First, print an integer `k`: the minimum number of colors.

After this, print `n` integers `c1, c2, ..., cn`: the colors of the nodes. The colors should satisfy `1 <= ci <= k`.

You may print any valid solution.

### Constraints

- `1 <= n <= 16`
- `0 <= m <= n*(n-1)/2`

### Examples

**Input**
```
4 4
1 2
2 3
3 4
4 1
```

**Output**
```
2
1 2 1 2
```

---

## What this problem really is

Compute the chromatic number `χ(G)` of a tiny graph (`n ≤ 16`) and output one optimal coloring.

## What to use

| Tool | Why |
|------|-----|
| Bitmask over vertices | `2^n ≤ 65536` states |
| Independent-set DP | Each color class is an independent set |
| Precompute valid masks | A mask is a color class iff it contains no edge |

## Approach

**Root idea:** a proper coloring partitions `V` into the fewest independent sets. DP over subsets: `dp[S]` = min colors to color exactly the vertices in `S`.

**Mental picture:** peel off one monochrome independent set at a time from the remaining uncolored blob until nothing is left; use the fewest peels.

**How that becomes the method**

1. Build adjacency bitmask `adj[v]` (bits of neighbors). Or edge check: for mask `S`, it is independent iff for every edge `{u,v}`, not both bits set.
2. Enumerate all masks `0..2^n-1`; mark `indep[S]=true` when `S` is independent (including empty).
3. `dp[0] = 0`; `dp[S] = INF` else.  
   For each `S`, for each submask `T ⊆ S` with `indep[T]`:  
   `dp[S] = min(dp[S], dp[S\T] + 1)`.  
   (Enumerate submasks in the usual `T = S; T > 0; T = (T-1)&S` loop.)
4. `k = dp[(1<<n)-1]`. Reconstruct: walk from full set, peel an independent `T` that achieves the transition, assign the next color to all bits in `T`.

**Data structures:** `indep[1<<n]`, `dp[1<<n]`, `prev`/`choice` for reconstruction; `color[n]`.

## Walkthrough (on a sample)

4-cycle: bipartite → `χ=2`. Independent sets include every other vertex. DP reaches full set with `2` peels; coloring `1 2 1 2`.

## Pitfalls

- Allowing a non-independent mask as a color class.
- `O(3^n)` submask DP is fine for `n=16` (`~43e6`); `O(2^n * 2^n)` without submask trick may TLE.
- Isolated vertices still need a color (`k ≥ 1` if `n≥1`).

## Complexity

- Time: `O(3^n + 2^n * poly)` 
- Space: `O(2^n)`

## Key takeaway

Tiny chromatic number → DP on subsets by peeling independent sets (color classes).
