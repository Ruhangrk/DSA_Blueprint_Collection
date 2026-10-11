# [3303 — Nearest Shops](https://cses.fi/problemset/task/3303)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** BFS, multi-source, graphs  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  
**Editorial:** [Codeforces hints thread](https://codeforces.com/blog/entry/144109)

---

## Problem Statement

There are `n` cities and `m` roads. Each road is bidirectional and connects two cities. It is also known that `k` cities have an anime shop.

If you live in a city, you of course know the local anime shop well if there is one. You would like to find the nearest anime shop that is **not** in your city.

For each city, determine the minimum distance to another city that has an anime shop.

### Input

The first line has three integers `n`, `m` and `k`: the number of cities, roads and anime shops. The cities are numbered `1,2,...,n`.

The next line contains `k` integers: the cities that have an anime shop.

Finally, there are `m` lines that describe the roads. Each line has two integers `a` and `b`: there is a road between cities `a` and `b`.

### Output

Print `n` integers: for each city, the minimum distance to another city with an anime shop. If there is no such city, print `-1` instead.

### Constraints

- `1 <= k <= n <= 1e5`
- `0 <= m <= 2e5`

### Examples

**Input**
```
9 6 4
2 4 5 7
1 2
1 3
1 8
2 4
3 4
5 6
```

**Output**
```
1 1 1 1 -1 1 -1 2 -1
```

---

## What this problem really is

Undirected unweighted graph: for every vertex `v`, distance to the nearest *shop vertex different from `v`*. Ordinary multi-source BFS fails for shop vertices themselves (their nearest shop distance is `0` to themselves).

## What to use

| Tool | Why |
|------|-----|
| Multi-source BFS | All shops expand together on an unweighted graph |
| Two best (dist, shopId) per node | A third shop never helps once two distinct shops already reached a node |
| Answer rule | Non-shop: use best dist; shop: use second-best dist (other shop) |

## Approach

**Root idea:** run a BFS that remembers, at each node, the closest shop and the second-closest shop from a *different* shop id. For a shop city, the answer is the second slot; for others, the first slot (as long as that shop is not “yourself” — for non-shops it never is).

**Mental picture:** paint fire from every shop at once, but each flame carries its shop id. A node keeps the two earliest arrivals from different shops. Anything later is useless for “nearest other shop.”

**How that becomes the method**

1. Build undirected adjacency lists.
2. For each node store `best[2] = {(dist, shopId), ...}` initialized to `INF`.
3. Queue all shops with `best[v][0] = (0, v)`.
4. BFS: from state `(u, whichSlot)`, try to relax neighbors `w` with candidate `(dist[u]+1, shopId)`. Insert into `w`’s first or second slot only if it improves a slot and comes from a **different** shop than the other slot.
5. For each city `v`:
   - if `v` is not a shop: answer = `best[v][0].dist` (or `-1` if `INF`)
   - if `v` is a shop: answer = `best[v][1].dist` (nearest *other* shop), else `-1`

Equivalent view used in some codes: after two-best distances are known, for shop pairs that meet at a node, update both shops with `d1+d2` along that meeting path.

**Data structures:** `adj` lists; `dist[n+1][2]`, `shopFrom[n+1][2]`; queue of pairs `(node, slot)`; shop set / boolean array.

## Walkthrough (on a sample)

Shops at `2,4,5,7`.

- City `1`: reaches shop `2` in one edge → answer `1`.
- City `4` (shop): nearest other shop is `2` at distance `1` → `1`.
- City `5` (shop): component `{5,6}` has only shop `5` → no other shop → `-1`. City `6` reaches shop `5` at dist `1`.
- City `7` (shop): isolated → `-1`.
- City `8`: path `8-1-2` length `2` → `2`.
- City `9`: missing from edges → `-1`.

Matches `1 1 1 1 -1 1 -1 2 -1`.

## Pitfalls

- Using plain multi-source BFS and reporting `0` for every shop.
- Keeping only one distance — shop-to-shop answers go wrong.
- Allowing the same shop id to fill both slots.
- `n,m` large: must be `O(n+m)`, not per-shop BFS.

## Complexity

- Time: `O(n + m)` (each node’s two states enqueued a constant number of times)
- Space: `O(n + m)`

## Key takeaway

“Nearest shop except myself” → multi-source BFS that stores the **two** best distinct shop sources per node.
