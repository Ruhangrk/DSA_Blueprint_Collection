# [3357 — Fixed Length Walk Queries](https://cses.fi/problemset/task/3357)

**Platform:** CSES  
**Source:** CSES Problem Set — Advanced Graph Problems  
**Tags:** BFS, walks, parity, queries  
**Difficulty / Rating:** Hard  
**Time limit:** 1.00 s  
**Memory limit:** 512 MB  

---

## Problem Statement

You are given an undirected graph with `n` nodes and `m` edges. The graph is simple and connected.

You start at a specific node, and on each turn you must move through an edge to another node.

Your task is to answer `q` queries of the form: "is it possible to start at node `a` and end up on node `b` after exactly `x` turns?"

### Input

The first line contains three integers `n`, `m` and `q`: the number of nodes, edges and queries. The nodes are numbered `1,2,...,n`.

After this, there are `m` lines which describe the edges. Each line contains two integers `a` and `b`: there is an edge between nodes `a` and `b`.

Finally, there are `q` lines, each describing a query. Each line contains three integers `a`, `b` and `x`.

### Output

For each query, print the answer (`YES` or `NO`) on its own line.

### Constraints

- `2 <= n <= 2500`
- `1 <= m <= 5000`
- `1 <= q <= 1e5`
- `0 <= x <= 1e9`

### Examples

**Input**
```
4 5 6
1 2
2 3
1 3
2 4
3 4
1 2 2
1 4 1
1 4 5
2 2 1
2 2 2
3 4 8
```

**Output**
```
YES
NO
YES
NO
YES
YES
```

---

## What this problem really is

For many queries, decide if an undirected walk of **exact** length `x` exists from `a` to `b`. Lengths are huge (`1e9`), so you cannot simulate — use shortest walks by parity and the “+2 by back-and-forth” rule.

## What to use

| Tool | Why |
|------|-----|
| Parity-state BFS | State `(node, steps mod 2)` — shortest walk of each parity |
| +2 detours | From any length `L` walk, bounce on an edge to get `L+2` |
| Precompute per source | `n <= 2500`, `q` large → BFS from each node once |

## Approach

**Root idea:** once you have the shortest walk of a given parity from `a` to `b`, every larger length of the same parity is achievable by wasting `2` steps at a time (go to a neighbor and back — always possible since `n >= 2` and the graph is connected).

**Mental picture:** two ladders from `a` to `b` — even-length and odd-length. Climb to the lowest rung of the ladder whose parity matches `x`; if `x` is at least that high, you can stand on every higher same-parity rung.

**How that becomes the method**

1. For each source `s`, BFS on states `(v, p)` with `p in {0,1}`:
   - start `(s, 0)` distance `0`;
   - edge flips parity; store `best[s][v][p] =` min length.
2. For query `(a,b,x)`:
   - `need = best[a][b][x % 2]`;
   - `YES` iff `need` is finite and `x >= need`  
     (then `x - need` is automatically even).
3. If the graph is bipartite, the wrong parity stays `INF` forever — correctly yields `NO`.

**Data structures:** `adj`; 3D or flattened `best[n+1][n+1][2]` (about `2 * 2500^2` ints); queue for BFS.

**Space note:** if memory is tight, store only `best[v][p]` per source while answering offline queries grouped by `a`, or keep `dist_even/odd` matrices.

## Walkthrough (on a sample)

- `1 → 2` in `2` steps: shortest even is `2` (via `3`) → `YES`.
- `1 → 4` in `1`: shortest odd path length is `2` (e.g. `1-2-4`), no length-`1` edge → `NO`.
- `1 → 4` in `5`: odd ladder starts at `2` or a longer odd walk; `5 >=` that → `YES` (use the odd cycle / detours as in the statement).
- `2 → 2` in `1`: no odd closed walk of length `1` → `NO`; in `2`: neighbor and back → `YES`.

## Pitfalls

- Using only ordinary `dist(a,b)` and bipartite parity — fails when odd cycles allow the other parity at larger `x`.
- Forgetting `x = 0`: `YES` iff `a == b`.
- `O(q * (n+m))` per query TLE — must precompute.

## Complexity

- Time: `O(n(n + m) + q)` for all parity BFS + answers
- Space: `O(n^2)` for both parities (or `O(n)` per source if queries sorted)

## Key takeaway

Exact-length walks → shortest length per parity, then all larger same-parity lengths via +2 back-and-forth.
