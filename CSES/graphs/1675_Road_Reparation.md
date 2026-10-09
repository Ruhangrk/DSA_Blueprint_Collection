# [1675 — Road Reparation](https://cses.fi/problemset/task/1675)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, MST, Kruskal, Union-Find  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 128 MB  

---

## Problem Statement

There are `n` cities and `m` roads between them. Unfortunately, the condition of the roads is so poor that they cannot be used. Your task is to repair some of the roads so that there will be a decent route between any two cities.

For each road, you know its reparation cost, and you should find a solution where the total cost is as small as possible.

### Input

The first input line has two integers `n` and `m`: the number of cities and roads. The cities are numbered `1, 2, ..., n`.

Then, there are `m` lines describing the roads. Each line has three integers `a`, `b` and `c`: there is a road between cities `a` and `b`, and its reparation cost is `c`. All roads are two-way roads.

Every road is between two different cities, and there is at most one road between two cities.

### Output

Print one integer: the minimum total reparation cost. However, if there are no solutions, print `"IMPOSSIBLE"`.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
5 6
1 2 3
2 3 5
2 4 2
3 4 8
5 1 7
5 4 4
```

**Output**
```
14
```

---

## What this problem really is

Undirected weighted graph: find a **Minimum Spanning Tree** (connect all cities with min total edge cost). If the graph isn’t connectable, `IMPOSSIBLE`.

## What to use

| Tool | Why |
|------|-----|
| Kruskal: sort edges by cost | Greedy MST |
| DSU (Union-Find) | Skip edges that join the same component |
| `long long` | Sum of costs |

## Approach

**Root idea:** always repair the cheapest road that links two still-separate components. Stop when you have `n-1` roads (a tree), or run out of edges.

**Mental picture:** cities as islands; repair cheapest bridges that merge islands until one island remains.

**Data structures**

- Edge list `{c, a, b}`, sort by `c`.
- DSU: `parent`, `rank`/`size`, `find`, `unite`.
- `long long ans`, `int taken`.

**Steps**

1. Read edges, sort ascending by cost.
2. For each edge, if `find(a) != find(b)`, unite, add `c` to `ans`, `taken++`.
3. If `taken == n-1`, print `ans`; else `IMPOSSIBLE`.

(Prim with a heap also works; Kruskal is natural here.)

## Walkthrough (on a sample)

Cheapest useful repairs build an MST of total **`14`** connecting all 5 cities (e.g. edges costs `2+3+4+5`).

## Pitfalls

- Graph disconnected → `IMPOSSIBLE` (not a huge fake sum).
- Overflow without `long long`.
- Uniting even when already connected — creates cycles and wrong cost (DSU check prevents this).

## Complexity

- Time: `O(m log m)` for sorting (+ almost `O(m α(n))` DSU)  
- Space: `O(n + m)`

## Key takeaway

**Min cost to connect all cities = MST.** Kruskal: sort edges, add if DSU says different components; need exactly `n-1` edges.
