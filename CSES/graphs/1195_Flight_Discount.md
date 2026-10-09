# [1195 — Flight Discount](https://cses.fi/problemset/task/1195)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, shortest paths, Dijkstra, state graph  
**Difficulty / Rating:** Advanced (shortest path with extra state)  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to find a minimum-price flight route from Syrjälä to Metsälä. You have one discount coupon, using which you can halve the price of any single flight during the route. However, you can only use the coupon once.

When you use the discount coupon for a flight whose price is `x`, its price becomes `floor(x / 2)` (it is rounded down to an integer).

### Input

The first input line has two integers `n` and `m`: the number of cities and flight connections. The cities are numbered `1, 2, ..., n`. City `1` is Syrjälä, and city `n` is Metsälä.

After this there are `m` lines describing the flights. Each line has three integers `a`, `b`, and `c`: a flight begins at city `a`, ends at city `b`, and its price is `c`. Each flight is unidirectional.

You can assume that it is always possible to get from Syrjälä to Metsälä.

### Output

Print one integer: the price of the cheapest route from Syrjälä to Metsälä.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
3 4
1 2 3
2 3 1
1 3 7
2 1 5
```

**Output**
```
2
```

---

## What this problem really is

Directed weighted graph. Find the cheapest path from `1` to `n`, where on the path you may **once** replace one edge cost `c` with `floor(c / 2)`.

## What to use

| Tool | Why |
|------|-----|
| Adjacency list `vector<vector<pair<int,int>>> g` | Store each directed flight as `g[a].push_back({b, c})` — `n, m` are large |
| `dist[n+1][2]` of `long long` | `dist[u][used]` = cheapest way to reach city `u` with coupon status `used` (`0` unused / `1` already used) |
| Min-heap PQ of `{cost, city, used}` | Dijkstra always expands the currently cheapest unfinished state |
| `INF` sentinel | Unreachable cells start as a huge value; never use as a real answer |

## Approach

**Root idea:** the coupon is one bit of memory. A “position” is not only the city, but **`(city, used)`**. Ordinary Dijkstra on this expanded state space finds the cheapest route.

**Mental picture — two layers**

```
Layer used=0 (coupon still free)     Layer used=1 (coupon spent)

   (1,0) ----full c----> (v,0)         (u,1) ----full c----> (v,1)
     |
     +---- half c ----> (v,1)
```

From `(u, 0)` every flight can be taken **full** (stay in layer 0) or **halved** (jump to layer 1).  
From `(u, 1)` only **full** flights exist — the coupon is gone.

**Data structures (how they work together)**

1. **Graph:** adjacency list of outgoing flights `(to, cost)`.
2. **Distances:** 2D array `dist[city][used]`. Init all to `INF`; set `dist[1][0] = 0`.
3. **Priority queue:** min-heap of triples `(cost, city, used)`. Push `(0, 1, 0)` to start.  
   While the heap is not empty, pop the smallest `cost`. If `cost > dist[city][used]`, skip (stale entry). Otherwise relax neighbors:
   - Always: try full edge → state `(to, used)` with `cost + c`.
   - If `used == 0`: also try discount → state `(to, 1)` with `cost + c/2` (integer division).
   - If the new cost is strictly smaller than `dist[next][nextUsed]`, update and push.

**Answer:** `min(dist[n][0], dist[n][1])` — you may finish without spending the coupon.

**Steps**

1. Build `g` from the `m` flights.
2. Init `dist`, push start state into the PQ.
3. Run Dijkstra relaxations as above.
4. Print the min of the two distances at city `n`.

## Walkthrough (on a sample)

`n = 3`, flights:

| from | to | c |
|------|----|---|
| 1 | 2 | 3 |
| 2 | 3 | 1 |
| 1 | 3 | 7 |
| 2 | 1 | 5 |

Init: `dist[1][0] = 0`, everything else `INF`. PQ = `{(0, 1, 0)}`.

| Pop | Action | Update |
|-----|--------|--------|
| `(0, 1, 0)` | full `1→2` cost `3` | `dist[2][0] = 3`, push `(3, 2, 0)` |
| | discount `1→2` cost `1` | `dist[2][1] = 1`, push `(1, 2, 1)` |
| | full `1→3` cost `7` | `dist[3][0] = 7`, push `(7, 3, 0)` |
| | discount `1→3` cost `3` | `dist[3][1] = 3`, push `(3, 3, 1)` |
| `(1, 2, 1)` | coupon already used; full `2→3` cost `1+1=2` | `dist[3][1] = 2` (better than `3`), push `(2, 3, 1)` |
| | full `2→1` | `dist[1][1] = 6` (not needed for answer) |
| `(2, 3, 1)` | at destination with cost `2` | done expanding useful path |
| `(3, 2, 0)` | still have coupon; e.g. discount `2→3` → cost `3+0=3` | worse than `2`, no improve |
| … | remaining pops are stale or worse | — |

Final: `dist[3][0] = 7`, `dist[3][1] = 2` → answer **`2`**.

That `2` is exactly path `1 → 2 → 3` with the coupon on the edge of cost `3`: `floor(3/2) + 1 = 2`.

## Pitfalls

- **Discount only from `used == 0`** — never halve an edge after the coupon is spent.
- **Stale PQ entries** — always check `if (cost > dist[city][used]) continue` after pop.
- **`floor(c/2)`** — use integer `/`; no floats.
- **`long long` for `dist` and PQ cost** — sums of `1e9` edges overflow `int`.
- **Must take `min` of both layers at `n`** — best path might never use the coupon.
- **`n, m` huge** — adjacency list + Dijkstra only; no dense `n x n` matrix.

## Complexity

- Time: `O((n + m) log n)` — up to about `2` states per city, heap ops like normal Dijkstra  
- Space: `O(n + m)` for the graph, `O(n)` for `dist`

## Key takeaway

**Limited extra resource → bake it into the state.** Here: `(city, coupon_used)` + Dijkstra with an adjacency list, a `dist[][2]` table, and a min-heap of `(cost, city, used)`.
