# [3409 — MST Edge Cost](https://cses.fi/problemset/task/3409)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** MST, binary lifting, path maximum  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an undirected weighted graph, determine for each edge the minimum spanning tree cost if the edge must be included in the spanning tree.

### Input

The first line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

The following `m` lines describe the edges. Each line has three integers `a`, `b`, `w`: there is an edge between nodes `a` and `b` with weight `w`.

You can assume that the graph is connected and simple and each edge appears at most once in the graph.

### Output

For each edge in the input order, print the minimum spanning tree cost when the edge is included.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`
- `1 <= w <= 1e9`

### Examples

**Input**
```
5 6
1 2 4
1 3 2
2 4 2
3 4 1
3 5 4
4 5 3
```

**Output**
```
10
8
8
8
9
8
```

---

## What this problem really is

For each edge `e`, compute the MST cost of the graph under the constraint “`e` is forced into the spanning tree.”

## What to use

| Tool | Why |
|------|-----|
| Unconstrained MST cost `M` | Baseline |
| Path-max on the MST | Forced non-tree edge replaces the heaviest edge on the cycle it creates |
| Binary lifting / LCA | Query max edge weight on MST path in `O(log n)` |

## Approach

**Root idea:** build any MST with cost `M`. Forcing a tree edge changes nothing (`answer = M`). Forcing a non-tree edge `e=(u,v,w)` creates a cycle with the MST path `u…v`; delete the heaviest edge on that path to restore a tree → cost `M - maxPath + w`.

**Mental picture:** the MST is a backbone. Add a chord of weight `w`; the cycle’s most expensive backbone edge is the one you kick out.

**How that becomes the method**

1. Kruskal/Prim → MST edges, cost `M`, adjacency of the MST with weights.
2. Root the MST; binary lifting: `up[k][v]`, `mx[k][v]` = max edge on the climb of `2^k` steps.
3. `maxOnPath(u,v)` via LCA + lifting maxima.
4. For each input edge `(a,b,w)` in order:
   - if it is an MST edge: print `M`;
   - else print `M - maxOnPath(a,b) + w` (64-bit safe).

**Data structures:** DSU for Kruskal; MST adj; lifting tables `O(n log n)`; bool `inMST[m]`.

## Walkthrough (on a sample)

One MST: edges `3-4 (1)`, `1-3 (2)`, `2-4 (2)`, `4-5 (3)` → `M = 8`.

- Force `1-2 (4)`: path `1-3-4-2` max `2` → `8 - 2 + 4 = 10`.
- Force an MST edge e.g. `1-3`: still `8`.
- Force `3-5 (4)`: path `3-4-5` max `3` → `8 - 3 + 4 = 9`.

## Pitfalls

- Using path max on the original graph instead of the MST.
- 32-bit overflow when summing weights.
- For MST edges, do not apply `M - w + w` via a path that includes other edges incorrectly — special-case or note path max equals `w` only for the single-edge path.

## Complexity

- Time: `O((n + m) log n)`
- Space: `O(n log n + m)`

## Key takeaway

Forced-edge MST cost = `M - (max MST-path edge between ends) + w` (and `M` if the edge is already in the MST).
