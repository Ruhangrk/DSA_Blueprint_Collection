# [2177 — Strongly Connected Edges](https://cses.fi/problemset/task/2177)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** bridges, DFS tree, strong connectivity, orientation  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an undirected graph, your task is to choose a direction for each edge so that the resulting directed graph is strongly connected.

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

After this, there are `m` lines describing the edges. Each line has two integers `a` and `b`: there is an edge between nodes `a` and `b`.

You may assume that the graph is simple, i.e., there is at most one edge between two nodes and every edge connects two distinct nodes.

### Output

Print `m` lines describing the directions of the edges. Each line has two integers `a` and `b`: there is an edge from node `a` to node `b`. You can print any valid solution.

If there are no solutions, only print `IMPOSSIBLE`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
3 3
1 2
1 3
2 3
```

**Output**
```
1 2
2 3
3 1
```

---

## What this problem really is

Orient edges of an undirected simple graph to make the digraph strongly connected — possible iff the graph is connected and bridgeless (2-edge-connected).

## What to use

| Tool | Why |
|------|-----|
| Bridge finding (`tin` / `low`) | A bridge can never lie on a directed cycle either way |
| Connectivity check | Strong digraph ⇒ underlying undirected must be connected |
| DFS orientation | Tree edges down, back edges up ⇒ one strong component when bridgeless |

## Approach

**Root idea:** if any bridge exists, whichever way you orient it, you cannot return across it — not strongly connected. If the graph is connected and has no bridges, a classic DFS orientation works.

**Mental picture:** bridges are single ropes between islands — directing a rope only lets you cross one way. No bridges means every edge sits on a cycle; you can circulate around those cycles.

**How that becomes the method**

1. Build undirected adjacency with edge indices (to ignore the parent edge cleanly).
2. DFS from node `1` (or any node): compute `tin`, `low`. An edge `u-v` is a bridge if `low[v] > tin[u]` (when `v` is a child of `u`).
3. If some node is unvisited → not connected → `IMPOSSIBLE`.
4. If any bridge exists → `IMPOSSIBLE`.
5. Otherwise orient during (or after) DFS:
   - spanning-tree edge parent→child: orient `parent -> child`;
   - back edge to an ancestor: orient `node -> ancestor`  
   (equivalently: orient every edge from the end with larger `tin` to the smaller, or follow the “tree down / back up” rule consistently).
6. Print the `m` directed edges.

**Data structures:** `adj` of `(to, edgeId)`; arrays `tin`, `low`, `vis`; list of undirected edges; output orientation per `edgeId`.

## Walkthrough (on a sample)

Triangle `1-2-3-1`: connected, no bridges.

One DFS from `1`: tree `1->2`, `2->3`, back edge `3->1` (or similar). Resulting cycle visits all nodes both ways around the orientation — strongly connected. Matches a valid sample output.

If you remove one edge, a path of two edges has bridges → `IMPOSSIBLE`.

## Pitfalls

- Connected but with a bridge → still impossible.
- Mis-detecting bridges when handling parent edges / multiple edges (statement forbids multis, still be careful with parent skip).
- Orienting randomly after bridge check — use the DFS rule so the whole graph becomes one SCC.

## Complexity

- Time: `O(n + m)`
- Space: `O(n + m)`

## Key takeaway

Strong orientation exists iff the undirected graph is 2-edge-connected; then orient DFS tree downward and back-edges upward.
