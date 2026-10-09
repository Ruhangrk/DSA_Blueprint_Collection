# [2081 — Fixed-Length Paths II](https://cses.fi/problemset/task/2081)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** centroid decomposition, Fenwick, range of lengths  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a tree of `n` nodes, your task is to count the number of distinct paths that have at least `k1` and at most `k2` edges.

### Input

The first input line contains three integers `n`, `k1` and `k2`: the number of nodes and the path lengths. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print one integer: the number of paths.

### Constraints

- `1 <= k1 <= k2 <= n <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 2 3
1 2
2 3
3 4
3 5
```

**Output**
```
6
```

---

## What this problem really is

Same as Fixed-Length Paths I, but count paths whose length lies in `[k1, k2]` instead of a single `k`.

## What to use

| Tool | Why |
|------|-----|
| Centroid decomposition | Same path split as part I |
| Fenwick / prefix on depths | For each dist `d`, count partners with depth in `[k1-d, k2-d]` |
| `long long` | Path counts can be large |

## Approach

**Root idea:** identical centroid framework. At centroid `c`, when processing a node at distance `d` from `c`, add the number of already-seen nodes whose distance `x` satisfies `k1 <= d+x <= k2`, i.e. `x in [k1-d, k2-d]` (clamped to valid range). Then insert `d` into the structure.

**Mental picture:** same “match two legs through the centroid,” but the other leg can be any length in a window — query a range on the depth histogram instead of one bucket.

**Data structures**

- Fenwick of depth frequencies (or prefix array if you rebuild per centroid carefully).
- Centroid recursion with `removed[]`.

**Steps**

1. Centroid-decompose.
2. At each centroid: for each subtree, query then update depths; clear structure after the centroid.
3. Sum all contributions; print.

## Walkthrough (on a sample)

Lengths in `[2,3]`: the 4 paths of length 2 plus length-3 paths (e.g. `1-2-3-4`, `1-2-3-5`) → **6**.

## Pitfalls

- Off-by-one in the interval `[k1-d, k2-d]`.
- Negative indices when `d > k2` — those nodes contribute 0 partners.
- Reusing part I’s single-index `freq[k-d]` without generalizing to a range.

## Complexity

- Time: `O(n log^2 n)` with Fenwick at centroids  
- Space: `O(n)`

## Key takeaway

**Paths with length in `[k1,k2]` = centroid decomposition + range queries on depth frequencies** (generalizes exact-`k` counting).
