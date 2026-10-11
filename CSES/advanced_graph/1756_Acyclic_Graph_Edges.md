# [1756 — Acyclic Graph Edges](https://cses.fi/problemset/task/1756)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** orientation, DAG, graphs  
**Difficulty / Rating:** Easy  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Given an undirected graph, your task is to choose a direction for each edge so that the resulting directed graph is acyclic.

### Input

The first input line has two integers `n` and `m`: the number of nodes and edges. The nodes are numbered `1,2,...,n`.

After this, there are `m` lines describing the edges. Each line has two distinct integers `a` and `b`: there is an edge between nodes `a` and `b`.

### Output

Print `m` lines describing the directions of the edges. Each line has two integers `a` and `b`: there is an edge from node `a` to node `b`. You can print any valid solution.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`

### Examples

**Input**
```
3 3
1 2
2 3
3 1
```

**Output**
```
1 2
3 2
3 1
```

---

## What this problem really is

Orient every undirected edge so the digraph has no directed cycles. Always possible for any undirected graph (even with cycles).

## What to use

| Tool | Why |
|------|-----|
| Label order | Direct each edge toward the larger endpoint → global potential `label` |
| DFS discovery time | Same idea with `tin[u]`; also yields a DAG |

## Approach

**Root idea:** pick a total order on vertices and orient every edge from earlier to later (or always smaller label → larger label). A directed cycle would need labels to both increase and return — impossible.

**Mental picture:** number the nodes on a line. Every arrow only points right. You can never loop back.

**How that becomes the method**

For each undirected edge `{a,b}`:

- print `min(a,b) max(a,b)`  
  i.e. orient `a -> b` if `a < b`, else `b -> a`.

That is a valid DAG. (Any other topological numbering works the same way.)

**Data structures:** none beyond reading edges; `O(1)` per edge.

## Walkthrough (on a sample)

Edges `{1,2}`, `{2,3}`, `{3,1}` become `1->2`, `2->3`, `1->3`. No directed cycle.

The sample’s printed orientation `1->2`, `3->2`, `3->1` is another valid DAG (sink at `2`); both are acceptable.

## Pitfalls

- Thinking undirected cycles make the task impossible — only *directed* cycles are forbidden, and you choose the directions.
- Orienting arbitrarily (e.g. input order) can create directed cycles; use a consistent order.

## Complexity

- Time: `O(n + m)`
- Space: `O(1)` extra (or `O(m)` if storing)

## Key takeaway

Any undirected graph has an acyclic orientation: point every edge along a fixed vertex order (e.g. increasing labels).
