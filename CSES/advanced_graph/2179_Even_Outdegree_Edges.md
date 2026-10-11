# [2179 — Even Outdegree Edges](https://cses.fi/problemset/task/2179)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** orientation, DFS tree, parity  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  
**Editorial:** [USACO Guide](https://usaco.guide/problems/cses-2179-even-outdegree-edges/solution)

---

## Problem Statement

Given an undirected graph, your task is to choose a direction for each edge so that in the resulting directed graph each node has an even outdegree. The outdegree of a node is the number of edges coming out of that node.

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

After this, there are `m` lines describing the edges. Each line has two integers `a` and `b`: there is an edge between nodes `a` and `b`.

You may assume that the graph is simple, i.e., there is at most one edge between any two nodes and every edge connects two distinct nodes.

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
4 4
1 2
2 3
3 4
1 4
```

**Output**
```
1 2
3 2
3 4
1 4
```

---

## What this problem really is

Orient edges so every outdegree is even. Sum of outdegrees equals `m`, so a necessary condition is `m` even (globally — and per component after the construction’s accounting).

## What to use

| Tool | Why |
|------|-----|
| DFS spanning tree | Non-tree edges are back-edges; orient them upward first |
| Bottom-up parity fix | Orient each tree edge to fix the child’s outdegree parity |
| Component root check | If a root is left with odd outdegree → impossible |

## Approach

**Root idea:** force all back-edges to point up the DFS tree (they contribute to the lower node’s outdegree). Then each tree edge is the only free choice left to fix a child’s parity.

**Mental picture:** after back-edges are fixed, every subtree root has a “parity debt.” The edge to its parent is a switch: point it up or down to clear the child’s debt. Debts bubble to the component root; the root has no parent switch, so its final parity must already be even.

**How that becomes the method**

1. For each connected component, DFS to build a spanning tree; mark tree vs back edges.
2. Orient every back-edge toward the ancestor (lower → higher in the tree, or child → ancestor).
3. Post-order on the tree: for node `v` with parent `p`, after processing children, if `outdeg[v]` is odd, orient `v -> p` (fix `v`); else orient `p -> v`.
4. After fixing, if the component root has odd outdegree → `IMPOSSIBLE`.
5. Print all directed edges (any order).

**Why the root works when `m` is compatible:** sum of outdegrees = `m`. All non-roots even ⇒ root even automatically in a single tree with the right global parity; disconnected components each need their own check.

**Data structures:** adjacency with edge ids; `vis`, parent / tree flags; `outdeg[]`; answer list of directed pairs.

## Walkthrough (on a sample)

Cycle `1-2-3-4-1`. One DFS tree might be `1-2-3-4` with back-edge `4-1`.

- Orient back-edge `4 -> 1`.
- Fix tree edges bottom-up so every outdegree is even → one valid orientation is the sample (`1->2`, `3->2`, `3->4`, `1->4`).

## Pitfalls

- Forgetting multi-component handling (each root checked).
- Orienting back-edges arbitrarily.
- Odd number of edges in a component can force `IMPOSSIBLE`.

## Complexity

- Time: `O(n + m)`
- Space: `O(n + m)`

## Key takeaway

Even outdegrees → orient DFS back-edges up, then flip each tree edge to clear child parity; root must end even.
