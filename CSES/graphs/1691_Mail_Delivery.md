# [1691 — Mail Delivery](https://cses.fi/problemset/task/1691)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Eulerian circuit, Hierholzer  
**Difficulty / Rating:** Medium–Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to deliver mail to the inhabitants of a city. For this reason, you want to find a route whose starting and ending point are the post office, and that goes through every street exactly once.

### Input

The first input line has two integers `n` and `m`: the number of crossings and streets. The crossings are numbered `1, 2, ..., n`, and the post office is located at crossing `1`.

After that, there are `m` lines describing the streets. Each line has two integers `a` and `b`: there is a street between crossings `a` and `b`. All streets are two-way streets.

Every street is between two different crossings, and there is at most one street between two crossings.

### Output

Print all the crossings on the route in the order you will visit them. You can print any valid solution.

If there are no solutions, print `"IMPOSSIBLE"`.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
6 8
1 2
1 3
2 3
2 4
2 6
3 5
3 6
4 5
```

**Output**
```
1 2 6 3 2 4 5 3 1
```

---

## What this problem really is

Undirected multigraph (here simple): find an **Eulerian circuit** starting and ending at `1` (traverse every edge exactly once), or `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Degree parity check | Undirected Eulerian circuit ↔ all degrees even (+ connectivity of edges) |
| Hierholzer’s algorithm | Build the circuit in `O(m)` |
| Adjacency with edge ids / used flags | Undirected edges must be deleted both ways |

## Approach

**Root idea:** you can close a tour using every street exactly once iff every vertex has **even degree** and all edges lie in one connected component (ignoring isolated vertices with degree 0).

**Mental picture:** draw each street once and return to the post office — classic “envelope” tracing.

**Data structures**

- Undirected adj: store `{to, edge_id}`; `used[edge_id]`
- Stack for Hierholzer; vector for the circuit (nodes)

**Steps**

1. Build graph; compute degrees.
2. If any `deg[v]` is odd → `IMPOSSIBLE`.
3. Ensure all vertices with `deg>0` are in the same component as `1` (and `deg[1]>0` or `m==0` edge cases).
4. Hierholzer from `1`: while unused edges remain at current vertex, traverse unused edge, mark used, push; when stuck, append vertex to path and pop.
5. Reverse the constructed node list; verify it used `m` edges (`path` has `m+1` nodes).
6. Print the node sequence or `IMPOSSIBLE`.

## Walkthrough (on a sample)

All degrees even; Hierholzer from `1` yields a closed walk such as **`1 2 6 3 2 4 5 3 1`** covering every street once.

## Pitfalls

- Odd degree anywhere → impossible (not only at `1`).
- Forgetting connectivity of the edged part.
- Not marking undirected edges used in both directions.
- Printing vertices without checking `|edges used| == m`.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Tour every street once and return = undirected Eulerian circuit.** Even degrees + connectivity, then Hierholzer from the post office.
