# [2101 — New Roads Queries](https://cses.fi/problemset/task/2101)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** DSU, Kruskal reconstruction tree, binary lifting  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities in Byteland but no roads between them. However, each day, a new road will be built. There will be a total of `m` roads.

Your task is to process `q` queries of the form: "after how many days can we travel from city `a` to city `b` for the first time?"

### Input

The first input line has three integers `n`, `m` and `q`: the number of cities, roads and queries. The cities are numbered `1,2,...,n`.

After this, there are `m` lines that describe the roads in the order they are built. Each line has two integers `a` and `b`: there will be a road between cities `a` and `b`.

Finally, there are `q` lines that describe the queries. Each line has two integers `a` and `b`: we want to travel from city `a` to city `b`.

### Output

For each query, print the number of days, or `-1` if it is never possible.

### Constraints

- `1 <= n, m, q <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 4 3
1 2
2 3
1 3
2 5
1 3
3 4
3 5
```

**Output**
```
2
-1
4
```

---

## What this problem really is

Edges appear in order (day = index). For each query `(a,b)`, find the earliest time their components merge — or `-1` if they never connect.

## What to use

| Tool | Why |
|------|-----|
| DSU while adding edges | Detect the exact day two cities first share a component |
| Offline + Kruskal tree | Build a tree of merges; answer = max edge time on path / LCA of merge nodes |
| Binary lifting on rebuild tree | Fast path / LCA queries |

## Approach

**Root idea:** connectivity only changes when an edge joins two different DSU components. That merge day is the first time every pair across those components becomes connected. Encode merges as a binary tree (Kruskal reconstruction tree): leaves are cities; an internal node created on day `t` means “components joined at time `t`”. Then the first connection time of `a` and `b` is the time label of `LCA(a,b)` in that tree.

**Mental picture:** cities are islands; each new bridge may glue two islands. The moment your two cities sit on the same island is the answer. The merge tree remembers every gluing event.

**How that becomes the method**

1. Process roads in order with DSU. When `find(a)!=find(b)`, create a new tree node with `time = day`, children = the two component roots’ current tree roots; link both into the new node.
2. After all edges, each original city has a leaf; climb to the global roots of rebuild trees (forest if not fully connected).
3. For query `(a,b)`: if different final components → `-1`; else `time[LCA(a,b)]` (with binary lifting / Euler+RMQ).
4. Same city query → `0` days? Statement asks travel between cities — usually `0` if `a==b`; check convention (often print `0`).

Simpler offline alternative: binary search the day + DSU rollback / parallel binary search — same complexity class.

**Data structures:** DSU; rebuild tree parents + `time[]`; `up[k][v]`, `depth[]` for LCA.

## Walkthrough (on a sample)

Day 1: `1-2`. Day 2: `2-3` merges `3` into `{1,2}` → query `1 3` answers `2`. `3-4` never connected → `-1`. Day 4: `2-5` connects `5` → `3 5` answers `4`.

## Pitfalls

- Answering with the day of a later redundant edge inside a component.  
- Forgetting `-1` when never connected.  
- 1-based day indices matching output.

## Complexity

- Time: `O((n + m + q) log n)`  
- Space: `O(n + m)`

## Key takeaway

**First connectivity day = merge time of LCA** on the Kruskal reconstruction tree (or parallel binary search + DSU).
