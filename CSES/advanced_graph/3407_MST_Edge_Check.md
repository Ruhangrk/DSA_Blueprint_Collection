# [3407 — MST Edge Check](https://cses.fi/problemset/task/3407)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** MST, Kruskal, DSU, spanning tree  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an undirected weighted graph, determine for each edge if it can be included in a minimum spanning tree.

### Input

The first line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

The following `m` lines describe the edges. Each line has three integers `a`, `b`, `w`: there is an edge between nodes `a` and `b` with weight `w`.

You can assume that the graph is connected and simple and each edge appears at most once in the graph.

### Output

For each edge in the input order, print `YES` if it can be included in the minimum spanning tree and `NO` otherwise.

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
3 5 3
4 5 3
```

**Output**
```
NO
YES
YES
YES
YES
YES
```

---

## What this problem really is

For every edge, decide whether there exists some MST that contains it (not whether it lies in every MST).

## What to use

| Tool | Why |
|------|-----|
| Kruskal by weight | An edge can enter some MST iff its ends are in different components when that weight is processed |
| Same-weight batches | Equal weights need “check all, then unite all” so ties can swap |
| DSU | Fast component queries / merges at `m <= 2e5` |

## Approach

**Root idea:** edge `e` of weight `w` is MST-safe iff it is a lightest edge across the cut between its endpoints’ components in the forest built from all cheaper edges — i.e. Kruskal would still be allowed to pick it.

**Mental picture:** grow the MST forest with increasing weights. When you reach weight `w`, every edge of weight `w` that still bridges two trees is interchangeable — each can appear in some MST. Edges that already sit inside one tree are strictly heavier than some connecting path and are useless (`NO`).

**How that becomes the method**

1. Index edges `0..m-1` with `(a,b,w)`.
2. Sort by `w` ascending (keep original indices).
3. Process in groups of equal `w`:
   - For each edge in the group: if `find(a) != find(b)`, mark `YES`, else `NO`.
   - Then unite all edges of the group that connect different components (standard Kruskal merges).
4. Print answers in input order.

**Equivalent view:** build one MST; a non-tree edge `e` is `YES` iff `w(e)` equals the maximum edge weight on the MST path between its ends (can swap). Tree edges of that MST are always `YES`. The Kruskal batch method avoids heavy path queries.

**Data structures:** edge struct with `id`; DSU; `string`/`bool` answer array size `m`.

## Walkthrough (on a sample)

Sorted weights: `1` (`3-4`), then `2` (`1-3`,`2-4`), then `3` (`3-5`,`4-5`), then `4` (`1-2`).

- `3-4` weight `1`: connects → `YES`, unite.
- Both weight-`2` edges connect different comps → `YES`, unite.
- Both weight-`3` edges still useful for linking `5` → `YES` (either can be in some MST).
- `1-2` weight `4`: `1` and `2` already connected through cheaper edges → `NO`.

## Pitfalls

- Uniting inside a same-weight group before checking siblings → wrongly mark some `NO`.
- Confusing “in all MSTs” (bridges of the MST) with “in some MST.”
- Output must follow **input** order, not sorted order.

## Complexity

- Time: `O(m log m)` 
- Space: `O(n + m)`

## Key takeaway

Edge is in some MST iff Kruskal still sees its endpoints disconnected when its weight’s turn arrives (check before uniting equal-weight peers).
