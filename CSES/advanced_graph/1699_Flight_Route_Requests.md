# [1699 — Flight Route Requests](https://cses.fi/problemset/task/1699)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** digraphs, weakly connected components, cycles  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities with airports but no flight connections. You are given `m` requests which routes should be possible to travel.

Your task is to determine the minimum number of one-way flight connections which makes it possible to fulfil all requests.

### Input

The first input line has two integers `n` and `m`: the number of cities and requests. The cities are numbered `1,2,...,n`.

After this, there are `m` lines describing the requests. Each line has two integers `a` and `b`: there has to be a route from city `a` to city `b`. Each request is unique.

### Output

Print one integer: the minimum number of flight connections.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
4 5
1 2
2 3
2 4
3 1
3 4
```

**Output**
```
4
```

Explanation: You can create the connections `1 → 2`, `2 → 3`, `2 → 4` and `3 → 1`. Then you can also fly from city `3` to city `4` using the route `3 → 1 → 2 → 4`.

---

## What this problem really is

Start from an empty digraph on `n` nodes. Add the fewest directed edges so that for every request `(a,b)` there is a directed path `a ↝ b`.

## What to use

| Tool | Why |
|------|-----|
| Underlying undirected components | Requests in different weak components never interact |
| Directed-cycle check | A cycle forces mutual reachability ⇒ need a loop of edges |
| Per-component formula | DAG-like component → `V-1` edges; cyclic → `V` edges |

## Approach

**Root idea:** each weakly connected piece of the request graph can be satisfied by a sparse backbone of flights. If that piece is acyclic as a digraph, a tree-shaped set of `V-1` flights can realize all required reachability (routing through intermediate cities). If it contains a directed cycle, mutual reachability forces at least one extra edge → exactly `V` flights suffice and are necessary (e.g. a single cycle through all vertices, plus using it for shortcuts).

**Mental picture:** ignore cities with no requests. For each “blob” of cities tied together by requests (forgetting direction), lay down a cheap airline network: a chain/tree if requests never loop; a full cycle through the blob if they do.

**How that becomes the method**

1. Build the directed request graph `adj`, and also the undirected version for components.
2. For each undirected connected component with `V` vertices (skip `V=0` empties; isolated unused cities add `0`):
   - Check whether the **directed** graph inside the component contains a cycle (DFS colors / SCC / edge to visited-in-stack).
   - If yes: `ans += V`.
   - If no: `ans += V - 1` (if `V >= 1`; a single vertex with no loop request contributes `0`).
3. Print `ans`.

**Data structures:** `adj` directed; undirected adj or union-find for weak components; `vis` for cycle DFS.

## Walkthrough (on a sample)

All four cities sit in one undirected component. Requests include the cycle `1→2→3→1` → directed cycle exists → answer `4`, matching the sample construction (3-cycle plus an edge toward `4`).

## Pitfalls

- Treating this as “add edges to make the whole graph strongly connected” (`max(#sources,#sinks)` on the SCC DAG) — different problem; overshoots or undershoots required reachability.
- Counting isolated cities with no incident requests as needing edges.
- Using only SCCs without the weak-component `V`/`V-1` split.

## Complexity

- Time: `O(n + m)`
- Space: `O(n + m)`

## Key takeaway

Min flights for request reachability = per undirected request-component, `V` if the digraph has a cycle, else `V-1`.
