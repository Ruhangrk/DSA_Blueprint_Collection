# [1752 — Creating Offices](https://cses.fi/problemset/task/1752)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** tree, greedy, packing  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `n-1` roads between them. There is a unique route between any two cities, and their distance is the number of roads on that route.

A company wants to have offices in some cities, but the distance between any two offices has to be at least `d`. What is the maximum number of offices they can have?

### Input

The first input line has two integers `n` and `d`: the number of cities and the minimum distance. The cities are numbered `1,2,...,n`.

After this, there are `n-1` lines describing the roads. Each line has two integers `a` and `b`: there is a road between cities `a` and `b`.

### Output

First print an integer `k`: the maximum number of offices. After that, print the cities which will have offices. You can print any valid solution.

### Constraints

- `1 <= n,d <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
5 3
1 2
2 3
3 4
3 5
```

**Output**
```
2
1 4
```

---

## What this problem really is

Maximum set of vertices on a tree with pairwise distance ≥ `d` (distance-`d` packing / strong independent set).

## What to use

| Tool | Why |
|------|-----|
| Deepest-first greedy | On trees, packing greedily from deepest nodes is optimal |
| Tree DP distances | Track distance to the nearest chosen office above/below |
| `O(n)` DFS | `n = 2e5` forbids heavy recomputation |

## Approach

**Root idea:** place offices as deep as possible. When you consider a deep node that is still ≥ `d` away from every already chosen office, taking it never blocks a better packing later.

**Mental picture:** hang the tree from an arbitrary root. Walk from the leaves upward. Whenever a “gap” of length `d` opens with no office, plant one at the deepest available city in that gap.

**How that becomes the method (one standard O(n) greedy)**

1. Root the tree at node `1` (or any node); compute parents and order nodes by decreasing depth (DFS timestamps / sort).
2. Maintain `near[v]` = distance from `v` to the closest already chosen office (`INF` initially).
3. Process vertices from deepest to shallowest:
   - If `near[v] >= d`, select `v` as an office.
   - After selecting, update distances for ancestors: walk up at most `d` steps (or do a second multi-source BFS from all selected offices at the end to verify — for the linear greedy, propagate: for each selected office, the forbidden zone is the ball of radius `d-1`).
4. Practical linear implementation used in many AC codes:
   - DFS returns the maximum distance downward to a selected office in the subtree (or how much “slack” remains).
   - When combining children, if the best downward office is far enough that the current node can host one without violating `d` against subtree offices and against the parent chain, select it.
5. Output the selected set (any maximum set is fine).

**Construction that always works:** multi-source thinking — repeatedly pick a leaf of the remaining forest at maximum depth, add it, delete its `(d-1)`-ball — simulated carefully in `O(n)` via tree DP.

**Data structures:** adj; `depth[]`, `parent[]`; boolean `office[]`; stacks/queues for updates.

## Walkthrough (on a sample)

`d=3`. Path distances: `dist(1,4)=3`, so both OK. Cannot take three cities — any third is within `<3` of one of them. Answer size `2`, e.g. `{1,4}`.

## Pitfalls

- Using graph independent set (`d=2` only) for general `d`.
- Centroid / wrong greedy order losing optimality.
- `d=1`: every pair of distinct cities has distance ≥ 1 → can take all `n` cities.

## Complexity

- Time: `O(n)`
- Space: `O(n)`

## Key takeaway

Max distance-`d` packing on a tree → deepest-first greedy / tree DP that plants an office whenever a gap of length `d` appears.
