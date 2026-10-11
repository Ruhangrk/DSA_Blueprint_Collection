# [3358 — Split into Two Paths](https://cses.fi/problemset/task/3358)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** DAG, path cover, matching  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an acyclic directed graph with `n` nodes and `m` edges.

Determine whether two paths can be formed in the graph such that each node of the graph appears in exactly one of the paths. Note that all edges of the graph do not need to appear in the paths.

### Input

The first line has two integers `n` and `m`: the number of nodes and the number of edges. The nodes are numbered `1, 2, ..., n`.

After this, there are `m` lines which describe the edges. Each line has two integers `a` and `b`: there is an edge in the graph from node `a` to node `b`.

### Output

First print the line `YES` if the paths can be formed, or `NO` otherwise.

If the paths can be formed, print them on the following two lines.

At the beginning of both lines, print the amount of nodes in the path and then the nodes of the path in order. There must be an edge in the graph between subsequent nodes. One of the paths may contain zero nodes.

If there exist multiple solutions, you can print any solution.

### Constraints

- `2 <= n <= 2e5`
- `0 <= m <= 5e5`

### Examples

**Example 1**

**Input**
```
5 4
1 2
1 4
3 4
4 5
```

**Output**
```
YES
2 1 2
3 3 4 5
```

**Example 2**

**Input**
```
5 4
1 2
1 3
1 4
1 5
```

**Output**
```
NO
```

---

## What this problem really is

Partition all vertices of a DAG into **at most two** directed paths (using a subset of the edges). Equivalent to a path cover of size ≤ 2 that covers every vertex.

## What to use

| Tool | Why |
|------|-----|
| Dilworth / bipartite matching view | Min path cover in a DAG = `n - max matching` in the split graph |
| Check `n - matching ≤ 2` | Need cover size ≤ 2 ⇒ matching ≥ `n-2` |
| Reconstruct paths from matching | Chosen edges chain into paths |

## Approach

**Root idea:** in a DAG, chain vertices with unused in/out “ports.” Each matching edge in the split bipartite graph (left copy = out, right copy = in) merges two vertices into the same path and reduces the path count by one. Starting from `n` singleton paths, after a maximum matching of size `μ` you need `n - μ` paths. We need `n - μ ≤ 2`.

**Mental picture:** each vertex is a segment. An edge `u→v` can glue the end of `u`’s segment to the start of `v`’s if both ends are free. You may glue at most twice short of a single path — i.e. leave at most two chains.

**How that becomes the method**

1. Build the DAG; split each vertex `v` into `vL` and `vR`.
2. For every original edge `u → v`, add bipartite edge `uL → vR`.
3. Compute maximum matching (`Hopcroft–Karp` / DFS matching — with `n=2e5` need an efficient matching; often the graph structure allows greedy after topo if degrees are constrained — use Hopcroft–Karp carefully or Dinic on unit network).
4. If `n - matchingSize > 2` → `NO`.
5. Else: matching edges define `next[u]=v` links. Follow chains from vertices with no matched in-edge to build the paths (there will be `n-μ ≤ 2` paths; if fewer than 2, print an empty path).

**Necessary local checks (fast reject):** at most 2 vertices with indegree `0` in the used path graph; each vertex in-/out-degree in the chosen path edges is ≤ 1. The matching enforces this globally.

**Data structures:** adj; bipartite matching (`pairU`, `pairV`); `next[]` for reconstruction.

## Walkthrough (on a sample)

Ex1: can match enough edges to form paths `1→2` and `3→4→5` → cover size 2 → `YES`.

Ex2: star out of `1` to four sinks — need four paths after using at most the edges from `1` (only one out-slot on `1`) → `NO`.

## Pitfalls

- Trying to use all edges (Eulerian) — only a subset is needed.
- Allowing a vertex on both paths.
- `O(nm)` matching TLE — use Hopcroft–Karp / Dinic; sum of edges `5e5`.

## Complexity

- Time: Hopcroft–Karp `O(E √V)` roughly on the split graph
- Space: `O(n + m)`

## Key takeaway

Partition DAG vertices into ≤2 paths ↔ min path cover ≤ 2 ↔ bipartite matching size ≥ `n-2`, then reconstruct chains.
