# [1671 — Shortest Routes I](https://cses.fi/problemset/task/1671)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Dijkstra, shortest paths  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` flight connections between them. Your task is to determine the length of the shortest route from Syrjälä to every city.

### Input

The first input line has two integers `n` and `m`: the number of cities and flight connections. The cities are numbered `1, 2, ..., n`, and city `1` is Syrjälä.

After that, there are `m` lines describing the flight connections. Each line has three integers `a`, `b` and `c`: a flight begins at city `a`, ends at city `b`, and its length is `c`. Each flight is a one-way flight.

You can assume that it is possible to travel from Syrjälä to all other cities.

### Output

Print `n` integers: the shortest route lengths from Syrjälä to cities `1, 2, ..., n`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
3 4
1 2 6
1 3 2
3 2 3
1 3 4
```

**Output**
```
0 5 2
```

---

## What this problem really is

Directed graph, non-negative weights. Single-source shortest paths from city `1` to all nodes.

## What to use

| Tool | Why |
|------|-----|
| Adjacency list `g[a].push_back({b,c})` | Sparse digraph |
| `dist[n+1]` of `long long` | Path sums can exceed `int` |
| Dijkstra + min-heap | Non-negative `c`; `n, m` large |

## Approach

**Root idea:** repeatedly settle the unsettled node with smallest tentative distance; relax its outgoing flights. With non-negative weights this is optimal (Dijkstra).

**Mental picture:** expand a “confirmed distance” frontier from Syrjälä, always growing via the currently cheapest unconfirmed city.

**Data structures**

- `vector<vector<pair<int,int>>> g`
- `dist` init `INF`, `dist[1] = 0`
- `priority_queue` min-heap of `{distance, node}`

**Steps**

1. Build directed graph.
2. Push `{0, 1}`.
3. While heap not empty: pop `d, u`; if `d > dist[u]` continue (stale); for each edge `u→v` with cost `w`, if `dist[u]+w < dist[v]`, update and push.
4. Print `dist[1] .. dist[n]`.

## Walkthrough (on a sample)

`1→3` cost `2`, `3→2` cost `3` → path to `2` costs `5`, better than direct `1→2` cost `6`. Distances: **`0 5 2`**.

## Pitfalls

- **`long long`** — missing it overflows.
- **Stale heap entries** — always check `d > dist[u]`.
- Bellman-Ford is too slow for these limits; Floyd impossible at `n=1e5`.
- Multiple edges — Dijkstra handles them via relaxations.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n + m)`

## Key takeaway

**Non-negative weighted digraph, one source → Dijkstra.** `dist[]` + min-heap + stale-skip; print all distances.
