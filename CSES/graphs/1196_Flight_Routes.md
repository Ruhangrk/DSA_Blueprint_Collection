# [1196 — Flight Routes](https://cses.fi/problemset/task/1196)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Dijkstra, k-shortest paths  
**Difficulty / Rating:** Advanced  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Your task is to find the `k` shortest flight routes from Syrjälä to Metsälä. A route can visit the same city several times.

Note that there can be several routes with the same price and each of them should be considered (see the example).

### Input

The first input line has three integers `n`, `m`, and `k`: the number of cities, the number of flights, and the parameter `k`. The cities are numbered `1, 2, ..., n`. City `1` is Syrjälä, and city `n` is Metsälä.

After this, the input has `m` lines describing the flights. Each line has three integers `a`, `b`, and `c`: a flight begins at city `a`, ends at city `b`, and its price is `c`. All flights are one-way flights.

You may assume that there are at least `k` distinct routes from Syrjälä to Metsälä.

### Output

Print `k` integers: the prices of the `k` cheapest routes sorted according to their prices.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`
- `1 <= k <= 10`

### Examples

**Input**
```
4 6 3
1 2 1
1 3 3
2 3 2
2 4 6
3 2 8
3 4 1
```

**Output**
```
4 4 7
```

### Note

The cheapest routes are `1 → 3 → 4` (price `4`), `1 → 2 → 3 → 4` (price `4`) and `1 → 2 → 4` (price `7`).

---

## What this problem really is

Directed non-negative weighted graph. Find the `k` smallest path **costs** from `1` to `n` (paths may revisit cities; equal costs count separately as different routes). `k <= 10`.

## What to use

| Tool | Why |
|------|-----|
| Dijkstra-like best-first search | Non-negative weights |
| `vector<long long> best[n+1]` size up to `k` | Store up to `k` best distances found to each node |
| Min-heap `{cost, node}` | Expand partial routes in order of cost |

## Approach

**Root idea:** ordinary Dijkstra keeps **one** best distance per node. Here keep up to **`k`** best distances per node. Push a new arrival into a node only if it is strictly among the `k` best (or you have fewer than `k` so far).

**Mental picture:** you explore route prices in increasing order. Each city remembers its `k` cheapest ways to get there; when a better (or additional) way arrives, you continue flying from there.

**Data structures**

- Adj list of directed flights.
- `best[u]`: sorted multiset / vector of accepted distances to `u` (at most `k`).
- Priority queue of partial paths.

**Steps**

1. Push `{0, 1}`; record `0` as a distance to `1`.
2. While PQ not empty:
   - Pop `{d, u}`. Skip if this `d` is already worse than the `k`-th best at `u` (implementation-dependent stale check).
   - For each flight `u→v` with cost `w`, let `nd = d + w`.
   - If `best[v]` has fewer than `k` values, or `nd` is smaller than the current worst in `best[v]`, accept `nd`, maintain only `k` best, push `{nd, v}`.
3. When finished, `best[n]` holds (at least) `k` distances — print them sorted ascending.

Because `k` is tiny, accepting up to `k` arrivals per node keeps the search manageable.

## Walkthrough (on a sample)

Three cheapest `1→n` costs: **`4, 4, 7`** as in the note (two different routes share price `4`).

The algorithm finds multiple entries at `n` rather than collapsing them into one distance.

## Pitfalls

- Keeping only **one** `dist[u]` — wrong; need `k` per node.
- Treating equal costs as one route — sample needs both `4`s.
- `long long` for costs.
- `k` routes guaranteed to exist — you shouldn’t output fewer than `k`.

## Complexity

- Time: roughly `O(k * (n + m) log (k n))` order with heap  
- Space: `O(k n + m)`

## Key takeaway

**`k` shortest path costs:** Dijkstra where each node stores up to `k` best distances, not one. Push onward whenever you record a new competitive arrival.
