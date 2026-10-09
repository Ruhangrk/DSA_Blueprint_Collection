# [2080 — Fixed-Length Paths I](https://cses.fi/problemset/task/2080)

**Platform:** CSES  
**Source:** CSES Problem Set — Tree Algorithms  
**Tags:** centroid decomposition, tree paths  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given a tree of `n` nodes, your task is to count the number of distinct paths that consist of exactly `k` edges.

### Input

The first input line contains two integers `n` and `k`: the number of nodes and the path length. The nodes are numbered `1, 2, ..., n`.

Then there are `n-1` lines describing the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print one integer: the number of paths.

### Constraints

- `1 <= k <= n <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 2
1 2
2 3
3 4
3 5
```

**Output**
```
4
```

---

## What this problem really is

Count unordered paths of length exactly `k` (edges) in a tree. `n` large → need centroid decomposition (or similar global tree technique), not `O(n^2)`.

## What to use

| Tool | Why |
|------|-----|
| Centroid decomposition | Split paths into those through a centroid |
| Depth frequency array | Count pairs of depths summing to `k` |
| Recurse on components | After removing centroid |

## Approach

**Root idea:** every path has a unique highest centroid on the decomposition. At centroid `c`, count paths that go through `c` with total length `k`, then recurse on each forest component.

**At centroid `c`:**

1. Paths with one end in a subtree and the other in another (or `c` itself): maintain a frequency map/array of distances from `c` already processed; for each new subtree, for each node at dist `d`, add `freq[k-d]`, then merge that subtree’s distances into `freq`.
2. Clear `freq` between different centroid calls (or use timestamps).

**Mental picture:** pin the middle of the path at the centroid; match “down `d`” with “down `k-d`” from different branches.

**Steps**

1. Build adjacency; find centroids recursively with subtree sizes and a `removed[]` mark.
2. Accumulate global answer `long long`.
3. Print answer.

## Walkthrough (on a sample)

`k=2` on the sample tree: paths of length 2 include e.g. `1-2-3`, `2-3-4`, `2-3-5`, `4-3-5` → **4**.

## Pitfalls

- Counting paths twice (ordered vs unordered) — add carefully.
- `freq` array size: distances only up to component size; allocate `n+1` with timestamps.
- Forgetting paths that have the centroid as an endpoint (`d=0` and `d=k`).

## Complexity

- Time: `O(n log n)`  
- Space: `O(n)`

## Key takeaway

**Exact length-`k` tree paths = centroid decomposition + depth-frequency convolution** at each centroid.
