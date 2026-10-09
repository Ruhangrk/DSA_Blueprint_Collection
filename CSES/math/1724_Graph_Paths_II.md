# [1724 — Graph Paths II](https://cses.fi/problemset/task/1724)

**Platform:** CSES  
**Source:** CSES Problem Set — Mathematics  
**Tags:** matrix exponentiation, min-plus algebra, shortest path  
**Difficulty / Rating:** Medium  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

Consider a directed weighted graph having `n` nodes and `m` edges. Your task is to calculate the minimum path length from node `1` to node `n` with exactly `k` edges.

### Input

The first input line contains three integers `n`, `m` and `k`: the number of nodes and edges, and the length of the path. The nodes are numbered `1,2,...,n`.

Then, there are `m` lines describing the edges. Each line contains three integers `a`, `b` and `c`: there is an edge from node `a` to node `b` with weight `c`.

### Output

Print the minimum path length. If there are no such paths, print `-1`.

### Constraints

- `1 <= n <= 100`
- `1 <= m <= n*(n-1)`
- `1 <= k <= 1e9`
- `1 <= a,b <= n`
- `1 <= c <= 1e9`

### Examples

**Input**
```
3 4 8
1 2 5
2 3 4
3 1 1
3 2 2
```

**Output**
```
27
```

---

## What this problem really is

Shortest **weight** among walks that use **exactly `k` edges** from `1` to `n`. Ordinary Dijkstra ignores the exact-edge constraint; `k` up to `1e9` blocks naive DP layers.

## What to use

| Tool | Why |
|------|-----|
| Weighted adjacency matrix | `W[u][v] = min` edge weight `u -> v` (else `INF`) |
| Min-plus matrix multiply | `(X ⊗ Y)[i][j] = min_t (X[i][t] + Y[t][j])` concatenates walks |
| Binary exponentiation under `⊗` | “Raise” `W` to power `k` for exactly-`k`-edge distances |

## Approach

**Root idea:** usual matrix multiply does `sum` of products; here you want `min` of sums. That is the tropical / min-plus product. Then `W^{⊗k}[u][v]` = minimum total weight of a `k`-edge walk `u -> ... -> v`.

**Mental picture:** each matrix multiply “glues” two walk segments at a midpoint `t`. Fast pow still works because min-plus multiply is associative.

**How that becomes the method**

1. Fill `W[n][n]` with `INF` (use something like `4e18` so two adds do not overflow `long long` badly — or clamp).
2. For each edge `a -> b` weight `c`: `W[a-1][b-1] = min(W[a-1][b-1], c)`.
3. Compute `D = W^{⊗k}` via binary exponentiation with min-plus multiply.
4. If `D[0][n-1] >= INF/2` (unreachable) print `-1`, else print `D[0][n-1]`.

**Identity for min-plus pow:** start result as “0 on diagonal, INF off” if using the multiply-into-result pattern — or start result as a copy of `W` and exponentiate with power `k` carefully (`pow(W,k)` for `k >= 1`). For `k >= 1`, powering `W` itself is enough.

## Walkthrough (on a sample)

`k = 8`, edges as given. Min-plus powering yields distance `27` from `1` to `3` with exactly 8 hops (one optimal routing among the cycle options).

## Pitfalls

- Using `+/*` matrix multiply instead of `min/+`.
- Not taking `min` on parallel edges when building `W`.
- `INF` too small → overflow when adding, or false “reachable”.
- Printing `INF` instead of `-1`.
- Dijkstra / Bellman without fixing edge count.

## Complexity

- Time: `O(n^3 log k)`  
- Space: `O(n^2)`

## Key takeaway

**Exact-`k`-edge shortest path = min-plus matrix power `W^{⊗k}`** — replace `sum`/`*` with `min`/`+`, then binary-exponentiate.
