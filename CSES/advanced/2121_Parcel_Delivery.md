# [2121 — Parcel Delivery](https://cses.fi/problemset/task/2121)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Techniques  
**Tags:** min-cost max-flow, successive shortest paths  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` routes through which parcels can be carried from one city to another city. For each route, you know the maximum number of parcels and the cost of a single parcel.

You want to send `k` parcels from Syrjälä to Lehmälä. What is the cheapest way to do that?

### Input

The first input line has three integers `n`, `m` and `k`: the number of cities, routes and parcels. The cities are numbered `1,2,...,n`. City `1` is Syrjälä and city `n` is Lehmälä.

After this, there are `m` lines that describe the routes. Each line has four integers `a`, `b`, `r` and `c`: there is a route from city `a` to city `b`, at most `r` parcels can be carried through the route, and the cost of each parcel is `c`.

### Output

Print one integer: the minimum total cost or `-1` if there are no solutions.

### Constraints

- `2 <= n <= 500`
- `1 <= m <= 1000`
- `1 <= k <= 100`
- `1 <= a, b <= n`
- `1 <= r, c <= 1000`

### Examples

**Input**
```
4 5 3
1 2 5 100
1 3 10 50
1 4 7 500
2 4 8 350
3 4 2 100
```

**Output**
```
750
```

Explanation: One parcel is delivered through route `1 → 2 → 4` (cost `1*450=450`) and two parcels are delivered through route `1 → 3 → 4` (cost `2*150=300`).

---

## What this problem really is

Send exactly `k` units of flow from `1` to `n` on a digraph with capacities and per-unit costs; minimize total cost (or impossible).

## What to use

| Tool | Why |
|------|-----|
| Min-cost max-flow | Classic MCMF on residual graph |
| Successive shortest paths | `k <= 100` → at most `k` augmentations |
| Potentials / Dijkstra | Nonnegative reduced costs after Bellman-Ford init |

## Approach

**Root idea:** each parcel is one unit of flow. Capacity `r` limits how many parcels share an arc; each unit pays `c`. Cheapest way to push `k` units = min-cost flow of value `k`.

**Mental picture:** pipes with tolls and thickness limits. Push `k` tokens from city `1` to `n`, always along a currently cheapest residual path, paying tolls as you go.

**How that becomes the method**

1. Build flow network: for each route, forward edge capacity `r`, cost `c`; residual backward edge cost `-c`.
2. Repeat up to `k` times: find shortest path from `1` to `n` in residual graph by cost (SPFA or Dijkstra with potentials).
3. If none, print `-1`. Else augment `1` unit (or `min(remaining, bottleneck)` — since `k` small, unit augments are fine), add `path_cost * pushed` to answer.
4. When `k` units sent, print total cost.

**Data structures:** adjacency of residual edges `{to, rev, cap, cost}`; distance / parent arrays; potential `pi[]`.

## Walkthrough (on a sample)

Push along cheap `1-3-4` twice (cap `2` on last hop) and once along `1-2-4`; total `750`.

## Pitfalls

- Max flow without costs.  
- Not handling residual reverse edges.  
- Negative cycles (should not exist if no negative cost cycles in input — costs are positive here).  
- Stopping at max flow `< k` without printing `-1`.

## Complexity

- Time: roughly `O(k * (n+m) log n)` with Dijkstra+potentials  
- Space: `O(n + m)`

## Key takeaway

**Cheapest way to send `k` parcels = min-cost flow of value `k`** from `1` to `n`.
