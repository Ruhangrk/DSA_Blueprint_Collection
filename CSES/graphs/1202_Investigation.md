# [1202 — Investigation](https://cses.fi/problemset/task/1202)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Dijkstra, multi-criteria on shortest paths  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are going to travel from Syrjälä to Lehmälä by plane. You would like to find answers to the following questions:

- what is the minimum price of such a route?
- how many minimum-price routes are there? (modulo `1e9+7`)
- what is the minimum number of flights in a minimum-price route?
- what is the maximum number of flights in a minimum-price route?

### Input

The first input line contains two integers `n` and `m`: the number of cities and the number of flights. The cities are numbered `1, 2, ..., n`. City `1` is Syrjälä, and city `n` is Lehmälä.

After this, there are `m` lines describing the flights. Each line has three integers `a`, `b`, and `c`: there is a flight from city `a` to city `b` with price `c`. All flights are one-way flights.

You may assume that there is a route from Syrjälä to Lehmälä.

### Output

Print four integers according to the problem statement.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
4 5
1 4 5
1 2 4
2 4 5
1 3 2
3 4 3
```

**Output**
```
5 2 1 2
```

---

## What this problem really is

Directed non-negative prices. Among all **cheapest** `1→n` routes, report: min price, count (mod `1e9+7`), min flight count, max flight count.

## What to use

| Tool | Why |
|------|-----|
| Dijkstra on price | Primary objective is min cost |
| Extra arrays at each node | Track count / min flights / max flights **among** ways that achieve `dist[u]` |
| `long long` + modulo | Prices and counts |

## Approach

**Root idea:** run Dijkstra for min price, but when you relax an edge, also update secondary stats for routes that stay optimal on price.

**Mental picture:** every city remembers “best price so far,” and among only those best-price arrivals: how many ways, shortest hop-length, longest hop-length.

**Data structures**

- `dist[u]`, `ways[u]`, `mnfl[u]`, `mxfl[u]`
- Init: `dist[1]=0`, `ways[1]=1`, `mnfl[1]=mxfl[1]=0`; others `INF` / `0`
- Min-heap `{price, node}`

**Relax `u → v` with cost `w`** (`nd = dist[u] + w`, `nfl = mnfl`/`mxfl` from `u` + 1):

1. If `nd < dist[v]`: set new best price; `ways[v]=ways[u]`; `mnfl[v]=mxfl[v]=mnfl[u]+1` (and same for max from `mxfl[u]+1`); push heap.
2. Else if `nd == dist[v]`: `ways[v] = (ways[v]+ways[u]) % MOD`; `mnfl[v] = min(mnfl[v], mnfl[u]+1)`; `mxfl[v] = max(mxfl[v], mxfl[u]+1)`.

**Answer:** `dist[n]`, `ways[n]`, `mnfl[n]`, `mxfl[n]`.

## Walkthrough (on a sample)

Min price `5` via `1→4` (1 flight) and `1→3→4` (2 flights). Count `2`, min flights `1`, max flights `2` → **`5 2 1 2`**.

## Pitfalls

- Updating flight stats on **non-optimal** price routes — only when `nd <= dist` as above.
- Mixing “number of cities” vs “number of flights” — output wants flights (edges).
- Stale Dijkstra pops — ignore if popped price `> dist[u]`.

## Complexity

- Time: `O((n + m) log n)`  
- Space: `O(n + m)`

## Key takeaway

**Layer secondary metrics on Dijkstra.** When a new price wins, reset count/min/max hops; when price ties, merge them.
