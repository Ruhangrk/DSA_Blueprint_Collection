# [1707 — Graph Girth](https://cses.fi/problemset/task/1707)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** BFS, shortest cycle, undirected graph  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an undirected graph, your task is to determine its girth, i.e., the length of its shortest cycle.

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

After this, there are `m` lines describing the edges. Each line has two integers `a` and `b`: there is an edge between nodes `a` and `b`.

You may assume that there is at most one edge between each two nodes.

### Output

Print one integer: the girth of the graph. If there are no cycles, print `-1`.

### Constraints

- `1 <= n <= 2500`
- `1 <= m <= 5000`

### Examples

**Input**
```
5 6
1 2
1 3
2 4
2 5
3 4
4 5
```

**Output**
```
3
```

---

## What this problem really is

Find the length of the shortest undirected cycle (or `-1` if the graph is a forest).

## What to use

| Tool | Why |
|------|-----|
| BFS from every node | Shortest cycle through a node shows up as the first time BFS hits an already visited non-parent |
| `n <= 2500` | `O(n(n+m))` is acceptable |

## Approach

**Root idea:** the shortest cycle has some vertex `s` on it; a BFS from `s` finds the shortest paths, and the first “cross” into a visited node closes the shortest cycle involving that BFS layer structure.

**Mental picture:** grow a distance onion from `s`. The moment two onions touch through a non-tree edge, you have a cycle of length `dist[u] + dist[v] + 1`.

**How that becomes the method**

1. `ans = INF`.
2. For each start `s = 1..n`:
   - BFS: `dist[s]=0`, parent `[s]=-1`.
   - When exploring edge `u-v`:
     - if `v` unvisited: set `dist[v]=dist[u]+1`, parent `v=u`.
     - else if `v != parent[u]`: cycle length `dist[u] + dist[v] + 1`; update `ans`.
   - Optional prune: stop early if `dist[u]` already makes shorter cycles impossible vs `ans`.
3. Print `ans` if finite, else `-1`.

**Data structures:** adj lists; `dist[n+1]`, `parent[n+1]` reused per BFS; queue.

## Walkthrough (on a sample)

Triangle `2-4-5` (and others). BFS from `2` soon sees a back/cross edge that closes length `3`. No digon (multiple edges forbidden), so girth `3`.

## Pitfalls

- Counting the parent edge as a cycle (always skip parent).
- Directed-graph mentality — this is undirected.
- `n=1` / no edges → `-1`.

## Complexity

- Time: `O(n(n + m))` ≈ fine for given limits
- Space: `O(n + m)`

## Key takeaway

Girth = min over BFS-from-each-vertex of the first non-parent revisit cycle length `dist[u]+dist[v]+1`.
