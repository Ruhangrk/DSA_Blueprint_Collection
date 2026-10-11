# [3408 — MST Edge Set Check](https://cses.fi/problemset/task/3408)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** MST, Kruskal, DSU rollback  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an undirected weighted graph and edge sets, determine for each set if the edges can be included in a minimum spanning tree.

### Input

The first line has three integers `n`, `m` and `q`: the number of nodes, edges and edge sets. The nodes are numbered `1,2,...,n`.

The following `m` lines describe the edges. Each line has three integers `a`, `b`, `w`: there is an edge between nodes `a` and `b` with weight `w`. The edges are numbered `1,2,...,m` in the input order.

The following `2q` lines describe the edge sets. For each set, the first line contains its size and the second line contains its edges. The total number of edges in all sets is at most `m`.

You can assume that the graph is connected and simple and each edge appears at most once in the graph.

### Output

For each edge set, print `YES` if the edges can be included in the minimum spanning tree and `NO` otherwise.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m, q <= 2e5`
- `1 <= a,b <= n`
- `1 <= w <= 1e9`

### Examples

**Input**
```
5 6 4
1 2 4
1 3 2
2 4 2
3 4 1
3 5 3
4 5 3
3
2 3 4
1
1
2
2 6
2
5 6
```

**Output**
```
YES
NO
YES
NO
```

---

## What this problem really is

For each query set `S` of edges, decide whether there exists one MST that contains **all** edges of `S` at once (not each edge separately).

## What to use

| Tool | Why |
|------|-----|
| Kruskal by increasing weight | MST-safety is decided against cheaper edges only |
| Same-weight batches | Within one weight, `S`’s edges must form a forest on the DSU |
| DSU with rollback | Test a query’s unions, then undo before permanent Kruskal merges |

## Approach

**Root idea:** `S` fits in some MST iff, for every weight `w`, when only edges lighter than `w` are already united, the edges of `S` with weight `w` connect different components and do not form a cycle among themselves.

**Mental picture:** grow the MST forest by weight. At weight `w`, the query temporarily “claims” some bridges. If two claimed edges want the same cut, or one is already useless, the set is over-greedy → `NO`. Then discard the claim and let Kruskal add the real edges of weight `w`.

**How that becomes the method**

1. Index edges `1..m` with weights; bucket / sort by `w`.
2. Group each query’s edge ids by weight.
3. Process weights ascending with a rollback DSU:
   - For each query that has edges of this weight: unite them one by one; if any edge has `find(u)==find(v)` before unite → mark that query `NO`. Rollback all trial unions.
   - Then permanently unite all graph edges of this weight that bridge components (normal Kruskal).
4. Queries never marked `NO` are `YES`.

**Data structures:** edge list; per-weight lists of (queryId, edgeId); DSU with undo stack (`parent`, `rank`/`size` changes); `ok[q]` flags.

## Walkthrough (on a sample)

Graph as in MST Edge Check. Sets:

- `{2,3,4}` = edges weights `2,2,1` — all MST-compatible together → `YES`.
- `{1}` = edge weight `4` (`1-2`) — never in any MST → `NO`.
- `{2,6}` = weight `2` and `3` — can coexist → `YES`.
- `{5,6}` = both weight `3` into the same cut toward `5` — cannot both be in one MST → `NO`.

## Pitfalls

- Checking edges of `S` independently (MST Edge Check) — too weak for sets.
- Permanently uniting while testing a query (need rollback).
- Forgetting that conflict can be within `S` at the same weight even if each edge alone is OK.

## Complexity

- Time: `O((n + m + total_set_edges) α(n) + m log m)` with sorting / bucketing
- Space: `O(n + m + total_set_edges)`

## Key takeaway

A whole set sits in some MST iff at every weight it forms a forest on Kruskal’s DSU — test with rollback, then commit the real edges.
