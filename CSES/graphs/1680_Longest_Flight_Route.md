# [1680 — Longest Flight Route](https://cses.fi/problemset/task/1680)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, DAG DP, topological order, path reconstruction  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Uolevi has won a contest, and the prize is a free flight trip that can consist of one or more flights through cities. Of course, Uolevi wants to choose a trip that has as many cities as possible.

Uolevi wants to fly from Syrjälä to Lehmälä so that he visits the maximum number of cities. You are given the list of possible flights, and you know that there are no directed cycles in the flight network.

### Input

The first input line has two integers `n` and `m`: the number of cities and flights. The cities are numbered `1, 2, ..., n`. City `1` is Syrjälä, and city `n` is Lehmälä.

After this, there are `m` lines describing the flights. Each line has two integers `a` and `b`: there is a flight from city `a` to city `b`. Each flight is a one-way flight.

### Output

First print the maximum number of cities on the route. After this, print the cities in the order they will be visited. You can print any valid solution.

If there are no solutions, print `"IMPOSSIBLE"`.

### Constraints

- `2 <= n <= 1e5`
- `1 <= m <= 2e5`
- `1 <= a, b <= n`

### Examples

**Input**
```
5 5
1 2
2 5
1 3
3 4
4 5
```

**Output**
```
4
1 3 4 5
```

---

## What this problem really is

DAG (no directed cycles). Longest path by **number of cities** from `1` to `n`. Print length and one such path, or `IMPOSSIBLE` if `n` unreachable.

## What to use

| Tool | Why |
|------|-----|
| Topological order | DAG → process nodes in topo order for DP |
| `dp[u]` = max cities on a path `1 ↝ u` | `dp[v] = max(dp[v], dp[u]+1)` over edges `u→v` |
| `parent[v]` | Reconstruct the route |

## Approach

**Root idea:** on a DAG, longest paths are DP over a topological order. Track how you arrived for reconstruction.

**Mental picture:** flights only go “forward” in the DAG. At each city, keep the richest way from Syrjälä so far.

**Data structures**

- Digraph; indegrees for Kahn topo (or DFS topo).
- `dp[u]` init `-INF`, `dp[1] = 1` (count cities, starting alone).
- `parent[u] = -1`.

**Steps**

1. Build digraph; compute a topological order of all nodes (or only the part reachable from `1`).
2. For `u` in topo order, if `dp[u]` is valid, for each `u→v`: if `dp[u]+1 > dp[v]`, update `dp[v]` and `parent[v]=u`.
3. If `dp[n]` still invalid → `IMPOSSIBLE`.
4. Else walk parents from `n` to `1`, reverse, print `dp[n]` and the list.

## Walkthrough (on a sample)

Paths `1-2-5` (3 cities) vs `1-3-4-5` (4 cities). Best **`4`**: `1 3 4 5`.

## Pitfalls

- Counting **edges** instead of **cities** — sample wants city count.
- Running Dijkstra (wrong objective / unnecessary); this is unweighted longest path on a DAG.
- Updating DP without topo order can miss improvements.

## Complexity

- Time: `O(n + m)`  
- Space: `O(n + m)`

## Key takeaway

**Longest path on a DAG = DP in topological order.** Store parents to rebuild `1 → ... → n`.
