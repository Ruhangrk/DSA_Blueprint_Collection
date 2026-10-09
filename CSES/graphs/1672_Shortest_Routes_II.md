# [1672 — Shortest Routes II](https://cses.fi/problemset/task/1672)

**Platform:** CSES  
**Source:** CSES Problem Set — Graph Algorithms  
**Tags:** graphs, Floyd-Warshall, all-pairs shortest paths  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

There are `n` cities and `m` roads between them. Your task is to process `q` queries where you have to determine the length of the shortest route between two given cities.

### Input

The first input line has three integers `n`, `m` and `q`: the number of cities, roads, and queries.

Then, there are `m` lines describing the roads. Each line has three integers `a`, `b` and `c`: there is a road between cities `a` and `b` whose length is `c`. All roads are two-way roads.

Finally, there are `q` lines describing the queries. Each line has two integers `a` and `b`: determine the length of the shortest route between cities `a` and `b`.

### Output

Print the length of the shortest route for each query. If there is no route, print `-1` instead.

### Constraints

- `1 <= n <= 500`
- `1 <= m <= n^2`
- `1 <= q <= 1e5`
- `1 <= a, b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
4 3 5
1 2 5
1 3 9
2 3 3
1 2
2 1
1 3
1 4
3 2
```

**Output**
```
5
5
8
-1
3
```

---

## What this problem really is

Undirected weighted graph, `n <= 500`, many queries. Answer many pairwise shortest-path length queries (or `-1` if disconnected).

## What to use

| Tool | Why |
|------|-----|
| Dense `dist[n+1][n+1]` of `long long` | All-pairs table fits (`500^2`) |
| Floyd-Warshall | `O(n^3)` OK for `n=500`; then each query `O(1)` |
| Min on parallel edges | Keep smallest `c` if multiple roads between same pair |

## Approach

**Root idea:** precompute shortest paths between **every** pair once, then answer queries by lookup. With small `n`, Floyd-Warshall is the straightforward tool.

**Mental picture:** try every possible intermediate city `k` and see if `i → k → j` improves `i → j`.

**Data structures**

- `dist[i][j]` init `INF`; `dist[i][i] = 0`
- For each undirected road `{a,b,c}`: `dist[a][b] = dist[b][a] = min(dist[a][b], c)`

**Floyd-Warshall**

```
for k = 1..n:
  for i = 1..n:
    for j = 1..n:
      if dist[i][k] + dist[k][j] < dist[i][j]:
        dist[i][j] = dist[i][k] + dist[k][j]
```

(Only add when `dist[i][k]` and `dist[k][j]` are not `INF`.)

**Steps**

1. Init matrix, add edges both ways.
2. Run Floyd-Warshall.
3. For each query `(a,b)`: print `dist[a][b]` or `-1` if still `INF`.

## Walkthrough (on a sample)

Roads: `1-2:5`, `1-3:9`, `2-3:3`.

After FW: `1↔2 = 5`, `2↔3 = 3`, `1↔3 = min(9, 5+3) = 8`, `4` unreachable → `-1`. Matches the five outputs.

## Pitfalls

- **`INF + weight` overflow** — use a large `INF` like `4e18` and guard additions, or skip if either side is `INF`.
- **`long long`** for distances.
- Queries `a == b` → `0`.
- Multiple edges — take the minimum weight.

## Complexity

- Time: `O(n^3 + q)`  
- Space: `O(n^2)`

## Key takeaway

**Many pairwise queries + small `n` → Floyd-Warshall once, then O(1) lookups.** For large `n`, you’d switch to repeated Dijkstra instead.
