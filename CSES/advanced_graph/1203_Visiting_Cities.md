# [1203 — Visiting Cities](https://cses.fi/problemset/task/1203)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** shortest paths, DAG of SP, path counting  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You want to travel from Syrjälä to Lehmälä by plane using a minimum-price route. Which cities will you certainly visit?

### Input

The first input line contains two integers `n` and `m`: the number of cities and the number of flights. The cities are numbered `1,2,...,n`. City `1` is Syrjälä, and city `n` is Lehmälä.

After this, there are `m` lines describing the flights. Each line has three integers `a`, `b`, and `c`: there is a flight from city `a` to city `b` with price `c`. All flights are one-way flights.

You may assume that there is a route from Syrjälä to Lehmälä.

### Output

First print an integer `k`: the number of cities that are certainly in the route. After this, print the `k` cities sorted in increasing order.

### Constraints

- `1 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a,b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
5 6
1 2 3
1 3 4
2 3 1
2 4 5
3 4 1
4 5 8
```

**Output**
```
4
1 3 4 5
```

---

## What this problem really is

Among all **shortest** `1 → n` paths (by total price), which vertices lie on every such path?

## What to use

| Tool | Why |
|------|-----|
| Dijkstra from `1` | Distances `dist[v]` on minimum-price routes |
| Shortest-path DAG | Keep only edges `u→v` with `dist[u] + w(u,v) = dist[v]` |
| Path counts mod 2 primes | `v` is unavoidable iff `#SP through v = #SP total` |

## Approach

**Root idea:** a city `v` is certain iff every minimum-price route visits `v`, iff (number of shortest paths `1↝v`) × (number of shortest paths `v↝n`) equals (number of shortest paths `1↝n`).

**Mental picture:** the shortest-path DAG is a layered funnel from `1` to `n`. A node is mandatory when it is a cut vertex of that DAG for paths from source to sink — equivalently when it lies on all counted shortest paths.

**How that becomes the method**

1. Dijkstra from `1` → `dist[1..n]` (`long long`).
2. Build SP-DAG: for each edge `u→v` with weight `w`, if `dist[u] + w == dist[v]`, add DAG edge `u→v` (and reverse for backward DP).
3. Count shortest paths on the DAG:
   - `waysFwd[1] = 1`; propagate in topo / dist order: `waysFwd[v] += waysFwd[u]` for DAG edges `u→v`.
   - `waysBwd[n] = 1`; propagate backward on reversed DAG.
4. Let `total = waysFwd[n]`. For each `v` with `dist[v] < INF`, if `waysFwd[v] * waysBwd[v] == total` (under modular arithmetic — use **two** large primes / moduli to avoid false collisions), mark `v`.
5. Print marked cities sorted (always includes `1` and `n`).

**Data structures:** adj with weights; Dijkstra PQ; DAG adj + reverse; `waysFwd/Bwd` arrays under two mods.

## Walkthrough (on a sample)

Shortest cost `1→n` is `4+1+1+8=14` via `1-3-4-5` (also check `1-2-3-4-5` = `3+1+1+8=13` — actually `13`). Cities on all shortest routes: sample output says `1,3,4,5` (city `2` is optional / not on every min route depending on ties). The count test keeps exactly the unavoidable ones.

## Pitfalls

- Comparing only one Dijkstra tree path (misses alternate shortest routes).
- 64-bit overflow in distances; modular overflow / using a single mod for path counts (collision → wrong YES).
- Including vertices unreachable on any shortest path (`waysFwd=0`).

## Complexity

- Time: `O((n + m) log n)` for Dijkstra + `O(n + m)` DP
- Space: `O(n + m)`

## Key takeaway

Certainly-visited on all shortest `1↝n` paths ↔ `ways(1→v) * ways(v→n) = ways(1→n)` on the shortest-path DAG.
